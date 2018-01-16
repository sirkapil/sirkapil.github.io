---
layout: post
title: Triggering CircleCI builds from tags
tags: [circleci]
excerpt_separator: <!--more-->
---

With CircleCI 2.0 there is a possibility execute jobs on git tags, but the usage of this feature has a few quirks that are not self-evident and needed to be highlighted from the documentation.

<!--more-->

### As the documentation states: 'For a tag push unaffected by any filters, CircleCI skips the job'

... so a job **must have a filters tags section** to run as a part of a tag push

{% highlight yaml linenos=table%}

workflows:
  version: 2
  build-workflow:
    jobs:
      - build:
          filters:
            tags:
              only: /.*/

{% endhighlight %}

### If a job has an other job it is depending on

... the **first job must have a filter tags section, to be able to use filter tags in the dependent build**

{% highlight yaml linenos=table%}

workflows:
  version: 2
  build-n-deploy:
    jobs:
      - build:
          filters:
            tags:
              only: /.*/
      - deploy:
          requires:
            - build
          filters:
            tags:
              only: /^release-*/

{% endhighlight %}

In this case the
- build job will be ran on every tag (and every branch)
- the deploy job will be ran only on the 'release-*' branches (and on no branch)

### The tag builds will be visible only on the `Builds` tab, but not on the `Workflows`.

This can be easily missed, resulting in many minutes of debugging...