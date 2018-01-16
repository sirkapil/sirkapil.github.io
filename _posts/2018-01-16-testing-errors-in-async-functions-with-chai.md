---
layout: post
title: Testing errors in async functions with Chai
tags: [testautomation, chai, javascript]
excerpt_separator: <!--more-->
---

Testing errors in async code which uses promises with Chai can be a bit of a hassle, but it becomes very easy with a plugin called [chai-as-promised](https://github.com/domenic/chai-as-promised). See the example below.

<!--more-->

{% highlight javascript linenos=table%}
/* eslint-env jest */

const chai = require('chai')
const chaiAsPromised = require('chai-as-promised')
const expect = chai.expect

// use chai-as-promised
chai.use(chaiAsPromised)

// a simple async function which throws an error
async function exampleAsyncFunction () {
  throw Error('Message with some message fragment inside')
}

describe('exampleAsyncFunction', () => {

  // this case will result as OK
  it('check for value which is exact match to the error message', () => {
    return expect(exampleAsyncFunction())
      .to.be.rejectedWith(Error, 'Message with some message fragment inside')
  })

  // this case will result as OK, partial error message is checked
  it('check for value which is contained by the error message', () => {
    return expect(exampleAsyncFunction())
      .to.be.rejectedWith(Error, /message fragment/)
  })

  // this case will result with AssertionError: expected promise
  // to be rejected with an error matching /message missing fragment/ but
  // got 'Message with some message fragment inside'
  it('check for value which is NOT contained by the error message', () => {
    return expect(exampleAsyncFunction())
      .to.be.rejectedWith(Error, /message missing fragment/)
  })
})

{% endhighlight %}
