---
title: Reliable Actors – časovače a připomenutí | Dokumentace Microsoftu
description: Úvod do časovače a připomenutí pro Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667425"
---
# <a name="actor-timers-and-reminders"></a>Objekt actor časovače a připomenutí
Objekty actor můžete naplánovat pravidelné práci na samotných tak, že zaregistrujete časovače a připomenutí. Tento článek ukazuje, jak pomocí časovače a připomenutí a vysvětluje rozdíly mezi nimi.

## <a name="actor-timers"></a>Objekt actor časovače
Objekt actor časovače poskytují jednoduché Obálka kolem rozhraní .NET nebo Javě časovač Ujistěte se, že metody zpětného volání respektovat souběžnosti založená na řadě vy zaručuje, že poskytuje modul runtime objektů actor.

Můžete použít objekty actor `RegisterTimer`(C#) nebo `registerTimer`(Java) a `UnregisterTimer`(C#) nebo `unregisterTimer`metody (Java) na své základní třídy se zaregistrovat a zrušit jejich časovače. Následující příklad ukazuje použití rozhraní API časovače. Rozhraní API jsou velmi podobné časovače .NET nebo Javě časovače. V tomto příkladu po vypršení platnosti, časovač Actors modul runtime zavolá `MoveObject`(C#) nebo `moveObject`– metoda (Java). Metoda je zaručeno, že se respektoval souběžnosti založená na řadě vy. To znamená, že žádné další metody objektu actor a zpětná volání časovače a připomenutí v průběhu až do této zpětné volání dokončení provádění.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Další období časovač spustí po dokončení zpětného volání. Z toho vyplývá, že časovač zastavení při zpětném volání provádí a jestli je spuštěná při dokončení zpětného volání.

Modul runtime Actors uloží změny provedené objektu actor State Manager po dokončení zpětného volání. V případě chyby při ukládání stavu objektu actor se deaktivuje a aktivuje se nová instance.

Pokud jako součást uvolňování paměti je deaktivována objekt actor, zastaví se všechny časovače. Žádná zpětná volání časovače jsou vyvolány po tomto. Modul runtime Actors navíc žádné informace o časovače, které byly spuštěné před deaktivací nezachová. Záleží objektu actor pro všechny časovače, které jsou potřebné při aktivaci v budoucnu zaregistrovat. Další informace najdete v části na [uvolňování paměti objektu actor](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Připomenutí objektu actor
Připomenutí slouží jako mechanismus pro aktivaci trvalé zpětná volání na prvek "actor" časech. Jejich funkce je podobný časovače. Ale na rozdíl od časovače, připomenutí spuštěná za všech okolností dokud nebude objekt actor explicitně zruší registraci jejich nebo objekt actor sami výslovně neodstraníte. Konkrétně se neaktivuje připomenutí napříč deaktivací objektu actor a převzetí služeb při selhání, protože modul runtime Actors nevyřeší informace o objektu actor připomenutí pomocí zprostředkovatele stavu objektu actor. Mějte prosím na paměti, že spolehlivost připomenutí se váže na stav spolehlivost záruky poskytované poskytovatelem stavu objektu actor. To znamená, že pro objekty actor jehož trvalost stavu je nastavena na hodnotu None, upomínky neaktivují po převzetí služeb. 

Připomenutí registrace, prvek "actor" volá `RegisterReminderAsync` metodu k dispozici na základní třídu, jak je znázorněno v následujícím příkladu:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

V tomto příkladu `"Pay cell phone bill"` je název připomenutí. Toto je řetězec, který objekt actor se používá k jednoznačné identifikaci připomenutí. `BitConverter.GetBytes(amountInDollars)`(C#) je kontext, který je přidružený k připomenutí. Bude předáno zpět na objekt actor jako argument pro zpětné volání připomenutí, tj `IRemindable.ReceiveReminderAsync`(C#) nebo `Remindable.receiveReminderAsync`(Java).

Musí implementovat objektů actor, které používají připomenutí `IRemindable` rozhraní, jak je znázorněno v následujícím příkladu.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Když se aktivuje připomenutí, modul runtime Reliable Actors vyvolá `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`– metoda (Java) na objekt Actor. Prvek "actor" lze registrovat více připomenutí a `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`vyvolání metody (Java) při aktivaci všech těchto připomenutí. Objekt actor můžete použít název připomenutí, které je předáno do `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`– metoda (Java) zjistit, které připomenutí byla aktivována.

Stav objektů actor, modul runtime ukládá objektu actor, kdy `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`volání (Java). V případě chyby při ukládání stavu objektu actor se deaktivuje a aktivuje se nová instance.

Zrušit registraci připomenutí, prvek "actor" volá `UnregisterReminderAsync`(C#) nebo `unregisterReminderAsync`– metoda (Java), jak je znázorněno v následujících příkladech.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak je uvedeno výše, `UnregisterReminderAsync`(C#) nebo `unregisterReminderAsync`(Java) metoda přijímá `IActorReminder`(C#) nebo `ActorReminder`rozhraní (Java). Podporuje základní třídy objektu actor `GetReminder`(C#) nebo `getReminder`– metoda (Java), který slouží k načtení `IActorReminder`(C#) nebo `ActorReminder`rozhraní (Java) předáním názvu připomenutí. To je vhodné, protože není potřeba zachovat objekt actor `IActorReminder`(C#) nebo `ActorReminder`rozhraní (Java), který byl vrácen z `RegisterReminder`(C#) nebo `registerReminder`volání metody (Java).

## <a name="next-steps"></a>Další kroky
Další informace o Reliable Actors události a vícenásobnému přístupu:
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
