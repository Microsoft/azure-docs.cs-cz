---
title: Azure Traffic Manager | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Traffic Manager. Zjistěte, zda se jedná o správnou volbu pro síťový provoz vyrovnávání zatížení pro vaši aplikaci.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182158"
---
# <a name="what-is-traffic-manager"></a>Co je Traffic Manager?
Azure Traffic Manager je nástroj pro vyrovnávání zatížení pro provoz založený na DNS. Tato služba umožňuje distribuovat provoz do vašich veřejných aplikací napříč globálními oblastmi Azure. Traffic Manager taky poskytuje veřejné koncové body s vysokou dostupností a rychlou odezvou.

Traffic Manager používá DNS k směrování požadavků klienta na příslušný koncový bod služby na základě metody směrování provozu. Traffic Manager také poskytuje monitorování stavu pro každý koncový bod. Koncovým bodem může být libovolná internetová služba hostovaná v systému Azure nebo mimo něj. Traffic Manager poskytuje celou řadu [metod směrování provozu](traffic-manager-routing-methods.md) a [možností monitorování koncových bodů](traffic-manager-monitoring.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. 
> * Pokud chcete vyrovnávat zatížení mezi servery v oblasti aplikační vrstvy, přečtěte si [Application Gateway](../application-gateway/overview.md).
> * Pokud potřebujete optimalizovat globální směrování webového provozu a optimalizovat výkon a spolehlivost koncového uživatele nejvyšší úrovně prostřednictvím rychlého globálního převzetí služeb při selhání, přečtěte si část [přední dveře](../frontdoor/front-door-overview.md).
> * Pro vyrovnávání zatížení síťové vrstvy si přečtěte [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Vaše ucelené scénáře můžou v případě potřeby těžit z kombinace těchto řešení.
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager nabízí následující funkce:

## <a name="increase-application-availability"></a>Zvýšení dostupnosti aplikací

Traffic Manager zajišťuje vysokou dostupnost klíčových aplikací tím, že monitoruje koncové body a poskytuje automatické převzetí služeb při selhání v případě, že nějaký koncový bod přestane fungovat.
    
## <a name="improve-application-performance"></a>Zlepšení výkonu aplikací

Azure umožňuje provozovat cloudové služby a weby v datových centrech po celém světě. Traffic Manager může zlepšit odezvu vašeho webu tím, že směruje provoz na koncový bod s nejnižší latencí.

## <a name="service-maintenance-without-downtime"></a>Údržba služby bez výpadků

Můžete mít plánované údržbě v aplikacích bez výpadků. Traffic Manager může směrovat provoz do alternativních koncových bodů, zatímco probíhá údržba.

## <a name="combine-hybrid-applications"></a>Kombinace hybridních aplikací

Traffic Manager podporuje externí koncové body mimo Azure a díky tomu umožňuje použití s hybridním cloudem i místními nasazeními, včetně scénářů přenosu do cloudu ([burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)), migrace do cloudu (migrate-to-cloud) a převzetí služeb cloudem při selhání (failover-to-cloud).

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuce provozu pro komplexní nasazení

Pomocí [vnořených profilů Traffic Manager](traffic-manager-nested-profiles.md)lze kombinovat různé metody směrování provozu, aby bylo možné vytvářet sofistikovaná a flexibilní pravidla pro škálování na požadavky většího, složitějšího nasazení.

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu [Ceny služby Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvořit profil služby Traffic Manager](./quickstart-create-traffic-manager-profile.md).
- Zjistěte, [jak služba Traffic Manager funguje](traffic-manager-how-it-works.md).
- Přečtěte si [nejčastější dotazy](traffic-manager-FAQs.md) ohledně služby Traffic Manager.