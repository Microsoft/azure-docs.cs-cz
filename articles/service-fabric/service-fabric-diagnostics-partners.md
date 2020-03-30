---
title: Partneři pro monitorování infrastruktury služeb Azure
description: Zjistěte, jak monitorovat aplikace, clustery a infrastrukturu Azure Service Fabric pomocí řešení pro monitorování partnerů.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645714"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partneři pro monitorování infrastruktury služeb Azure

Tento článek ukazuje, jak lze sledovat jejich service fabric aplikace, clustery a infrastrukturu s několika partnerských řešení. Spolupracovali jsme s každým z níže uvedených partnerů na vytvoření integrovaných nabídek pro Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Naše integrace s Dynatrace poskytuje mnoho funkcí po vybalení z krabice pro sledování clusterů Service Fabric. Instalace Dynatrace OneAgent na vaše instance VMSS vám poskytuje čítače výkonu a topologie nasazení Service Fabric až na úroveň aplikace. Dynatrace je také skvělou volbou pro místní monitorování. Podívejte se na další funkce uvedené v [oznámení](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) a [pokyny](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) k povolení Dynatrace ve vašem clusteru. 

## <a name="datadog"></a>Datový pes

Datadog má rozšíření pro VMSS pro Windows i Linux instance. Pomocí Datadog můžete shromažďovat protokoly událostí systému Windows a tím shromažďovat události platformy Service Fabric v systému Windows. Podívejte se na pokyny, jak odeslat diagnostická data data Datadog [zde](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Integrace Service Fabric s AppDynamics je na úrovni aplikace. Aktualizací proměnných prostředí a pomocí aplikace Dynamics NuGets můžete odeslat telemetrii aplikace do AppDynamics. V těchto [pokynech](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) naleznete informace o integraci aplikací .NET Service Fabric s aplikací AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic je další nástroj pro správu výkonu aplikací, který se dobře integruje s aplikacemi Service Fabric. Můžete nainstalovat nové balíčky Relic NuGet a přidat specifické proměnné prostředí v souborech manifestu k odeslání telemetrie aplikace do new relic. V těchto [pokynech](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) povolte novou telemetrická data relic pro aplikace .NET Service Fabric.

## <a name="elk"></a>Elk 

Zásobník ELK je kolekce open source technologií: Elasticsearch, Logstash a Kibana. Pomocí těchto technologií v kombinaci, můžete shromažďovat, ukládat a analyzovat service fabric monitorování a diagnostická data. Máme návod, jak to udělat s service fabric nativní Java aplikace [zde](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio je služba shromažďování protokolů, která může shromažďovat protokoly z vašich aplikací a událostí z Service Fabric v cloudu nebo v místním prostředí v reálném čase. Kromě živé pozorovatelnosti nabízí humio nejmodernější možnosti analýzy a vizualizace pro prohlížení a shromažďování informací z vaší diagnostiky. Humio má nákladově efektivní cenové plány a je postaven tak, aby škálovat při zachování je to odlehčení rychlé rychlosti. Přímo se integruje s událostmi platformy Service Fabric a telemetrií aplikací. Více informací o integraci Humio a Service Fabric si můžete přečíst [zde](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Další kroky

* Získejte [přehled o monitorování a diagnostice](service-fabric-diagnostics-overview.md) v service fabric
* Naučte se [diagnostikovat běžné scénáře](service-fabric-diagnostics-common-scenarios.md) pomocí našich nástrojů první strany
