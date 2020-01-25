﻿﻿﻿---
title: >-
  Server to Client messaging using SignalR
date: 2020-01-24 17:38:24
tags: server-to-client messaging signalR web performance TOPIC
---


# Improve web performance using Server to Client messaging

We came across multiple instances in our project where we used to poll APIs to get updated data to display on web pages. There are scenarios where an API call triggers a backend process which may take more than 60 seconds to complete. Most optimal way to handle such scenarios is to ask server to send a notification when there is a change that may affect the displayed data on clients.

[Asp.net SignalR](https://docs.microsoft.com/en-us/aspnet/core/tutorials/signalr?view=aspnetcore-3.1&tabs=visual-studio) provides great support for achieving this. SignalR can be easily setup to enable communications within the same site.

We wanted a way where a external service could communicate to our websites clients when there is an update.

Here is our solution:


![Server to client communication using micro service](https://github.com/mahesh8488/static-warehouse/blob/master/images/serverToClientUsingAspNetSignalR.PNG?raw=truehttps://github.com/mahesh8488/static-warehouse/blob/master/images/serverToClientUsingAspNetSignalR.PNG?raw=true)


### Client 
Web clients will subscribe for messages unique to their requirements.
We used a unique identifier as "Topic" name. [Topic](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions#topics-and-subscriptions) is a service bus concept which supports on to many communication in a publish/subscribe pattern.

Below is the controller which will help subscribe/unsubscribe "TOPIC" from client. Since it will communicate directly to SignalR, we have to refer the signalR client library in Web project. (it could have better if I had moved this to Communications service.)

To subscribe to a topic invoke subscribe method with a specific topic string.




```js
topicHubModule.factory("messagingHub", ["utils", function (utils) {
    return {
        start: async function (signalRUrl) {
            if (this.connection === null || this.connection === undefined) {
                this.activeSubscriptions = [];
                this.connection = new signalR.HubConnectionBuilder()
                    .withUrl(signalRUrl)
                    .build();

                await this.connection.start();
            }
        },

        stop: async function () {
            if (this.connection !== null) {
                await this.connection.stop();
            }
        },

        subscribe: async function (topic, callback, connectionUrl) {
            await this.start(connectionUrl);
            if (this.activeSubscriptions === null || this.activeSubscriptions === undefined) {
                this.activeSubscriptions = [];
            }

            await this.connection.invoke("subscribe", topic);
            await this.connection.on(topic, callback);
            this.activeSubscriptions.push({ topic, callback });
        },

        unsubscribe: async function (topic, callback, connectionUrl) {
            await this.start(connectionUrl);

            await this.connection.invoke("unsubscribe", topic);
            await this.connection.off(topic, callback);

            this.activeSubscriptions = utils.RemoveItemFromArray(this.activeSubscriptions, { topic, callback }, (item1, item2) => {
                return item1.topic === item2.topic && item2.callback === item2.callback;
            });
        }
    };
}]);
```


### Communications service
This is a micro service which acts as a wrapper for messaging using SignalR. 

API that need to be implemented:

This will enable external services to broadcast messages to all clients listening to topic.

```js
[HttpPost("{message}")]
public async Task<string> SendMessageToSignalR(string topic, [FromBody] Message message)
{
   await _hubContext.Clients.GroupExcept(topic, new List<string>()).SendAsync(topic, message.body);
}
```

  
### External service
This service will connect to the Communications service and invoke SendMessageToSignalR API with the TOPIC name and message. SignalR brodcasts message to every client which is listening to the TOPIC.

Feel free to comment and suggest better solutions or improve this solution !