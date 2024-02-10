---
layout: post
title: 'Wrangling Legacy Data: Denormalize and Purge'
no_image: true
---

Does a user *need* to access data from 5+ years ago? For most stores / tables and most organizations the answer is "no".
For most business software, we are managing the day-to-day. We will not be running reports over the past half decade or
accessing a delivery route from 2018. For these cases, we denormalize and purge.

## Why?

We may want to do this for a number of reasons:

- Reduce the effective size of our database and indexes by pruning data
- We are trying to do a migration, but cannot backfill old data
- Reduce our compliance and security overhead by not storing as much data in our live database 

## Denormalize Reports

Say we have a schema similar to this:

```sql
CREATE TABLE delivery_route_stops (
  id integer PRIMARY KEY GENERATED always AS IDENTITY,
  location_id integer NOT NULL, -- FK: Location
  order_id integer, -- FK: Order, if relevant
  stop_number integer NOT NULL
  -- etc
);

CREATE TABLE delivery_routes (
  id integer PRIMARY KEY GENERATED always AS IDENTITY,
  driver_id integer NOT NULL, -- FK: Driver
  start_at timestamp,
  end_at timestamp
  -- etc
);

-- Supporting tables: locations, orders, drivers, etc.
```

The first step here is usually to have some kind of reports table. Pick a time range: 5 years, 7 years, etc. and
generate reports of common durations into these tables. You way want to generate them by day, week, month, or even
multiple durations. We denormalize the major data points into these tables, allowing  us to display a quick overview of
our old data. We will add a reports table like this one:

```sql
CREATE TABLE delivery_route_reports (
  id integer PRIMARY KEY GENERATED always AS IDENTITY,
  driver_ids integer[] NOT NULL, -- Relevant Drivers
  location_ids integer[] NOT NULL, -- Relevant Locations
  gross_value integer NOT NULL, -- How much product was delivered?
  some_other_metric integer NOT NULL, -- Any other summary metrics...
  excel_uri character varying(255) NOT NULL, -- URI for Excel file of the detail report
  start_on date, -- Start and end dates for this particular report
  end_on date
  -- etc
);
```

If there are details on the reports, we will need to decide whether those details need preserved. If so, you might
consider generating the reports as files and saving them to [S3 Flexible Retrieval] or some similar service. S3 Glacier
cheap as hell. Use it. When reports are selected in these timeframes, have them fetched from cold storage instead.

## Purge Old Data

Next we will want to disable viewing details prior to the date we selected. If we are accessing any rows / documents by
date, simply have the app refuse. If we navigate by cursor etc., then we will want to alert the user that they'll need
to consult reports for data older than X years.

**We will want to wait here and sip some tea.**

So far, we've done nothing that isn't immediately reversible. The data has
not changed at all. The UX has been finalized though, and now is the time to wait for feedback. Is 5 years a respectable
timeframe? That's a UX decision, and gathering feedback could take months. If this assumption turns out to be wrong...
just revert the changes to the UI/API.

Once we have validated our UX assumptions, we can begin by deleting data starting with the oldest first. This should be
done in batches, matching your database backup schedule. This way we have a backup before each batch of deletions.

We then go around to different reports and subsets of our data and perform the same dance again.

[S3 Flexible Retrieval]: https://aws.amazon.com/s3/storage-classes/#Flexible_Retrieval
