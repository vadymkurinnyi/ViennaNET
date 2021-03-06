# v2.0

### Release Summary
The new version of ViennaNET.WebApi. * Was made based on AspNetCore 3, and .NET Core 3.1 is recommended for its use.
Assemblies with configurators were renamed to ViennaNET.WebApi.Configurators. *, And some of them were merged or split.
In the new version, the use of composite configurators has become easier, for example, if earlier it was necessary to call several extension methods, now only one public connection method remains in each configurator.

### Breaking Changes
1. AspNetCore 3 is not compatible with the classic .NET Framework, only with .NET Core 2+.
2. In the new version of the ViennaNET.WebApi. * Libraries, some incompatible changes have been made. Assemblies with configurators were renamed ViennaNET.WebApi.Configurators. *, As well as some of them were merged or divided. To migrate existing services to new versions of these libraries, you must perform the migration.
3. In SecurityKeysContainer, the default values ​​for the publisher and the audience have changed, which means that you need to check the compatibility of these parameters with existing authorization services. It is better in the configuration to explicitly override these fields as in all services.


### Migration of AspNetCore applications from ViennaNET.WebApi 1. \ *. \ * (NET Core 2.2) to 2. \ *. \ * (NET Core 3.1)

#### Case I. Used by DefaultKestrelRunner or DefaultHttpSysRunner
1. In all assemblies using <TargetFramework> netcoreapp2.2 </TargetFramework>, change to <TargetFramework> netcoreapp3.1 </TargetFramework>.
2. In the main assembly with the service, delete the link to the ViennaNET.WebApi.DefaultConfiguration package for the service on Kestrel or ViennaNET.WebApi.DefaultHttpSysRunner for the service on HttpSys.
3. Install the latest version of the package (2. *. *) ViennaNET.WebApi.Runners.BaseKestrel or ViennaNET.WebApi.Runners.BaseHttpSys, respectively.
4. We also update versions of Company libraries to (2. *. *) In all assemblies. Version conflicts with third-party libraries (for example, SimpleInjector) may occur, so they also need to be updated.
5. Redoing Program.cs:
  * Replace DefaultKestrelRunner with BaseKestrelRunner (for DefaultHttpSysRunner - BaseHttpSysRunner)
  * If you used AddOnStartAction <T> / AddOnStopAction <T> extension methods, then you need to connect the ViennaNET.WebApi.Runners.Extensions package
6. We redo the configuration file:
   * Remove unnecessary parameters from the webApiConfiguration section: logRequests and swaggerSubmit
   * If you want to use the dynamic search for SimpleInjector packages, as is happening now, you need to add a section:
  
       "simpleInjector": {
         "loadPackagesDynamically": true
       },
       
   The loadPackagesDynamically flag is responsible for enabling automatic loading of packages from all assemblies that are at the root of the application folder, so that all dependencies get to the application folder, you need to add the parameter to the service project file:
  
       <PropertyGroup>
          <CopyLocalLockFileAssemblies> true </CopyLocalLockFileAssemblies>
       </PropertyGroup>
       
   By default, the flag is considered set to false, which means that all dependencies must be connected explicitly manually, using the extension method for the container from the ViennaNET.SimpleInjector.Extensions assembly in the root service package, for example:

       container.AddPackage (new DiagnosingPackage ())
                .AddPackage (new RabbitMqPackage ())
                .AddPackage (new MessagingPackage ())
      
   In this case, there is no need to use the CopyLocalLockFileAssemblies parameter
7. The swagger enable flag has been moved from the webApiConfiguration section to the swagger section with the renaming swaggerSubmit -> useSwagger

       "swagger": {
         "useSwagger": true,
         ...
       },

8. If you used HttpClients through the ViennaNET.WebApi.Configurators.HttpClients.Jwt/Ntlm assemblies, you need to add a parameter with an authentication type to the section of each webApiEndpoints:

       "webApiEndpoints": [
         ...
         {
           ...
           "authType": "jwt"
         },
         {
           ...
           "authType": "ntlm"
         },
         ...
       ],

#### Case II. The service was built manually (without using DefaultKestrelRunner or DefaultHttpSysRunner)
1) In all assemblies using <TargetFramework> netcoreapp2.2 </TargetFramework>, change to <TargetFramework> netcoreapp3.1 </TargetFramework>.
2) Assemblies with configurators were renamed to ViennaNET.WebApi.Configurators. *, As well as some of them were merged or divided, so you need to familiarize yourself with their description and select the ones you need.

   Example:
     
       return CompanyHostBuilder.Create ()
                                .UseKestrel ()
                                .UseCommonModules ()
                                .UseSimpleInjector ()
                                .UseSwaggerWithJwtAuth ()
                                .UseDiagnosing ()
                                .UseJwtHttpClients ()
                                .UseJwtAuth ();

3) We update versions of Company libraries to (2. *. *) In all assemblies. Version conflicts with third-party libraries (for example, SimpleInjector) may occur, so they need to be synchronized.


# v1.4
# v1.3
# v1.2
# v1.1
# v1.0