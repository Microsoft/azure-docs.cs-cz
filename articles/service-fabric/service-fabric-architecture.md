---
title: Architektura služby Azure Service Fabric | Microsoft Docs
description: Service Fabric je platforma distribuovaných systémů sloužící k vytvoření škálovatelný, spolehlivý a snadno spravovat aplikace pro cloud. Tento článek popisuje architekturu Service Fabric.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: 5e69d4b09261c90fd3c33e60645fe484b816e369
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209966"
---
# <a name="service-fabric-architecture"></a>Architektura Service Fabric
Service Fabric je vytvořené s vrstvami subsystémy. Tyto subsystémy umožňují psaní aplikací, které jsou:

* Vysoce dostupný
* Škálovatelné
* Spravovat
* Možností intenzivního testování

Následující diagram znázorňuje hlavní subsystémy služby prostředků infrastruktury.

![Diagram architektury Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

V distribuované systému je zásadní možnost k bezpečné komunikaci mezi uzly v clusteru. Na bázi zásobníku je subsystém přenos, který poskytuje zabezpečenou komunikaci mezi uzly. Výše přenosu je subsystému subsystému federace, který clusterů různých uzlech do jedné entity (s názvem clustery), aby mohli Service Fabric rozpoznala chyby, proveďte vedoucí volba a poskytují konzistentní směrování. Subsystém spolehlivost jako vrstva nad subsystém federace je zodpovědná za spolehlivost služby Service Fabric prostřednictvím mechanismy, jako je replikace, správy prostředků a převzetí služeb při selhání. Subsystém federační také základem subsystém hostování a aktivace, která spravuje životní cyklus aplikace v jednom uzlu. Subsystém management spravuje životní cyklus aplikací a služeb. Subsystém testovatelnosti pomáhá vývojářům aplikací testování jejich služeb prostřednictvím simulované chyb před a po nasazení aplikací a služeb do produkčního prostředí. Service Fabric nabízí možnost řešení umístění služeb pomocí jeho komunikačního subsystému. Programovací modely aplikace zpřístupněny vývojářům jsou vrstva nad tyto subsystémy spolu s aplikací modelu, který má povolení nástrojů.

## <a name="transport-subsystem"></a>Subsystém přenosu
Subsystém přenosu implementuje point-to-point datagram komunikační kanál. Tento kanál se používá pro komunikaci v rámci clusterů service fabric a komunikaci mezi service fabric cluster a klienty. Podporuje jednosměrné a vzory komunikace požadavku a odpovědi, které poskytuje základ pro implementaci všesměrového a vícesměrového vysílání ve vrstvě federace. Subsystém přenosu zabezpečuje komunikaci pomocí X509 certifikáty nebo zabezpečení systému Windows. Tento subsystém používá se interně pomocí Service Fabric a není přímo přístupný pro vývojáře pro programování aplikací.

## <a name="federation-subsystem"></a>Subsystém Federation
Chcete-li důvodu o sada uzlů v distribuované systému, musíte mít konzistentní zobrazení v systému. Subsystém federační používá primitiv komunikace poskytované subsystém přenosu a spojí do jediného jednotná clusteru, který můžete důvodu o různých uzlech. Poskytuje primitiv distribuovaných systémů vyžaduje další subsystémy - detekce chyb, vedoucí volba a konzistentní směrování. Subsystém federace je postavená na distribuované zatřiďovacích tabulkách tokenu mezerami 128-bit. Subsystém vytvoří kruhová topologie přes uzly s každý uzel v řetězci se podmnožinu tokenu místo přidělené vlastnictví. Pro zjišťování selhání vrstvu používá mechanismus "pronájmu" na základě prezenční signálu a arbitrážního. Subsystém federační také zaručuje prostřednictvím komplikované spojení a odeslání protokolů, které existuje jenom jeden vlastník token kdykoli. To poskytuje vedoucí volba a konzistentní směrování záruky.

## <a name="reliability-subsystem"></a>Spolehlivost subsystému
Subsystém spolehlivost poskytuje mechanismus, který chcete nastavit vysokou dostupnost prostřednictvím stav služby Service Fabric *Replikátor*, *Failover Manager*, a *Resource Balancer*.

* Replikátor zajišťuje změny stavu v replice primární služba bude automaticky replikovat na sekundární repliky zachování konzistence mezi primární a sekundární repliky v sadě replik služby. Replikátor zodpovídá za správu kvora mezi repliky sady replik. Komunikuje se službou jednotky převzetí služeb při selhání se získat seznam operací k replikaci, a poskytne mu agent Rekonfigurace konfiguraci sady replik. Tato konfigurace označuje replik, které operace se musí replikovat. Service Fabric nabízí výchozí Replikátor, názvem Replikátor Fabric, který můžete použít rozhraním API programovací model pro vysoce dostupné a spolehlivé, zkontrolujte stav služby.
* Správce převzetí služeb při selhání zajistí, že při uzly jsou přidat nebo odebrat z clusteru, zatížení automaticky rozloží mezi uzly k dispozici. Pokud uzel v clusteru selže, bude cluster automaticky překonfigurujte repliky služby údržbu dostupnosti.
* Resource Manager umístí repliky služby napříč domény selhání v clusteru a zajišťuje, aby všechny jednotky převzetí služeb při selhání provozu. Prostředky služby Správce prostředků také vyrovnává mezi základní sdílený fond uzlů clusteru k dosažení distribuce optimální uniform zatížení.

## <a name="management-subsystem"></a>Subsystém správy
Subsystém správu poskytuje služby začátku do konce a správa životního cyklu aplikací. Rutiny prostředí PowerShell a rozhraní API pro správu umožňují zřizovat, nasazení, opravit, upgradovat a zrušte zřizování aplikace bez ztráty dostupnosti. Subsystém správy to provádí pomocí následujících služeb.

* **Správce clusteru**: Toto je primární služba, která komunikuje pomocí převzetí služeb při selhání správce z spolehlivost umístit aplikace na uzlech podle omezení umístění služby. Správce prostředků v subsystému převzetí služeb při selhání zajistí omezení nikdy přerušená. Správce clusteru spravuje životní cyklus aplikace z zřízení zrušit. Integruje se s správce stavu zajistit, že dostupnosti aplikace není během upgradu ke ztrátě z hlediska sémantického stavu.
* **Správce stavu**: Tato služba umožňuje sledování stavu aplikací, služeb a entity clusteru. Informace o stavu, který je pak agregovat do úložiště centralizované stavu mohou zasílat zprávy o entitami clusteru (jako je například uzlů, oddílů služby a repliky). Tato informace o stavu poskytuje snímek celkové v daném okamžiku stavu služby a uzly distribuované ve více uzlech v clusteru, umožňuje provést všechny potřebné opravné akce. Dotaz stavu, že rozhraní API umožňují dotazování události stavu hlášené pro subsystém stavu. Dotaz stavu, které rozhraní API vrací nezpracovaná stavu data uložená v úložišti health nebo agregované, interpretovat data o stavu pro určitý cluster entitu.
* **Úložiště bitových kopií**: Tato služba poskytuje úložiště a distribuci binární soubory aplikace. Tato služba poskytuje jednoduché souborů DFS úložiště, kde jsou aplikace odesílat a stahovat z.

## <a name="hosting-subsystem"></a>Hostování subsystému
Správce clusteru informuje hostování subsystému (spuštěná v každém uzlu), služby, které potřebuje ke správě pro konkrétní uzel. Subsystém hostování potom spravuje životní cyklus aplikace na tomto uzlu. Komunikuje se službou spolehlivost a stavu součásti, které zajistěte, aby byly správně umístěny repliky a jsou v pořádku.

## <a name="communication-subsystem"></a>Komunikačního subsystému
Tento subsystém poskytuje spolehlivé zasílání zpráv v rámci clusteru a službu zjišťování prostřednictvím pojmenování služby. Službu Naming překládá názvy služby do umístění v clusteru a umožňuje uživatelům spravovat služby názvy a vlastnosti. Pomocí zásady vytváření názvů služby, klienti mohou bezpečně komunikovat s libovolného uzlu v clusteru vyřešit název služby a načíst metadata služby. Pomocí jednoduchého pojmenování klientského rozhraní API, můžete vyvíjet uživatelé Service Fabric, služeb a klientů podporujících řešení aktuální umístění v síti i přes uzel náskok nebo znovu dimenzování clusteru.

## <a name="testability-subsystem"></a>Testovatelnosti subsystému
Testovatelnosti je sada nástrojů, které jsou vytvořené speciálně pro testování služby založený na Service Fabric. Nástroje umožní vývojář snadno vyvolat smysluplný chyb a spustit testovací scénáře postupu a ověření množství stavy a přechody, které služba bude mít v průběhu své životnosti, všechny řízené a bezpečné způsobem. Testovatelnosti také poskytuje mechanismus pro spuštění delší testy, které můžete iteraci prostřednictvím různých možných selhání bez ztráty dostupnosti. To poskytuje test v provozním prostředí.

