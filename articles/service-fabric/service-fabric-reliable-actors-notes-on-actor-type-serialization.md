---
title: Reliable Actors – poznámky k objektu actor serializaci typu | Dokumentace Microsoftu
description: Tento článek popisuje základní požadavky pro definování serializovatelné třídy, které lze použít k definování stavů Service Fabric Reliable Actors a rozhraní
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725648"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Poznámky k Service Fabric Reliable Actors typ serializace
Argumenty všechny metody, vrátí typy výsledků úlohy každé metodě v rozhraní objektu actor a musí být objekty uložené v správce stavu objektu actor [kontraktů dat serializovatelný](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). To platí i pro argumenty metody definované v [rozhraní objektu actor](service-fabric-reliable-actors-events.md). (Metody rozhraní objektu actor událostí vždy vracet typ void.)

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

Rozhraní je implementováno prvek "actor", který používá správce stavu k uložení `VoicemailBox` objektu:

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

V tomto příkladu `VoicemailBox` objekt serializován při:

* Objekt se přenášejí mezi instance objektu actor a volající.
* Objekt se uloží do Správce stavu, ve kterém se jako trvalý disk a replikují do dalších uzlů.

Reliable Actors rozhraní používá serializace pomocí kontraktu DataContract. Proto vlastní datové objekty a jejich členy musí být komentována atributem **kontraktu dat DataContract** a **DataMember** atributů.

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


## <a name="next-steps"></a>Další postup
* [Životní cyklus a uvolňování paměti kolekce objektu actor](service-fabric-reliable-actors-lifecycle.md)
* [Objekt actor časovače a připomenutí](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Polymorfismus objektu actor a objektově orientované návrhové vzory](service-fabric-reliable-actors-polymorphism.md)
* [Monitorování výkonu a Diagnostika objektů actor](service-fabric-reliable-actors-diagnostics.md)
