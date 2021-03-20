---
title: Osvědčené postupy pro Azure Service Fabric monitoring
description: Osvědčené postupy a faktory návrhu pro monitorování clusterů a aplikací s využitím Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529246"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Osvědčené postupy monitorování a diagnostiky pro Azure Service Fabric

[Monitorování a diagnostika](./service-fabric-diagnostics-overview.md) jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli cloudovém prostředí. Můžete například sledovat, jak se aplikace používají, akce prováděné Service Fabric platformou, využití prostředků s čítači výkonu a celkový stav clusteru. Pomocí těchto informací můžete diagnostikovat a opravovat problémy a zabránit jejich výskytu v budoucnu.

## <a name="application-monitoring"></a>Monitorování aplikace

Monitorování aplikací sleduje, jak se používají funkce a komponenty vaší aplikace. Monitorujte své aplikace a ujistěte se, že problémy, které mají vliv na uživatele, jsou zachyceny. Monitorování aplikací je zodpovědností za to, že vyvíjí aplikaci a její služby, protože je jedinečná pro obchodní logiku vaší aplikace. Doporučujeme, abyste nastavili monitorování aplikací pomocí [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), nástroje pro monitorování aplikací Azure.

## <a name="cluster-monitoring"></a>Monitorování clusteru

Jedním z cílů Service Fabric je zajištění odolnosti aplikací proti selhání hardwaru. Tento cíl se dosahuje prostřednictvím schopnosti systémových služeb platformy zjistit problémy infrastruktury a rychlé úlohy převzetí služeb při selhání na jiné uzly v clusteru. Ale co když mají samotné systémové služby problémy? Nebo pokud při pokusu o nasazení nebo přesunutí úlohy dojde k porušení pravidel pro umístění služeb? Service Fabric poskytuje diagnostiku pro tyto a další problémy, abyste měli jistotu, že máte informace o tom, jak Service Fabric platforma komunikuje s vašimi aplikacemi, službami, kontejnery a uzly.

U clusterů Windows doporučujeme nastavit monitorování clusteru pomocí [diagnostického agenta](./service-fabric-diagnostics-event-aggregation-wad.md) a [protokolů Azure monitor](./service-fabric-diagnostics-oms-setup.md).

U Azure Monitor clusterů se systémem Linux je také doporučeným nástrojem pro monitorování infrastruktury a platforem Azure. Diagnostika platformy pro Linux vyžaduje jinou konfiguraci, jak je uvedeno v [událostech clusteru Service Fabric Linux v protokolu SYSLOG](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Monitorování infrastruktury

Pro monitorování událostí na úrovni clusteru se doporučuje používat [protokoly Azure monitor](./service-fabric-diagnostics-oms-agent.md) . Jakmile nakonfigurujete agenta Log Analytics s pracovním prostorem, jak je popsáno v předchozím odkazu, budete moct shromažďovat metriky výkonu, jako je využití procesoru, čítače výkonu .NET, jako je například využití procesoru na úrovni procesu, Service Fabric čítače výkonu, jako je například počet výjimek z spolehlivé služby a metriky kontejnerů, jako je například využití procesoru.  Do stdout nebo stderr budete muset zapisovat protokoly kontejnerů, aby byly dostupné v Azure Monitorch protokolech.

## <a name="watchdogs"></a>Sledovací zařízení

Standardně se jedná o samostatnou službu, která sleduje stav a zatížení napříč službami, příkazy příkazového řádku otestuje a hlásí neočekávané události stavu v clusteru. To může přispět k tomu, aby nedocházelo k chybám, které se nemusí detekovat na základě výkonu jedné služby. Sledovací zařízení jsou také dobrým místem pro hostování kódu, který provádí nápravné akce, které nevyžadují zásah uživatele, jako je například čištění souborů protokolu v úložišti v určitých časových intervalech. Podívejte se na ukázkovou implementaci sledovací služby v  [událostech clusteru Service Fabric Linux v protokolu SYSLOG](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Další kroky

* Začněte instrumentovat vaše aplikace: [událost a generování protokolu na úrovni aplikace](service-fabric-diagnostics-event-generation-app.md).
* Projděte si postup nastavení Application Insights pro vaši aplikaci s [monitorováním a diagnostikou ASP.NET Core aplikace na Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Přečtěte si další informace o monitorování platformy a o událostech, které vám Service Fabric poskytuje: [generování událostí a protokolů na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace Azure Monitor protokolů integrace s Service Fabric: [nastavení protokolů Azure monitor pro cluster](service-fabric-diagnostics-oms-setup.md)
* Naučte se nastavit protokoly Azure Monitor pro monitorování kontejnerů: [monitorování a diagnostika kontejnerů Windows v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Podívejte se na příklady problémy s diagnostikou a řešení pomocí Service Fabric: [diagnostikování běžných scénářů](service-fabric-diagnostics-common-scenarios.md)
* Přečtěte si o obecných doporučeních pro monitorování prostředků Azure: [osvědčené postupy – monitorování a diagnostika](/azure/architecture/best-practices/monitoring).
