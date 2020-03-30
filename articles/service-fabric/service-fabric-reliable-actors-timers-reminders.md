---
title: Spolehlivé actors časovače a připomenutí
description: Úvod k časovačům a připomenutí pro spolehlivé objekty actor service fabric, včetně pokynů, kdy použít.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639611"
---
# <a name="actor-timers-and-reminders"></a>Časovače a připomenutí herce
Objekty actor mohou naplánovat pravidelnou práci na sobě registrací časovačů nebo připomenutí. Tento článek ukazuje, jak používat časovače a připomenutí a vysvětluje rozdíly mezi nimi.

## <a name="actor-timers"></a>Časovače herce
Časovače objektu actor poskytují jednoduchý obálku kolem časovače .NET nebo Java, aby bylo zajištěno, že metody zpětného volání respektují záruky souběžnosti založené na časové obměny, které poskytuje objekt actor.

Objekty `RegisterTimer`actor můžete použít `registerTimer`(C#) `UnregisterTimer`nebo (Java) a (C#) nebo `unregisterTimer`(Java) metody na jejich základní třídy zaregistrovat a zrušit registraci jejich časovače. Následující příklad ukazuje použití časových api. Rozhraní API jsou velmi podobná časovači .NET nebo časovači Java. V tomto příkladu, když je časovač splatný, `MoveObject`bude čas `moveObject`Actors volat metodu (C#) nebo (Java). Metoda je zaručena respektovat tahovou souběžnost. To znamená, že žádné jiné metody objektu actor nebo časovač/připomenutí zpětná volání bude probíhá, dokud toto zpětné volání dokončí spuštění.

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

Další období časovače spustí po dokončení spuštění zpětného volání. To znamená, že časovač je zastaven, zatímco zpětné volání je spuštěna a je spuštěna po dokončení zpětného volání.

Actors runtime uloží změny provedené ve správci stavu objektu actor po dokončení zpětného volání. Pokud dojde k chybě při ukládání stavu, bude tento objekt objektu objektu actor deaktivován a bude aktivována nová instance.

Všechny časovače jsou zastaveny při actor je deaktivován jako součást uvolňování paměti. Po tom nejsou vyvolána žádná zpětná volání časovače. Také actors runtime neuchovává žádné informace o časovače, které byly spuštěny před deaktivací. Je na objektu actor zaregistrovat všechny časovače, které potřebuje, když je znovu aktivován v budoucnu. Další informace naleznete v části o [uvolnění paměti objektu actor](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Připomenutí herce
Připomenutí jsou mechanismus pro aktivaci trvalých zpětných volání u objektu actor v určených časech. Jejich funkčnost je podobná časovačům. Ale na rozdíl od časovače připomenutí se aktivují za všech okolností, dokud objekt actor explicitně zruší registraci nebo objekt actor je explicitně odstraněn. Konkrétně připomenutí se aktivují napříč deaktivace objektu actor a převzetí služeb při selhání, protože objekt actor runtime uchovává informace o objektu actor připomenutí pomocí zprostředkovatele stavu objektu actor. Vezměte prosím na vědomí, že spolehlivost upomínek je vázána na státní záruky spolehlivosti poskytované poskytovatelem stavu objektu actor. To znamená, že pro objekty actor, jejichž trvalost stavu je nastavena na none, připomenutí nebude požární po převzetí služeb při selhání. 

Chcete-li zaregistrovat připomenutí, `RegisterReminderAsync` objekt actor volá metodu poskytnutou v základní třídě, jak je znázorněno v následujícím příkladu:

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

V tomto `"Pay cell phone bill"` příkladu je název připomenutí. Toto je řetězec, který objekt actor používá k jednoznačné identifikaci připomenutí. `BitConverter.GetBytes(amountInDollars)`(C#) je kontext, který je přidružen k připomenutí. Bude předána zpět objektu actor jako argument zpětného volání `IRemindable.ReceiveReminderAsync`připomenutí, tj.(C#) nebo `Remindable.receiveReminderAsync`(Java).

Objekty actor, které `IRemindable` používají připomenutí musí implementovat rozhraní, jak je znázorněno v příkladu níže.

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

Při aktivaci připomenutí, bude reliable actors `ReceiveReminderAsync`runtime vyvolat `receiveReminderAsync`(C#) nebo (Java) metoda actor. Objekt actor může zaregistrovat více `ReceiveReminderAsync`připomenutí a `receiveReminderAsync`metoda (C#) nebo (Java) je vyvolána při aktivaci některého z těchto připomenutí. Objekt actor můžete použít název připomenutí, `ReceiveReminderAsync`který je předán `receiveReminderAsync`do (C#) nebo (Java) metoda zjistit, které připomenutí bylo spuštěno.

Actors runtime uloží stav objektu actor `ReceiveReminderAsync`po dokončení `receiveReminderAsync`volání (C#) nebo (Java). Pokud dojde k chybě při ukládání stavu, bude tento objekt objektu objektu actor deaktivován a bude aktivována nová instance.

Chcete-li zrušit registraci připomenutí, objekt actor volá metodu `UnregisterReminderAsync`(C#) nebo `unregisterReminderAsync`(Java), jak je znázorněno v následujících příkladech.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak je uvedeno `UnregisterReminderAsync`výše, metoda `unregisterReminderAsync`(C#) nebo `IActorReminder`(Java) `ActorReminder`přijímá rozhraní (C#) nebo (Java). Základní třída objektu `GetReminder`actor podporuje `getReminder`metodu (C#) nebo (Java), kterou lze použít k načtení rozhraní `IActorReminder`(C#) nebo `ActorReminder`(Java) předáním názvu připomenutí. To je výhodné, protože objekt actor `IActorReminder`nemusí zachovat `ActorReminder`rozhraní (C#) nebo `RegisterReminder`(Java), které `registerReminder`bylo vráceno z volání metody (C#) nebo (Java).

## <a name="next-steps"></a>Další kroky
Další informace o událostech spolehlivého herce a reentrancy:
* [Akce herce](service-fabric-reliable-actors-events.md)
* [Herec reentrancy](service-fabric-reliable-actors-reentrancy.md)
