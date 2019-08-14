## Blazor Preview 8 Broken Libraries

Blazor Preview 8 sees the death of the "blazorlib" template, 
which was used for Comnponent Libraries in previous versions.

The new Razor Component Library is supposed to replace it, 
but there are problems...

### No Client Side Blazor support for "content"

RCL does not provide support for "content" files in 
Client-Side Blazor.

Using the old _blazorlib_ template, any content files 
(JS/CSS etc) would automatically be packed as 
Embedded Resources in the component assembly and
served up by the Blazor WASM web server.

You can still achieve this, by adding these lines into your 
RCL project file

``` XML
  <ItemGroup Label="Client Side Blazor">
    <!-- .js/.css files will be referenced via <script>/<link> tags; other content files will just be included in the app's 'dist' directory without any tags referencing them -->
    <EmbeddedResource Include="content\**\*.js" LogicalName="blazor:js:%(RecursiveDir)%(Filename)%(Extension)" />
    <EmbeddedResource Include="content\**\*.css" LogicalName="blazor:css:%(RecursiveDir)%(Filename)%(Extension)" />
    <EmbeddedResource Include="content\**" Exclude="**\*.js;**\*.css" LogicalName="blazor:file:%(RecursiveDir)%(Filename)%(Extension)" />
  </ItemGroup>
```

### No Server-Side Content When Not In Development

If the environment variable `ASPNETCORE_ENVIRONMENT` is not
set to `Development`, no content files are served by the SSB
project - whether published or not.

## This repo contains sample projects to demonstrate these issues

To see the problem with SSB content files, change the environment
variable `ASPNETCORE_ENVIRONMENT` to anything other than 
`Development` and run it. Check the browser console and see the 
error where it cannot load the `Library.js` file.

To see the problem in CSB, comment out the `ItemGroup` labelled 
*Client Side Blazor* in **BlazorCSB8.csproj** and run the BlazorCSB8 
project, then check the browser console for errors.