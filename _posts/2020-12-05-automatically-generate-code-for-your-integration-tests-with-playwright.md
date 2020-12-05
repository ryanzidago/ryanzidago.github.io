---
layout: post
title:  "Automatically generate code for your integration tests with Playwright"
date:   2020-12-03 17:30:00 +0100
---

# The problem with traditional integration tests
Traditional integration tests (with the browser), take me a lot of time to write. I have to inspect the page that I'd like my test to use, find the proper CSS selector to either interact with the element or extract the HTML content of the element to make an assertion. Those types of tests can break quite easily since CSS selectors can be subject to frequent changes. Even simply selecting an element was painfully awkward sometime ... I could use some custom attributes for the sole purpose of testing (something like `data-test-id="a-formular"`), but now I need to maintain those attributes and production code also has those test attributes.

I always found those integration tests important, but painful to write and quite slow to execute. At least now with Playwright and its `codegen` tool, I will not have to write them, so I can write more of them and test more edge cases.

# Using Playwright CLI to automatically generate our integration test code for us

I recently discovered Microsoft's [Playwright](https://github.com/microsoft/playwright#-playwright), a "Node.js library to automate Chromium, Firefox and Webkit with a single API". With Playwright comes the [Playwright CLI](https://github.com/microsoft/playwright-cli); and with Playwright CLI comes the `codegen` tool, that will allow you to automatically translate user interaction via the browser into JavaScript code!

Let's try it out. Create a new directory named `playwright` and `cd` into this directory:

```bash
$ mkdir playwright && cd playwright
```

Then install the `playwright-cli` via NPM:
```bash
$ npm install --save-dev playwright-cli
```

Now, you can use npx to run the playwright-cli:

```bash
$ npx playwright-cli --help
```

It will display the following text:
```
Usage: playwright-cli [options] [command]

Options:
  -V, --version                          output the version number
  -b, --browser <browserType>            browser to use, one of cr, chromium, ff, firefox, wk,
                                         webkit (default: "chromium")
  --color-scheme <scheme>                emulate preferred color scheme, "light" or "dark"
  --device <deviceName>                  emulate device, for example  "iPhone 11"
  --geolocation <coordinates>            specify geolocation coordinates, for example
                                         "37.819722,-122.478611"
  --lang <language>                      specify language / locale, for example "en-GB"
  --proxy-server <proxy>                 specify proxy server, for example
                                         "http://myproxy:3128" or "socks5://myproxy:8080"
  --timezone <time zone>                 time zone to emulate, for example "Europe/Rome"
  --timeout <timeout>                    timeout for Playwright actions in milliseconds
                                         (default: "10000")
  --user-agent <ua string>               specify user agent string
  --viewport-size <size>                 specify browser viewport size in pixels, for example
                                         "1280, 720"
  -h, --help                             display help for command

Commands:
  open [url]                             open page in browser specified via -b, --browser
  cr [url]                               open page in Chromium
  ff [url]                               open page in Firefox
  wk [url]                               open page in WebKit
  codegen [options] [url]                open page and generate code for user actions
  screenshot [options] <url> <filename>  capture a page screenshot
  pdf [options] <url> <filename>         save page as pdf
  install                                Ensure browsers necessary for this version of
                                         Playwright are installed
  help [command]                         display help for command
```

As you can see, there is a `codegen` command. We can use it like so:
```bash
$ npx playwright-cli codegen -o <filename> <url>
````

Let's say that we want to test the edX.org website. We will create a script that will make sure that a user can :
* look for programming courses
* filter the results based on the following criterias:
  * introductory courses
  * available now
  * in English
* then, the script will select the first course appearing in the results!

All of this, without writing any code, we will let the `codegen` command take care of it for us.

```bash
$ npx playwright-cli codegen -o edx_test.js edx.org
```

It will open the browser. You can then interact with the browser as you usually do, except this time your actions will generate code:

![edx test](/assets/gifs/edx_test.gif)

Since we provided the `-o` option, the generated code is also saved onto the `test.js` file:

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({
    headless: false
  });
  const context = await browser.newContext();

  // Open new page
  const page = await context.newPage();

  // Go to https://www.edx.org/
  await page.goto('https://www.edx.org/');

  // Click input[id="home-search"]
  await page.click('input[id="home-search"]');

  // Fill input[id="home-search"]
  await page.fill('input[id="home-search"]', 'programming');

  // Press Enter
  await page.press('input[id="home-search"]', 'Enter');
  // assert.equal(page.url(), 'https://www.edx.org/search?q=programming');

  // Click div:nth-child(4) div .collapsible .btn-block .collapsible-title .svg-inline--fa path
  await page.click('div:nth-child(4) div .collapsible .btn-block .collapsible-title .svg-inline--fa path');

  // Click //label[normalize-space(.)='Introductory161']
  await page.click('//label[normalize-space(.)=\'Introductory161\']');
  // assert.equal(page.url(), 'https://www.edx.org/search?level=Introductory&q=programming');

  // Click text="Availability"
  await page.click('text="Availability"');

  // Click text="Available now"
  await page.click('text="Available now"');
  // assert.equal(page.url(), 'https://www.edx.org/search?availability=Available%20now&level=Introductory&q=programming');

  // Click text="Language"
  await page.click('text="Language"');

  // Click text="English"
  await page.click('text="English"');
  // assert.equal(page.url(), 'https://www.edx.org/search?availability=Available%20now&language=English&level=Introductory&q=programming');

  // Click //div[starts-with(normalize-space(.), 'Computer Science forWeb Programming…Schools and Partners: HarvardX…Professional ')]/div[1]/img
  await page.click('//div[starts-with(normalize-space(.), \'Computer Science forWeb Programming…Schools and Partners: HarvardX…Professional \')]/div[1]/img');
  // assert.equal(page.url(), 'https://www.edx.org/professional-certificate/harvardx-computer-science-for-web-programming');

  // Click //div[normalize-space(.)="CS50's Introduction to Computer Science"]
  await page.click('//div[normalize-space(.)="CS50\'s Introduction to Computer Science"]');

  // Click text="View the course"
  await page.click('text="View the course"');
  // assert.equal(page.url(), 'https://www.edx.org/course/cs50s-introduction-to-computer-science');

  // Close page
  await page.close();

  // ---------------------
  await context.close();
  await browser.close();
})();
```

