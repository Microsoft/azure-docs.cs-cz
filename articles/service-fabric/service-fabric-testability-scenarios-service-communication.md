---
title: "Testovatelnosti: Komunikace služby | Microsoft Docs"
description: "Komunikace služby služby je důležité integrační bod aplikace Service Fabric. Tento článek popisuje aspekty návrhu a testování techniky."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c87b5d82b6eef2b1d28a3280cc2fa07c28084f90
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric testovatelnosti scénáře: komunikace služby
Prostor orientované na služby architektury styly přirozeně v Azure Service Fabric a Mikroslužeb. V těchto typech distribuované architektury se komponentizované mikroslužbu aplikace obvykle skládají z více služeb, které je potřeba komunikovat. V případech i nejjednodušší obecně mít alespoň bezstavové webová služba a služba úložiště stavová data, která musí komunikovat.

Komunikace Service-to-service je důležité integrační bod aplikace, protože každá služba zpřístupní rozhraní API pro vzdálené k jiným službám. Práce s sadu rozhraní API hranice, která zahrnuje vstupně-výstupních operací obvykle vyžaduje některé pozor, s dobrou množstvím testování a ověření.

Existuje řada informace týkající se při tyto služby hranice představují kabelová dohromady v distribuovaného systému:

* *Přenosový protokol*. Použijete pro maximální propustnost HTTP pro vyšší interoperabilitu nebo vlastní binární protokol?
* *Zpracování chyb*. Jak se budou trvalý a přechodné chyby zpracovávat? Co se stane, když služba přesune do jiného uzlu?
* *Vypršení časových limitů a latence*. V aplikacích možných jak budou jednotlivé úrovně služby zpracovávat latence prostřednictvím zásobníku a pro uživatele?

Jestli používáte jedna z komponent komunikace integrovanou službu poskytované Service Fabric nebo vytvoříte vlastní testování interakce mezi vaší služby je důležité zajistit odolnost proti chybám v aplikaci.

## <a name="prepare-for-services-to-move"></a>Příprava pro služby pro přesun
Instance služby může pohybovat v čase. To platí hlavně když jsou nakonfigurované s vlastní přizpůsobit optimální prostředků Vyrovnávání zatížení metriky. Service Fabric přesune vaší instance služby maximalizovat jejich dostupnost i během upgradu, převzetí služeb při selhání, Škálováním na více systémů a jiné situace, ke kterým došlo během životního cyklu distribuovaného systému.

Jako služby pohyb v clusteru, klienty a další služby by měl připravit zpracování dva scénáře, pokud se obraťte se na službu:

* Služba instance nebo oddíl repliky se přesunul od doby poslední mluvili k němu. To je normální součást životního cyklu služby a je třeba provést po dobu životnosti aplikace očekávat.
* Služba instance nebo oddíl repliky se přesouvá. I když v Service Fabric, dojde k velmi rychle převzetí služeb při selhání služby z jednoho uzlu do druhého, může uběhnout v dostupnosti při pomalém spustit komponentu komunikace služby.

Pohodlné zpracování těchto scénářů je důležité pro protokol smooth spuštění systému. Uděláte to tak, mějte na paměti, že:

* Každý služba, která může být připojen k má *adresu* která naslouchá na (například HTTP nebo objekty WebSockets). Pokud se přesune instance služby nebo oddíl, změní jeho adresa koncového bodu. (Přesune do jiného uzlu s jinou IP adresu.) Pokud používáte integrované komunikační součásti, že bude zpracovávat znovu řešení adresy služby pro vás.
* Může být dočasné zvýšení latence služby, jako je služba spuštěna instance si jeho naslouchací proces znovu. To závisí na tom, jak rychle službu otevře naslouchací proces po přesunutí instance služby.
* Všech existujících připojení je nutné zavřít a znovu otevřít, když se otevře okno služby na nový uzel. Uzel řádné vypnutí nebo restartování umožňuje dobu pro existující připojení korektně vypnout.

### <a name="test-it-move-service-instances"></a>Test se: přesunout instance služby
Pomocí nástrojů testovatelnosti Service Fabric, můžete vytvořit testovací scénář otestovat těchto situacích různými způsoby:

1. Přesuňte stavové služby primární repliky.
   
    Primární repliky oddílu stavové služby se dají přesunout pro libovolný počet důvodů. Použijte na cílové primární repliky na konkrétní oddíl zobrazíte, jak se vaše služby reagovat na přesunutí velmi kontrolovaně.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zastavení uzlu.
   
    Pokud uzel je zastavená, Service Fabric přesune všechny instance služby nebo oddíly, které byly na tomto uzlu s jedním z jiných dostupných uzlů v clusteru. Použijte k testování situaci, kdy dojde ke ztrátě z clusteru a všechny instance služby uzlu a repliky na tomto uzlu je nutné přesunout.
   
    Uzel můžete zastavit pomocí prostředí PowerShell **Stop-ServiceFabricNode** rutiny:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Zachovat dostupnost služeb
Jako platformu Service Fabric slouží k zajištění vysoké dostupnosti služeb. Ale ve výjimečných případech může způsobit problémy s základní infrastrukturou stále nedostupnosti. Je důležité k testování pro tyto scénáře příliš.

Stavové služby pomocí systému kvora se replikovat stav pro vysokou dostupnost. To znamená, že kvorum repliky musí být k dispozici k provedení operace zápisu. Ve výjimečných případech, například k poruše hardwaru rozšířeným nemusí být k dispozici kvorum repliky. V těchto případech nebudete moci provádět operace zápisu, ale bude stále možné provádět operace čtení.

### <a name="test-it-write-operation-unavailability"></a>Test se: nedostupnosti operace zápisu
Pomocí nástroje testovatelnosti v Service Fabric můžete vložit chybu, která indukuje ztrátě kvora jako testu. I když takové situaci je taková situace vzácná, je důležité, aby klienti a služby, které závisí na stavové služby připravené pro řešení situací, kdy nelze provádění požadavků na zápis do něj. Je také důležité stavové služby, samotné si je vědoma tuto možnost a můžete řádně sdělit pro volající.

Pomocí prostředí PowerShell může být nutné ztrátě kvora **Invoke-ServiceFabricPartitionQuorumLoss** rutiny:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

V tomto příkladu jsme nastavený `QuorumLossMode` k `QuorumReplicas` indikující, že má vyvolat ztrátě kvora bez nutnosti převádět dolů všechny repliky. Tímto způsobem operace čtení jsou stále možné. K testování scénář, kde je k dispozici celý oddíl, můžete nastavit na tento přepínač `AllReplicas`.

## <a name="next-steps"></a>Další postup
[Další informace o akcích testovatelnosti](service-fabric-testability-actions.md)

[Další informace o scénářích testovatelnosti](service-fabric-testability-scenarios.md)

