---
layout: post
title: Communication With Hosted Service using Channels
subtitle: 
tags: [development]
comments: true
---

When you working with file processing you often need to initiate a long-running task and send the response to the client immidiately. [Hosted services](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.1&tabs=visual-studio) are a great fit in this case. But what should you do if you need to process some specific data with a hosted service? In this case [System.Threading.Channels](https://docs.microsoft.com/en-us/dotnet/api/system.threading.channels?view=netcore-3.1) is the answer. 

What is a channel? In the past if you wanted to set up producer/consumer relation in your code you needed to setup ConcurrentQueue with some synchronization context, now instead you can use the channel. Channel is a pipe that connects your producer and consumer. It's an elegant way to implement rabbit/SQS-like communication within one service. Also System.Threading.Channels are part of the .NET Core framework right now so no additional packages are needed if you want to try it.

Here is some demo code that illustrates my idea. Data producer in my case is .Net Controller method:
```csharp
[ApiController]
public class UploadController : ControllerBase
{
    private readonly ChannelWriter<Stream> _channel;
    
    public UploadController (
        ChannelWriter<Stream> channel)
    {
        _channel = channel;
    }

    public async Task<IActionResult> Upload([FromForm] FileInfo fileInfo)
    {
        var ms = new MemoryStream();
        await fileInfo.FormFile.CopyToAsync(ms);
        await _channel.WriteAsync(ms);
        return Ok();
    }
}
```

The consumer is a hosted service that does some basic data processing:
```csharp
public class HostedService: BackgroundService
{
        private readonly ILogger _logger;
        private readonly ChannelReader<Stream> _channel;

        public HostedService(
            ILogger logger,
            ChannelReader<Stream> channel)
        {
            _logger = logger;
            _channel = channel;
        }

        protected override async Task ExecuteAsync(CancellationToken cancellationToken)
        {
            await foreach (var item in _channel.ReadAllAsync(cancellationToken))
            {
                try
                {
                    // do your work with data
                }
                catch (Exception e)
                {
                    _logger.Error(e, "An unhandled exception occured");
                }
            }
        }
}
```

And there is my DI-related code. In this case, I have only one consumer so I created a channel with the parameter that helps to avoid unnecessary locks:
```csharp
internal static IServiceCollection AddChannel(this IServiceCollection services)
{
        services.AddSingleton<Channel<Stream>>(Channel.CreateUnbounded<Stream>(new UnboundedChannelOptions() { SingleReader = true }));
        services.AddSingleton<ChannelReader<Stream>>(svc => svc.GetRequiredService<Channel<Stream>>().Reader);
        services.AddSingleton<ChannelWriter<Stream>>(svc => svc.GetRequiredService<Channel<Stream>>().Writer);
        return services;
}
```
 
## What to read

To understand the topic better I strongly recommend reading these two articles. They will help to understand hosted services and channels better.

- https://devblogs.microsoft.com/dotnet/an-introduction-to-system-threading-channels/
- https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.1&tabs=visual-studio
