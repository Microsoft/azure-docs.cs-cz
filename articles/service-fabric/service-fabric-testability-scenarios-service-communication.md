---
title: 'Testovatelnost: Komunikace | Dokumentace Microsoftu'
description: Služba služba komunikace je bod integrace kritické aplikace Service Fabric. Tento článek popisuje aspekty návrhu a techniky pro testování.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665742"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric scénářů testovatelnosti: Komunikace služeb
Mikroslužby a styly architektury orientované na služby plochy přirozeně v Azure Service Fabric. V těchto typech distribuované architektury se skládají z více služeb, které potřebují komunikovat mezi sebou obvykle komponentní vyladěných aplikací mikroslužeb. V případech i nejjednodušší obecně byste mít alespoň bezstavovou webovou službu a službu úložiště stavová data, která potřebují komunikovat.

Služba služba komunikace je bod integrace kritické aplikace, protože každá služba zpřístupňuje vzdálené rozhraní API k jiným službám. Práce s sadu rozhraní API hranice, která obvykle zahrnuje vstupně-výstupní operace vyžaduje některé péče díky vhodné množství testování a ověřování.

Existuje mnoho aspekty při tyto hranice služeb představují kabelová společně v distribuovaném systému:

* *Přenosový protokol*. Budete používat protokol HTTP pro zvýšení spolupráce, nebo vlastní binární protokol pro maximální propustnost?
* *Zpracování chyb*. Jak se zpracovává trvalé a přechodné chyby? Co se stane, když se služba přesune na jiný uzel?
* *Vypršení časových limitů a zpoždění*. V aplikacích s více vrstvami jak každou vrstvu služby zpracuje latence prostřednictvím zásobníku a uživateli?

Je důležité pro zajištění odolnosti proti chybám ve vaší aplikaci, zda použijete jeden z komponenty komunikace integrovaná služba Service Fabric poskytuje nebo vlastní, testování interakce mezi službami sestavení.

## <a name="prepare-for-services-to-move"></a>Příprava na přesun služeb
Instance služby může pohybovat v čase. To platí zejména pokud jsou konfigurovány s metrikami zatížení vyvažování vlastní přizpůsobené optimální prostředků. Service Fabric přesune instance vaší služby k maximalizaci jejich dostupnost i během upgradu, převzetí služeb při selhání, horizontální navýšení kapacity a dalších situacích, ke kterým dochází za dobu existence distribuovaného systému.

Jak služby pohybovat v clusteru, klienty a dalších služeb by měli být připraveni zpracování dva scénáře, když se hovoří službě:

* Repliku instance nebo oddílu služby se přesunul od posledního mluvil jste k němu. To je normální součástí životního cyklu služeb a je třeba očekávat, která se provede po celou dobu životnosti vaší aplikace.
* Služba instance nebo oddíl repliky Probíhá přesun. I když v Service Fabric, dojde k velmi rychlé převzetí služeb při selhání služby z jednoho uzlu do jiného, může docházet k prodlevám v dostupnosti při pomalém spustit komponentu komunikace služby.

Řádně zpracování těchto scénářů je důležité pro protokol smooth spuštění systému. Uděláte to tak, mějte na paměti, že:

* Má každá služba, která se dá připojit *adresu* , která naslouchá na (například HTTP nebo protokoly Websocket). Pokud se pohybuje, instance služby nebo oddíl, změní svůj koncový bod adresy. (Přesunu do jiného uzlu s jinou IP adresu.) Pokud používáte integrované komunikační součásti, budou zpracovávat znovu překladu adres služby za vás.
* Může být častěji docházet k latenci služby jako spuštění instance služby do jeho naslouchací proces znovu. To závisí na tom, jak rychle službu otevře naslouchací proces po přesunutí instance služby.
* Existující připojení je nutné zavřít a znovu otevřete po otevření služby na nový uzel. Uzel řádné vypnutí nebo restartování umožňuje dobu pro stávající připojení vynuceně vypnout.

### <a name="test-it-move-service-instances"></a>Otestujte ji: Přesunutí instance služby
Pomocí nástroje Service Fabric testovatelnosti můžete upravit scénář testování k testování těchto situacích různými způsoby:

1. Přesuňte do stavové služby primární repliky.
   
    Primární repliky oddílu stavové služby je možné přesunout pro z nejrůznějších důvodů. Použijte cílit na konkrétní oddíl a zobrazíte tak, jak se vaše služby reagovat na přesunutí velmi kontrolovaně primární replice.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zastavení uzlu.
   
    Při zastavení uzlu přesune všechny instance služby nebo oddíly, které byly na tomto uzlu do jednoho z jiných dostupných uzlů v clusteru Service Fabric. To lze použijte k testování situaci, kdy dojde ke ztrátě z clusteru a všechny instance služby uzlu a repliky na tomto uzlu je nutné přesunout.
   
    Uzel můžete zastavit pomocí Powershellu **Stop-ServiceFabricNode** rutiny:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Zachovat dostupnost služeb
Jako platforma pro Service Fabric je navržená pro zajištění vysoké dostupnosti vašich služeb. Ale v extrémních případech se může způsobit problémy infrastruktury stále nedostupnosti. Je důležité, otestovat pro tyto scénáře příliš.

Stavové služby pomocí systému kvora replikovat stav pro zajištění vysoké dostupnosti. To znamená, že kvorum replik musí být k dispozici k provedení operace zápisu. Ve výjimečných případech, jako je například selhání rozšířených hardwaru nemusí být k dispozici kvorum replik. V těchto případech nebudete mít k provádění operací zápisu, ale stále budete moci provést operace čtení.

### <a name="test-it-write-operation-unavailability"></a>Otestujte ji: Nedostupnost operace zápisu
Pomocí nástrojů testovatelnosti v Service Fabric, můžete vložit selhání, který indukuje ztráty kvora jako test. I když se takový scénář je vzácné, je důležité, klientů a služeb, které závisí na stavové služby jsou připraveny pro řešení situací, ve kterém se nemůže provádět požadavky na zápis do něj. Je také důležité, že samotné stavové služby této možnosti zohledňuje a řádně komunikovat ho do volající.

Můžete zahájit ztráty kvora pomocí Powershellu **Invoke-ServiceFabricPartitionQuorumLoss** rutiny:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

V tomto příkladu jsme nastavili `QuorumLossMode` k `QuorumReplicas` k označení, že má zahájit ztráty kvora bez nutnosti převádět mimo provoz všechny repliky. Tímto způsobem operace čtení jsou stále možné. K otestování scénářů, kde je k dispozici celý oddíl, můžete nastavit na tento přepínač `AllReplicas`.

## <a name="next-steps"></a>Další postup
[Další informace o akcemi testovatelnosti](service-fabric-testability-actions.md)

[Další informace o scénářů testovatelnosti](service-fabric-testability-scenarios.md)

