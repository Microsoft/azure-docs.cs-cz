---
title: Přehled životního cyklu objektu actor fabric azure service fabric
description: Vysvětluje service fabric spolehlivý actor životní cyklus, uvolňování paměti a ruční odstranění objekty actor a jejich stav
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258314"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Životní cyklus objektu actor, automatické uvolňování paměti a ruční odstranění
Objekt actor je aktivován při prvním volání některé z jeho metod. Objekt actor je deaktivován (uvolňování shromážděné actors runtime), pokud není použit pro konfigurovatelné časové období. Objekt actor a jeho stav lze také odstranit ručně kdykoli.

## <a name="actor-activation"></a>Aktivace herce
Při aktivaci objektu actor dochází k následujícímu:

* Když přijde volání pro herce a jeden ještě není aktivní, vytvoří se nový herec.
* Stav objektu actor je načten, pokud udržuje stav.
* Metoda `OnActivateAsync` (C#) `onActivateAsync` nebo (Java) (kterou lze přepsat v implementaci objektu actor) se nazývá.
* Herec je nyní považován za aktivního.

## <a name="actor-deactivation"></a>Deaktivace herce
Při deaktivaci objektu actor dojde k následujícímu:

* Pokud objekt actor není používán po určitou dobu, je odebrán z tabulky Aktivní aktéři.
* Metoda `OnDeactivateAsync` (C#) `onDeactivateAsync` nebo (Java) (kterou lze přepsat v implementaci objektu actor) se nazývá. Tím se vymažou všechny časovače pro herce. Operace objektu actor, jako jsou změny stavu, by neměly být volány z této metody.

> [!TIP]
> Objekty Actor aplikace Fabric vyzařuje některé [události související s aktivací a deaktivací objektu actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Jsou užitečné při diagnostice a sledování výkonu.
>
>

### <a name="actor-garbage-collection"></a>Uvolnění paměti objektu actor
Při deaktivaci objektu actor jsou uvolněny odkazy na objekt objektu actor a může být uvolňování obvykle vyvolaný v běžném čase clr (CLR) nebo java virtuální počítač (JVM) uvolňování paměti. Uvolňování paměti pouze vyčistí objekt objektu actor; **neodebere** stav uložený ve Správci stavu objektu actor. Při příštím aktivaci objektu je vytvořen nový objekt objektu objektu actor a jeho stav je obnoven.

Co se počítá jako "používá" pro účely deaktivace a uvolňování paměti?

* Příjem hovoru
* `IRemindable.ReceiveReminderAsync`metoda je vyvolána (platí pouze v případě, že objekt actor používá připomenutí)

> [!NOTE]
> Pokud objekt actor používá časovače a jeho zpětné volání časovače je vyvolána, **nepočítá** se jako "používá".
>
>

Než se pustíme do podrobností deaktivace, je důležité definovat následující pojmy:

* *Interval skenování*. Toto je interval, ve kterém actors runtime prohledá jeho aktivní aktéři tabulka pro objekty actor, které lze deaktivovat a uvolňování paměti. Výchozí hodnota pro toto je 1 minuta.
* *Časový limit nečinnosti*. Toto je doba, kterou objekt actor potřebuje k tomu, aby zůstal nepoužívaný (nečinný), než může být deaktivován a uvolněn. Výchozí hodnota pro toto je 60 minut.

Obvykle není nutné tyto výchozí hodnoty měnit. V případě potřeby však tyto intervaly lze změnit prostřednictvím `ActorServiceSettings` při registraci [služby actor](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Pro každý aktivní objekt actor objektu sleduje běhový čas objektu actor dobu, po kterou byl nečinný (tj. nepoužívá se). Objekt actor runtime zkontroluje `ScanIntervalInSeconds` každý z aktérů každý, zda může být odpadky shromažďovány a označí ji, pokud byla nečinná pro `IdleTimeoutInSeconds`.

Při každém použití objektu actor je jeho doba nečinnosti nastavena na hodnotu 0. Poté může být objekt actor uvolněnpouze v případě, že opět zůstane nečinný pro `IdleTimeoutInSeconds`. Připomeňme, že objekt actor je považován za použitý, pokud je spuštěna metoda rozhraní objektu actor nebo zpětné volání připomenutí objektu actor. Objekt actor **není** považován za použitý, pokud je spuštěno jeho zpětné volání časovače.

Následující diagram znázorňuje životní cyklus jednoho objektu actor pro ilustraci těchto konceptů.

![Příklad doby nečinnosti][1]

Příklad ukazuje vliv volání metody objektu actor, připomenutí a časovače na životnost tohoto objektu actor. Za zmínku stojí následující body o příkladu:

* ScanInterval a IdleTimeout jsou nastaveny na 5 a 10 v uvedeném pořadí. (Jednotky zde nezáleží, protože naším cílem je pouze ilustrovat koncept.)
* Hledání pro herce, které mají být odpadky shromážděné se stane na T = 0,5,10,15,20,25, jak je definováno intervalskenování 5.
* Periodický časovač se aktivuje na T=4,8,12,16,20,24 a jeho zpětné volání se spustí. Nemá vliv na dobu nečinnosti herce.
* Volání metody objektu actor na T = 7 obnoví dobu nečinnosti na 0 a zpozdí uvolnění paměti objektu actor.
* Zpětné volání připomenutí objektu actor provede na T = 14 a další zpoždění uvolnění paměti objektu actor.
* Během prohledávače uvolňování paměti na T = 25, čas nečinnosti objektu actor nakonec překročí časový limit nečinnosti 10 a objekt actor je uvolněno.

Objekt actor nikdy být uvolňování při provádění jedné z jeho metod, bez ohledu na to, kolik času je stráveno při provádění této metody. Jak již bylo zmíněno dříve, provádění metod rozhraní objektu actor a připomenutí zpětná volání zabraňuje uvolnění paměti obnovením doby nečinnosti objektu actor na 0. Spuštění zpětné hojné volání časovače neobnoví dobu nečinnosti na 0. Uvolňování paměti objektu actor je však odloženo, dokud není dokončeno spuštění zpětného volání časovače.

## <a name="manually-deleting-actors-and-their-state"></a>Ruční odstranění aktérů a jejich stavu
Uvolňování paměti deaktivované objekty actor pouze vyčistí objekt objektu actor, ale neodebere data, která je uložena ve správci stavu objektu actor. Když je objekt actor znovu aktivován, jeho data jsou mu znovu zpřístupněna prostřednictvím správce stavu. V případech, kdy subjekty ukládají data ve Správci stavu a jsou deaktivovány, ale nikdy znovu aktivovány, může být nutné vyčistit jejich data.  Příklady, jak odstranit objekty actor, číst [odstranit objekty actor a jejich stav](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Další kroky
* [Časovače a připomenutí herce](service-fabric-reliable-actors-timers-reminders.md)
* [Akce herce](service-fabric-reliable-actors-events.md)
* [Herec reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostika a sledování výkonu actoru](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód jazyka C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ukázkový kód Javy](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
