## Main Application Templates

The three main WebSharper application types are:

 1. **Client-Server Application** - a full client-server application implemented as a WebSharper [sitelet](Sitelets.md).

    The sitelet uses a dynamic HTML template and placeholders that are instantiated by the sitelet. See the Reactive HTML documentation page for more information on dynamic templating.
	
    These applications exist in two kinds: a **Client-Server Web Application** runs as an ASP.NET module, while a **Self-Hosted Client-Server Web Application** runs as a self-contained executable using an OWIN self-host container.

 2. **HTML Site** - a multi-page HTML/JavaScript application.

    Like the Client-Server Application, this application also uses [sitelets](Sitelets.md) and dynamic HTML to define web pages.
    However, it creates a set of static HTML and JavaScript files that can be deployed in any HTML container.

    WebSharper mobile web applications can also be created from this template.

 3. **Single-Page Application** - a single-page HTML/JavaScript application with an HTML page and an F# source file that plugs content into it.

    The easiest way to get started with WebSharper: write a few lines of F#, add a placeholder for it in the HTML, and you are ready to go.

    See the "Your first app in 2 minutes" tab for an example.

The following helper projects are also available:

 1. **Library** - a simple WebSharper library. Annotated code is compiled to JavaScript, and can then be used from any application project.
 
 2. **Extension** - defines the interface to an existing JavaScript library using [a convenient declarative F# syntax](InterfaceGenerator.md).

 3. **Mobile Application** - a single-page application without web hosting and with a readme with instructions about how to wrap the code output in an Apache Cordova application.

 4. **Owin-hosted Site** - a client-server application with Owin hosting.

 5. **Suave-hosted Site** - a client-server application with [Suave](https://github.com/SuaveIO/suave) hosting.

## .NET Core

Some templates are available for .NET Core/Standard, see [Installing WebSharper Templates for .NET Core/Standard](Install.md#netcore)

## Capabilities

This table summarizes the capabilities of the available application/helper project templates:

<table class="price-table">
    <tbody>
        <tr class="header">
            <td style="border:none;">Template</td>
            <td><code>dotnet new</code></td>
            <th class="first">C# available</th>
            <th>Is Sitelet?</th>
            <th>Client</th>
            <th>Server</th>
            <th class="last">Remote</th>
        </tr>
        <tr class="header">
            <th colspan="7">Applications</th>
        </tr>
        <tr>
            <td>Client-Server App<br/><span style="color:#888;font-size:smaller">ASP.NET / Core</span></td>
            <td><code>websharper-web</code></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
        </tr>
        <tr>
            <td>Client-Server App<br/><span style="color:#888;font-size:smaller">Hosted via OWIN or Suave</span></td>
            <td></td>
            <td></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
        </tr>
        <tr>
            <td>HTML App</td>
            <td><code>websharper-html</code></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
        </tr>
        <tr>
            <td>Single-Page App<br/><span style="color:#888;font-size:smaller">ASP.NET / Core</span></td>
            <td><code>websharper-spa</code></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
        </tr>
        <tr>
            <td>Single-Page App<br/><span style="color:#888;font-size:smaller">Packaging for mobile</span></td>
            <td></td>
            <td></td>
            <td></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td></td>
            <td></td>
        </tr>
        <tr class="header">
            <th colspan="7">Helpers</th>
        </tr>
        <tr>
            <td>Library</td>
            <td><code>websharper-lib</code></td>
            <td><img src="https://raw.githubusercontent.com/dotnet-websharper/docs/master/images/ok.png" alt="X"/></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>Extension</td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

  * **Client** - Contains code compiled to JavaScript, executing on the client-side.

  * **Server** - Contains code you deploy for your application, executing on your server-side.

  * **Remote** - Contains code you from someone else's server, typically through a web service.

## MSBuild Project File Configuration

The WebSharper compiler is included in the NuGet packages `WebSharper.CSharp` and `WebSharper.FSharp`.
The type of project is driven by the `Project` value in a `wsconfig.json` file, 
or the property `WebSharperProject` in the `.csproj`/`.fsproj`. Here are the possible values:

* `Site`: for Client-Server Applications.
    * Compiles JavaScript-annotated code;
    * Extracts resources in the folders `Content/WebSharper` and `Scripts/WebSharper`.
* `Html`: for HTML Applications.
    * Compiles JavaScript-annotated code;
    * Generates HTML files in `$(WebSharperHtmlDirectory)` and extracts resources in `$(WebSharperHtmlDirectory)/Content/WebSharper` and `$(WebSharperHtmlDirectory)/Scripts/WebSharper`.
* `Bundle`: for Single-Page Applications.
    * Compiles JavaScript-annotated code;
    * Extracts and concatenates resources (js+css) into the folder `Content`.
* `BundleOnly`: for Single-Page Applications without .NET compilation.
    * Same as `Bundle` but skips any other build steps not related to producing the js+css output for quick iterative development. 
* `Library`: for Libraries.
    * Compiles JavaScript-annotated code.
* `InterfaceGenerator`: For Extensions.
    * Compiles the classes defined using WIG into an assembly.

The other compilation settings are fully documented [here](Configuration.md).
