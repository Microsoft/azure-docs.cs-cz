---
title: Spolehlivé Actors poznámky o serializace typu objektu actor
description: Popisuje základní požadavky pro definování serializovatelných tříd, které lze použít k definování stavů a rozhraní spolehlivého objektu actor service fabric
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349307"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Poznámky k serializace typu Spolehlivé objekty actor service fabric
Argumenty všech metod, typy výsledků úloh vrácených každou metodou v rozhraní objektu actor a objekty uložené ve správci stavu objektu musí být [datová smlouva serializovatelná](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). To platí také pro argumenty metod definovaných v [rozhraní události objektu actor](service-fabric-reliable-actors-events.md). (Metody rozhraní události objektu Actor vždy vrátí neplatné.)

## <a name="custom-data-types"></a>Vlastní datové typy
V tomto příkladu následující rozhraní objektu actor definuje metodu, která vrací vlastní datový typ s názvem `VoicemailBox`:

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

Rozhraní je implementováno objektem actor, který `VoicemailBox` používá správce stavu k uložení objektu:

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

V tomto příkladu `VoicemailBox` je objekt serializován, když:

* Objekt je přenášen mezi instancí objektu actor a volajícího.
* Objekt je uložen ve správci stavu, kde je trvalý na disku a replikován do jiných uzlů.

Architektura Reliable Actor používá serializaci DataContract. Proto vlastní datové objekty a jejich členy musí být anotovány s **DataContract** a **DataMember** atributy, v uvedeném pořadí.

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
* [Časovače a připomenutí herce](service-fabric-reliable-actors-timers-reminders.md)
* [Akce herce](service-fabric-reliable-actors-events.md)
* [Herec reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Herec kárný polymorfismus a objektově orientované návrhové vzory](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostika a sledování výkonu actoru](service-fabric-reliable-actors-diagnostics.md)
