---
title: Osvědčené postupy monitorování azure service fabric
description: Osvědčené postupy a aspekty návrhu pro monitorování clusterů a aplikací pomocí Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551812"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

[Monitorování a diagnostika](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) jsou důležité pro vývoj, testování a nasazování úloh v libovolném cloudovém prostředí. Můžete například sledovat, jak jsou vaše aplikace používány, akce prováděné platformou Service Fabric, využití prostředků s čítači výkonu a celkový stav clusteru. Tyto informace můžete použít k diagnostice a opravě problémů a zabránit jejich výskytu v budoucnu.

## <a name="application-monitoring"></a>Monitorování aplikace

Monitorování aplikací sleduje, jak jsou používány funkce a součásti aplikace. Sledujte své aplikace a ujistěte se, že problémy, které mají vliv na vaše uživatele, jsou zachyceny. Monitorování aplikací je odpovědností těch, kteří vyvíjejí aplikaci a její služby, protože je jedinečné pro obchodní logiku vaší aplikace. Doporučujeme nastavit monitorování aplikací pomocí [application insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), nástroje pro monitorování aplikací Azure.

## <a name="cluster-monitoring"></a>Monitorování clusteru

Jedním z cílů service fabric je, aby aplikace odolné vůči selhání hardwaru. Tohoto cíle je dosaženo prostřednictvím schopnosti systémových služeb platformy odhalovat problémy s infrastrukturou a rychle překračovat úlohy převzetí služeb při selhání do jiných uzlů v clusteru. Ale co když systémové služby samy o sobě mají problémy? Nebo pokud při pokusu o nasazení nebo přesunutí pracovního vytížení jsou porušena pravidla pro umístění služeb? Service Fabric poskytuje diagnostiku pro tyto a další problémy, abyste se ujistili, že jste informováni o tom, jak platforma Service Fabric spolupracuje s aplikacemi, službami, kontejnery a uzly.

U clusterů Windows doporučujeme nastavit monitorování clusteru pomocí [protokolů agenta diagnostiky](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) a [azure monitoru](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Pro linuxové clustery jsou protokoly Azure Monitoru také doporučeným nástrojem pro monitorování platformy Azure a infrastruktury. Diagnostika platformy Linuxu vyžaduje jinou konfiguraci, jak je uvedeno v [událostech clusteru Service Fabric Linux v Syslogu](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorování infrastruktury

[Protokoly Azure Monitor se](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) doporučuje pro monitorování událostí na úrovni clusteru. Jakmile nakonfigurujete agenta Analýzy protokolů s pracovním prostorem, jak je popsáno v předchozím odkazu, budete moci shromažďovat metriky výkonu, jako je využití procesoru, čítače výkonu .NET, jako je využití procesoru na úrovni procesu, čítače výkonu Service Fabric, jako je například # výjimek ze spolehlivé služby a metriky kontejneru, jako je využití procesoru.  Budete muset zapisovat protokoly kontejnerů do stdout nebo stderr tak, aby byly k dispozici v protokolech Azure Monitor.

## <a name="watchdogs"></a>Hlídací psi

Obecně watchdog je samostatná služba, která sleduje stav a zatížení napříč službami, ping koncové body a hlásí neočekávané události stavu v clusteru. To může pomoci zabránit chybám, které nemusí být zjištěny pouze na základě výkonu jedné služby. Watchdogs jsou také vhodné místo pro hostování kódu, který provádí nápravné akce, které nevyžadují interakci uživatele, jako je čištění souborů protokolu v úložišti v určitých časových intervalech. Podívejte se na ukázkovou implementaci služby watchdog v [událostech clusteru Service Fabric Linux v Syslogu](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Další kroky

* Začínáme s instrumentací aplikací: [Událost na úrovni aplikace a generování protokolu](service-fabric-diagnostics-event-generation-app.md).
* Projděte si kroky k nastavení Application Insights pro vaši aplikaci s [Monitor a diagnostikovat ASP.NET základní aplikace na Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Další informace o sledování platformy a události Service Fabric poskytuje pro vás: [Na úrovni platformy události a generování protokolu](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace integrace protokolů Azure Monitor pomocí service fabric: [Nastavení protokolů Azure Monitor pro cluster](service-fabric-diagnostics-oms-setup.md)
* Zjistěte, jak nastavit protokoly Azure Monitoru pro monitorování kontejnerů: [Monitorování a diagnostika pro kontejnery Windows v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Podívejte se na příklad diagnostických problémů a řešení pomocí service fabric: [diagnostika běžných scénářů](service-fabric-diagnostics-common-scenarios.md)
* Další informace o obecných doporučeních monitorování prostředků Azure: [Doporučené postupy – monitorování a diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).