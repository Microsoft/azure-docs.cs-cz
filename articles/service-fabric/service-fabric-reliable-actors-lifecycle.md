---
title: Přehled životního cyklu objektu actor pro Azure Service Fabric
description: Vysvětluje Service Fabric životní cyklus spolehlivého objektu actor, uvolňování paměti a ruční odstraňování objektů actor a jejich stavu.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576039"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Životní cyklus objektu actor, automatické uvolňování paměti a ruční odstranění
Objekt actor se aktivuje při prvním volání jakékoli z jeho metod. Objekt actor se deaktivuje (uvolňování paměti modulem Actors), pokud se nepoužívá pro konfigurovatelnou dobu. Objekt actor a jeho stav lze také kdykoli odstranit ručně.

## <a name="actor-activation"></a>Aktivace objektu actor
Při aktivaci objektu actor dojde k následujícímu:

* Pokud se pro objekt actor dostane volání a jedna z nich ještě není aktivní, vytvoří se nový objekt actor.
* Stav objektu actor je načten, pokud udržuje stav.
* `OnActivateAsync`Je volána metoda (C#) nebo `onActivateAsync` (Java) (která může být přepsána v implementaci objektu actor).
* Objekt actor je nyní považován za aktivní.

## <a name="actor-deactivation"></a>Deaktivace objektu actor
Při deaktivaci objektu actor dojde k následujícímu:

* Pokud se objekt actor nepoužívá v určitém časovém období, je odebrán z tabulky aktivních aktérů.
* `OnDeactivateAsync`Je volána metoda (C#) nebo `onDeactivateAsync` (Java) (která může být přepsána v implementaci objektu actor). Tím se vymažou všechny časovače objektu actor. Z této metody by neměly být volány operace objektu actor, jako jsou změny stavu.

> [!TIP]
> Modul runtime Fabric Actors generuje některé [události související s aktivací a deaktivací objektu actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Jsou užitečné v monitorování diagnostiky a výkonu.
>
>

### <a name="actor-garbage-collection"></a>Uvolňování paměti objektu actor
Když je objekt actor deaktivován, jsou uvolněny odkazy na objekt actor a v normálním případě se může jednat o uvolňování paměti modulem CLR (Common Language Runtime) nebo s uvolňováním paměti Java Virtual Machine (JVM). Uvolňování paměti vyčistí pouze objekt objektu actor; neodebere **stav** uložený ve Správci stavu objektu actor. Při dalším spuštění objektu actor je vytvořen nový objekt actor a jeho stav je obnoven.

Co se počítá jako "používá se" pro účely deaktivace a uvolňování paměti?

* Přijetí volání
* `IRemindable.ReceiveReminderAsync` vyvolaná metoda (platí pouze v případě, že objekt actor používá připomenutí)

> [!NOTE]
> Pokud objekt actor používá časovače a je vyvolána jeho zpětné volání časovače, **nepočítá se** jako "používaný".
>
>

Než přejdete k podrobnostem o deaktivaci, je důležité definovat následující výrazy:

* *Interval kontroly*. Toto je interval, ve kterém modul runtime Actors prohledává v tabulce aktivních aktérů pro aktéry, které mohou být deaktivovány a shromažďovány z paměti. Výchozí hodnota je 1 minuta.
* *Časový limit nečinnosti*. To je doba, po kterou musí objekt actor zůstat nepoužitý (nečinný) předtím, než bude možné ho deaktivovat a shromažďovat z paměti. Výchozí hodnota je 60 minut.

Obvykle není nutné měnit tato výchozí nastavení. V případě potřeby se ale tyto intervaly dají změnit `ActorServiceSettings` při registraci služby objektu [actor](service-fabric-reliable-actors-platform.md):

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
Pro každý aktivní objekt actor sleduje modul runtime objektu actor množství času, který je nečinný (tj. nepoužitý). Modul runtime objektu actor zkontroluje každý z objektů actor každý `ScanIntervalInSeconds` , aby zjistil, zda může být uvolněna z paměti a zda byla označena jako nečinná pro `IdleTimeoutInSeconds` .

Kdykoli je použit objekt actor, jeho doba nečinnosti je nastavena na hodnotu 0. V takovém případě může být objekt actor uvolněn z paměti pouze v případě, že opět zůstane nečinný pro `IdleTimeoutInSeconds` . Odvolání, že se objekt actor považuje za použitý, pokud je spuštěná metoda rozhraní objektu actor nebo zpětné volání připomenutí objektu actor. Objekt actor není **považován za** použitý, pokud je provedeno zpětné volání časovače.

Následující diagram znázorňuje životní cyklus jednoho objektu actor k ilustraci těchto konceptů.

![Příklad času nečinnosti][1]

Tento příklad ukazuje dopad volání, připomenutí a časovačů metody actor na životního cyklu tohoto objektu actor. Následující body tohoto příkladu představují zmínku o:

* Hodnoty ScanInterval a IdleTimeout jsou nastaveny na 5 a 10 v uvedeném pořadí. (Jednotky tady nezáleží, protože náš účel je jenom k ilustraci konceptu.)
* Vyhledávání objektů Actor, které se mají vyhodnotit do paměti, probíhá v T = 0, 5, 10, 15, 20, 25, jak je definováno v intervalu prohledávání 5.
* Pravidelný časovač je aktivován při T = 4, 8, 12, 16, 20, 24 a spustí se jeho zpětné volání. Nemá vliv na dobu nečinnosti objektu actor.
* Volání metody objektu actor při T = 7 obnoví dobu nečinnosti na hodnotu 0 a zpozdí uvolnění paměti objektu actor.
* Zpětné volání připomenutí objektu actor se provádí při T = 14 a dále zpozdí uvolňování paměti objektu actor.
* Během kontroly uvolňování paměti při T = 25, doba nečinnosti objektu actor nakonec překračuje časový limit nečinnosti 10 a objekt actor je uvolněn z paměti.

Objekt actor nebude nikdy uvolněn při provádění jedné z jeho metod, bez ohledu na to, kolik času stráví při provádění této metody. Jak bylo zmíněno dříve, spuštění metod rozhraní objektu actor a zpětného volání připomenutí brání uvolňování paměti resetováním doby nečinnosti objektu actor na hodnotu 0. Spuštění zpětného volání časovače neresetuje dobu nečinnosti na 0. Uvolňování paměti objektu actor je však odloženo, dokud zpětné volání časovače nedokončí provedení.

## <a name="manually-deleting-actors-and-their-state"></a>Ruční odstranění objektů actor a jejich stavu
Uvolňování paměti deaktivovaných aktérů vyčistí jenom objekt actor, ale neodebere data uložená ve Správci stavu objektu actor. Když je objekt actor znovu aktivován, jeho data jsou znovu zpřístupněna prostřednictvím Správce stavu. V případech, kdy objekty actor ukládají data do Správce stavů a deaktivují se, ale nikdy se neaktivují, může být nutné vyčistit svá data.  Příklady, jak odstranit aktéry, najdete v tématu [odstranění objektů actor a jejich stav](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Další kroky
* [Časovače a připomenutí objektu actor](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Monitorování diagnostiky a výkonu objektu actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace k rozhraní API actor](/previous-versions/azure/dn971626(v=azure.100))
* [Ukázkový kód C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Vzorový kód Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
