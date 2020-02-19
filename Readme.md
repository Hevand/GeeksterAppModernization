# Hands on lab
## Overview
In today's hands on lab, we plan to do the preparation + any combination of the following exercises: 

| # | Exercise | Description | Duration (min) |
| --- | --- | --- | --- |
|1| Run the application | - | 5-10 |
|1.a| Run locally | Host the website on your local machine. | - |
|1.b| Static Web Site | Deploy the web site to azure blob storage | - |
|1.c| Azure App Service Plan | Deploy the website to an App Service Plan | - |
|1.d| AKS | Deploy the application to a Kubernetes cluster | - |
|2| Custom DNS + TLS | Configure a DNS name for your website and configure a custom certificate | 60 |
|3| App Insights | Instrument the web site with App Insights and create custom reports / alerts | 5 |
|4| Azure AD | Configure Azure AD integration with the website, such that it is only accessible for authenticated users | 10 |
|5| Bonus: Microservice | Infuse CSAHoot with AI, via an Azure Function | 45 |
|6| Web Application Firewall | Protect the website with an Web Application Firewall | 45 |
|7| Azure Front Door | Make the website globally available | 45 |

## Preparation
- Make sure you have [VS Code](https://code.visualstudio.com/Download) or [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
- Make sure you have [.net core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Make sure you have access to [repository]
- [Clone](https://docs.microsoft.com/en-us/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio) the Azure DevOps repository to your local system.


## Exercise 1. Deploy and run the application
### Exercise 1a. Run Locally
In this exercise, we will run the application locally. This is the easiest way to particate in CSAHoot and it does not require any Azure infrastructure to be deployed. 

After downloading the repository, open the solution in your favorite IDE. This is an opinionated HOL, so we're assuming that this will be either Visual Studio or Visual Studio Code. 

_When using Visual Studio Code_
- Open the folder that contains the \*.csproj
- Using the terminal, build the solution: **dotnet build**
- Using the terminal, run the solution: **dotnet run**
- Using the browser, navigate to the URL provided. E.g.: https://localhost:5001/index.html

_When using Visual Studio_
- Open the solution file (\*.sln)
- Press F5 to run the project (in debug mode)

### Excercise 1b. Static Web Site
In this exercise, we will host the application as a static website. Static websites are a cheap and scalable way to host web applications that consist only of client-side code.

After downloading the repository, navigate to the folder. 

Follow [these steps](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website) to deploy your application:
- Enable Static Website hosting on the storage account
- Upload *the contents of* /wwwroot to the $web container (via the portal or storage explorer)
- In the StatiSet index.html as the default document
- Browse to the provided URI

_Alternatives_:
- https://docs.microsoft.com/en-us/azure/javascript/tutorial-vscode-static-website-node-01



### Exercise 1c. Azure App Service Plan
In this exercise, we will hosts the application as an Azure Web App. 

For this, you'll have to create an Azure App Service Plan and Azure Web App. 

```bash
l="WestEurope"
rg="csahoot"
name="[csahootapp]"

az group create --name $rg --location $l
az appservice plan create --name $name --resource-group $rg
az webapp create --name $name --plan $name --resource-group $rg
```

After downloading the repository, open the solution in your favorite IDE. This is an opinionated HOL, so we're assuming that this will be either Visual Studio or Visual Studio Code. 

_When using Visual Studio Code_
- Open the folder that contains the \*.csproj
- Use the terminal window to **dotnet publish**
- **Either** copy the contents of the /publish folder to the app service plan via Kudo
**or**
- Publish via https://docs.microsoft.com/en-us/azure/javascript/tutorial-vscode-azure-app-service-node-01

_When using Visual Studio_
- Open the solution file (\*.sln)
- Right-click on the project, select 'Publish'
- Create a publish profile, targetting the previously created Azure Web App
- Click publish. 

After these steps, your website will be available.

### Exercise 1d. Azure Kubernetes Services
Really? For 200 lines of static HTML? 

## Exercise 2. Custom DNS + TLS for static websites and websites hosted in Azure App Services
    **NOTE** If you did exercise 1a, skip this exercise. 

At this moment, you have a website - but it does not have a very pretty URI. To change that, we need to configure a custom domain (e.g. csahoot.nl) and set a certificate.  

In this exercise, you'll use or request your own custom domain ($11.99) and generate a (private, managed, free) certificate for said domain: 
- [Request a custom domain]()
- Wait for a long, long time. Please go ahead and look at some of the other exercises now. 
- [Create a free, managed certificate and bind to the custom domain](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate#create-a-free-certificate-preview)

As a result, your website is accessible via 'https://www.yourcustomdomain.com', using a valid and trusted certificate.


## Exercise 3. Application Insights
Application insights provides telemetry and health data for your application. 

In this exercise, we're going to instrument the client application. Instructions can be found [here](https://docs.microsoft.com/en-us/azure/azure-monitor/app/javascript). 

To create the application insights resource:

```bash
l="WestEurope"
rg="csahoot"
name="[your csahoot app name]"

az extension add --name application-insights 
az monitor app-insights component create --app $name --location $l --resource-group $rg

echo "To complete the exercise, use the instrumentation key provided."
```

Use the instrumentation key to update the code in index.html: 

```html
    <script type="text/javascript">
        var sdkInstance = "appInsightsSDK"; window[sdkInstance] = "appInsights"; var aiName = window[sdkInstance], aisdk = window[aiName] || function (e) { function n(e) { t[e] = function () { var n = arguments; t.queue.push(function () { t[e].apply(t, n) }) } } var t = { config: e }; t.initialize = !0; var i = document, a = window; setTimeout(function () { var n = i.createElement("script"); n.src = e.url || "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", i.getElementsByTagName("script")[0].parentNode.appendChild(n) }); try { t.cookie = i.cookie } catch (e) { } t.queue = [], t.version = 2; for (var r = ["Event", "PageView", "Exception", "Trace", "DependencyData", "Metric", "PageViewPerformance"]; r.length;)n("track" + r.pop()); n("startTrackPage"), n("stopTrackPage"); var s = "Track" + r[0]; if (n("start" + s), n("stop" + s), n("addTelemetryInitializer"), n("setAuthenticatedUserContext"), n("clearAuthenticatedUserContext"), n("flush"), t.SeverityLevel = { Verbose: 0, Information: 1, Warning: 2, Error: 3, Critical: 4 }, !(!0 === e.disableExceptionTracking || e.extensionConfig && e.extensionConfig.ApplicationInsightsAnalytics && !0 === e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)) { n("_" + (r = "onerror")); var o = a[r]; a[r] = function (e, n, i, a, s) { var c = o && o(e, n, i, a, s); return !0 !== c && t["_" + r]({ message: e, url: n, lineNumber: i, columnNumber: a, error: s }), c }, e.autoExceptionInstrumented = !0 } return t }(
            {
                instrumentationKey: "INSTRUMENTATION_KEY_GOES_HERE"
            }
        ); window[aiName] = aisdk, aisdk.queue && 0 === aisdk.queue.length && aisdk.trackPageView({});
    </script>
```

Publish or run the application, and inspect the information collected by Application Insights. The previous link will give you an impression on the information that is collected. 

## Exercise 4. Azure AD integration. Integrated via the portal, or via code in the client application 
In this step, we will configure authentication on the web application. 

This can be handled in different ways: 
- On the level of the Azure Web App. This will forcefully authenticate users before they're allowed to access the website and its resources. The user identity is not available to the client application. 
- As part of the web application. This will authenticate the user from the client and provide a token to the web application. The user identity is available to the application.

_Integrated, via the portal_
- Navigate to the Azure Web App that you created
- In the list of properties, find 'authentication / authorization'
- Select the following defaults:
  - Enable App Service Authentication
  - Action to take when request is not authenticated: Log in with Azure Active Directory
  - Configure Azure Active Directory with a new application

_In the application code, via MSAL_ 

Documentation: 
- [github](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))
- [document](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-js-initializing-client-applications)

Steps:
- Create an app registration in Azure AD.
- Take note of the Client ID and Tenant ID
- In Index.html, uncomment the code related to Azure AD / MSAL and update the ClientID and Tenant ID. 
- Run the application.


## Bonus 6. Extend the application with a microservice. 
Should you have time remaining, why not create an Azure Function and leverage that? Bonus points for creativity - like using AI to answer the questions for you, faster?

## Bonus 7. Web Application Gateway. 
[link](https://docs.microsoft.com/en-us/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

## Bonus 8. Azure Front Door.
[link](https://docs.microsoft.com/en-us/azure/frontdoor/quickstart-create-front-door)