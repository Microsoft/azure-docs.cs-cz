---
title: Partneři monitorování Azure Service Fabric
description: Naučte se monitorovat aplikace, clustery a infrastrukturu Azure Service Fabric pomocí řešení monitorování partnerů.
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 92ac0627f02ccefdd4c93aa51cac7c9dd7790460
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627723"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partneři monitorování Azure Service Fabric

Tento článek ukazuje, jak může monitorovat své Service Fabric aplikace, clustery a infrastrukturu několik partnerskými řešeními. Společně s každým z partnerů jsme pracovali pro vytváření integrovaných nabídek pro Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Naše integrace s dynaTrace poskytuje mnoho funkcí v krabicích pro monitorování clusterů Service Fabric. Při instalaci dynaTrace OneAgent na vaše instance VMSS získáte čítače výkonu a topologii nasazení Service Fabric až do úrovně aplikace. DynaTrace je také skvělou volbou pro místní monitorování. Podívejte se na více funkcí uvedených v [oznámení](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) a [pokyny](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) , jak povolit dynaTrace v clusteru. 

## <a name="datadog"></a>Datadog

Služby Datadog má rozšíření pro VMSS pro instance systému Windows i Linux. Pomocí služby Datadog můžete shromažďovat protokoly událostí Windows a shromažďovat tak Service Fabric události platformy ve Windows. Přečtěte si pokyny k odeslání diagnostických dat [do služby Datadog.](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)

## <a name="appdynamics"></a>AppDynamics

Service Fabric integrace s AppDynamics je na úrovni aplikace. Díky aktualizaci proměnných prostředí a používání aplikace Dynamics balíčky NuGet můžete odeslat telemetrii aplikace do AppDynamics. Informace o tom, jak integrovat aplikace Service Fabric .NET pomocí AppDynamics, najdete v těchto [pokynech](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) .

## <a name="new-relic"></a>New Relic

New Relic je další nástroj pro správu výkonu aplikací, který se dobře integruje s Service Fabric aplikacemi. Můžete nainstalovat nové balíčky NuGet Relic a přidat konkrétní proměnné prostředí do souborů manifestu pro odeslání telemetrie aplikace do nového Relic. Podívejte se na tyto [pokyny](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) , abyste povolili novou telemetrii Relic pro aplikace Service Fabric .NET.

## <a name="elk"></a>ELK 

ELK Stack je kolekcí Open Source technologií: Elasticsearch, Logstash a Kibana. Pomocí těchto technologií v kombinaci můžete shromažďovat, ukládat a analyzovat Service Fabric monitorování a diagnostická data. V tomto kurzu se naučíme, jak to udělat s Service Fabric nativními [aplikacemi Java](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio je služba Shromažďování protokolů, která dokáže shromažďovat protokoly z vašich aplikací a událostí z Service Fabric v cloudu nebo místně v reálném čase. Kromě živého pozorování Humio nabízí špičkové možnosti analýzy a vizualizace pro zobrazení a shromažďování informací z diagnostiky. Humio má nákladově efektivní cenové plány a je sestavená tak, aby bylo možné škálovat a současně udržet rychlou rychlost. Přímo se integruje s Service Fabricmi událostmi platforem a Telemetrie aplikací. [Tady](https://github.com/humio/service-fabric-humio)si můžete přečíst další informace o integraci Humio a Service Fabric.

## <a name="next-steps"></a>Další kroky

* [Přehled monitorování a diagnostiky](service-fabric-diagnostics-overview.md) v Service Fabric
* Naučte se [diagnostikovat běžné scénáře](service-fabric-diagnostics-common-scenarios.md) pomocí našich nástrojů první strany.
