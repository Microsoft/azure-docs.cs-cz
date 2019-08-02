---
title: Transakce a režimy zámků v Azure Service Fabric Reliable Collections | Microsoft Docs
description: Azure Service Fabric Reliable State Manager a Reliable Collections – transakce a uzamykání.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: atsenthi
ms.openlocfilehash: 8e77e488a3c0a40a714a0e8efffba0a2947454bf
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599330"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakce a režimy zámků ve službě Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transakce
Transakce je posloupnost operací prováděná jako jediná logická jednotka práce.
Transakce musí vykazovat následující vlastnosti KYSELosti. si https://technet.microsoft.com/library/ms190612)
* **Nedělitelnost**: Transakce musí být atomická jednotka práce. Jinými slovy, buď dojde k provedení všech úprav dat, nebo žádný z nich není proveden.
* **Konzistence**: Po dokončení transakce musí opustit všechna data v konzistentním stavu. Všechny interní datové struktury musí být na konci transakce správné.
* **Izolace**: Změny prováděné souběžnými transakcemi musí být izolované od úprav provedených jinými souběžnými transakcemi. Úroveň izolace, která se používá pro operaci v rámci ITransaction, je určena IReliableState, který provádí operaci.
* **Odolnost**: Po dokončení transakce jsou její účinky trvale v systému zavedeny. Úpravy zůstanou zachované i v případě selhání systému.

### <a name="isolation-levels"></a>Úrovně izolace
Úroveň izolace definuje míru, na kterou musí být transakce izolovaná od úprav provedených ostatními transakcemi.
Ve spolehlivých kolekcích jsou podporovány dvě úrovně izolace:

* **Opakované čtení**: Určuje, že příkazy nemohou číst data, která byla upravena, ale nebyla dosud potvrzena jinými transakcemi a které žádné jiné transakce nemohou upravovat data, která byla přečtena aktuální transakcí, dokud není dokončena aktuální transakce. Další podrobnosti najdete v tématu [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Snímek**: Určuje, že data přečtená jakýmkoli příkazem v transakci jsou přemístěné konzistentní verze dat, která existovala na začátku transakce.
  Transakce může rozpoznat pouze změny dat, které byly potvrzeny před zahájením transakce.
  Změny dat provedené jinými transakcemi po spuštění aktuální transakce nejsou viditelné pro příkazy prováděné v aktuální transakci.
  Efekt je, jako by příkazy v transakci získaly snímek potvrzených dat, který existoval na začátku transakce.
  Snímky jsou konzistentní v rámci spolehlivých kolekcí.
  Další podrobnosti najdete v tématu [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Spolehlivé kolekce automaticky zvolí úroveň izolace, která se má použít pro danou operaci čtení v závislosti na operaci a roli repliky v době vytváření transakce.
Následuje tabulka, která znázorňuje výchozí hodnoty úrovně izolace pro spolehlivé operace Dictionary a Queue.

| Operace \ role | Primární | Sekundární |
| --- |:--- |:--- |
| Čtení jedné entity |Opakované čtení |Snímek |
| Výčet, počet |Snímek |Snímek |

> [!NOTE]
> Běžné příklady operací s `IReliableDictionary.TryGetValueAsync` `IReliableQueue.TryPeekAsync`jednou entitou jsou.
> 

Spolehlivý slovník i spolehlivá fronta podporují čtení zápisů.
Jinými slovy, jakýkoli zápis v rámci transakce bude viditelný pro následující čtení, které patří do stejné transakce.

## <a name="locks"></a>Zámky
Ve spolehlivých kolekcích všechny transakce implementují přísné dva zámky fáze: transakce neuvolní zámky, které získaly, dokud transakce nekončí buď přerušením, nebo potvrzením.

Spolehlivý slovník používá uzamykání na úrovni řádků pro všechny operace s jednou entitou.
Spolehlivá fronta se zařadí do souběžnosti pro striktní transakční vlastnost FIFO.
Spolehlivá fronta používá zámky na úrovni operace, `TryPeekAsync` takže `EnqueueAsync` v jednom `TryDequeueAsync` okamžiku umožňuje jednu transakci s a/nebo a jednou transakcí.
Uvědomte si, že pokud chcete zachovat `TryPeekAsync` FIFO `TryDequeueAsync` , pokud v nebo někdy znamená, že je spolehlivá fronta prázdná, `EnqueueAsync`taky se uzamkne.

Operace zápisu vždy berou exkluzivní zámky.
Pro operace čtení závisí uzamykání na několika faktorech.
Všechny operace čtení provedené pomocí izolace snímku jsou bez zámku.
Všechny opakující se operace čtení ve výchozím nastavení přebírají sdílené zámky.
Pro jakoukoliv operaci čtení, která podporuje opakované čtení, si ale uživatel může místo sdíleného zámku požádat o zámek aktualizace.
Zámek aktualizace je asymetrický zámek, který slouží k tomu, aby se zabránilo běžnému způsobu zablokování, ke kterému dochází v případě, že více transakcí zamkne prostředky pro potenciální aktualizace později.

Matice o kompatibilitě zámku najdete v následující tabulce:

| Žádost – uděleno | Žádné | Sdílené | Aktualizace | Vyhrazen |
| --- |:--- |:--- |:--- |:--- |
| Sdílené |Bez konfliktu |Bez konfliktu |Konflikt |Konflikt |
| Aktualizace |Bez konfliktu |Bez konfliktu |Konflikt |Konflikt |
| Vyhrazen |Bez konfliktu |Konflikt |Konflikt |Konflikt |

Pro detekci zablokování se používá argument časového limitu ve spolehlivých kolekcích rozhraní API.
Například dvě transakce (T1 a T2) se pokoušejí přečíst a aktualizovat K1.
Je možné je zablokovat, protože oba končí sdíleným zámkem.
V takovém případě dojde k vypršení časového limitu jedné nebo obou operací.

Tento scénář vzájemného zablokování je skvělým příkladem, jak může zámek aktualizace zabránit zablokování.

## <a name="next-steps"></a>Další postup
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurace spolehlivého správce stavu](service-fabric-reliable-services-configuration.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

