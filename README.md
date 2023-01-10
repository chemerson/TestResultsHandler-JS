# ApplitoolsTestResultsHandler - Javascript/TypeScript
### v2.0.3-beta

The Applitools Test Results Handler extends the capabilities of TestResults with additional API calls.
With these additional API calls you will be able to retrieve additional details at the end of the test.

Note: The Test Results Handler requires your account View Key - which can be found in the admin panel. Contact Applitools support at support@applitools.com if you need further assistance retrieving it.

## The images that can be downloaded are:

- The test baseline image - Unless specified, the images will be downloaded to the working directory.

- The actual images - Unless specified, the images will be downloaded to the working directory.

- The images with the differences highlighted - Unless specified, the images will be downloaded to the working directory.

- Get the status of each step [Missing, Unresolved, Passed, New]

### How to use the tool:

#### To initialize the Handler:

###### Without Runner

```javascript
let results = await eyes.close(false);
const handler= new ApplitoolsTestResultHandler(results, applitoolsViewKey);
```

###### With Runner
```javascript
const allTestResults = await runner.getAllTestResults(false);

for (result of allTestResults) {
  const handler = new ApplitoolsTestResultHandler(result.getTestResults(), applitoolsViewKey);

  await handler.downloadImages(downloadDir, 'diff'); // Example
}
```
###### With Cypress
Cypress requires the use of cy.task to run functions out of the browser process which is needed for creating directories and making api calls to Applitools. Place the following code where you are handling tasks. 

This is typically in the project directory in cypress/plugins/index.js

```javascript
module.exports = (on, config) => {
  // `on` is used to hook into various events Cypress emits
  // `config` is the resolved Cypress config

  on('task', {
    downloadImages (summary) {

      console.log('Downloading Applitools Result Images')

      var ApplitoolsTestResultHandlerCypress = require('ApplitoolsTestResultsHandler-Javascript').ApplitoolsTestResultHandlerCypress;

      var applitoolsViewKey = '<Applitools Read Key>'
      let downloadPath = process.cwd()+'/<Your download directory>'
      var downloadDir = downloadPath

      for (const result of summary) {
        const handler = new ApplitoolsTestResultHandlerCypress(result.testResults, applitoolsViewKey);
        // const handler = new ApplitoolsTestResultHandler(results, applitoolsViewKey, "ProxyServerlURL", "ProxyPort");
        // const handler = new ApplitoolsTestResultHandler(results, applitoolsViewKey, "ProxyServerlURL", "ProxyPort", "ProxyServerUsername", "ProxyServerPassword");
        handler.downloadImages(downloadDir, 'diff'); //valid types = baseline, current, diff
      }

      return true
      
    }
  })
```

In your Cypress test
```javascript
after(() => {
    cy.eyesGetAllTestResults().then((summary) => {
      cy.task('downloadImages',summary).should('equal', true)
    });
  });
```


##### **downloadDiffs** -  Downloading the test images with the highlighted detected differences to a given directory. In case of New, Missing or passed step no image will be downloaded.
```javascript
handler.downloadImages(downloadDir, 'diff')
```

##### **downloadBaselineImages** -  Downloading the test baseline images to a given directory
```javascript
handler.downloadImages(downloadDir, 'baseline');
```

##### **downloadCurrentImages** -  Downloading the test current image to a given directory.
```javascript
handler.downloadImages(downloadDir, 'current');
```

# Further regarding:

Getting Diff Images Manually - http://support.applitools.com/customer/portal/articles/2457891
Getting Current/Baseline Images Manually - http://support.applitools.com/customer/portal/articles/2917372
Extend API features with EyesUtilities - http://support.applitools.com/customer/portal/articles/2913152
