---
title: Reliable Actors časovače a připomenutí
description: Úvod do časovačů a připomenutí Service Fabric Reliable Actors, včetně pokynů k použití jednotlivých.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791790"
---
# <a name="actor-timers-and-reminders"></a>Časovače a připomenutí objektu actor
Objekty actor mohou naplánovat pravidelnou práci sami registrací časovačů nebo připomenutí. Tento článek ukazuje, jak používat časovače a připomenutí a vysvětluje rozdíly mezi nimi.

## <a name="actor-timers"></a>Časovače objektu actor
Časovače objektu actor poskytují jednoduchou obálku kolem časovače .NET nebo Java, aby se zajistilo, že metody zpětného volání respektují záruky souběžnosti, které poskytuje modul runtime Actors.

Objekty actor mohou pomocí `RegisterTimer` metod (c#) nebo `registerTimer` (Java) a ( `UnregisterTimer` c#) nebo `unregisterTimer` (Java) na své základní třídě registrovat a odregistrovat své časovače. Následující příklad ukazuje použití rozhraní API časovače. Rozhraní API jsou velmi podobná časovači .NET nebo časovači jazyka Java. V tomto příkladu, při splnění časovače, modul runtime Actors zavolá `MoveObject` metodu (C#) nebo `moveObject` (Java). Metoda je zaručena respektovat souběžnost založenou na zapínání. To znamená, že žádné jiné metody objektu actor ani zpětná volání časovače nebo připomenutí nebudou probíhat, dokud toto zpětné volání nedokončí provedení.

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

Další období časovače se spustí poté, co zpětné volání dokončí provádění. To znamená, že časovač je zastaveno v průběhu provádění zpětného volání a je spuštěn po dokončení zpětného volání.

Modul runtime Actors ukládá změny provedené ve Správci stavu objektu actor po dokončení zpětného volání. Pokud při ukládání stavu dojde k chybě, bude objekt actor deaktivován a bude aktivována nová instance.

Na rozdíl od [připomenutí](#actor-reminders)nejde časovače aktualizovat. Pokud `RegisterTimer` je volána znovu, bude registrován nový časovač.

Všechny časovače jsou zastaveny, když je objekt actor deaktivován v rámci uvolňování paměti. Žádná zpětná volání časovače nejsou vyvolána za tímto. Modul runtime Actors také neuchovává žádné informace o časovačích, které byly spuštěny před deaktivací. Je až do objektu actor k registraci všech časovačů, které potřebuje, když se znovu aktivuje v budoucnu. Další informace najdete v části o [uvolňování paměti objektu actor](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Připomenutí objektu actor
Připomenutí jsou mechanismem, jak aktivovat trvalá zpětná volání v objektu actor v zadaných časech. Jejich funkce se podobá časovačům. Ale na rozdíl od časovačů se připomenutí spouštějí za všech okolností, dokud objekt actor explicitně zruší jejich registraci, nebo je objekt actor explicitně odstraněn. Konkrétně se v rámci deaktivace a převzetí služeb při selhání spouštějí připomenutí, protože modul runtime Actors uchovává informace o připomenutích objektů actor pomocí zprostředkovatele stavu objektu actor. I na rozdíl od časovačů lze existující připomenutí aktualizovat voláním metody registrace ( `RegisterReminderAsync` ) znovu pomocí stejného *připomenutí*.

> [!NOTE]
> Spolehlivost upomínek je vázána na záruky spolehlivosti stavu poskytované poskytovatelem stavu objektu actor. To znamená, že u aktérů, jejichž trvalost stavu je nastavená na *none*, se připomenutí po převzetí služeb při selhání neaktivují.

Pro registraci připomenutí volá objekt actor [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) metodu poskytnutou pro základní třídu, jak je znázorněno v následujícím příkladu:

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

V tomto příkladu `"Pay cell phone bill"` je název připomenutí. Toto je řetězec, který objekt actor používá k jednoznačné identifikaci připomenutí. `BitConverter.GetBytes(amountInDollars)`(C#) je kontext, který je spojen s připomenutím. Bude předána zpátky objektu actor jako argument zpětného volání připomenutí, tj. `IRemindable.ReceiveReminderAsync` (C#) nebo `Remindable.receiveReminderAsync` (Java).

Objekty actor, které používají připomenutí, musí implementovat `IRemindable` rozhraní, jak je znázorněno v následujícím příkladu.

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

Když se aktivuje připomenutí, modul runtime Reliable Actors vyvolá  `ReceiveReminderAsync` metodu (C#) nebo `receiveReminderAsync` (Java) objektu actor. Objekt actor může registrovat více připomenutí a `ReceiveReminderAsync` metoda (C#) nebo `receiveReminderAsync` (Java) je vyvolána při aktivaci kterékoli z těchto připomenutí. Objekt actor může použít název připomenutí, který je předán `ReceiveReminderAsync` metodě (C#) nebo `receiveReminderAsync` (Java), a zjistit, jaké připomenutí bylo aktivováno.

Modul runtime Actors uloží stav objektu actor po `ReceiveReminderAsync` dokončení volání (C#) nebo `receiveReminderAsync` (Java). Pokud při ukládání stavu dojde k chybě, bude objekt actor deaktivován a bude aktivována nová instance.

Chcete-li zrušit registraci připomenutí, volá objekt actor `UnregisterReminderAsync` metodu (C#) nebo `unregisterReminderAsync` (Java), jak je znázorněno v níže uvedených příkladech.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Jak vidíte výše, `UnregisterReminderAsync` metoda (c#) nebo `unregisterReminderAsync` (Java) přijímá `IActorReminder` rozhraní (c#) nebo `ActorReminder` (Java). Základní třída objektu actor podporuje `GetReminder` metodu (c#) nebo `getReminder` (Java), která se dá použít k načtení `IActorReminder` rozhraní (c#) nebo `ActorReminder` (Java) předáním názvu připomenutí. To je užitečné, protože objekt actor nemusí uchovávat `IActorReminder` rozhraní (c#) nebo `ActorReminder` (Java), které bylo vráceno `RegisterReminder` voláním metody (c#) nebo `registerReminder` (Java).

## <a name="next-steps"></a>Další kroky
Seznamte se s událostmi Reliable actor a Vícenásobný přístup:
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
