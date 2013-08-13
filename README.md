## LoginKey

The App SDK LoginKey is a feature that allows customers to create an app that runs external to the Rally product—hosted in an intranet portal such as Confluence or SharePoint, or on a user's desktop—without being prompted to enter login credentials. It is especially useful to create dashboards and information radiators for stakeholders that do not have Rally credentials or experience using the Rally product.

The LoginKey essentially provides a way to embed encrypted Rally credentials for a read-only user into the script tag used to reference the App SDK. Because it is possible for a savvy JavaScript programmer to decrypt the string and discover the credentials, we require customers wishing to use the LoginKey feature to read and accept the following disclaimer before using the LoginKey feature.

## Disclaimer

> The Rally LoginKey feature enables customers to view Rally apps and reports without the need to present user credentials (manually). Customers can use the LoginKey feature to show Rally content within systems like Sharepoint, Confluence, wikis, portals, etc. 
 
> The LoginKey feature accesses Rally via the encoded (not encrypted) username and password of a "read-only" Rally user. The encoded username and password are stored in a Javascript file that is delivered to the browser.

> This means that a malicious user could determine the read-only username and password to your Rally subscription by inspecting the code and thus login to your subscription (into the read-only account). 

> We strongly recommend only using this feature for displaying information on internal systems that already requires authentication. 

> If you understand and agree to the security concerns above, please continue reading. 

The following section describes the steps needed to generate and use an encoded login key with Rally's [App SDK](https://prod.help.rallydev.com/app-sdk) LoginKey functionality. This will provide viewer-level functionality for apps into which this code is incorporated. 

Warning: For the purpose of the LoginKey user, a workspace administrator account which has been demoted to a read-only account will not work as it is still considered a NON-read-only account by the LoginKey. 

## Using the App SDK LoginKey Feature

### Running an App outside of Rally

