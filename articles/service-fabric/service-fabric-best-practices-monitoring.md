---
title: Osvědčené postupy pro monitorování Azure Service Fabric | Dokumentace Microsoftu
description: Osvědčené postupy pro monitorování clusterů Service Fabric a aplikací.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 152a503181bba9e1ed39671794d512258759e251
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913806"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

[Monitorování a Diagnostika](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) jsou důležité pro vývoj, testování a nasazování úloh v jakémkoli prostředí cloud. Můžete například sledovat, jak vaše aplikace se používají, akce prováděné pomocí platformy Service Fabric, využití prostředků pomocí čítačů výkonu a celkový stav clusteru. Tyto informace můžete diagnostikovat a opravit problémy a zabránili jejich výskytu v budoucnu.

## <a name="application-monitoring"></a>Monitorování aplikace

Monitorování aplikací, sleduje používání funkcí a komponent vaší aplikace. Monitorujte své aplikace, abyste měli jistotu, že jsou zachyceny problémy, které ovlivní vaše uživatele. Monitorování aplikace zodpovídá za ty vývoj aplikace a její služby, protože je jedinečné pro obchodní logice aplikace. Doporučujeme, abyste nastavili monitorování aplikací pomocí [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), nástroj pro sledování aplikace Azure.

## <a name="cluster-monitoring"></a>Monitorování clusteru

Jedním z cílů Service Fabric je, aby aplikace odolné vůči selhání hardwaru. Tento cíl se dosahuje prostřednictvím možnosti platformy systémových služeb ke zjištění problémů s infrastrukturou a rychlé převzetí služeb při selhání úlohy jiným uzlům v clusteru. Ale co když vlastních služeb systému máte problémy? Nebo pokud při pokusu o nasazení nebo přesunutí úlohy, jsou pravidla pro umístění služby došlo k porušení? Service Fabric nabízí diagnostiky pro tyto a další problémy, abyste měli jistotu, že se že zobrazí se informace o způsobu interakce platformu Service Fabric s aplikací, služeb, kontejnerů a uzlů.

V případě clusterů Windows se doporučuje, abyste nastavili monitorování clusterů pomocí [agenta diagnostiky](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) a [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Pro clustery s Linuxem Log Analytics je také doporučeným nástrojem pro monitorování infrastruktury a platformy Azure. Diagnostika platformy Linux vyžadují jinou konfiguraci, jak je uvedeno v [události clusteru Service Fabric s Linuxem v procesu Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorování infrastruktury

[Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) se doporučuje pro sledování událostí na úrovni clusteru. Jakmile konfigurace agenta Log Analytics s pracovním prostorem, jak je popsáno v předchozí odkaz, bude moct shromažďovat metriky výkonu jako je využití procesoru, čítače výkonu .NET, jako je například proces úroveň využití procesoru, Service Fabric výkonu čítače, jako například počet výjimek z spolehlivé služby a container metriky, jako je využití procesoru.  Je potřeba zapisovat protokoly kontejneru do stdout a stderr, tak, že budou k dispozici ve službě Log Analytics.

## <a name="watchdogs"></a>Watchdogs

Obecně platí sledovacího zařízení je samostatná služba, která sleduje stav a zatížení napříč službami, odešle příkaz ping koncových bodů a hlásí neočekávané stavu události v clusteru. To může pomoci zabránit chybám, které nemusí být detekována pouze podle výkonu jediné služby. Watchdogs je také vhodné místo pro hostování kódu, který provede nápravné akce, které nevyžadují zásahu uživatele, jako je čištění protokolu souborů v úložišti v určitých časových intervalech. V tématu ukázková implementace služby sledovacího zařízení v [události clusteru Service Fabric s Linuxem v procesu Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Další postup

* Začínáme instrumentace vaší aplikace: [Aplikace úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-app.md).
* Projděte si kroky k nastavení Application Insights pro vaši aplikaci s [monitorování a Diagnostika aplikace ASP.NET Core v Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Další informace o sledování událostí, které obsahuje Service Fabric a platformy: [Generování úrovně událostí a protokolu platformy](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace integrace Log Analytics s využitím Service Fabric: [Nastavení Log Analytics pro cluster](service-fabric-diagnostics-oms-setup.md)
* Zjistěte, jak nastavit službu Log Analytics pro monitorování kontejnerů: [Monitorování a Diagnostika kontejnerů Windows v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Viz příklad diagnostiky problémů a řešení s využitím Service Fabric: [Diagnostika běžné scénáře](service-fabric-diagnostics-common-scenarios.md)
* Další informace o obecných doporučení pro monitorování prostředků Azure: [Osvědčené postupy – monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).