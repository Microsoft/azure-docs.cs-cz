---
title: Transakce a režimy uzamčení ve spolehlivých kolekcích
description: Azure Service Fabric reliable state manager a spolehlivé kolekce transakce a uzamčení.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938910"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakce a režimy uzamčení ve spolehlivých kolekcích Azure Service Fabric

## <a name="transaction"></a>Transakce

Transakce je posloupnost operací prováděných jako jedna logická jednotka práce. Vykazuje společné [VLASTNOSTI ACID](https://en.wikipedia.org/wiki/ACID) (*nedělitost*, *konzistence*, *izolace*, *trvanlivost*) databázových transakcí:

* **Nedělitost**: Transakce musí být atomovou jednotkou práce. Jinými slovy, buď jsou provedeny všechny jeho změny dat, nebo není provedena žádná z nich.
* **Konzistence**: Po dokončení musí transakce ponechat všechna data v konzistentním stavu. Všechny vnitřní datové struktury musí být správné na konci transakce.
* **Izolace**: Změny provedené souběžnými transakcemi musí být izolovány od změn provedených jinými souběžnými transakcemi. Úroveň izolace používané pro operaci v rámci [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) je určena [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) provádění operace.
* **Trvanlivost**: Po dokončení transakce jsou její účinky v systému trvale zavedeny. Změny přetrvávají i v případě selhání systému.

### <a name="isolation-levels"></a>Úrovně izolace

Úroveň izolace definuje míru, do jaké musí být transakce izolována od změn provedených jinými transakcemi.
Existují dvě úrovně izolace, které jsou podporovány v spolehlivé kolekce:

* **Opakovatelné čtení**: Určuje, že příkazy nemohou číst data, která byla změněna, ale dosud nebyla potvrzena jinými transakcemi, a že žádné jiné transakce nemohou upravovat data, která byla přečtena aktuální transakcí, dokud nebude dokončena aktuální transakce.
* **Snímek**: Určuje, že data přečtená libovolným příkazem v transakci je transakce konzistentní verze dat, která existovala na začátku transakce.
  Transakce může rozpoznat pouze změny dat, které byly potvrzeny před zahájením transakce.
  Změny dat provedené jinými transakcemi po zahájení aktuální transakce nejsou viditelné pro příkazy spuštěné v aktuální transakci.
  Efekt je, jako kdyby příkazy v transakci získat snímek potvrzených dat, jak existovala na začátku transakce.
  Snímky jsou konzistentní napříč spolehlivé kolekce.

Spolehlivé kolekce automaticky zvolit úroveň izolace pro danou operaci čtení v závislosti na operaci a roli repliky v době vytvoření transakce.
Následuje tabulka, která zobrazuje výchozí hodnoty úrovně izolace pro operace Spolehlivé slovníky a fronty.

| Operace \ Role | Primární | Sekundární |
| --- |:--- |:--- |
| Čtení jedné entity |Opakovatelné čtení |Snímek |
| Výčet, počet |Snímek |Snímek |

> [!NOTE]
> Běžné příklady operací s `IReliableDictionary.TryGetValueAsync` `IReliableQueue.TryPeekAsync`jednou entitou jsou .
> 

Spolehlivý slovník a spolehlivé fronty podporují *čtení zápisů*.
Jinými slovy, jakýkoli zápis v rámci transakce bude viditelný pro následující čtení, které patří ke stejné transakci.

## <a name="locks"></a>Zámky

V spolehlivé kolekce všechny transakce implementovat přísné dvě fáze uzamčení: transakce neuvolní zámky, které získala, dokud transakce ukončí s přerušení nebo potvrzení.

Spolehlivý slovník používá uzamčení na úrovni řádku pro všechny operace jedné entity.
Spolehlivá fronta obchoduje mimo souběžnost pro přísné transakční VLASTNOST FIFO.
Reliable Queue používá uzamčení na úrovni `TryPeekAsync` operace `TryDequeueAsync` umožňující jednu `EnqueueAsync` transakci s a/nebo jednu transakci s najednou.
Všimněte si, že chcete-li zachovat FIFO, pokud `TryPeekAsync` nebo `TryDequeueAsync` někdy `EnqueueAsync`zjistí, že spolehlivé fronty je prázdný, budou také zamknout .

Operace zápisu vždy trvat výhradní zámky.
Pro operace čtení, uzamčení závisí na několika faktorech:

- Všechny operace čtení provádí pomocí snímek izolace je zámek bez.
- Všechny opakovatelné operace čtení ve výchozím nastavení přebírá sdílené zámky.
- Však pro všechny operace čtení, která podporuje opakovatelné čtení, může uživatel požádat o zámek aktualizace namísto sdílené ho zámku.
Zámek aktualizace je asymetrický zámek, který se používá k zabránění běžné formě vzájemné vzájemné zablokování, ke které dochází, když více transakcí uzamkne prostředky pro potenciální aktualizace později.

Matice kompatibility zámků naleznete v následující tabulce:

| Požadavek \ Uděleno | Žádný | Sdílená | Aktualizace | Exkluzivní |
| --- |:--- |:--- |:--- |:--- |
| Sdílená |Žádný konflikt |Žádný konflikt |Konflikt |Konflikt |
| Aktualizace |Žádný konflikt |Žádný konflikt |Konflikt |Konflikt |
| Exkluzivní |Žádný konflikt |Konflikt |Konflikt |Konflikt |

Argument časového úsporemu v reakreditní kolekce API se používá pro zjišťování vzájemné vzájemné zablokování.
Například dvě transakce (T1 a T2) se pokoušejí číst a aktualizovat K1.
Je možné pro ně zablokování, protože oba skončí s shared lock.
V tomto případě bude časový rozsah jedné nebo obou operací. I tento scénář, zámek aktualizace může zabránit takové zablokování.

## <a name="next-steps"></a>Další kroky

* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Spolehlivá konfigurace správce stavu](service-fabric-reliable-services-configuration.md)
* [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
