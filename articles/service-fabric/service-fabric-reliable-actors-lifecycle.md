---
title: Přehled životního cyklu na základě objektu actor Azure mikroslužeb | Microsoft Docs
description: Popisuje spolehlivé objektu Actor prostředků infrastruktury služby životního cyklu, uvolňování paměti a ručně odstranit aktéři a jejich stavu
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
ms.openlocfilehash: 4e919c565574e0765227abda5832c858c36a77c0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Životní cyklus objektu actor, automatické uvolňování paměti a ruční delete
Objekt actor se aktivuje při prvním volání Přišla žádost o jeho metod. Objekt actor je deaktivované (paměti shromážděných modulem runtime aktéři), pokud se nepoužívá pro nastaveném časovém intervalu. Objekt actor a její stav lze také odstranit ručně kdykoli.

## <a name="actor-activation"></a>Aktivace objektu actor
Když se aktivuje objektu actor, dojde k následujícímu:

* Při volání je teď dostupná pro objekt actor a už není aktivní, se vytvoří nový objekt actor.
* Stav objektu actor je načtena, pokud se udržuje stavu.
* `OnActivateAsync` (C#) nebo `onActivateAsync` volání metody (Java), (které mohou být přepsána nastaveními v implementace objektu actor).
* Objektu actor je nyní považována za aktivní.

## <a name="actor-deactivation"></a>Deaktivace objektu actor
Když je objekt actor deaktivována, dojde k následující položky:

* Pokud objekt actor nepoužívá pro nějaké časové období, odebere se z tabulky Active aktéři.
* `OnDeactivateAsync` (C#) nebo `onDeactivateAsync` volání metody (Java), (které mohou být přepsána nastaveními v implementace objektu actor). Zruší všechny časovače pro objektu actor. Operace objektu actor jako stavu, ve kterém by neměl být volán změny z této metody.

> [!TIP]
> Modul runtime Fabric aktéři vysílá některé [události související s objektu actor aktivace a deaktivace](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Jsou užitečné v Diagnostika a sledování výkonu.
>
>

### <a name="actor-garbage-collection"></a>Uvolňování paměti objektu actor
Po deaktivaci objektu actor odkazy na objekt actor vydání a může být shromažďují normálně modul common language runtime (CLR) nebo java virtual machine (JVM) systém uvolňování paměti. Uvolňování paměti pouze vyčistí objekt actor; provede **není** odebrat stavu uložené v objektu actor správce stavu. Při příštím aktivaci objektu actor se vytvoří nový objekt actor a obnovit jeho stav.

Co se počítá jako "se používá" pro účely deaktivace a systém kolekce paměti?

* Přijímá volání
* `IRemindable.ReceiveReminderAsync` metody volané (platí jenom v případě objektu actor používá připomenutí)

> [!NOTE]
> Pokud objektu actor používá časovače a jeho zpětné volání časovače je volána, nemá **není** , se počítají jako "se používá".
>
>

Předtím, než jsme přejít na podrobné informace o deaktivaci, je důležité určit následující podmínky:

* *Interval sledování*. Toto je interval, kdy modul runtime aktéři hledá její tabulkou Active aktéři aktéři, které můžete deaktivovat a uklizeny. Výchozí hodnota je 1 minuta.
* *Časový limit nečinnosti*. Toto je množství času, vyžadující objektu actor zůstat nepoužívané (nečinný), než je možné deaktivovat a uklizeny. Výchozí hodnota je 60 minut.

Obvykle není potřeba změnit toto výchozí nastavení. Ale v případě potřeby tyto intervaly lze změnit prostřednictvím `ActorServiceSettings` při registraci vaší [služby objektu Actor](service-fabric-reliable-actors-platform.md):

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
Pro každou aktivní objektu actor uchovává informace runtime objektu actor o množství času, která byla nečinnosti (tzn. ne používá). Modul runtime objektu actor kontroluje každý aktéři každých `ScanIntervalInSeconds` chcete zobrazit, pokud může být paměti shromážděných a shromažďuje ji, pokud to bylo nečinné `IdleTimeoutInSeconds`.

Kdykoliv se používá objektu actor, jeho doba nečinnosti, po nastaven na hodnotu 0. Potom může být objektu actor uvolnění z paměti pouze v případě, že znovu zůstane neaktivní, pro `IdleTimeoutInSeconds`. Odvolat, aby nebyly použity, pokud je proveden metodu objektu actor rozhraní nebo zpětného volání objektu actor připomenutí považuje za objekt actor. Objekt actor je **není** považována za nebyly použity, pokud se spustí jeho zpětné volání časovače.

Následující diagram znázorňuje životní cyklus jednoho objektu actor pro ilustraci tyto koncepty.

![Příklad nečinnosti][1]

Příklad ukazuje na dobu životnosti tohoto objektu actor dopad volání metody objektu actor, připomenutí a časovače. Následující body o příkladu jsou důležité zmínit:

* ScanInterval a IdleTimeout jsou nastaveny na hodnotu 5 a 10 v uvedeném pořadí. (Jednotky, není podstatné tady, vzhledem k tomu, že je naše účel pouze k objasnění konceptu.)
* Vyhledávání aktéři na uvolnění z paměti se odehrává na T = 0, 5, 10, 15, 20, 25, jak jsou definovány kontrolu interval 5.
* Pravidelné časovač vyvolá v T = 4, 8, 12, 16, 20, 24, a provede zpětné volání. Doba nečinnosti objektu actor nemělo vliv.
* Volání metody objektu actor v T = 7 obnoví dobu nečinnosti, po na 0 a uvolňování objektu actor zpozdí.
* Zpětné volání objektu actor připomenutí provede na T = 14 a další zpozdí uvolňování objektu actor.
* Při kontrole kolekce paměti na T = 25 čas nečinnosti objektu actor nakonec překročí časový limit nečinnosti 10 a objektu actor je uvolnění z paměti.

Objekt actor nebude nikdy uvolnění z paměti, zatímco je prováděna jednu z jeho metod, bez ohledu na to, jak dlouho je věnovaný provedení této metody. Jak už bylo zmíněno dříve, brání spuštění metody rozhraní objektu actor a zpětná volání připomenutí uvolňování resetováním čas nečinnosti objektu actor na hodnotu 0. Provádění zpětných volání časovače neprovádí vynulování dobu nečinnosti, po na hodnotu 0. Uvolňování objektu actor je však odložené až zpětné volání časovače po dokončení provádění.

## <a name="manually-deleting-actors-and-their-state"></a>Ručním odstranění aktéři a jejich stavu
Uvolnění paměti deaktivované aktéři pouze vyčistí objekt actor, ale neodebere data, která je uložená v objektu actor správce stavu. Při opětovné aktivaci objektu actor jeho data se znovu k dispozici k němu pomocí Správce stavu. V případech, kdy aktéři ukládání dat do Správce stavu a jsou deaktivována ale nikdy znovu aktivovat může být nutné vyčistit svá data.  Příklady, jak odstranit aktéři, najdete v tématu [odstranit aktéři a jejich stavu](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Další postup
* [Časovače objektu actor a upomínek](service-fabric-reliable-actors-timers-reminders.md)
* [Události objektu actor](service-fabric-reliable-actors-events.md)
* [Vícenásobný přístup objektu actor](service-fabric-reliable-actors-reentrancy.md)
* [Objektu actor Diagnostika a sledování výkonu](service-fabric-reliable-actors-diagnostics.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java ukázkový kód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
