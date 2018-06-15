---
title: Spolehlivé aktéři časovače a upomínek | Microsoft Docs
description: Úvod do časovače a upomínek pro Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e43aec6630a4a688ffd6c52a5e5bd711243fa662
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206787"
---
# <a name="actor-timers-and-reminders"></a>Časovače objektu actor a upomínek
Aktéři můžete naplánovat pravidelné práce na samotných tak, že zaregistrujete časovače nebo připomenutí. Tento článek ukazuje, jak použít časovačů a připomenutí a vysvětluje rozdíly mezi nimi.

## <a name="actor-timers"></a>Časovače objektu actor
Časovače objektu actor poskytují jednoduché obálku kolem .NET nebo Java časovač zajistit, že metody zpětného volání respektují souběžnosti na základě zapnout zaručuje, že poskytuje modul runtime aktéři.

Můžete použít aktéři `RegisterTimer`(C#) nebo `registerTimer`(Java) a `UnregisterTimer`(C#) nebo `unregisterTimer`metody (Java) na jejich základní třída pro registraci a zrušit jejich časovače. Následující příklad ukazuje použití časovače rozhraní API. Rozhraní API jsou velmi podobné .NET časovačem nebo Java časovače. V tomto příkladu po splatnosti, časovač runtime aktéři zavolá `MoveObject`(C#) nebo `moveObject`– metoda (Java). Metoda záruku, respektují souběžnosti na základě vypněte. To znamená, že žádné další metody objektu actor nebo zpětná volání časovače nebo připomenutí bude v průběhu až po dokončení provádění této zpětného volání.

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

Další období časovač spustí po dokončení provádění zpětného volání. To znamená, že časovač je zastavena v průběhu zpětné volání provádí a jestli je spuštěná při dokončení zpětného volání.

Modul runtime aktéři uloží změny provedené správce stavu objektu actor po dokončení zpětné volání. Pokud dojde k chybě při ukládání stavu, bude tento objekt actor deaktivovat a aktivuje novou instanci.

Při deaktivaci objektu actor v rámci uvolňování paměti, zastaví se všechny časovače. Poté jsou vyvolány žádná zpětná volání časovače. Modul runtime aktéři navíc nezachovává žádné informace o časovače, které byly spuštěné před deaktivace. Je objektu actor k registraci všech časovače, které je nutné, když se znovu aktivuje v budoucnu. Další informace najdete v části na [uvolňování objektu actor](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Připomenutí objektu actor
Připomenutí jsou mechanismus pro aktivaci trvalé zpětná volání v objektu actor časech. Jejich funkce je podobná časovače. Ale na rozdíl od časovače, připomenutí aktivaci za všech okolností dokud objektu actor explicitně Odregistruje nebo objektu actor je explicitně odstranit. Konkrétně připomenutí se aktivují napříč objektu actor deaktivací a převzetí služeb při selhání, protože runtime aktéři potrvají informace o objektu actor připomenutí pomocí zprostředkovatele stavu objektu actor. Upozorňujeme, že spolehlivost připomenutí je vázaný na poskytované poskytovatelem stavu objektu actor záruky spolehlivost stavu. To znamená, že pro aktéři, jejichž trvalost stavu je nastaven na hodnotu None, nebude upomínky fire po převzetí služeb při selhání. 

K registraci, zobrazí se připomenutí, volání objektu actor `RegisterReminderAsync` metoda zadaný na základní třídy, jak je znázorněno v následujícím příkladu:

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

V tomto příkladu `"Pay cell phone bill"` je název připomenutí. Toto je řetězec, který objektu actor používá k jedinečné identifikaci připomenutí. `BitConverter.GetBytes(amountInDollars)`(C#) je kontext, který je přidružen připomenutí. Bude předáno zpět do objektu actor jako argument funkci zpětného volání připomenutí tj `IRemindable.ReceiveReminderAsync`(C#) nebo `Remindable.receiveReminderAsync`(Java).

Aktéři, které používají připomenutí musí implementovat `IRemindable` rozhraní, jak je znázorněno v následujícím příkladu.

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

Když se aktivuje připomenutí bude vyvolání runtime Reliable Actors `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`(Java) metoda objektu Actor. Objekt actor můžete zaregistrovat několik připomenutí a `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`(Java) metoda je volána při některé z těchto připomenutí se aktivuje. Objektu actor můžete použít připomenutí název, který je předán `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`(Java) metoda zjistěte, které připomenutí byla aktivována.

Aktéři runtime uloží objektu actor stavu, kdy `ReceiveReminderAsync`(C#) nebo `receiveReminderAsync`dokončení volání (Java). Pokud dojde k chybě při ukládání stavu, bude tento objekt actor deaktivovat a aktivuje novou instanci.

Zrušení registrace připomenutí, volání objektu actor `UnregisterReminderAsync`(C#) nebo `unregisterReminderAsync`(Java) metoda, jak je znázorněno v následujících příkladech.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jako v příkladu nahoře, `UnregisterReminderAsync`(C#) nebo `unregisterReminderAsync`(Java) metoda přijímá `IActorReminder`(C#) nebo `ActorReminder`rozhraní (Java). Podporuje základní třída objektu actor `GetReminder`(C#) nebo `getReminder`(Java) metoda, která slouží k načtení `IActorReminder`(C#) nebo `ActorReminder`(Java) rozhraní předáním v názvu připomenutí. To je vhodné, protože není potřeba zachovat objektu actor `IActorReminder`(C#) nebo `ActorReminder`rozhraní (Java), který byl vrácen ze `RegisterReminder`(C#) nebo `registerReminder`volání metody (Java).

## <a name="next-steps"></a>Další kroky
Další informace o události objektu Actor spolehlivé a vícenásobný přístup:
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
