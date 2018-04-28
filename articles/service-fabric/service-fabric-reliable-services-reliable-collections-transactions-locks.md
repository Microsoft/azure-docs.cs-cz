---
title: Spolehlivé kolekce prostředků infrastruktury služby transakce a uzamčení režimy v Azure | Microsoft Docs
description: Azure Service Fabric spolehlivé stavu Manager a spolehlivé kolekce transakce a uzamyká.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: f9d431d94a6df9636a48e1b2aaa59aaa576e2dc3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakce a uzamčení režimy v Azure Service Fabric spolehlivé kolekce

## <a name="transaction"></a>Transakce
Transakce je posloupnost operací provést jako jednu logickou jednotku práce.
Transakce musí mít květy ACID následující vlastnosti. (viz: https://technet.microsoft.com/library/ms190612)
* **Nedělitelnost**: transakce musí být atomické jednotky práce. Jinými slovy se provádí jeho změny dat, nebo žádná z nich probíhá.
* **Konzistence**: Po dokončení transakce musí zůstat všechna data v konzistentním stavu. Všechny interních datových strukturách musí být správné na konci transakce.
* **Izolace**: úpravy provedené souběžných transakcí musí být izolované od změny provedené při dalších souběžných transakcí. Úroveň izolace, používat pro operace v rámci ITransaction je určen podle IReliableState provádění této operace.
* **Odolnost**: Po dokončení transakce jeho účinky jsou trvale zavedené v systému. Změny zachovat i v případě selhání systému.

### <a name="isolation-levels"></a>Úrovně izolace
Úroveň izolace definuje úroveň, do které musí být transakce izolované od změny provedené při dalších transakcí.
Existují dvě úrovně izolace, které jsou podporovány v spolehlivé kolekce:

* **Opakovatelných čtení**: Určuje, že příkazy nelze číst data, která byla upravena, ale ještě nebyly potvrzeny podle dalších transakcí a že žádné další transakce můžete upravit data, která byla přečtena aktuální transakce až do aktuální transakce dokončení. Další podrobnosti najdete v tématu [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snímek**: Určuje, že data načtená žádné příkazem v transakci je stavu transakční konzistence verzi data, která byla na začátku transakce.
  Transakce poznáte pouze změny dat, které nebyly potvrzeny před zahájením transakce.
  Změny dat provedené dalších transakcí po začátku aktuální transakce nejsou viditelné pro příkazy v aktuální transakci.
  Výsledný efekt působí, jako kdyby příkazy v transakci získat snímek potvrdit dat tak, jak byly na začátku transakce.
  Snímky jsou konzistentní v rámci spolehlivé kolekce.
  Další podrobnosti najdete v tématu [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Spolehlivé kolekce automaticky zvolte úroveň izolace použitou pro danou operaci čtení v závislosti na operaci a role repliky v době vytvoření transakce.
Následuje tabulka, která znázorňuje úrovni výchozí nastavení izolace pro operace spolehlivé slovník a fronty.

| Operace \ Role | Primární | Sekundární |
| --- |:--- |:--- |
| Čtení jedné Entity |Opakovatelných pro čtení |Snímek |
| Výčet, počet |Snímek |Snímek |

> [!NOTE]
> Běžných příkladů pro jednu entitu operace jsou `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Slovníku spolehlivé a spolehlivé fronty podporovat vaše zapíše pro čtení.
Jinými slovy všechny zápisu v rámci transakce budou viditelné pro následující pro čtení, který patří do stejné transakci.

## <a name="locks"></a>Zámky
V kolekcích spolehlivé, všechny transakce implementace přísných dvě fáze uzamčení: transakce neuvolní zámky získala dokud neskončí transakce s přerušení nebo potvrzení.

Spolehlivé slovník používá uzamčení pro všechny operace jedné entity na úrovni řádků.
Spolehlivé fronty ztrátách souběžnosti pro striktní transakční FIFO vlastnost.
Spolehlivé fronty používá úrovně zámky operace povolení jednu transakci s `TryPeekAsync` nebo `TryDequeueAsync` a jednu transakci s `EnqueueAsync` najednou.
Všimněte si, že chcete zachovat FIFO, pokud `TryPeekAsync` nebo `TryDequeueAsync` někdy zjistí, že spolehlivé fronty je prázdný, budou také zamknout `EnqueueAsync`.

Zápisu operace vždy provést výhradní zámky.
Pro operace čtení blokovací závisí na několika faktorech.
Všechny operace čtení provádí pomocí izolace snímku je zamykání.
Všechny operace čtení opakovatelných ve výchozím nastavení trvá sdílené zámky.
Pro všechny operace čtení, podporující opakovatelných pro čtení, však může uživatel požádat o aktualizační zámek místo Sdílený zámek.
Aktualizační zámek je zámek asymetrické používá při prevenci běžnou zablokování, který nastane, když více transakcí zamknutí prostředků pro potenciální aktualizace později.

Matice kompatibility zámku naleznete v následující tabulce:

| Žádosti o \ udělena | Žádný | Shared | Aktualizace | Exkluzivní |
| --- |:--- |:--- |:--- |:--- |
| Shared |Ke konfliktu |Ke konfliktu |Konflikt |Konflikt |
| Aktualizace |Ke konfliktu |Ke konfliktu |Konflikt |Konflikt |
| Exkluzivní |Ke konfliktu |Konflikt |Konflikt |Konflikt |

Časový limit argument spolehlivé rozhraní API kolekce se používá pro zjišťování zablokování.
Dvě transakce (T1 a T2) se například pokoušíte číst a aktualizovat K1.
Je možné pro ně k zablokování, protože obě ukončení tím, že mají Sdílený zámek.
Jedna nebo obě operací v tomto případě bude časový limit.

Tento scénář vzájemného zablokování je skvělým příkladem jak aktualizační zámek může zabránit zablokování.

## <a name="next-steps"></a>Další postup
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Configuration Manager spolehlivé stavu](service-fabric-reliable-services-configuration.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