As you can see, it even automatically generated some basic (though commented) assertions for us! We can go a step further and integrate our test to a test framework, like [Jest](https://github.com/facebook/jest).

# Integrating the generated code to a test framework

Install Jest:
```bash
npm install --save-dev jest
```

Then, create a `package.json`:
```bash
$ touch package.json
```
And put the following content in it:
```
{
  "scripts": {
    "test": "jest"
  }
}
```

We can rename our `test.js` file to something more descriptive:
```bash
mv test.js edx.features.users-can-search-courses-and-filter-results.test.js
```

Replace the content of `edx.features.users-can-search-courses-and-filter-results.test.js` with this:

```javascript
const { chromium } = require('playwright');
const assert = require('assert');

// set Jest's timeout to 10 seconds
jest.setTimeout(1_000 * 10);

beforeAll(async () => {
  browser = await chromium.launch();
});

afterAll(async () => {
  await browser.close();
});

beforeEach(async () => {
  page = await browser.newPage();
});

afterEach(async () => {
  await page.close();
});

test('a user should be able to search courses and filter results', async () => {
  const browser = await chromium.launch({
    headless: false
  });
  const context = await browser.newContext();

  // Open new page
  const page = await context.newPage();

  // Go to https://www.edx.org/
  await page.goto('https://www.edx.org/');

  // Click input[id="home-search"]
  await page.click('input[id="home-search"]');

  // Fill input[id="home-search"]
  await page.fill('input[id="home-search"]', 'programming');

  // Press Enter
  await page.press('input[id="home-search"]', 'Enter');
  assert.equal(page.url(), 'https://www.edx.org/search?q=programming');

  // Click div:nth-child(4) div .collapsible .btn-block .collapsible-title .svg-inline--fa path
  await page.click('div:nth-child(4) div .collapsible .btn-block .collapsible-title .svg-inline--fa path');

  // Click //label[normalize-space(.)='Introductory161']
  await page.click('//label[normalize-space(.)=\'Introductory161\']');
  assert.equal(page.url(), 'https://www.edx.org/search?level=Introductory&q=programming');

  // Click text="Availability"
  await page.click('text="Availability"');

  // Click text="Available now"
  await page.click('text="Available now"');
  assert.equal(page.url(), 'https://www.edx.org/search?availability=Available%20now&level=Introductory&q=programming');

  // Click text="Language"
  await page.click('text="Language"');

  // Click text="English"
  await page.click('text="English"');
  assert.equal(page.url(), 'https://www.edx.org/search?availability=Available%20now&language=English&level=Introductory&q=programming');

  // Click //div[starts-with(normalize-space(.), 'Computer Science forWeb Programming…Schools and Partners: HarvardX…Professional ')]/div[1]/img
  await page.click('//div[starts-with(normalize-space(.), \'Computer Science forWeb Programming…Schools and Partners: HarvardX…Professional \')]/div[1]/img');
  assert.equal(page.url(), 'https://www.edx.org/professional-certificate/harvardx-computer-science-for-web-programming');

  // Click //div[normalize-space(.)="CS50's Introduction to Computer Science"]
  await page.click('//div[normalize-space(.)="CS50\'s Introduction to Computer Science"]');

  // Click text="View the course"
  await page.click('text="View the course"');
  assert.equal(page.url(), 'https://www.edx.org/course/cs50s-introduction-to-computer-science');

  // Close page
  await page.close();

  // ---------------------
  await context.close();
  await browser.close();
})
```
As you can see, I required the `assert` module and commented-out the `assert`s in the test. I also set Jest's timeout for this test to 10 seconds, otherwise, we might get a timeout error with Jest's default timeout of 5 seconds only.

Then run the test with
```bash
$ npm run test edx.features.users-can-search-courses-and-filter-results.test.js
```

And you'll see that our test pass!
```
> @ test /home/user/projects/playwright
> jest "edx.features.users-can-search-courses-and-filter-results.test.js"

 PASS  ./edx.features.users-can-search-courses-and-filter-results.test.js (8.392 s)
  ✓ a user should be able to search courses and filter results (7654 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        8.837 s
Ran all test suites matching /edx.features.users-can-search-courses-and-filter-results.test.js/i.
```
