# Install Unity.Microsoft.DependencyInjection 2.1.1 but got "No service for type" exception

My environment is VS2017 15.8.3

I create a new ASP.NET Core MVC project and install NuGet Package of Unity.Microsoft.DependencyInjection 2.1.1, then register Unity as default service provider and change the Startup class by [Unity.Microsoft.DependencyInjection Documentation](https://github.com/unitycontainer/microsoft-dependency-injection).

Whne I run this project and got "No service for type" exception, you can do following procedure for get this error

* I create ASP.NET Core MVC project
* Create Message.cs file and add following code
```
    public interface IMessage
    {
        void Send(string message);
    }

    public class ConsoleMessage : IMessage
    {
        public void Send(string message)
        {
            Console.WriteLine($"ConsoleMessage :{message}");
        }
    }
    public class FileMessage : IMessage
    {
        public void Send(string message)
        {
            Console.WriteLine($"FileMessage :{message}");
        }
    }
```
* Install package for "Unity.Microsoft.DependencyInjection" 2.1.1
* Add UseUnityServiceProvider() in CreateWebHostBuilder method of Program.cs file
```
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseUnityServiceProvider()   // Add Unity as default Service Provider
                .UseStartup<Startup>();
```
* Add ConfigureContainer method to Startup.cs
```
        public void ConfigureContainer(IUnityContainer container)
        {
            // Could be used to register more types
            container.RegisterType<IMessage, ConsoleMessage>();
        }
```
* Build and run this project, then get following exception
```
System.InvalidOperationException: 'No service for type 'System.Collections.Generic.IEnumerable`1[Microsoft.AspNetCore.Hosting.Internal.IStartupConfigureContainerFilter`1[Unity.IUnityContainer]]' has been registered.'
```

You can get this demo project from [this repository](https://github.com/vulcanlee/Issue_For_No_service_for_type)

# More Information
* when I remove ConfigureContainer method from Startup.cs, above error will disappear.
* If I downgrade to Unity.Microsoft.DependencyInjection 2.0.12 but ConfigureContainer method still in  Startup.cs file, the project work fine.