This document explains how to create an app that runs outside of Rally (in an Intranet portal, or on your desktop, for example) without requiring the user to enter Rally credentials. The credentials are
encoded using the [Encoder Page](https://rally1.rallydev.com/apps/html/EncoderPage.html) and supplied to the app using the “loginKey=” parameter on the script tag used to include the App SDK.

Since the encoded credentials could be reverse engineered by a savvy JavaScript programmer, we require the encoded username and password to refer to a read-only user, i.e. one that only has Viewer permissions for all project to which that user has access. This restriction to read-only users is enforced by both the [Encoder Page](https://rally1.rallydev.com/apps/html/EncoderPage.html) and the App SDK at the time your app is initialized.

To get the example working, follow these steps:

Copy and paste the example below into a local HTML file called ExternalAppExample.html:

Modify the meta tags to reflect your own name, version, and vendor (your company name) for your example app.

    <meta name="Name" content="App: your app name here">
    <meta name="Version" content="your version here">
    <meta name="Vendor" content="your company name here">

Replace <font face="courier">'__WORKSPACE_OID'</font> and <font face="courier">'__PROJECT_OID__'</font> with specific workspace and project object ids if you wish; otherwise the default workspace/project for the "LoginKey user" are used.

Replace <font face="courier">'__PROJECT_SCOPING_UP__'</font> and <font face="courier">'__PROJECT_SCOPING_DOWN__'</font> with 'true' or 'false' to override the defaults (false and true, respectively).2.  Generate an encoded login key for a "viewer-only" Rally user and add that value to the &lt;script&gt; tag for sdk.js:

To generate the encoded string, point your browser to the [Encoder Page](https://rally1.rallydev.com/apps/html/EncoderPage.html).

A Rally login dialog box will display when you access the [Encoder Page](https://rally1.rallydev.com/apps/html/EncoderPage.html). Enter the credentials for a "viewer-only" Rally user, one that has only Viewer permissions to any projects to which it has access.

Note: For the purpose of the LoginKey feature, a workspace administrator account which has been demoted to a read-only account will not work as it is still considered a NON read-only account by the LoginKey.

Enter the password for this user in the Password text box and click the 'Submit' button to generate the encoded key. Click the key string to select it, and use your browser's copy function (e.g. Control-C) to copy it to the clipboard.

Paste the encoded string as the value for the loginKey parameter in the sdk.js script tag in the example code below, replacing "ENCODED STRING GOES HERE". The resulting script tag will look similar to the following. Line breaks have been added here for readability, but should not be added when you paste the string into the script tag.

    <script type="text/javascript" src="/apps/1.25/sdk.js?debug=true&loginKey=acda07bd5e53c99ae953f5374cf6e9c4cd996a7ad133a5
    c2c8cc406caf1d9beb|f9f08a4699e83a27ecfbc462c3b51314|e5217f5acc26020b9a45f0009f0b028757b3a2ecfd7a72ed7a6ab5fb2f47df0ab64024ef268bd302a4117e0f93ed9bfb|71,50,65,97,121,3,17,51,117,7,150,38,80,97,148,71"&gt;&lt;/script>

NOTE: The encoder page requires a "viewer-only" user to generate the encoded key string.  The key is visible to anyone using the browser in which the external app is displayed using the brower’s View Page Source feature. This means that a malicious user could determine the readonly user and password to your Rally subscription by inspecting the code. **Treat the login key just as you would a password! Don’t share your code (which contains the key) with anyone you don’t trust.

Example Code:

      <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
      <!-- Copyright (c) 2011 Rally Software Development Corp. All rights reserved -->
      <html>
      <head>
      <title>Login Component Example</title>
      <meta name="Name" content="App Example: LoginKey RallyDataSource" />
      <meta name="Version" content="2011.04" />
      <meta name="Vendor" content="Rally Software" />
      <script type="text/javascript" src="https://rally1.rallydev.com/apps/1.25/sdk.js?loginKey=ENCODED STRING GOES HERE"></script>
      <script type="text/javascript">
      function onLoad() {
      var rallyDataSource = new rally.sdk.data.RallyDataSource('__WORKSPACE_OID__',
      '__PROJECT_OID__', '__PROJECT_SCOPING_UP__', '__PROJECT_SCOPING_DOWN__');
      var config = {type: "hierarchicalrequirement", columnKeys:["FormattedID", "Name"]};
      var table = new rally.sdk.ui.Table(config, rallyDataSource);
      table.display("tableDiv");
      }
      rally.addOnLoad(onLoad);
      </script>
      </head>
      <body>
      <div id="tableDiv" style="float:left;width:400px"></div>
      </body>
      </html>

### Running a Shared App outside of Rally

Similar to the steps above, you can also display shared Rally apps.  This means the App must be "shared" to run outside of Rally.  An unshared app will result in a 404 Page Not Found error.

### Confluence access to Standard Reports

This example shows how to display Rally [Standard Reports](http://prod.help.rallydev.com/help/standard-reports) in a Confluence page without requiring Confluence users to enter Rally credentials.

1.  Create a Confluence page and open it for editing. Make sure you select the "Wiki Markup" tab in the Confluence editor.

<p>Example Code:

      <title>Login Component Example</title>
      <meta name="Name" content="App Example: Confluence Standard Report" />
      <meta name="Version" content="2011.04" />
      <meta name="Vendor" content="Rally Software" />
      <script type="text/javascript" src="https://rally1.rallydev.com/apps/1.25/sdk.js?loginKey=ENCODED
      STRING GOES HERE"></script>
      <script type="text/javascript">
      function onLoad() {
      rally.sdk.ui.AppHeader.destroy();
      var reportConfig = {
      report: rally.sdk.ui.StandardReport.IterationBurndown,
      width : 400,
      height: 300
      };
      var report = new rally.sdk.ui.StandardReport(reportConfig);
      report.display("reportDiv");
      }
      rally.addOnLoad(onLoad);
      </script>
      <div id="reportDiv" style="float:left; width: 400px; margin-left:20px"></div>

### SharePoint access to Standard Reports

This example shows how to display a Rally Standard Report in a SharePoint form Web Part without requiring SharePoint users to enter Rally credentials.

<p>Example Code:

      <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
      <!-- Copyright (c) 2011 Rally Software Development Corp. All rights reserved -->
      <html>
      <head>
      <title>Login Component Example</title>
      <meta name="Name" content="App Example: SharePoint Standard Report" />
      <meta name="Version" content="2011.04" />
      <meta name="Vendor" content="Rally Software" />
      <script type="text/javascript" src="https://rally1.rallydev.com/apps/1.25/sdk.js?loginKey=ENCODED STRING GOES HERE"></script>
      <script type="text/javascript">
      function onLoad() {
      rally.sdk.ui.AppHeader.destroy();
      var reportConfig = {
      report: rally.sdk.ui.StandardReport.IterationBurndown,
      width : 400,
      height: 300
      };
      var report = new rally.sdk.ui.StandardReport(reportConfig);
      report.display("reportDiv");
      }
      rally.addOnLoad(onLoad);
      </script>
      </head>
      <body>
      <div id="reportDiv" style="float:left; width: 400px; margin-left:20px"></div>
      </body>
      </html>

### Multiple Rally Standard Reports in one SharePoint WebPart

Adding a second WebPart to a SharePoint page will cause the first WebPart to stop displaying its Rally content. Instead, use only one WebPart, but modify your HTML code to put each chart in its own div.  You will need to use a separate config object for each [Standard Report](http://prod.help.rallydev.com/help/standard-reports).

<p>Example Code:

      <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
      <!-- Copyright (c) 2011 Rally Software Development Corp. All rights reserved -->
      <html>
      <head>
      <title>Login Component Example</title>
      <meta name="Name" content="App Example: SharePoint Standard Reports" />
      <meta name="Version" content="2011.04" />
      <meta name="Vendor" content="Rally Software" />
      <script type="text/javascript" src="https://rally1.rallydev.com/apps/1.25/sdk.js?loginKey=ENCODED STRING GOES HERE"></script>
      <script type="text/javascript">
      function onLoad() {
      rally.sdk.ui.AppHeader.destroy();
      var reportConfig1 = {
      report: rally.sdk.ui.StandardReport.IterationDefectsbyState,
      width : 400,
      height: 300
      };
      var report1 = new rally.sdk.ui.StandardReport(reportConfig1);
      report1.display("reportDiv1");
      var reportConfig2 = {
      report: rally.sdk.ui.StandardReport.IterationDefectsbyPriority,
      width : 400,
      height: 300
      };
      var report2 = new rally.sdk.ui.StandardReport(reportConfig2);
      report2.display("reportDiv2");
      }
      rally.addOnLoad(onLoad);
      </script>
      </head>
      <body>
      <div id="reportDiv1" style="float:left;width:400px"></div>
      <div id="reportDiv2" style="float:left; width: 400px; margin-left:20px"></div>
      </body>
      </html>

## On-Premises Caveats

LoginKey functionality works out of box for Rally's on-demand (SAAS) editions. It still works in On-Premises Rally environment but with some limitations. LoginKey with on-demand Rally has a capability to trick the browser to create a separate session cookie to work in a scenario when a user loads a Rally app or a report externally in a browser (using encoded read-only credentials) alongside with another tab in the same browser where an editor user is already logged in to Rally tool. This use case should be avoided and is not supported with On-Premises. 
