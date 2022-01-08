[TOC]

# Upgrade .NET Framework MVC to .NEt 6

## Pre-Rreq

- Windows
- [Visual Studio 2022](https://visualstudio.microsoft.com/vs/)
- [.NET 6 SDK](https://dotnet.microsoft.com/en-us/download/dotnet)
- [upgrade-assistent](https://github.com/dotnet/upgrade-assistant/)
- [Visual Studio extension | Microsoft SARIF Viewer](https://marketplace.visualstudio.com/items?itemName=WDGIS.MicrosoftSarifViewer)

No limited internet access is required.

## Create .NET Framework MVC project

If you have your .NET Framework MVC project, you can skip this step.

Otherwise, you can directly clone this project or create an intial .NET Framework MVC project as below:

On Visual Studio 2022, refer to [Visual Studio : How to fix missing ASP.NET template for .NET Framework](https://www.howtosolutions.net/2021/11/visual-studio-missing-asp-net-web-application-template-dotnet-framework/) to add "Invidual components" for ".NET Framework project and item templates".

Create a new ASP.NET Web Application project using .NET Framework.
1. Choose template as "ASP.NET Web Application (.NET Framework)";
2. Input project name as "myWebAppMVC";
3. Check "Place solution and project in the same directory";
4. Keep framework as default which is ".NET Framework 4.7.2";
5. Click "Create" button.

Choose "MVC" in the next dialog, and then click "Create" button.

On Visual Studio 2022, click "IIS Express (Microsoft Edge)" to launch the web app and access the web app by browser.

## Install upgrade-assistent

Install [upgrade-assistent](https://github.com/dotnet/upgrade-assistant/)

Run in Windows command:
```bash
# upgrade-assistant depends on try-convert tool
dotnet tool install -g try-convert

# install upgrade-assistant tool
dotnet tool install -g upgrade-assistant

```

## Analyze .NET Framework project with upgrade-assistant

Run in Windows command:
```bash
# analze the .NET Framework project
cd myWebAppMVC
upgrade-assistant analyze ./myWebAppMVC.csproj

```

The SARIF formated analysis report `AnalysisReport.sarif` is generated under project folder.

On Visual Studio 2022, install the extension for "[Microsoft SARIF Viewer 2022](https://marketplace.visualstudio.com/items?itemName=WDGIS.MicrosoftSarifViewer)".

On Visual Studio 2022, click Tools / Open Static Analysis Results as SARIF, and open the above generated `AnalysisReport.sarif` analysis report.

View warnings / errors on error list, and double click the warnings / errors to inspect the details.


## Upgrade .NET Framework project to .NET 6

Run in Windows command:
```bash
upgrade-assistant upgrade ./myWebAppMVC.csproj
```

Follow the interative command guide to finalize the upgrade process:

1. Back up project
2. Convet project file to SDK style
3. Clean up NuGet package reference
4. Update TFM
5. Update NuGet Packages
6. Add tempalte files
7. Upgrade app config files
8. Update Razor files
9. Update source code
10. Move to next project or finalize upgrade

On Visual Studio 2022, reload or reopen the project.

Even the upgrade-assistant can simplfy the upgrade process, you still need do some manual work as belows.

### Fix errors

On Visual Sutdio 2022, check the error list.

Fix the NU1605 Newtonsoft.Json version error.

Modify `myWebAppMVC.csproj` to modify the Newtonsoft.Json version as expected version:
```xml
<PackageReference Include="Newtonsoft.Json" Version="13.0.1" />
```


### Remove uncessary files

- Remove `Global.asax` and `Global.asax.cs` files
- Remove `App_Start` folder and its files

### Migrate the laytout files

Refer to [Migrate the layout files](https://docs.microsoft.com/en-us/aspnet/core/migration/mvc?view=aspnetcore-5.0&preserve-view=true#migrate-the-layout-files) to migrate the layout files `Views/Shared/_Layout.cshtml`.


## Test after upgrade

On Visual Studio 2022, click "IIS Express (Microsoft Edge)" to launch the web app and access the web app by browser.

Run in Windows command:
```bash
dotnet run
```

Check in the code to Git repository.

For this project, you can check the upgraded project on `net6` branch.

You can execute `dotnet run` on a Mac for the upgraded project to verify its cross-platform portability.

## Deploy to OpenShift

Refer to [Run .NET WebApp on OpenShift](https://github.com/xdevops-caj-dotnet/myWebApp) to deploy the upgraded web app to OpenShift:

```bash
# Create a OpenShift project(namespace)
oc new-project will-dotnet-demo

# Create application by 'dotnet:6.0-ubi8' builder image stream tag
oc new-app dotnet:6.0-ubi8~https://github.com/xdevops-caj-dotnet/myWebAppMVC.git#net6 --name my-web-app-mvc

# Check build logs
oc logs -f bc/my-web-app-mvc

# Create a route that uses edge TLS termination if configure https for .NET app
oc get svc
oc create route edge --service=my-web-app-mvc

# Check route of the WebApp
oc get route

```

Access <https://my-web-app-mvc-will-dotnet-demo.${CLUSTER_ADMIN}> by browser.


## Troubleshooting

### Visual Studio : How to fix missing ASP.NET template for .NET Framework
- <https://www.howtosolutions.net/2021/11/visual-studio-missing-asp-net-web-application-template-dotnet-framework/>

### Could not find a part of the path ... bin\roslyn\csc.exe

- <https://stackoverflow.com/questions/32780315/could-not-find-a-part-of-the-path-bin-roslyn-csc-exe>

## References

- [Upgrade an ASP.NET MVC app to .NET 6 with the .NET Upgrade Assistant](https://docs.microsoft.com/en-us/dotnet/core/porting/upgrade-assistant-aspnetmvc)
- [Upgrading from .NET Framework to .NET 6](https://www.youtube.com/watch?v=cOHXt_0VDRI)
- [Migrate from .NET Framework to .NET 5](https://medium.com/@dumindudesilva/migrate-from-the-net-framework-to-net-5-5aef306d10f5)