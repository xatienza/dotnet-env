[![Windows build status](https://ci.appveyor.com/api/projects/status/github/tonerdo/dotnet-env?branch=master&svg=true)](https://ci.appveyor.com/project/tonerdo/dotnet-env)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![NuGet version](https://badge.fury.io/nu/DotNetEnv.svg)](https://www.nuget.org/packages/DotNetEnv)
# dotnet-env

A .NET library to load environment variables from .env files. Supports .NET Core and .NET Framework

## Installation

Available on [NuGet](https://www.nuget.org/packages/DotNetEnv/)

Visual Studio:

```powershell
PM> Install-Package DotNetEnv
```

.NET Core CLI:

```bash
dotnet add package DotNetEnv
```

## Usage

### Load env file

Will automatically look for a `.env` file in the current directory
```csharp
DotNetEnv.Env.Load();
```

You can also specify the path to the `.env` file
```csharp
DotNetEnv.Env.Load("./path/to/.env");
```

The variables in the `.env` can then be accessed through the `System.Environment` class
```csharp
System.Environment.GetEnvironmentVariable("IP")
```

### Additional arguments

You can also control whitespace trimming and allowing hashes in values
```csharp
DotNetEnv.Env.Load(false, false);
```

Both parameters default to true, which means:
1. `trimWhitespace`, first arg: true in order to trim
 leading and trailing whitespace from keys and values such that

```env
  KEY  =  value
```

Would then be available as
```csharp
"value" == System.Environment.GetEnvironmentVariable("KEY")
null == System.Environment.GetEnvironmentVariable("  KEY  ")
```

False would mean:
```csharp
"  value" == System.Environment.GetEnvironmentVariable("  KEY  ")
null == System.Environment.GetEnvironmentVariable("KEY")
```

2. `isEmbeddedHashComment`, second arg: true in order to allow inline comments

```env
KEY=value  # comment
```

Would then be available as
```csharp
"value" == System.Environment.GetEnvironmentVariable("KEY")
```

False would mean:
```csharp
"value  # comment" == System.Environment.GetEnvironmentVariable("KEY")
```

Which is most useful when you want to do something like:
```env
KEY=value#moreValue#otherValue#etc
```

3. `unescapeQuotedValues`, third arg: true in order to unescape/parse
 quoted (single or double) values as being strings with escaped chars
 such as newline ("\n"), but also handles unicode chars
 (e.g. "\u00ae" and "\U0001F680") -- note that you can always include
 unescaped unicode chars anyway (e.g. "日本") if your .env is in UTF-8.
 Also note that there is no need to escape quotes inside.

```env
KEY="quoted\n\tvalue"
```

Would then be available as
```csharp
"quoted
    value" == System.Environment.GetEnvironmentVariable("KEY")
```

False would mean:
```csharp
"\"quoted\\n\\tvalue\"" == System.Environment.GetEnvironmentVariable("KEY")
```

4. `clobberExistingVars`, fourth arg: false to avoid overwriting existing environment variables

```env
KEY=value
```

```csharp
System.Environment.SetEnvironmentVariable("KEY", "really important value, don't overwrite");
DotNetEnv.Env.Load(false, false, false, false); // fourth arg false, don't overwrite existing variables
System.Environment.GetEnvironmentVariable("KEY"); // == "really important value, don't overwrite"
```

## Issue Reporting

If you have found a bug or if you have a feature request, please report them at this repository issues section.

## License

This project is licensed under the MIT license. See the [LICENSE](LICENSE) file for more info.
