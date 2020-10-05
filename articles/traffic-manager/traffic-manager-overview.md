---
title: Azure Traffic Manager | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Traffic Manager. Zjistěte, jestli je pro vaši aplikaci tou správnou volbou pro vyrovnávání zatížení provozu uživatelů.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: 830700fb4a5ac57405877364e9cc4828e5d1a5a4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89392540"
---
# <a name="what-is-traffic-manager"></a>Co je Traffic Manager?
Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy.

Traffic Manager pomocí DNS směruje požadavky klientů do nejvhodnějšího koncového bodu služby v závislosti na metodě směrování provozu a stavu koncových bodů. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure. Traffic Manager poskytuje celou řadu [metod směrování provozu](traffic-manager-routing-methods.md) a [možností monitorování koncových bodů](traffic-manager-monitoring.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud chcete zajistit ukončování protokolu TLS (tzv. přesměrování zpracování SSL) nebo zpracování jednotlivých požadavků HTTP nebo HTTPS na úrovni aplikace, přečtěte si o službě [Application Gateway](../application-gateway/application-gateway-introduction.md). Pokud hledáte regionální vyrovnávání zatížení, přečtěte si [Load Balancer](../load-balancer/load-balancer-overview.md). Vašim kompletním scénářům by mohla prospět kombinace těchto řešení podle potřeby.
>
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager nabízí následující funkce:

## <a name="increase-application-availability"></a>Zvýšení dostupnosti aplikací

Traffic Manager zajišťuje vysokou dostupnost klíčových aplikací tím, že monitoruje koncové body a poskytuje automatické převzetí služeb při selhání v případě, že nějaký koncový bod přestane fungovat.
    
## <a name="improve-application-performance"></a>Zlepšení výkonu aplikací

Azure umožňuje provozovat cloudové služby nebo weby v datacentrech umístěných po celém světe. Traffic Manager zlepšuje odezvu aplikací tím, že směruje provoz do koncového bodu s nejnižší latencí sítě pro konkrétního klienta.

## <a name="perform-service-maintenance-without-downtime"></a>Provádění údržby služeb bez výpadků

U svých aplikací můžete provádět operace plánované údržby bez jakýchkoli výpadků. Traffic Manager může směrovat provoz do alternativních koncových bodů, zatímco probíhá údržba.

## <a name="combine-hybrid-applications"></a>Kombinace hybridních aplikací

Traffic Manager podporuje externí koncové body mimo Azure a díky tomu umožňuje použití s hybridním cloudem i místními nasazeními, včetně scénářů přenosu do cloudu ([burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)), migrace do cloudu (migrate-to-cloud) a převzetí služeb cloudem při selhání (failover-to-cloud).

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuce provozu pro komplexní nasazení

Pomocí [vnořených profilů Traffic Manager](traffic-manager-nested-profiles.md)lze kombinovat různé metody směrování provozu, aby bylo možné vytvářet sofistikovaná a flexibilní pravidla pro škálování na požadavky většího, složitějšího nasazení.

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu [Ceny služby Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md).
- Zjistěte, [jak služba Traffic Manager funguje](traffic-manager-how-it-works.md).
- Přečtěte si [nejčastější dotazy](traffic-manager-FAQs.md) ohledně služby Traffic Manager.




