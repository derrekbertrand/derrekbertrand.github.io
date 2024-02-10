---
layout: post
title: 'Deep Magic: Sparse Tests using a Makefile'
banner_caption: "'Make' typed in neon lettering"
---

When running tests and deployments in CI, we do not want to test and deploy projects that were not affected by code changes. Here we discuss how to handle that with a Makefile.

Throughout this discussion I'll be referencing the [demo project](https://github.com/derrekbertrand/make-ci-demo), which you can pull and experiment with. The workflow is:

```shell
make init git-diff
make test
```

The demo has the following effects:

- If there are no changes, then no tests are run
- If files in `src/front-end` are changed, then the front-end tests are run
- If files in `src/back-end` are changed, then the front-end AND back-end tests are run

I would encourage you to pull the repo and give it a try. There really isn't much there at all.

## Why Make? Isn't that old?

Make is a tool that describes the relationships between targets ("dependencies") and how to update them ("recipes"). It uses those dependencies to determine if anything is out of date, and run only the recipes required to keep our targets up to date. Even better, Make will do this **recursively**.

> Do not cite the Deep Magic to me, Witch! I was there when it was written.  
> &#8212; Aslan

Yes, Make is old. Make is Deep Magic from the dawn of time. Thus, it is installed on the Ubuntu runner in GitHub Actions. Hell, it is probably on your machine right now.

Really, we can't get much better than that: a general purpose dependency resolution tool that's installed on most Mac, Linux and WSL workstations by default!

## How does the demo work?

We need succinct a way to describe our code changes to Make. For this, we use stub files - files that represent work done. When we run `make init`, it [touches some files](https://github.com/derrekbertrand/make-ci-demo/commit/661ee9d7804d65f43f8014cdb71320c5ae36a66e?diff=unified&w=0#diff-76ed074a9305c04054cdebb9e9aad2d818052b07091de1f20cad0bbac34ffb52R23) in the `tmp/make/` directory.

Then when we run `make git-diff`, it uses `git diff` on the relevant project directory. If there have been changes, then Make will [remove the stub file](https://github.com/derrekbertrand/make-ci-demo/commit/661ee9d7804d65f43f8014cdb71320c5ae36a66e?diff=unified&w=0#diff-76ed074a9305c04054cdebb9e9aad2d818052b07091de1f20cad0bbac34ffb52R33-R38) (thus showing the work has not been done).

Finally we run `make test` and the magic happens. Make will detect whether to run the tests or not, and even better it will [respect our project dependencies](https://github.com/derrekbertrand/make-ci-demo/commit/661ee9d7804d65f43f8014cdb71320c5ae36a66e?diff=unified&w=0#diff-76ed074a9305c04054cdebb9e9aad2d818052b07091de1f20cad0bbac34ffb52R51).

That's basically it - that's all the Makefile is doing.

## Final Thoughts

This demo is a stripped down MVP from a real project. The original handles sparse tests using Git's diff command and sparse deploys by comparing Docker image hashes.

I have spent untold hours over the years trying to solve the problem of testing/deploying only what is necessary. I've used many dozens of tools in half a dozen languages trying to solve this and the wildest thing is that none of them are any better at this job than Make - a 47 year old Unix utility.
