# High Performance Coding with .NET Core and C#

### Course Overview [1]
### Note for .NET Core experts [2]

CoreCLR, CoreFx, .NET Standard, Self Contained Deployment

- CoreCLR runtime - crossplatform, small, GC, JIT...
- CoreFX - basic types
- Frameworks: ASP.NET Core, EF Core, ... other frameworks
- Run on Windows and Linux

### .NET Core History - Performance [3]

### .NET Core Concepts and Definitions [4]

#### .NET Standard

- Contract that defines an API surface
- Versions: 1.0 – 2.0
- A list of Types and methods that are available in different versions
- “.NET” flavors implement the standard
- .NET Standard version are backward compatible

- Code sharing!
  - Target the standard – run everywhere

- Full framework, .NET Core, Mono (Xamarin): all support .NET Standard 2.0
  - As a library author: you should target .NET Standard (and not e.g. .NET Core)

Portable Class Library (PCL) and Shared Projects?
- Recommendation: use .NET Standard
- PCL and Shared Projects: previous attempt to share code across .NET Platforms (e.g. Full Framework and Xamarin)
- So Instead of PCL you should create .NET Standard Library!

.NET Standard 2.0

- Supported by .NET Core 2.0
- Doubled the number of APIs
- Supports mscorlib based .NET Framework libs.

#### Deployment Options
##### Deployment Option: Self Contained
- Contains the CLR
- No preinstalled framework needed 
- Bigger deployment package which targets a specific OS 
- The executable is not cross-platform
- Need multiple Self Contained packages to target multiple OS systems

Pass runtime identifier: --runtime / -r

dotnet publish --runtime osx.10.11-x64
 - (Or –r osx.10.11-x64)

```xml
 <PropertyGroup>
   <TargetFramework>netcoreapp2.0</TargetFramework>
   <RuntimeIdentifier>win10-x64</RuntimeIdentifier>
 </PropertyGroup>
```

##### Deployment Option: Shared Framework

- Only contains the application’s code
- Requires preinstalled .NET Core on the target machine
- Cross platform deployment package
- Small package size
- Default

ASP.NET Core 2.0

Self contained win10-x64 - 96 Mb
vs.
Shared Framework - 2 Mb

##### .NET Core Application Types

* 1: Web Application - ASP.NET Core - run Kestrel
* 2: Console Application
+ 1: UWP (also run on Core CLR)

##### Summary

- Great for cloud
- Performance: 1,7 mio resp/sec.
- NET Standard, deployment models, versions

### Measuring CPU [5]

**Advice N 1: Always Measure**

- Numbers, performance tests, simply measure
- Collect Numbers

- Sampling Profilers 
- Inclusive vs. Exclusive samples
- Instrumentation Profilers

- CPU Utilization: The % of time when the CPU does work
- Wall clock time: simply the time that an operation takes on one specific hardware 

- Typical question: in which method do we spend a long time? 

Types of Profilers
- Sampling
- Instrumentation

#### Sampling Profilers
- Huge number of snapshots with callstacks -> aggregates this data
- Can be done on a running process without restart
- Minimal overhead
- Non CPU work is not visible! (e.g.: Thread.Sleep(…), I/O Operation)

#### Instrumentation Profiler

- Injects code to measure methods
- Measures also non CPU work
- Can potentially have more overhead
- No attach/detach 

```cs
public void MyMethod()
{
    MethodStart(nameof(MyMethod)); //*
    //Further instructions
    MethodCallStart(nameof(CallAnotherMethod)); //*
    CallAnotherMethod();
    MethodCallFinished(nameof(CallAnotherMethod)); //*
    //Further instructions
    MethodEnd(nameof(MyMethod)); //*
}
//* Injected Profiler Code
```

### Measuring Memory [6]

C++ new/delete manual management = source of errors (memory leaks)

C# - no need? - GC delete

This GC work = is time

No GC root referencing it.

Can ref obj directly or indirectly (List for ex.)

Measuring Memory 2 Questions:
- In what method we allocate?
- Which GC root holds the ref?

Visual Studio Memory Profiler
- 3 lists:
  - Functions Allocated Most Memory
  - Types With Most Memory Allocated
  - Types With Most Instances
- Call Stack

First lesson - use StringBuilder

Which GC root holds the ref?
- We have to search for GC roots
- Allocation Stack does not help

PerfView - shows allocated objs with GC root paths

```cs
public static void StringProcessingMethod()
{
    String str = "";         
    for (int i = 0; i < 10; i++)
    {
        str += “SampleString1";
        str += “SampleString2";
        str += “SampleString3";
        str += “SampleString4";
        str += “SampleString5";
    }
    //Do something with str
}

```

```cs
class Program
{
    public static List<Person> People = new List<Person>();

    static void Main(string[] args)
    {
        for (int i = 0; i < 1000000; i++)
        {
            CreatePerson();
        }
        Console.ReadKey();
    }

    public static void CreatePerson()
    {
        People.Add(new Person { Name = "Test Person", Age = 34 });
    }
}

```
#### GC Settings in .NET Core – Same as Full Framework

As a dev. we “control” the GC by 2 things:
- 1: Don’t allocate too much
- 2: Don’t have GC roots to objects that we don’t need

GC settings can be changed in the .csproj file:
```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
  <ConcurrentGarbageCollection>true</ConcurrentGarbageCollection>   
</PropertyGroup>
```
- Concurrent GC: application threads can run (almost completely) parallel with the GC 

- Server GC: Dedicated GC threads

#### What about GC performance counters? 

- Performance counters is a Windows-Concept (desktop)
- .NET Core is cross platform 
- No Performance Counters on .NET Core

### Visual Studio Performance Tools - PerfTips and Profiler [7]
### Visual Studio Performance Tools – The Diagnostic Tools Window [8]
### Event Tracing: ETW and PerfView [9]
### Micro Benchmarking with BenchmarkDotNet [10]
### .NET Core Performance Diagnostic on Linux [11]
### Performance Monitoring on the Developer Machine with Stackify Prefix [12]
### Performance Monitoring on the Developer Machine with MiniProfiler [13]
### Value Types vs. Reference Types and Reducing Pressure on the GC [14]
### Value Types vs. Reference Types and Reducing Pressure on the GC - Demo [15]
### Saving Threads with async/await [16]
### Saving Threads with async/await – Demo [17]
### Choosing the Right Collection [18]
### Choosing the Right Collection - Demo [19]
### Reusing Arrays with ArrayPool<T> [26]
### Accessing all Types of Memory Safely and Efficiently with Span<T> [27]
### Accessing all Types of memory Safely and Efficiently with Span<T> - Demo [28]
### Make faster Queries with Entity Framework Core [29]
### Loading Dependent Entities Efficiently [30]
### EF Core Performance: Maximum Length, Client Side Execution, Change Tracking [31]
### Pre-JIT .NET Core Applications with CrossGen [32]
### Make your .NET Core Application Smaller with Mono’s Linker [33]
### Faster up with ASP.NET Core Precompiled Views [34]
### Enabling Application Insights for ASP.NET Core Applications [35]
### Tracking Slow Requests and Performance Testing with Application Insights [36]
### Tracking Custom Dependencies with Application Insights [37]
### Monitoring .NET Core Applications with Dynatrace [38]
