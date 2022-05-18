---
title: UnbondingPriorityMailBox
author: Banjung
date: 2022-05-16 10:00:00 +0800
categories: [Akka.net, Etc]
tags: [Akka.net]
math: true
mermaid: true
---

## Akka.Net Dispatcher(C#)

#### UnbondingPriorityMailBox
- 메시지 큐에 N개의 메시지가 대기중이어서 긴급하게 처리해야 하는 메시지가 줄을 설 때, 특정 메시지를 먼저 처리하도록 하는 옵션

- .config 파일


```
My-mailBox
{
  mailbox-type = "Mirero.IRIS.MessagePriority.Sample.Actors.MyMailBox, Mirero.IRIS.MessagePriority.Sample"
}
          
akka.actor.deployment
{
    /OnlyReceiveActor
	{
		router = round-robin-pool
		nr-of-instances = 1
        mailbox = My-mailBox
	}

	/PriorityActor
	{
	router = round-robin-pool
	nr-of-instances = 1
	}

	/SendingActor
	{
	router = round-robin-pool
	nr-of-instances = 1
	}
}

```

- MailBox.cs
```cs
public class MyMailBox : UnboundedStablePriorityMailbox
    {
        public MyMailBox(Settings settings, Config config) : base(settings, config) { }

        protected override int PriorityGenerator(object message)
        {
            ReceiveMessage msg = (message as ReceiveMessage);
            return msg.Priority;##0:high priority ##1:low priority
        }
    }
```

- Actor 생성 
```cs
Akka.Actor.Props.Create(() => new OnlyReceiveActor())
               .WithRouter(FromConfig.Instance)
               .WithMailbox("My-mailBox");
```

- 테스트
![image](/assets/img/Akka/Test1.jpg)

- 결과


```
Sending 1
Sending 2
Sending 3
Priority 1
Priority 2
Priority 3
Sending 4
Sending 5
```