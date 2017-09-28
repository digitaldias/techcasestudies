---
layout: post
title: "Adding tests to improve quality standards at Lufthansa"
author: "Julien Stroheker, Damien Caro, and Max Knor"
author-link: "#"
date: 2017-09-27
categories: [DevOps]
color: "blue"
image: "images/2017-06-15-Lufthansa/tile-mydeaOverview.png" #must be 440px wide
excerpt: Lufthansa Industry Solutions had been delivering new features for its innovation management app without testing of any kind. They knew this needed to change to maintain quality and avoid regressions. Working with Microsoft, they explored best practices for implementing testing into their build-and-release pipeline.  
language: [English]
verticals: [Hospitality & Travel]
geolocation: [Europe]
---


<img alt="Lufthansa Logo" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/Lufthansa-Industry_Solutions-logo.png" width="300">

Lufthansa Industry Solutions had been delivering new features for its innovation management app without testing of any kind. They knew this needed to change to maintain quality and avoid regressions. Working with Microsoft, they explored best practices for implementing testing into their build-and-release pipeline.

**Key technologies used:**

- [SharePoint Online](https://products.office.com/sharepoint/sharepoint-online-collaboration-software)
- [Web Apps](https://azure.microsoft.com/en-us/services/app-service/web/)
- [Visual Studio Team Services](https://www.visualstudio.com/team-services/)
- [AngularJS 1.x](https://angularjs.org/)
- [Jasmine](https://jasmine.github.io/)
- [Karma](https://karma-runner.github.io/1.0/index.html)
- [Gulp](http://gulpjs.com/)
- [Visual Studio 2017](https://www.visualstudio.com/vs/)
- [Azure Storage](https://azure.microsoft.com/en-us/services/storage/?v=16.50)

**Hackfest members:**

- Till Schomborg – Product Owner & Manager, Lufthansa Industry Solutions
- Daniel Staroste – Consultant / Solution Engineer, Lufthansa Industry Solutions
- Taylan Kenanoglu – Frontend Developer, Lufthansa Industry Solutions
- Ole Albers – Senior Software Developer, Lufthansa Industry Solutions
- Benjamin Wagener – Developer, Lufthansa Industry Solutions
- [Julien Stroheker](https://twitter.com/ju_stroh) – Technical Evangelist, Microsoft
- [Damien Caro](http://twitter.com/dcaro) – Technical Evangelist Manager, Microsoft
- Max Knor – Software Development Lead, Microsoft

## Customer profile ##

[Lufthansa Industry Solutions](https://www.lufthansa-industry-solutions.com) is a service provider for IT consulting and system integration. This subsidiary of Lufthansa supports its customers in the digital transformation of their companies. The customer base includes not only companies within Lufthansa Group, but also more than 200 companies in a variety of industries. The Norderstedt-based company employs more than 1,200 people at several branches in Germany, Switzerland, and the United States.

## Mydea, a crowd-based innovation management platform ##

Lufthansa developed *Mydea*, an application that enables employees to take an active part in shaping their company’s future. This innovation management application involves the entire staff by means of gamification and crowdfunding mechanisms—from generating and developing ideas to deciding how to implement innovative projects. This promotes a high participation rate and employee motivation, thus contributing to a flourishing, long-lasting culture of innovation inside the organization.

*Overview of the Mydea application*

<img alt="Overview of the Mydea application" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaOverview.png">

<br/>

Mydea is available in the cloud through Office 365, as well as on-premises. Because the app is SharePoint-based, other Microsoft applications such as Microsoft Project and Microsoft Teams can be easily linked with Mydea. SharePoint’s modular concept—in its standard configuration—makes it possible to adapt Mydea to the needs of different companies. 

## Problem statement ##

The Mydea team is small with only three developers. This allows them to work like a startup and to be very independent, thanks to the cloud. From the beginning, the team focused on delivering new features without implementing any kind of tests. This created a problem for growth. Today, the team realizes they need testing to improve the quality and make sure they can add new code without any regressions.

The Mydea team asked Microsoft to spend three days with them and cover the **test** topic. During this hackfest, we had the following discussions:

* Unit tests on the front end (AngularJS)
* Unit tests on the back end (.NET)
* Automated UI tests / load tests (using Selenium) acting as end-to-end (E2E) tests

Lufthansa was already on the way to implementing tests, but they needed some recommendations in terms of best practices and especially best practices on how to integrate it into their current build-and-release pipeline using Visual Studio Team Services.

## Approach to testing ## 

We discussed the test-driven development (TDD) approach with the team and their vision for it. The short definition of TDD is to think about the tests first before coding anything in the application. We decided to take this approach for the next few days. Trying it would help Lufthansa see how they can implement it into future projects.

Because Mydea is split into multiple projects, and interacting with different layers and technologies such as Office 365 or Azure, it was important to keep the scope of the unit tests well defined. Indeed, the unit tests must test the logic of code and should be fast to execute without any dependencies. For example, on the front-end piece, we decided to mock up the back-end response and make sure our Angular controller worked fine with that mockup. 

### Front-end unit tests in AngularJS ###

For the front end, we decided to start from scratch by implementing a new feature called *Trends*. It will be an interface in the product to catch innovative trends and to inspire users to generate ideas.

Using the TDD approach, before implementing the controller and the directive with Angular, we asked ourselves what the needs of this feature will be and how we can map that to tests.

We listed the following requirements:

* Mandatory fields should give visual feedback.
* We cannot pick the same tags more than once.
* Adds tags correctly.

Now that we have our tests, we can start the implementation. We decided to use Jasmine as a framework and Karma to run it. 

Jasmine is a behavior-driven development framework for testing JavaScript code. It does not depend on any other JavaScript frameworks. It does not require a Document Object Model (DOM). And it has a clean, obvious syntax so that you can easily write tests. Multiple choices are available, such as Mocha or Chai, but we didn't spend time comparing them—the idea was to at least start to implement tests. 

To run our tests, we needed Karma. The main goal for Karma is to bring a productive testing environment to developers. Instead of having to set up loads of configurations, developers can just write the code and get instant feedback from their tests. This quick feedback helps them be more productive and creative.

Karma has this concept of *launcher*, a platform on which the tests will be run. Because we are writing front-end code, it means this code will be run in a browser such as Chrome, Internet Explorer, or Firefox. In our case, we decided to use PhantomJS because we wanted to run our tests in an in-memory browser—this will help us later in Visual Studio Team Services.

The first thing to do is install the correct dependencies in our project using NPM. We installed the following: 

* jasmine-core: Official Jasmine package
* karma: Official Karma package
* karma-jasmine: Karma adapter for the Jasmine testing framework
* karma-phantomjs-launcher: Karma launcher for PhantomJS

We installed it using ```npm install jasmine-core karma karma-jasmine karma-phantomjs-launcher --save-dev```.

**Note:** Karma could be installed globally if you prefer.

As an option, we also decided to use these plug-ins in Karma:

* karma-coverage: Karma plug-in to generate code coverage
* karma-junit-reporter: Karma plug-in to generate report compatible with Team Services
* karma-chrome-launcher: Karma launcher for Google Chrome, Google Chrome Canary, and Google Chromium

When we finished the installation, we set up Karma with the command ```karma init```.

After answering the questions and modifying a few settings, this is the ```karma.config.js``` file that we used:

{% highlight javascript %}
// Karma configuration

module.exports = function (config) {
    config.set({
        // base path that will be used to resolve all patterns (eg. files, exclude)
        basePath: '',
        // frameworks to use
        // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
        frameworks: ['jasmine'],
        // list of files / patterns to load in the browser
        files: [
            "Scripts/moment-with-locales.js",
            "Scripts/jquery-3.1.1.min.js",
            "Scripts/jquery.fabric.min.js",
            "Scripts/jquery.validate.min.js",
            "Scripts/modernizr-2.8.3.js",
            "Scripts/respond.min.js",
            "Scripts/spcontext.js",
            "Scripts/angular.min.js",
            "Scripts/angular-resource.min.js",
            "Scripts/angular-cookies.min.js",
            "Scripts/angular-animate.min.js",
            "Scripts/angular-mocks.js",
            "Scripts/angular-route.min.js",
            "Scripts/angular-sanitize.min.js",
            "Scripts/angular-animate.min.js",
            "Scripts/angular-material/angular-material.min.js",
            "Scripts/angular-messages.min.js",
            "Scripts/angular-aria.min.js",
            "Scripts/i18n/angular-locale_de-de.js",
            "Scripts/Chart.js",
            "Scripts/bootstrap.min.js",
            "Scripts/video.js",
            "Scripts/verge.js",
            "Scripts/jquery.signalR-2.2.1.min.js",
            "SignalR/hubs",
            "App/dist/Mydea.js",
            "App/test/TrendFormController.spec.js"
        ],
        // preprocess matching files before serving them to the browser
        // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
        // Code coverage using karma-coverage on our JS code :
        preprocessors: {
            'App/dist/Mydea.js': ['coverage']
        },
        // test results reporter to use
        // possible values: 'dots', 'progress'
        // available reporters: https://npmjs.org/browse/keyword/karma-reporter
        reporters: ['progress', 'coverage', 'junit'],
        // Configuration of karma-junit-reporter to be able to publish report for the Unit Test and the Code Coverage and grab it in VSTS
        junitReporter: {
            outputDir: '', // results will be saved as $outputDir/$browserName.xml
            outputFile: undefined, // if included, results will be saved as $outputDir/$browserName/$outputFile
            suite: '', // suite will become the package name attribute in xml testsuite element
            useBrowserName: true, // add browser name to report and classes names
            nameFormatter: undefined, // function (browser, result) to customize the name attribute in xml testcase element
            classNameFormatter: undefined, // function (browser, result) to customize the classname attribute in xml testcase element
            properties: {}, // key value pair of properties to add to the <properties> section of the report
            xmlVersion: null // use '1' if reporting to be per SonarQube 6.2 XML format
        },
        // Configuration of karma-coverage to be able to publish report for the Unit Test and the Code Coverage and grab it in VSTS
        coverageReporter: {
            // specify a common output directory
            dir: '../coverage/',
            reporters: [
                // reporters not supporting the `file` property
                { type: 'html', subdir: '.' },
                { type: 'cobertura', subdir: '.', file: 'cobertura-coverage.xml' }
            ]
        },
        // We increased those values for the Hosted VSTS Agent
        browserNoActivityTimeout: 120000, //default 10000
        browserDisconnectTimeout: 10000, // default 2000
        browserDisconnectTolerance: 1, // default 0
        // web server port
        port: 9876,
        // enable / disable colors in the output (reporters and logs)
        colors: true,
        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_DEBUG,
        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: false,
        // start these browsers
        // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
        // We are using PhantomJS, the others one could generated issues in VSTS
        browsers: ['PhantomJS'],
        // Continuous Integration mode
        // if true, Karma captures browsers, runs the tests and exits
        // We need to setup the singleRun to true for VSTS
        singleRun: true,
        // Concurrency level
        // how many browser should be started simultaneous
        concurrency: 1
    })
}
{% endhighlight %}

<br/>

Now we can launch Karma and run our tests using the ```karma start``` command. 

The next step is to write our code using Jasmine. We decided to create a folder named ```tests``` in the solution and create our first file named ```TrendFormController.spec.js```.

Like most of the test frameworks, the idea is to define tests using the keywords ```describe```, ```it``` or ```expect```.

For example:

{% highlight javascript %}
describe('Addition two tags', function () {
    it('adds two tags together', function () {
        expect("Tag1" + "Tag2").toEqual(["Tag1","Tag2"]);
    });
});
{% endhighlight %}

<br/>

As mentioned before, we will need to "mock" some data and prepare our controller to be tested using the method ```beforeEach``` with Jasmine.

This is one part of our tests for the Trend controller:

{% highlight javascript %}
describe("TrendFormController Tests", function () {
    beforeEach(module('Mydea'));

    var $controller;
    var $scope;
    var $mdDialog;
    var TrendService;
    var IdeaService;
    var controller;

    beforeEach(inject(function (_$controller_) {
        $controller = _$controller_;
        $scope = {};
        $mdDialog = {};
        TrendService = {};
        IdeaService = {};
        controller = $controller('TrendFormController', {
            $scope: $scope,
            $mdDialog: $mdDialog,
            TrendService: TrendService,
            IdeaService: IdeaService
        });
    }));

    describe('Tags', function () {
        
        it('is an array', function () {
            expect(controller.taxPickerLoaded).toEqual(false);
        });

        it('adds tags correctly', function () {
            controller.addTag({
                TermGuid: 'some-guid',
                Label: 'Some Tag'
            });
            expect(controller.asyncTags.length).toEqual(1);
            expect(controller.asyncTags[0].TermGuid).toEqual('some-guid');
            expect(controller.asyncTags[0].Label).toEqual('Some Tag');
        });

        it('does not add the same tag twice', function () {
            var tag = {
                TermGuid: 'some-guid',
                Label: 'Some Tag'
            }
            controller.addTag(tag);
            controller.addTag(tag);
            expect(controller.asyncTags.length).toEqual(1);
        });
    });
});
{% endhighlight %}

<br/>

Now that we have three tests, we can run Karma locally and see how it goes with ```karma start```.

```
[INFO [karma]: [39mKarma v1.7.0 server started at http://0.0.0.0:9876/
[INFO [launcher]: [39mLaunching browser PhantomJS with concurrency 1
[INFO [launcher]: [39mStarting browser PhantomJS
[INFO [PhantomJS 2.1.1 (Windows 8 0.0.0)]: [Connected on socket w0OJF9qAAA with id 70776
PhantomJS 2.1.1 (Windows 8 0.0.0): Executed 0 of 3 SUCCESS (0 secs / 0 secs)
[2KPhantomJS 2.1.1 (Windows 8 0.0.0): Executed 1 of 3 SUCCESS (0 secs / 0.374 secs)
[2KPhantomJS 2.1.1 (Windows 8 0.0.0): Executed 2 of 3 SUCCESS (0 secs / 0.387 secs)
[2KPhantomJS 2.1.1 (Windows 8 0.0.0): Executed 3 of 3 SUCCESS (0 secs / 0.397 secs)
[2KPhantomJS 2.1.1 (Windows 8 0.0.0): Executed 3 of 3 SUCCESS (0.027 secs / 0.397 secs)
```

<br/>

To give more context, here is a code snippet from the TrendsController:

{% highlight javascript %}
'use strict';

function TrendFormController($scope, $mdDialog, TrendService, IdeaService) {
    this.$mdDialog = $mdDialog;
    this.TrendService = TrendService;
    this.IdeaService = IdeaService;
    this.$scope = $scope;

    this.taxPickerLoaded = false;
    this.taxonomyRootNode = "Tags";
    this.asyncTags = [];

    this.formData = {};
    this.nodes = [{
        Label: "Tags",
        TermGuid: null,
        Children: []
    }];

    this.addTag = angular.bind(this, this.addTag);
    this.tagAlreadySelected = angular.bind(this, this.tagAlreadySelected);

}

[...]

TrendFormController.prototype.toggleTax = function () {
    var self = this;
    if (!this.taxonomyPicker) {
        this.taxPickerLoaded = false;
        this.IdeaService.getTerms().then(function (data) {
            self.nodes[0].Children = data;
            self.taxPickerLoaded = true;
        });
    }
    this.taxonomyPicker = !this.taxonomyPicker;
};

TrendFormController.prototype.tagAlreadySelected = function (tagId) {
    var tagFound = false;
    for (var i in this.asyncTags) {
        if (this.asyncTags[i].TermGuid == tagId) {
            tagFound = true;
            break;
        }
    }
    return tagFound;
};

TrendFormController.prototype.addTag = function (node, path) {
    if (!this.tagAlreadySelected(node.TermGuid)) {
        this.asyncTags.push({
            TermGuid: node.TermGuid,
            Label: node.Label,
            Path: path
        });
    }
};

[...]

angular.module('Mydea.Controller').controller('TrendFormController', TrendFormController);
{% endhighlight %}

<br/>

Now that we have our tests implemented for our code, let's automate it and integrate it inside the Team Services build.

Because the Mydea team is already using Gulp, we decided to add a task to execute the tests from it.

{% highlight javascript %}
// Run test once and exit
gulp.task('tests', function (done) {
    new Server({
        configFile: __dirname + '/karma.conf.js',
        singleRun: true
    }, function (exitCode) {
        process.exit(exitCode);
        done(exitCode);
    }).start();
});
{% endhighlight %}

### Back-end unit tests ###

The unit tests also need to cover the back-end component of the application that manages the interaction with SharePoint. We organized one of the workstreams to focus on the creation of those tests.

#### 1. Scoping

We started by defining the minimum set of tests that are needed to verify that the back end is working properly. It was then decided to implement the following set of tests:

- Validating that the instantiation of a ```Trend``` class has the expected number of fields.
- Verifying that we can create a list. 
- Verifying that we can delete a list.
    
<br/>
    
#### 2. Applying the AAA pattern 

When you write tests, a good practice is to organize your code with the AAA pattern. Your code will therefore be arranged in the following three sections: 

- **Arrange:** Initialize the objects or set the values that will be needed to run the test.
- **Act:** Run the test with the arranged parameters.
- **Assert:** Verify that the method being tested has produced the expected results.
    
<br/>
    
**Tip:** Create a code snippet to facilitate the use of the test method consistently following the AAA pattern. For details, see [how to create a code snippet in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/walkthrough-creating-a-code-snippet).

It also is a good practice to name your test methods with the following pattern:
       
``` "WorkToBeDone_WhatIsTested_ExpectedResult"  ```
    
<br/>
    
#### 3. Implementation 

We added to the existing solution a new ```Unit Test Project (.Net Framework)```. This project will be leveraged later in order to integrate the tests into the build process.
    
*Add Visual Studio test project*
        
<img alt="Add VS Test project" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/add_test_project.png">
        
<br/>
    
In the newly created ```unit test project```, we've added a class called ```TrendTests```. In this class we will create a method for each of the unit tests that we have defined earlier. 

**Note:** The Microsoft unit testing framework requires the following attributes: 
    
- ```[TestClass]``` for any class that contains test methods
- ```[TestMethod]``` for every method that will be run by the Test Explorer
    
<br/>
    
While we were building those methods, we realized we needed some shared logic in the class to perform the authentication against SharePoint Online.
    
We decided to add this logic in three additional methods that do not have any attributes so that the unit testing framework does not run them directly. We created the following methods: 

{% highlight csharp %} 
    // The following code  manages the login to Sharepoint online that will be used by the test methods.
    private ClientContext GetSpContext()
    // Get the Sharepoint Online context after successful logon
    {
        string webUrl = "https://hackfest.sharepoint.com/sites/mysite/";
        using (var context = new ClientContext(webUrl))
        {
            context.Credentials = new SharePointOnlineCredentials("user@hackfest.onmicrosoftonline.com", GetPassword());
            context.Load(context.Web, w => w.Title);
            context.ExecuteQuery();
            return context;
        }
    }

    private static SecureString GetPassword()
    // Return a secure string of the user's password
    {
        ConsoleKeyInfo info;
        SecureString securePassword = new SecureString();
        foreach (char ch in "Password")
        {
            securePassword.AppendChar(ch);
        }
        return securePassword;
    }

    private Backend.Users.SharePointUser GetCurrentUser()
    // Return the Sharepoint user to use to perform the tests
    {
        var userContext = GetSpContext();
        var user = userContext.Web.CurrentUser;
        userContext.Load(user);
        userContext.ExecuteQuery();
        return new SharePointUser(user);
    }
{% endhighlight %}
  
  <br/>
    
Let's look in detail at each one of the test methods for the back end of the application: 
        
- Validating the instantiation of the ```Trend``` class.
    
  The class has been created to enable trends in the application. A trend is represented by a SharePoint list with three fields. Our first unit test will verify that the constructor has the expected three fields.

    {% highlight csharp %}  
    
        [TestMethod()]
        public void Constructor_ValidFieldsGetDefined_FieldsHaveBeenDefined()
        {
        // Arrange 
           // There is nothing to arrange in this test so we just proceed to the next stage.
        // Act
        Trend t = new Trend(null, null);
        // Assert
        Assert.AreEqual(3, t.Fields.Count);
        }
    
    {% endhighlight %}
    
<br/>
    
- Verifying that the list can be created in SharePoint Online.
    
  This will actually test the creation operation and fail if the operation does not succeed.
    
    {% highlight csharp %} 
    
       [TestMethod()]
        public void Create_ListCreation_NewListGetsCreated()
        {
            // Arrange
            var cc = GetSpContext();
            Trend t = new Trend(GetCurrentUser(), new Measure(), cc);
            // Act
            var newList = t.Create(false);
            // Assert
            Assert.IsNotNull(newList);
        }
    
    {% endhighlight %}
    
<br/>
    
- Deleting the list that was created.
        
  After running this set of tests, we will need to clean up the environment to be able to test again if necessary. Therefore, this method will be called upon to clean the SharePoint Online environment.
    
    {% highlight csharp %}
    
        [TestMethod()]
        public void Delete_ListStructure_ListRemoved()
        {
            // Arrange
           var cc = GetSpContext();
            Trend t = new Trend(GetCurrentUser(), new Measure(), cc);
            // Act
            bool deletionResult = t.Remove();
            // Assert
            Assert.IsTrue(deletionResult);
        }
    
    {% endhighlight %}
    
<br/>
    
#### 4. Ordering the tests
    
In this hackfest, we had to be deterministic with the order of the tests so that the delete method would be called after the list was created and the test of the constructor would be called first.
    
If you do not specify any order, the Microsoft unit testing framework will run the test in the order in which they appear in the code, but this is not the most convenient way to manage the order in which the tests are being run. With a right-click on the project, you can add an ordered test, as shown in the following screenshot.
    
*Adding an ordered test*
    
<img alt="Adding an ordered test" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/add_ordered_test.png">
    
<br/>
    
This lists all the test methods of the project and allows you to specify in which order they will run. You may have several ordered tests in the project and you can organize your ordered tests, which allows more granularity in how you manage them.
    
*Organize your tests*
    
<img alt="Organize your tests" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/ordered_test.png">
    
<br/>
    
Those tests are now ready to be integrated into the build process of the application.
    
### Automated UI tests / E2E tests ###

Besides the unit tests described above, Lufthansa wanted to test Mydea end to end in an automated way. These E2E tests should then be called during the continuous delivery pipeline to every successful release of a new version.

To implement the automated testing, we chose [*Selenium*](http://www.seleniumhq.org/) as a tool and used it from within C#, because that is the team's preferred language. Also, we wanted to call the tests from within Visual Studio Team Services release management later on.

**Implementation of the tests**

#### 1. Create a new test project and add Selenium packages

To write Selenium-based tests, use the regular unit test framework of your choice. For instance, we created another new ```Unit Test Project (.Net Framework)```. 
    
*Add Visual Studio test project*

<img alt="Add VS Test project" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/add_test_project.png">

<br/>

Next, install a couple of **NuGet Packages** to the project:
    
*Add NuGet packages*
    
<img alt="Add NuGet packages" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/selenium_nuget_packages.png">

<br/>

| Package Name                               | Purpose               |
|-----------------------------|-------------------------------------------------|
| Selenium.WebDriver                  | .NET Bindings for Selenium WebDriver   |
| Selenium.Support                    | Helper Classes                         |
| Selenium.WebDriver.xxDriver        | Drivers for the different browser (Chrome, Edge, ...)  |
| Selenium.PhantomJS.WebDriver          | Driver for the headless PhantomJS browser  |


**What's the right Selenium driver to use?**
    
Depending on the environment in which you want to run your tests later on, you will have to decide on the driver and browser.
    
Chrome, Firefox, and Edge drivers use the regular browsers. Upon test execution, you will see the browser window come up. The driver will then automatically click around in the browser and simulate a user. 
    
It's important to note that, since it's a regular browser UI, you will have to run the Selenium tests in an **interactive UI session**, with the respective browser installed. This is ideal for **locally debugging the tests**. 
    
In case you want to run them in the **VSTS Managed Build Agent**, you will have to use **PhantomJS**, which is a headless browser that can also run without an interactive UI session. So we are using the ``PhantomJSDriver`` below. To learn more about the capabilities and limitations of a hosted agent, see [Hosted agents](https://www.visualstudio.com/en-us/docs/build/concepts/agents/hosted).

#### 2. Create a base class for Selenium tests

Next we created a **common base class** for all Selenium tests. In the base class, we initialize the Selenium driver and maximize the window before each test. After each test, we tear down the driver.

{% highlight csharp %}

    [TestClass]
    public class SeleniumTestBase
    {
        public IWebDriver Driver { get; private set; }

        public WebDriverWait Wait { get; private set; }

        [TestInitialize]
        public void InitDriver()
        {
            // Driver = new ChromeDriver();
            Driver = new PhantomJSDriver();
            Driver.Manage().Window.Maximize();

            Wait = new WebDriverWait(Driver, new TimeSpan(0, 0, 5));
        }

        [TestCleanup]
        public void DestroyDriver()
        {
            Driver.Close();
            Driver.Quit();
        }
    }
{% endhighlight %}

We also create a ``WebDriverWait`` object, which can be used to find objects in the HTML website DOM tree.

<br/>

#### 3. Create a Selenium test for Azure Active Directory logon

Because **Lufthansa's Mydea** is using **Office 365**, users always have to log on first. So our first Selenium test had to automate log on to Office 365 and Azure Active Directory.

{% highlight csharp %}

    [TestClass]
    public class LoginTests : SeleniumTestBase
    {
        [TestMethod, TestCategory("UI")]
        public void Login_ValidUser_LandingPageShown()
        {
            Driver.Navigate().GoToUrl("http://test.sharepoint.com");

            // enter user
            var userName = Wait.Until((s) => By.Id("cred_userid_inputtext"));
            userName.FindElement(Driver).SendKeys(validUsername);

            // enter password
            var password = Wait.Until((s) => By.Id("cred_password_inputtext"));
            password.FindElement(Driver).SendKeys(validPassword);

            // Submit the form
            var form = Wait.Until((s) => By.Id("credentials"));
            form.FindElement(Driver).Submit();

            // Check for the user header button to be present
            var userHeaderButton = Wait.Until((s) => By.CssSelector("#O365_MeFlexPane_ButtonID span.o365cs-topnavText"))
                .FindElement(Driver);

            // Check the username of the logged in user
            userHeaderButton.Text.Should().Be("Mydea Testuser");
        }
    }
{% endhighlight %}

<br/>

Using ``Wait.Until``, we pause the test until a certain HTML element becomes available in the browser's page DOM. The element can be selected by ID (``By.Id``), CSS query (``By.CssSelector``), or other options.

Next we send keys into the username and password text boxes using ``SendKeys``. Finally, we submit the logon form using ``Submit``. 

To verify the logon, we check for the availability of the username in the header.

*Voilà...* Run the test like you would run ANY regular unit test and watch it click through. 

For better visibility, switch to the Chrome driver so you can actually see what goes on in the browser.

<br/>

#### 4. Reuse logon across multiple tests

Mydea always needs a logged-on user. As a result, run times of the tests become longer because you always have to log on first.

During the hackfest, we looked at options for keeping the user across tests, such as:

*  **Reuse of the driver / browser window**, so that it keeps the user cookie. The disadvantage, however, is that you have to dictate the order of test execution and that's against some of the principles.
* **Passing in a cookie to the driver**: Capture the OAuth token during the first logon test and then pass the cookie into the driver. ``Driver.Manage().Cookies`` does provide that functionality, but because we couldn't get it working, we decided to skip it.

So ultimately we decided to *always* run the logon-flow before each of the other UI tests.

We created another base class, which encapsulated the *logon* functionality, so that each of the tests could then just call it in the beginning.

{% highlight csharp %}

    public class MydeaSeleniumTestBase : SeleniumTestBase
    {
        public void GotoAndLogin(string url, string userName, string password)
        {
            Driver.Navigate().GoToUrl(url);

            var waitme = new WebDriverWait(Driver, new TimeSpan(0, 0, 5));

            // enter user
            var userNameEl = waitme.Until((s) => By.Id("cred_userid_inputtext"));
            userNameEl.FindElement(Driver).SendKeys(userName);

            // enter password
            var passwordEl = waitme.Until((s) => By.Id("cred_password_inputtext"));
            passwordEl.FindElement(Driver).SendKeys(password);

            // Submit the form
            var form = waitme.Until((s) => By.Id("credentials"));
            form.FindElement(Driver).Submit();
        }
    }

{% endhighlight %}

<br/>

A final Selenium test of an AuthN protected page would then look like this:

{% highlight csharp %}

    [TestClass]
    public class LandingPageTests : MydeaSeleniumTestBase
    {
        [TestMethod, TestCategory("UI")]
        public void ClickOnAddIdeaButton_PopupShown_DropDownValueMatches()
        {
            GotoAndLogin("https://mydea.sharepoint.com/Mydea",
                username, password);

            // Are we on the landing page?
            var header = Driver.FindElement(By.CssSelector("span.ms-fontSize-xl.ms-fontWeigt-semibold"), 60);
            header.Text.Should().Be("My Capital");

            var challengeText = Driver.FindElement(By.CssSelector("div.challengeSlider-heading.whiteframe-bottom > span.ng-binding"), 60).Text;

            var addButton = Driver.FindElement(By.CssSelector(".challengeSlider-footerButton i.ms-Icon--Add"), 60);
            addButton.Click();


            var mdDropdownOption = Driver.FindElement(By.CssSelector("md-select-value span div.md-text"), 60);
            mdDropdownOption.Text.Should().Be(challengeText);
        }
    }

{% endhighlight %}

<br/>

**Tip:** Have a look at **Selenium IDE**, which makes writing Selenium tests easier through a click recording functionality. We still found that we had to modify the recorded tests afterward anyways...

#### 5. Using test parameters

To make parameters such as username and password configurable, you have to use ``TestContext``.

Add a property to your unit test class (or base class):

    public TestContext TestContext { get; set; }

<br/>

The context is automatically filled in during execution of the test.

Using ``TestContext.Properties["username"]``, you can then access a parameter *username*.

To feed username and others with values, create a ``xx.runsettings`` file in your project and add the following content:

    <?xml version="1.0" encoding="utf-8"?>  
    <RunSettings> 
        <TestRunParameters>  
            <Parameter name="url" value="http://localhost" />  
            <Parameter name="username" value="Admin" />  
            <Parameter name="password" value="Password" />  
        </TestRunParameters> 
    </RunSettings>

<br/>

You can then select the *runsettings* file to be used locally in Visual Studio.

*Select runsettings*

<img alt="select runsettings" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/select_runsettings.png">

<br/>

**Why use this over a simple app.config?**

When you run the UI tests as part of your release or build pipeline, you can override the *runsettings values* from within the Visual Studio Team Services build task.

We've now finished a few UI tests with Selenium that are ready to be run from within the CI/CD pipeline with custom parameters.

## Continuous integration / continuous deployment ## 

The goal of the hackfest was to implement a solution that would verify the absence of breaking changes in any new code that is checked in. The CI/CD approach for the application runs the tests every time a build is run and provides a mechanism to enforce the success of those tests before code changes get merged into the final product.

### Build and release ###

Because the team at Lufthansa Industry Solutions already had a build pipeline in place for their "master" branch, we decided to modify the existing pipeline.

#### 1. Front-end unit tests ####

Now that we have added the Gulp task from earlier in this report, we can call it in Visual Studio Team Services by adding a new Gulp task inside the build pipeline and tick the option : Publish... the tests results, in the JUnit section thanks to the karma-junit-reporter plug-in :

*Visual Studio Team Services Gulp task*

<img alt="VSTS Gulp Task" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaFETests.png">

<br/>

The last step is to publish the code coverage from the Karma-coverage plug-in by using the 'Publish Code Coverage Results' task in Team Studio with the following parameters :

*Code coverage parameters*

<img alt="Code Coverage parameters" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaFETestsCoverage.png">

<br/>

#### 2. Back-end tests ####

The back-end tests are Visual Studio tests, so we only need to add the ```Visual Studio Test``` task in the pipeline. 

*Add a Visual Studio test task*

<img alt="Add a VS Test task" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/add_vsts_test.png">

<br/>

We modified the following settings:

* Version: Make sure to use the version **2.\***.
  Using the version 2 allows you to use the same agent for build, release, and test. 
* Test assemblies: ```**\*.orderedtest```
  This indicated to the Visual Studio Team Services agent that it will run the task to execute the tests in the order specified in the ordered test. 
* Test platform version: ```Visual Studio 2015```
  We matched the capabilities of the agent that will run the tests.

Altogether, this is how the build looks:

*Current build definition*

<img alt="Current Build Definition" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaBuildDef.png">

<br/>

#### 3. Adding tests to release

The logic behind the release is that by the time the application gets in release, the unit and integration tests have passed and we can put the effort on activities that consume more resources. This is where we perform end-to-end tests. They also validate that the solution continues to work in a QA environment and is ready to go to production. 

To get started, we created a new release definition that runs the Visual Studio test task. During the hackfest, we created only one environment that can serve as a base for future improvements. The task has the following settings: 

* Version: Select version **2.\*** to use the same agent for build, release, and test.
* Test filter criteria: ```TestCategory=Backend```
  This will filter and run only the tests with the corresponding attribute: ```[TestMethod, TestCategory("Backend")]```. 

If the test is successful, the release process will move to the next stage. If the test fails, the release will fail and the faulty code will not move forward.

<br/>

#### 4. Automated UI tests ####

To run the automated UI tests out of a build or release, add a new Visual Studio test task again.

*Add a Visual Studio test task*

<img alt="Add a VS Test task" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/add_vsts_test.png">

<br/>

Use version **2.\*** as described above and filter for ```TestCategory=UI``` (or whatever other category you've used).

``Test mix contains UI tests`` can be turned off because we're using the headless PhantomJS browser.

Pass in parameters via ``Override test run parameters``. These are the same ones from the testcontext above. 

**Note:** You can use $(..) build variables for the test run parameters as well.

<br/>

*Visual Studio Team Services test task running Selenium tests*

<img alt="Selenium Test in VSTS" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/vsts_selenium_test_task.png">

<br/>

Now the UI tests will automatically be run as part of the release or build.

### Governance with branch policies ###

Now that we have a clear build definition of who can run our unit tests, we want to apply a secure and automated governance on our repository.

We used the branch policies feature in Visual Studio Team Services and applied some rules on the Dev and Master branch.

*Branch policies*

<img alt="Branch Policies" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaBranchPolicies.png">

<br/>

Thanks to the branch policy, you can avoid mistakes such as pushing code straight into the master branch, or linking work items from the Kanban board. We also implemented the review cycle by forcing the team to do a pull request and code review before each merge.

*Branch policy overview*

<img alt="Branch Policy Overview" src="{{ site.baseurl }}/images/2017-06-15-Lufthansa/mydeaSketchBP.png">


## Conclusion ##

Implementing a test methodology is only the begining of the journey. The potential next steps to continue to improve how the application is built and deployed are adding telemetry with Application Insights, A/B testing to allow the test in production of new features while mitigating the impact on users, feature flags to facilitate the parallelization of the development and implementation of new features.

### Lessons learned ###

As with every hackfest, we learned a lot and we love to share those learnings with the community. Here are some:

- **Build minutes with hosted agents**
  
  During the hackfest, each group was creating and editing build definitions and queuing new builds each time. Even if the project is not massive, each build consumes some minutes from the free credit that comes with any Visual Studio Team Services subscription. At the beginning of the third day of the hackfest, we had consumed all of the 240 free minutes! 
  
  In order to finish the hack within the allocated time, we had to switch (and upgrade) to the free private agent. You can read more about the [free agent and pipeline](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/get-more-build-or-load-testing-vs) in the Team Services documentation.

- **Hosted agent with Selenium test**
  
  To run Selenium tests with the Team Services hosted agent, you will have to use the PhantomJS driver. 
  
- **Test context/run settings**
  
  You can supply test parameters to unit test or UI tests through ``TestContext.Properties`` and the ``.runsettings`` file. These parameters can then be overridden in the Team Services test task.
  
- **Keeping up with Visual Studio Team Services changes**

  One of the challenges that Team Services users face regularly is how to stay up to date with the continuous improvements that Team Services makes. The [Visual Studio Team Services Product Updates](https://www.visualstudio.com/team-services/updates/) page is where the engineering team keeps track of the changes that are happening on the service. You can also subscribe to the feed associated with that page.

- **Consider the latency using any cloud provider tool for Karma** 
  
  Indeed, when you run your test locally with Karma, you are running it locally and it will be very fast (a few seconds). When you do the same thing using a cloud provider such as Visual Studio Team Services, Travis, or CircleCI, consider those tools to be slower than your local environment. With that in mind, you may have to [change some settings in Karma](http://karma-runner.github.io/1.0/config/configuration-file.html) like we did for the timeout threshold.

- **The Live Unit Testing (LUT) feature of Visual Studio 2017 may not appear in the project**

## Resources ##

- [The Art of Unit Testing, Second Edition, by Roy Osherove](https://www.manning.com/books/the-art-of-unit-testing-second-edition)
- Visual Studio Test Platform source code: [Visual Studio Test Platform](https://github.com/Microsoft/vstest)
- [Testing with unified agents and phases](https://blogs.msdn.microsoft.com/visualstudioalm/2017/03/26/vstest-task-dons-a-new-avatar-testing-with-unified-agents-and-phases/)
- [Test-driven development (TDD) - Wikipedia page](https://en.wikipedia.org/wiki/Test-driven_development)

