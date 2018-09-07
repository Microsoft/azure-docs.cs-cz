---
title: Přehled životního cyklu Azure Service Fabric actor | Dokumentace Microsoftu
description: Vysvětluje, Service Fabric Reliable Actor životního cyklu uvolňování paměti a ručně odstranění objektů actor a jejich stavu
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: dbd9551027744d443613e32e0a082c10d4f357d5
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052043"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Životní cyklus objektů actor, automatické uvolňování paměti a ruční odstranění
Objekt actor se aktivuje při prvním volání k některé z jeho metod. Prvek "actor" je deaktivované (uvolňování paměti shromážděných modulem Actors runtime), pokud se nepoužívá pro nastaveném časovém. Prvek "actor" a jeho stav může také odstranit ručně kdykoli.

## <a name="actor-activation"></a>Aktivace objektu actor
Při aktivaci objektu actor, dojde k následujícímu:

* Při volání pochází pro prvek "actor" a už není aktivní, se vytvoří nový objekt actor.
* Pokud se udržuje stav, se načte stav objektu actor.
* `OnActivateAsync` (C#) nebo `onActivateAsync` volání metody (Java), (které mohou být přepsána nastaveními v implementace objektu actor).
* Objekt actor se teď považuje za aktivní.

## <a name="actor-deactivation"></a>Deaktivace objektu actor
Při deaktivaci prvek "actor" se stane toto:

* Pokud prvek "actor" není používána k po nějaké časové období, odebere se z tabulky aktivní účastníky.
* `OnDeactivateAsync` (C#) nebo `onDeactivateAsync` volání metody (Java), (které mohou být přepsána nastaveními v implementace objektu actor). Tato akce smaže všechny časovače pro objekt actor. Operace objektu actor, jako jsou stavu, ve kterém změn by neměla být volána z této metody.

> [!TIP]
> Modul runtime Fabric Actors vysílá některé [události související s objektu actor aktivace a deaktivace](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Jsou užitečné pro diagnostiku a sledování výkonu.
>
>

### <a name="actor-garbage-collection"></a>Uvolňování paměti objektu actor
Při deaktivaci prvek "actor" odkazy na objekt actor se vydávají a může být uvolněn z paměti obvykle common language runtime (CLR) nebo systému uvolňování paměti java virtual machine (JVM). Uvolňování paměti vyčistí jenom objekt actor. provádí **není** odebrání stavu uloženého v správce stavu objektu actor. Při příštím objektu actor se aktivuje, vytvoří nový objekt actor a obnovit jeho stav.

Co se počítá jako "se používá" pro účely deaktivaci a systém kolekce paměti?

* Přijímá volání
* `IRemindable.ReceiveReminderAsync` metody volané (platí jenom v případě, že objekt actor používá připomenutí)

> [!NOTE]
> Pokud objekt actor používá časovačů a jeho zpětné volání časovače je volána, nemá **není** se počítají jako "používá se".
>
>

Než přejdeme k podrobnostem o deaktivaci, je potřeba definovat následující podmínky:

* *Interval sledování*. Jedná se o interval, ve kterém Actors modul runtime vyhledává objektů actor, které lze deaktivovat svou tabulku aktivních objektů actor a uklizeny. Výchozí hodnota je 1 minuta.
* *Časový limit nečinnosti*. Toto je množství času, kterou je potřeba nepoužíval. prvek "actor" (nečinné) předtím, než může být deaktivované a prováděno uvolnění paměti. Výchozí hodnota je 60 minut.

Obvykle není potřeba změnit výchozí nastavení. Ale v případě potřeby tyto intervaly lze změnit prostřednictvím `ActorServiceSettings` při registraci vaší [služba objektu Actor](service-fabric-reliable-actors-platform.md):

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
Pro každého herce aktivní uchovává informace modulu runtime actor o množství času, který byl nečinný (tzn. ne použít). Modulu runtime actor zkontroluje každý aktérů každý `ScanIntervalInSeconds` zobrazíte, pokud může být uvolňování paměti shromažďují a shromažďuje ji, pokud byl nečinný `IdleTimeoutInSeconds`.

Kdykoli se používá prvek "actor", její nečinnosti se resetuje na hodnotu 0. Potom může být objekt actor uvolněn pouze v případě, že znovu zůstane neaktivní, pro `IdleTimeoutInSeconds`. Připomínáme, že nebyly použity, pokud spuštění metody rozhraní objektu actor nebo zpětné volání objektu actor připomenutí považován za prvek "actor". Prvek "actor" je **není** považovány za nebyly použity, pokud se provádí jeho zpětné volání časovače.

Následující diagram znázorňuje životní cyklus jednoho objektu actor pro ilustraci těchto konceptů.

![Příklad nečinnosti][1]

Příklad ukazuje dopad volání metody objektu actor, připomenutí a časovače týkající se doby platnosti tohoto objektu actor. Za zmínku jsou následující body o příkladu:

* ScanInterval a IdleTimeout jsou nastaveny na hodnotu 5 a 10 v uvedeném pořadí. (Jednotky není důležitá, protože Naším cílem je pouze pro ilustraci koncept.)
* Kontrola koncepce actorů je naprosto uvolněna z paměti se odehrává na T = 0, 5, 10, 15, 20, 25, tak jak je definoval interval skenování 5.
* Pravidelné časovač vyvolá na T = 4, 8, 12, 16, 20, 24, a provede zpětné volání. Doba nečinnosti objektu actor nemá vliv.
* Volání metody rozhraní objektu actor v T = 7 dobu nečinnosti, po resetuje na hodnotu 0 a zpoždění uvolnění paměti objektu actor.
* Zpětné volání objektu actor připomenutí spouští v T = 14 a dalších zpoždění uvolnění paměti objektu actor.
* Při kontrole kolekce uvolnění paměti na T = 25 čas nečinnosti objektu actor a nakonec překročí časový limit nečinnosti 10 a objekt actor je uvolněna z paměti.

Prvek "actor" nebude nikdy uvolněna z paměti při provádění jedné z jeho metod, bez ohledu na to, jak dlouho se stráven v provádění této metody. Jak už bylo zmíněno dříve, brání provádění metody rozhraní objektu actor a upomínky zpětná volání uvolňování paměti obnovením objektu actor nečinnosti na hodnotu 0. Provádění zpětných volání časovače neprovádí vynulování dobu nečinnosti, po na hodnotu 0. Uvolnění paměti objektu actor, ale je odloženo, dokud zpětné volání časovače byl dokončen.

## <a name="manually-deleting-actors-and-their-state"></a>Ruční odstranění objektů actor a jejich stav
Uvolnění paměti deaktivované actors pouze vyčistí objekt actor, ale neodeberou se data ukládaná ve službě Správce stavu prvek "actor". Při opětovné aktivaci prvek "actor" je znovu svoje data k dispozici k němu prostřednictvím Správce stavu. V případech, kde actors ukládání dat v State Manager a se deaktivuje, ale nikdy znovu aktivovat může být potřeba vyčistit svá data.  Příklady odstranění objektů actor, najdete v článku [odstranění objektů actor a jejich stav](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Další postup
* [Objekt actor časovače a připomenutí](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Monitorování výkonu a Diagnostika objektů actor](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázka v jazyce C# kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java ukázkový kód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
