---
title: Přehled Azure kontejnery monitorování | Microsoft Docs
description: Tento článek obsahuje přehled různé metody, které jsou k dispozici v Azure k monitorování kontejnerů v Azure a rychle pochopit clustery stavu a dostupnosti.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Přehled monitorování kontejnerů v Azure
S Azure můžete efektivně monitorovat a spravovat úlohy na Azure kontejnery systémem Kubernetes nebo Docker nasazené. Je důležité pochopit, jak kontejnery s více aplikacemi mikroslužbu fungují pro poskytování spolehlivé služeb ve velkém měřítku a podporují plán monitorování. Tento článek poskytuje stručný přehled správy a možnosti monitorování v Azure ke každé rozumíte a které jsou vhodné podle svých požadavků.

Pomocí [Azure monitorování kontejneru stav](monitoring-container-health.md), můžete přehledné zobrazení výkonu a stavu infrastruktury Linux kontejneru a prozkoumat problémy rychle. Telemetrie je uložen v prostoru analýzy protokolů a integrovaná v portálu Azure, kde můžete prozkoumat, filtrovat a segment agregovaná data pomocí řídicích panelů dohlížet na stav, výkon a zatížení.  

Pro kontejnery, které jsou spuštěné mimo hostovanou službu Azure Kubernetes Log Analytics [řešení pro systém Windows a kontejner Docker](../log-analytics/log-analytics-containers.md) pomáhá zobrazení a správa hostitelů s Windows a Docker kontejneru. Z pracovního prostoru analýzy protokolů můžete zobrazit podrobnosti inventáře, výkonu a událostí z uzlů a kontejnery v prostředí. Můžete zobrazit podrobné informace o auditování zobrazující příkazy používané s kontejnery a kontejnery můžete vyřešit tak, že zobrazení a hledání centralizované protokoly bez nutnosti vzdálený přístup hostitelů Docker nebo Windows.

K dosažení komplexní nebo pro kompletní monitorování aplikace, některé závislé tom, zda je Azure nebo místní prostředek, by se měly monitorovat pomocí monitorování Azure nebo analýzy protokolů.  Aplikační vrstvu by měly být zahrnuty, aby bylo možné přidat další vrstvu sledování stavu, i na úrovni platformy a aplikace pomocí Application Insights. Na úrovni platformy je sadách Application Insights SDK pro [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), a [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Mikroslužbu aplikací, je podpora [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md), [.Net základní](../application-insights/app-insights-asp-net-core.md), a také řadu dalších [jazyky nebo rozhraní](../application-insights/app-insights-platforms.md). 

Jinak, přejde neidentifikovaný problémy, může mít vliv na dostupnost aplikace a nebude dosaženo cílových úrovní služeb.  
