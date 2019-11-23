---
title: Azure Traffic Manager | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Traffic Manager. Zjistěte, jestli je pro vaši aplikaci tou správnou volbou pro vyrovnávání zatížení provozu uživatelů.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: allensu
ms.openlocfilehash: 48ce72104c4cc08fc26777aa7111b705c57317f5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420280"
---
# <a name="what-is-traffic-manager"></a>Co je Traffic Manager?
Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy.

Traffic Manager pomocí DNS směruje požadavky klientů do nejvhodnějšího koncového bodu služby v závislosti na metodě směrování provozu a stavu koncových bodů. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure. Traffic Manager poskytuje celou řadu [metod směrování provozu](traffic-manager-routing-methods.md) a [možností monitorování koncových bodů](traffic-manager-monitoring.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud chcete zajistit ukončování protokolu TLS (tzv. přesměrování zpracování SSL) nebo zpracování jednotlivých požadavků HTTP nebo HTTPS na úrovni aplikace, přečtěte si o službě [Application Gateway](../application-gateway/application-gateway-introduction.md). If you are looking for regional load balancing, review [Load Balancer](../load-balancer/load-balancer-overview.md). Vašim kompletním scénářům by mohla prospět kombinace těchto řešení podle potřeby.
>
> For an Azure load-balancing options comparison, see [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).
Traffic Manager offers following features:

## <a name="increase-application-availability"></a>Zvýšení dostupnosti aplikací

Traffic Manager zajišťuje vysokou dostupnost klíčových aplikací tím, že monitoruje koncové body a poskytuje automatické převzetí služeb při selhání v případě, že nějaký koncový bod přestane fungovat.
    
## <a name="improve-application-performance"></a>Zlepšení výkonu aplikací

Azure umožňuje provozovat cloudové služby nebo weby v datacentrech umístěných po celém světe. Traffic Manager zlepšuje odezvu aplikací tím, že směruje provoz do koncového bodu s nejnižší latencí sítě pro konkrétního klienta.

## <a name="perform-service-maintenance-without-downtime"></a>Provádění údržby služeb bez výpadků

U svých aplikací můžete provádět operace plánované údržby bez jakýchkoli výpadků. Traffic Manager can direct traffic to alternative endpoints while the maintenance is in progress.

## <a name="combine-hybrid-applications"></a>Kombinace hybridních aplikací

Traffic Manager podporuje externí koncové body mimo Azure a díky tomu umožňuje použití s hybridním cloudem i místními nasazeními, včetně scénářů přenosu do cloudu ([burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)), migrace do cloudu (migrate-to-cloud) a převzetí služeb cloudem při selhání (failover-to-cloud).

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuce provozu pro komplexní nasazení

Using [nested Traffic Manager profiles](traffic-manager-nested-profiles.md), multiple traffic-routing methods can be combined to create sophisticated and flexible rules to scale to the needs of larger, more complex deployments.

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu [Ceny služby Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md).
- Zjistěte, [jak služba Traffic Manager funguje](traffic-manager-how-it-works.md).
- Přečtěte si [nejčastější dotazy](traffic-manager-FAQs.md) ohledně služby Traffic Manager.




