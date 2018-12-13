---
title: Transakce a režimy zamykání v Azure Service Fabric Reliable Collections | Dokumentace Microsoftu
description: Azure Service Fabric Reliable State Manager a Reliable Collections transakcích a zamykání.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: twhitney
ms.openlocfilehash: a7e2bfba736e3b6cee738d5a2b5283f51f60d7c5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185388"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakce a režimy zamykání v Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transakce
Transakce je sekvence operací provedených jako jednu logickou jednotku práce.
Transakce musí mít následující vlastnosti ACID. (viz: https://technet.microsoft.com/library/ms190612)
* **Atomicitu**: Transakce musí být atomickou jednotku práce. Jinými slovy jsou prováděny všechny jeho změny dat, nebo žádná z nich se provádí.
* **Konzistence**: Po dokončení transakce ponechte všechna data v konzistentním stavu. Všechny interní datové struktury, musí být správná na konci transakce.
* **Izolace**: Změny provedené ve souběžných transakcí musí být izolované od změny provedené ve všech ostatních souběžných transakcí. Určuje úroveň izolace, používá pro operace v rámci ITransaction IReliableState provádění této operace.
* **Odolnost**: Po dokončení transakce jeho efekty jsou trvale zavedené v systému. Změny zachovat i v případě selhání systému.

### <a name="isolation-levels"></a>Úrovně izolace
Úroveň izolace určuje míru, do které musí být transakce izolované od změny provedené jinými transakcemi.
Existují dvě úrovně izolace, které jsou podporovány v Reliable Collections:

* **Čtení opakovatelné**: Určuje, že příkazy nelze číst data, která byla upravena, ale ještě nebyla potvrzena dalšími transakcemi a že žádné další transakce můžete upravit data, která byla načtena prostřednictvím aktuální transakce, dokud se nedokončí aktuální transakce. Další podrobnosti najdete v tématu [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snímek**: Určuje, že data číst libovolný příkaz v rámci transakce je transakčně konzistentní verzi dat, která byla uložena na začátku transakce.
  Transakce můžete rozpoznat pouze změny dat, které byly potvrzeny před zahájení transakce.
  Data změny provedené jinými transakcemi po začátku aktuální transakce nejsou viditelné pro příkazy spouští v aktuální transakci.
  Efekt je, jako kdyby příkazů v transakci získat snímek potvrzená data tak, jak byly na začátku transakce.
  Snímky jsou konzistentní vzhledem k aplikacím v rámci spolehlivých kolekcí.
  Další podrobnosti najdete v tématu [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Reliable Collections automaticky vybrat úroveň izolace použitou pro danou operaci čtení v závislosti na operaci a role repliky v době vytvoření transakce.
Tady je tabulka znázorňující úrovně výchozí nastavení izolace pro operace spolehlivého slovníku a fronty.

| Operace \ Role | Primární | Sekundární |
| --- |:--- |:--- |
| Čtení jedné Entity |Opakovatelné pro čtení |Snímek |
| Výčet, počet |Snímek |Snímek |

> [!NOTE]
> Běžné příklady pro jednu entitu operace `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Spolehlivého slovníku a spolehlivá fronta podporovat vaše zapíše pro čtení.
Jinými slovy všechny zápisu v rámci transakce se nebude zobrazovat na následující pro čtení, který patří do jedné transakce.

## <a name="locks"></a>Zámky
V Reliable Collections implementovat všechny transakce přísné dvě fáze uzamčení: transakce neuvolní zámky získala až do doby ukončení transakce s přerušení nebo potvrzení.

Spolehlivého slovníku používá uzamčení pro všechny operace jednu entitu na úrovni řádků.
Spolehlivá fronta obchoduje vypnout souběžnosti striktní transakční FIFO vlastnosti.
Spolehlivá fronta používá úroveň zámky operace povolení jedna transakce s `TryPeekAsync` a/nebo `TryDequeueAsync` a jedna transakce s `EnqueueAsync` najednou.
Všimněte si, že chcete zachovat FIFO, pokud `TryPeekAsync` nebo `TryDequeueAsync` stále dodržuje, spolehlivá fronta je prázdná, bude také uzamknout `EnqueueAsync`.

Zápisu operace vždy provést exkluzivní zámky.
Pro operace čtení uzamčení závisí na několika faktorech.
Všechny operace čtení provést pomocí izolace snímku se bez zámku.
Všechny operace čtení opakovatelné ve výchozím nastavení používá sdílené zámky.
Ale pro všechny operace čtení, podporující opakovatelné pro čtení, uživatel požádat o aktualizační zámek místo zámku Shared.
Aktualizační zámek je asymetrického uzamčen, použít k zabránění tedy v běžné formě o zablokování, která nastane, pokud více transakcí zamknutí prostředků pro potenciální aktualizace později.

Matice kompatibility zámek najdete v následující tabulce:

| Požádat o \ udělena | Žádný | Shared | Aktualizace | Exkluzivní |
| --- |:--- |:--- |:--- |:--- |
| Shared |Žádný konflikt. |Žádný konflikt. |Konflikt |Konflikt |
| Aktualizace |Žádný konflikt. |Žádný konflikt. |Konflikt |Konflikt |
| Exkluzivní |Žádný konflikt. |Konflikt |Konflikt |Konflikt |

Argument vypršení časového limitu v rozhraní API pro spolehlivé kolekce slouží k rozpoznávání zablokování.
Například dvě transakce (T1 a T2) jsou pokusu o čtení a aktualizaci K1.
Je možné pro ně k zablokování, protože oba skončit s Sdílený zámek.
V tomto případě jeden nebo oba z operací vyprší časový limit.

Tento scénář zablokování je skvělé příklad, jak můžete aktualizační zámek zabránit zablokování.

## <a name="next-steps"></a>Další postup
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurace Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

