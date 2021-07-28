# How to use

This image creates a development environment for dotnet. However, it will not work when developing programs that use the windows extension to this SDK, for example, WPF applications, as these libraries don't yet work on Linux.

## 1. Creating the container

Creating a container that is connected to your current working directory:

```bash
docker container run -it -v ${PWD}:/work -w /work -p 5000:5000 boshoffwillem/dotnet-sdk:latest /bin/sh
```

The ```/work``` could be named whatever you want. This is where your current directory will be mapped to in the container.

Also, dotnet applications usually expose two ports, an HTTP version, and an HTTPS version. Those ports also change depending on whether you are in Debug or Release configuration. The ports are:

- 5000: HTTP, Debug
- 5001: HTTPS, Debug
- 80: HTTP, Release
- 443: HTTPS, Release

So you would potentially need to map all these ports, depending on what you want to do.
Also, any programs that you plan to run using the container that exposes ports, like WebApp, WebApi, etc., need a tweak.
In their launchSettings.json file under Properties, they have configuration concerning their URL. For example, the default for a WebApp is:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:46695",
      "sslPort": 44329
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "WebApp": {
      "commandName": "Project",
      "dotnetRunMessages": "true",
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

A docker container does not have a localhost network. So you need to replace localhost with a +, to use the underlying network of the container:

```json
{
    "WebApp": {
      "commandName": "Project",
      "dotnetRunMessages": "true",
      "launchBrowser": true,
      "applicationUrl": "https://+:5001;http://+:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

## 2. Using the container

When you're at a point where you want to build build, test, or run your code you obviously need to execute some dotnet commands in the container.

To enter/access the container run:

```bash
docker container exec -it <container-name> sh
```

This opens a shell to the container that you can use to execute dotnet commands.
