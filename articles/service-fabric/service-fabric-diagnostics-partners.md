---
title: Azure Service Fabric monitorování partneři | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat Azure Service Fabric pomocí partnerských řešení monitorování
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: 43b7b2e7b4f4da4939e08a68bc2ac7ea0a2c2a6b
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49459014"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric monitorování partnerů

Tento článek ukazuje, jak jeden monitorování svých aplikací Service Fabric, clustery a infrastruktury s několika partnerských řešení. Jsme pracovali s každou z níže uvedených partnerů k vytvoření integrovatelných nabídek pro Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Naše integrace pomocí služby Dynatrace poskytuje mnoho z funkcí pole k monitorování clusterů Service Fabric. Instalace na vaše instance VMSS Dynatrace OneAgent poskytuje vám čítače výkonu a topologie nasazení Service Fabric na úrovni aplikace. Dynatrace je také skvělou volbou pro místní monitorování. Podívejte se na informace uvedené v funkce [si oznámení](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) a [pokyny](https://www.dynatrace.com/support/help/cloud-platforms/azure/how-do-i-monitor-azure-service-fabric-applications/) umožňující Dynatrace ve vašem clusteru. 

## <a name="datadog"></a>Datadog

Služby Datadog má rozšíření pro VMSS pro instance Windows i Linux. Pomocí služby Datadog můžete shromažďovat protokoly událostí Windows a tím shromažďovat události platformy Service Fabric na Windows. Přečtěte si návod, jak posílat diagnostická data do služby Datadog [tady](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Integrace Service Fabric s AppDynamics je na úrovni aplikace. Aktualizace proměnné prostředí a pomocí aplikace Dynamics balíčky Nuget, můžete odeslat telemetrii application AppDynamics. Použijte tyto [pokyny](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) jak integrujte své aplikace Service Fabric v .NET s AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic je jiný nástroj správy výkonu aplikací, která se integruje s aplikací Service Fabric. Můžete nainstalovat balíčky NuGet nové Relic a přidat určité proměnné prostředí v souborech manifestu k odesílání telemetrie vaší aplikace na špičkové funkce New Relicu. Projděte si tyto [pokyny](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) povolit telemetrii špičkové funkce New Relicu pro aplikace .NET pro Service Fabric.

## <a name="elk"></a>ELK 

ELK stack je kolekce technologiích s otevřeným zdrojem: Elasticsearch, Logstash a Kibana. Když použijete v kombinaci, můžete shromažďovat, ukládat a analyzovat data monitorování a diagnostiky v Service Fabric. Máme k dispozici návod jak to udělat pomocí nativních aplikací v Javě Service Fabric [tady](service-fabric-tutorial-java-elk.md). 


## <a name="next-steps"></a>Další postup

* Získat [Přehled monitorování a diagnostiku](service-fabric-diagnostics-overview.md) v Service Fabric
* Zjistěte, jak [diagnostikovat běžné scénáře](service-fabric-diagnostics-common-scenarios.md) s našimi nástroji první strany
