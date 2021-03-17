---
title: Transakce a režimy zámků ve spolehlivých kolekcích
description: Azure Service Fabric Reliable State Manager a Reliable Collections – transakce a uzamykání.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 5d2cbb517ea5ca45697cd9124b82e9ef13dd32db
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784338"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakce a režimy zámků ve službě Azure Service Fabric Reliable Collections

## <a name="transaction"></a>Transakce

Transakce je posloupnost operací prováděná jako jediná logická jednotka práce. Vykazuje běžnou [kyselinu](https://en.wikipedia.org/wiki/ACID) (vlastnosti *atomie*, *konzistence*, *izolace*, *odolnosti*) databázových transakcí:

* **Nedělitelnost**: transakce musí být atomická jednotka práce. Jinými slovy, buď dojde k provedení všech úprav dat, nebo žádný z nich není proveden.
* **Konzistence**: po dokončení transakce musí opustit všechna data v konzistentním stavu. Všechny interní datové struktury musí být na konci transakce správné.
* **Izolace**: úpravy provedené souběžnými transakcemi musí být izolované od úprav provedených jinými souběžnými transakcemi. Úroveň izolace, která se používá pro operaci v rámci [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction) , je určena [IReliableState](/dotnet/api/microsoft.servicefabric.data.ireliablestate) , který provádí operaci.
* **Trvanlivost**: po dokončení transakce jsou její účinky trvale v systému zavedeny. Úpravy zůstanou zachované i v případě selhání systému.

### <a name="isolation-levels"></a>Úrovně izolace

Úroveň izolace definuje míru, na kterou musí být transakce izolovaná od úprav provedených ostatními transakcemi.
Ve spolehlivých kolekcích jsou podporovány dvě úrovně izolace:

* **Rerepeated Read**: Určuje, že příkazy nemohou číst data, která byla změněna, ale nebyla dosud potvrzena jinými transakcemi a že žádné jiné transakce nemohou upravovat data, která byla přečtena aktuální transakcí, dokud není dokončena aktuální transakce.
* **Snapshot**: Určuje, že data přečtená jakýmkoli příkazem v transakci jsou přemístěné konzistentní verze dat, která existovala na začátku transakce.
  Transakce může rozpoznat pouze změny dat, které byly potvrzeny před zahájením transakce.
  Změny dat provedené jinými transakcemi po spuštění aktuální transakce nejsou viditelné pro příkazy prováděné v aktuální transakci.
  Efekt je, jako by příkazy v transakci získaly snímek potvrzených dat, který existoval na začátku transakce.
  Snímky jsou konzistentní v rámci spolehlivých kolekcí.

Spolehlivé kolekce automaticky zvolí úroveň izolace, která se má použít pro danou operaci čtení v závislosti na operaci a roli repliky v době vytváření transakce.
Následuje tabulka, která znázorňuje výchozí hodnoty úrovně izolace pro spolehlivé operace Dictionary a Queue.

| Operace \ role | Primární | Sekundární |
| --- |:--- |:--- |
| Čtení jedné entity |Opakované čtení |Snímek |
| Výčet, počet |Snímek |Snímek |

> [!NOTE]
> Běžné příklady operací s jednou entitou `IReliableDictionary.TryGetValueAsync` jsou `IReliableQueue.TryPeekAsync` .
> 

Spolehlivý slovník i spolehlivá fronta podporují *čtení zápisů*.
Jinými slovy, jakýkoli zápis v rámci transakce bude viditelný pro následující čtení, které patří do stejné transakce.

## <a name="locks"></a>Zámky

Ve spolehlivých kolekcích všechny transakce implementují přísné dva zámky fáze: transakce neuvolní zámky, které získaly, dokud transakce nekončí buď přerušením, nebo potvrzením.

Spolehlivý slovník používá uzamykání na úrovni řádků pro všechny operace s jednou entitou.
Spolehlivá fronta se zařadí do souběžnosti pro striktní transakční vlastnost FIFO.
Spolehlivá fronta používá zámky na úrovni operace, takže v jednom okamžiku umožňuje jednu transakci s `TryPeekAsync` a/nebo `TryDequeueAsync` a jednou transakcí `EnqueueAsync` .
Uvědomte si, že pokud chcete zachovat FIFO, pokud v `TryPeekAsync` nebo `TryDequeueAsync` někdy znamená, že je spolehlivá fronta prázdná, taky se uzamkne `EnqueueAsync` .

Operace zápisu vždy berou exkluzivní zámky.
Pro operace čtení závisí uzamykání na několika faktorech:

- Veškerá operace čtení, kterou provedla použití izolace snímku, je bezplatná.
- Všechny opakující se operace čtení ve výchozím nastavení přebírají sdílené zámky.
- Pro jakoukoliv operaci čtení, která podporuje opakované čtení, si ale uživatel může místo sdíleného zámku požádat o zámek aktualizace.
Zámek aktualizace je asymetrický zámek, který slouží k tomu, aby se zabránilo běžnému způsobu zablokování, ke kterému dochází v případě, že více transakcí zamkne prostředky pro potenciální aktualizace později.

Matice o kompatibilitě zámku najdete v následující tabulce:

| Žádost – uděleno | Žádné | Shared | Aktualizace | Vyhrazen |
| --- |:--- |:--- |:--- |:--- |
| Shared |Bez konfliktu |Bez konfliktu |Konflikt |Konflikt |
| Aktualizace |Bez konfliktu |Bez konfliktu |Konflikt |Konflikt |
| Vyhrazen |Bez konfliktu |Konflikt |Konflikt |Konflikt |

Pro detekci zablokování se používá argument Timeout v rozhraních API Reliable Collections.
Například dvě transakce (T1 a T2) se pokoušejí přečíst a aktualizovat K1.
Je možné je zablokovat, protože oba končí sdíleným zámkem.
V takovém případě dojde k vypršení časového limitu jedné nebo obou operací. V tomto scénáři by zámek aktualizace mohl zabránit takovému zablokování.

## <a name="next-steps"></a>Další kroky

* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurace spolehlivého správce stavu](service-fabric-reliable-services-configuration.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
