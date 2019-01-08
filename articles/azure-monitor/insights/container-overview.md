---
title: Přehled monitorování kontejnerů Azure | Dokumentace Microsoftu
description: Tento článek obsahuje přehled různých metod, které jsou k dispozici v Azure pro monitorování kontejnerů v Azure, které umožňují rychle porozumět clusterů stav a dostupnost.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 825c6a7dd0c0163a1fd298ca7510f2483a5df2ad
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076514"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Přehled monitorování kontejnerů v Azure
S Azure můžete efektivně monitorovat a spravovat vaše úlohy nasazené v Azure kontejnery s Kubernetes a Docker. Je důležité pochopit, jak jsou kontejnery s několika aplikacemi mikroslužeb provádění k zajištění spolehlivých služeb ve velkém měřítku a monitorování plán podpory. Tento článek poskytuje stručný přehled správy a možnosti v Azure vám pomůžou pochopit jejich monitorování, a které jsou vhodné na základě vašich požadavků.

Pomocí [monitorování Azure pro kontejnery](container-insights-overview.md), můžete zobrazit na první pohled výkonu a stavu kontejneru svoji Linuxovou infrastrukturu a rychle prozkoumat problémy. Telemetrická data jsou uložena v pracovním prostoru Log Analytics a integrované na webu Azure Portal, kde můžete prozkoumat, filtrovat a segment agregovaná data v řídicích panelech dohlížet na zatížení, výkonu a stavu.  

Pro kontejnery spuštěné mimo hostované služby Kubernetes v Azure Log Analytics [řešení pro Windows a kontejneru Dockeru](../../azure-monitor/insights/containers.md) usnadňuje zobrazení a správa hostitelů kontejnerů Windows a Dockeru. Z pracovního prostoru Log Analytics můžete zobrazit podrobnosti inventáře, výkonu a události z uzly tak kontejnery v prostředí. Můžete zobrazit podrobné informace o auditování zobrazuje příkazy, které používá s kontejnery a kontejnerů můžete řešit pomocí zobrazení a hledání centralizované protokoly bez nutnosti pro vzdálený přístup ke hostitelů Docker nebo Windows.

Získat holistický přístup nebo začátku do konce monitorování aplikace všechny závislosti, zda je Azure nebo místních zdrojů, by se měly monitorovat s Azure Monitor nebo Log Analytics.  Aplikační vrstvu by měly být zahrnuty, chcete-li přidat další úroveň sledování stavu, i na úrovni platformy a aplikace pomocí Application Insights. Na úrovni platformy jsou sady SDK služby Application Insights pro [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), a [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Aplikace mikroslužeb je podpora [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/learn/nodejs-quick-start.md), [.Net](../../azure-monitor/app/asp-net.md), [.Net Core](../../azure-monitor/app/asp-net-core.md), a také řadu dalších [jazyků nebo architektur](../../azure-monitor/app/platforms.md). 

V opačném případě půjdou neidentifikovaný problémy, který může mít vliv na dostupnost aplikace a cíle úrovně služeb nebude splněna.  
