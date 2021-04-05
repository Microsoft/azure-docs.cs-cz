---
title: Reliable Actors poznámky k serializaci typu objektu actor
description: Popisuje základní požadavky pro definování serializovatelných tříd, které lze použít k definování Service Fabric Reliable Actors stavy a rozhraní.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576005"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Poznámky k Service Fabric Reliable Actors serializaci typu
Argumenty všech metod, výsledné typy úloh vrácených každou metodou v rozhraní objektu actor a objekty uložené ve Správci stavu objektu actor musí být [serializovatelné kontraktu dat](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). To platí také pro argumenty metod definovaných v [rozhraních událostí objektu actor](service-fabric-reliable-actors-events.md). (Metody rozhraní události objektu actor vždy vracejí typ void.)

## <a name="custom-data-types"></a>Vlastní datové typy
V tomto příkladu následující rozhraní objektu actor definuje metodu, která vrací vlastní datový typ s názvem `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Rozhraní je implementováno objektem Actor, který používá správce stavu k uložení `VoicemailBox` objektu:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

V tomto příkladu `VoicemailBox` je objekt serializován v těchto případech:

* Objekt je přenášen mezi instancí objektu actor a volajícím.
* Objekt je uložen ve Správci stavu, kde je trvalý na disk a replikován do jiných uzlů.

Rozhraní Reliable actor používá serializaci kontraktu DataContract. Proto je nutné vlastní datové objekty a jejich členy opatřit poznámkami pomocí atributů **DataContract** a **DataMember** v uvedeném pořadí.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Další kroky
* [Životní cyklus objektu actor a uvolňování paměti](service-fabric-reliable-actors-lifecycle.md)
* [Časovače a připomenutí objektu actor](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Polymorfismus objektu actor a objektově orientované vzory návrhu](service-fabric-reliable-actors-polymorphism.md)
* [Monitorování diagnostiky a výkonu objektu actor](service-fabric-reliable-actors-diagnostics.md)
