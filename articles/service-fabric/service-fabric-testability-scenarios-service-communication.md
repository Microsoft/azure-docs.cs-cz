---
title: 'Testovatelnost: Servisní komunikace'
description: Komunikace mezi službami je kritickým integračním bodem aplikace Service Fabric. Tento článek popisuje aspekty návrhu a techniky testování.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465560"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Scénáře testovatelnosti service fabric: Komunikace služby
Mikroslužeb a architektury orientované na služby se přirozeně objevují v Azure Service Fabric. V těchto typech distribuovaných architektur komponentizované aplikace mikroslužeb se obvykle skládají z více služeb, které potřebují mluvit mezi sebou. I v těch nejjednodušších případech obvykle máte alespoň bezstavovou webovou službu a stavovou službu úložiště dat, která potřebuje komunikovat.

Komunikace mezi službami je kritickým integračním bodem aplikace, protože každá služba zpřístupňuje vzdálené rozhraní API jiným službám. Práce se sadou hranic rozhraní API, která zahrnuje vstupně-va obecně vyžaduje určitou péči, s dobrým množstvím testování a ověřování.

Existuje mnoho aspektů, které je třeba provést, když jsou tyto hranice služeb propojeny v distribuovaném systému:

* *Transportní protokol*. Použijete protokol HTTP pro zvýšení interoperability nebo vlastní binární protokol pro maximální propustnost?
* *Při zpracování chyb*. Jak bude zpracována trvalá a přechodná chyba? Co se stane, když se služba přesune do jiného uzlu?
* *Časové osy a latence*. V vícestupňové aplikace, jak bude každá vrstva služby zpracovat latence prostřednictvím zásobníku a uživateli?

Ať už používáte jednu z předdefinovaných součástí komunikace služby poskytované Service Fabric nebo vytvořit vlastní, testování interakcí mezi službami je důležité pro zajištění odolnosti proti chybám ve vaší aplikaci.

## <a name="prepare-for-services-to-move"></a>Připravte se na přesun služeb
Instance služby se mohou v průběhu času pohybovat. To platí zejména v případě, že jsou konfigurovány s metriky zatížení pro vlastní přizpůsobené optimální vyrovnávání prostředků. Service Fabric přesune instance služby maximalizovat jejich dostupnost i během upgradů, převzetí služeb při selhání, horizontální navýšení kapacity a další situace, ke kterým dochází po dobu životnosti distribuovaného systému.

Jak se služby pohybují v clusteru, vaši klienti a další služby by měly být připraveny ke zpracování dvou scénářů při rozhovoru se službou:

* Instance služby nebo replika oddílu byla přesunuta od posledního rozhovoru s ní. Toto je normální část životního cyklu služby a je třeba očekávat, že dojde během životnosti vaší aplikace.
* Instance služby nebo replika oddílu je v procesu přesouvání. Přestože převzetí služeb při selhání služby z jednoho uzlu do druhého dochází velmi rychle v Service Fabric, může dojít ke zpoždění dostupnosti, pokud komunikační součást služby je pomalé spuštění.

Řádné zpracování těchto scénářů je důležité pro systém s hladkým chodem. Chcete-li tak učinit, mějte na paměti, že:

* Každá služba, ke které lze připojit, má *adresu,* na které naslouchá (například HTTP nebo WebSockets). Když se přesune instance služby nebo oddíl, změní se její koncový bod adresy. (Přesune se do jiného uzlu s jinou IP adresou.) Pokud používáte vestavěné komunikační součásti, budou zpracovávat opětovné řešení adres služeb za vás.
* Může dojít k dočasnému zvýšení latence služby, protože instance služby znovu spustí svůj naslouchací proces. To závisí na tom, jak rychle služba otevře naslouchací proces po přesunutí instance služby.
* Všechna existující připojení je třeba po otevření služby v novém uzlu zavřít a znovu otevřít. Řádné vypnutí nebo restartování uzlu umožňuje řádné vypnutí existujících připojení.

### <a name="test-it-move-service-instances"></a>Testování: Přesunutí instancí služby
Pomocí nástrojů service fabric je testovatelnost, můžete vytvořit testovací scénář k testování těchto situací různými způsoby:

1. Přesunutí primární repliky stavové služby
   
    Primární repliku oddílu stavové služby lze přesunout z libovolného počtu důvodů. Pomocí tohoto cíle primární repliky konkrétní oddíl zobrazíte, jak vaše služby reagují na přesunutí velmi řízeným způsobem.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zastavit uzel.
   
    Když je uzel zastaven, Service Fabric přesune všechny instance služby nebo oddíly, které byly na tomto uzlu do jednoho z dalších dostupných uzlů v clusteru. Pomocí této funkce můžete otestovat situaci, kdy dojde ke ztrátě uzlu z clusteru a všechny instance služby a repliky v tomto uzlu se musí přesunout.
   
    Uzel můžete zastavit pomocí rutiny PowerShell **Stop-ServiceFabricNode:**
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Udržovat dostupnost služby
Jako platforma je Service Fabric navržen tak, aby poskytoval vysokou dostupnost vašich služeb. V extrémních případech však základní problémy s infrastrukturou stále mohou způsobit nedostupnost. Je důležité otestovat pro tyto scénáře, příliš.

Stavové služby používají systém založený na kvoru k replikaci stavu pro vysokou dostupnost. To znamená, že kvorum replik musí být k dispozici k provedení operací zápisu. Ve výjimečných případech, jako je například rozsáhlé selhání hardwaru, kvorum replik nemusí být k dispozici. V těchto případech nebudete moci provádět operace zápisu, ale stále budete moci provádět operace čtení.

### <a name="test-it-write-operation-unavailability"></a>Otestovat: Nedostupnost operace zápisu
Pomocí nástrojů testovatelnosti v Service Fabric, můžete injekčně chybu, která indukuje ztrátu kvora jako test. I když takový scénář je vzácné, je důležité, aby klienti a služby, které jsou závislé na stavové služby jsou připraveni zpracovat situace, kdy nemohou psát požadavky na něj. Je také důležité, aby stavová služba sama o sobě si je vědoma této možnosti a může řádně komunikovat s volajícími.

Ztrátu kvora můžete vyvolat pomocí rutiny PowerShell **Invoke-ServiceFabricPartitionQuorumLoss:**

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

V tomto příkladu `QuorumLossMode` `QuorumReplicas` jsme nastavili na označení, že chceme vyvolat ztrátu kvora bez zanesení všech replik. Tímto způsobem jsou stále možné operace čtení. Chcete-li otestovat scénář, kdy není k dispozici `AllReplicas`celý oddíl, můžete tento přepínač nastavit na .

## <a name="next-steps"></a>Další kroky
[Další informace o akcích testovatelnosti](service-fabric-testability-actions.md)

[Další informace o scénářích testovatelnosti](service-fabric-testability-scenarios.md)

