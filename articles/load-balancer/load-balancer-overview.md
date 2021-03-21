---
title: Co je Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Přečtěte si, jak Load Balancer funguje a jak ho používat v cloudu.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181206"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

*Vyrovnávání zatížení* označuje rovnoměrně distribuci zatížení (příchozí síťový provoz) napříč skupinou back-end prostředků nebo serverů. 

Azure Load Balancer funguje ve vrstvě 4 modelu Open Systems proconnection (OSI). Je jedním kontaktním bodem pro klienty. Nástroj pro vyrovnávání zatížení distribuuje příchozí toky, které dorazí na front-end služby Vyrovnávání zatížení do instancí fondu back-endu. Tyto toky jsou podle nakonfigurovaných pravidel vyrovnávání zatížení a sond stavu. Instance fondu back-endu můžou být Azure Virtual Machines nebo instancemi v sadě škálování virtuálního počítače.

**[Veřejný Nástroj pro vyrovnávání zatížení](./components.md#frontend-ip-configurations)** může poskytovat odchozí připojení virtuálních počítačů v rámci vaší virtuální sítě. Tato připojení se provádí pomocí překladu svých privátních IP adres na veřejné IP adresy. Veřejné nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení internetového provozu na vašich virtuálních počítačích.

**[Interní (nebo soukromý) Nástroj pro vyrovnávání zatížení](./components.md#frontend-ip-configurations)** se používá, pokud jsou privátní IP adresy potřeba jenom na front-endu. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu v rámci virtuální sítě. Front-endu nástroje pro vyrovnávání zatížení je možné v hybridním scénáři použít z místní sítě.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Obrázek: vyrovnávání vícevrstvých aplikací s využitím veřejného i interního Load Balancer*

Další informace o jednotlivých součástech nástroje pro vyrovnávání zatížení najdete v tématu [Azure Load Balancer Components](./components.md).

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. 
> * Pokud chcete provést globální směrování na základě DNS **a nemáte požadavky** na ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL"), požadavky na protokol HTTP/HTTPS nebo zpracování aplikační vrstvy, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Pokud chcete vyrovnávat zatížení mezi servery v oblasti aplikační vrstvy, přečtěte si [Application Gateway](../application-gateway/overview.md).
> * Pokud potřebujete optimalizovat globální směrování webového provozu a optimalizovat výkon a spolehlivost koncového uživatele nejvyšší úrovně prostřednictvím rychlého globálního převzetí služeb při selhání, přečtěte si část [přední dveře](../frontdoor/front-door-overview.md).
> 
> Vaše ucelené scénáře můžou v případě potřeby těžit z kombinace těchto řešení.
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Proč použít Azure Load Balancer?
Pomocí Azure Load Balancer můžete škálovat aplikace a vytvářet vysoce dostupné služby. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozího i odchozího přenosu. Load Balancer poskytuje nízkou latenci a vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

Mezi klíčové scénáře, které můžete provádět pomocí Azure Standard Load Balancer patří:

- Vyrovnávání zatížení **[interního](./quickstart-load-balancer-standard-internal-portal.md)** a **[externího](./quickstart-load-balancer-standard-public-portal.md)** provozu do virtuálních počítačů Azure.

- Zvyšte dostupnost díky distribuci prostředků **[v rámci](./tutorial-load-balancer-standard-public-zonal-portal.md)** zón a **[mezi](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** nimi.

- Konfigurace **[odchozího připojení](./load-balancer-outbound-connections.md)** pro virtuální počítače Azure

- Pomocí **[sond stavu](./load-balancer-custom-probe-overview.md)** můžete monitorovat prostředky s vyrovnáváním zatížení.

- Využívejte **[předávání portů](./tutorial-load-balancer-port-forwarding-portal.md)** pro přístup k virtuálním počítačům ve virtuální síti pomocí veřejné IP adresy a portu.

- Povolí podporu pro **[Vyrovnávání zatížení](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** **[IPv6](../virtual-network/ipv6-overview.md)**.

- Load Balancer úrovně Standard poskytuje multidimenzionální metriky prostřednictvím [Azure monitor](../azure-monitor/overview.md).  Tyto metriky je možné filtrovat, seskupovat a rozdělit pro danou dimenzi.  Poskytují aktuální a historické poznatky o výkonu a stavu vaší služby. [Přehledy pro Azure Load Balancer](./load-balancer-insights.md) nabízí předkonfigurovaný řídicí panel s užitečnými vizualizacemi pro tyto metriky.  Podporuje se taky Resource Health. Další podrobnosti najdete v **[diagnostice nástroje Load Balancer úrovně Standard](load-balancer-standard-diagnostics.md)** .

- Vyrovnávání zatížení služeb na **[několika portech, několika IP adresách nebo obojím](./load-balancer-multivip-overview.md)**.

- Přesuňte **[interní](./move-across-regions-internal-load-balancer-portal.md)** a **[externí](./move-across-regions-external-load-balancer-portal.md)** prostředky nástroje pro vyrovnávání zatížení napříč oblastmi Azure.

- Vyrovnávání zatížení TCP a UDP na všech portech současně pomocí **[portů ha](./load-balancer-ha-ports-overview.md)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Zabezpečení ve výchozím nastavení

* Load Balancer úrovně Standard je založený na modelu zabezpečení sítě s nulovým vztahem důvěryhodnosti.

* Standard Load Balancer je ve výchozím nastavení zabezpečená a součástí vaší virtuální sítě. Virtuální síť je privátní a izolovaná síť.  

* Standardní nástroje pro vyrovnávání zatížení a standardní veřejné IP adresy se zavřou na příchozí připojení, pokud je neotevřou skupiny zabezpečení sítě. Skupin zabezpečení sítě se používají k explicitnímu povolení povoleného provozu.  Pokud nemáte NSG v podsíti nebo síťové kartě svého prostředku virtuálního počítače, přenos nemá povolený přístup k tomuto prostředku. Další informace o skupin zabezpečení sítě a o tom, jak je použít pro váš scénář, najdete v tématu [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).

* Služba Load Balancer úrovně Basic je ve výchozím nastavení otevřená pro Internet. 

* Load Balancer neukládá zákaznická data.

## <a name="pricing-and-sla"></a>Ceny a smlouvy SLA

Informace o cenách za službu Load Balancer úrovně Standard najdete v tématu [ceny služby Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
Load Balancer úrovně Basic se nabízí zdarma.
Podívejte se [na smlouvu SLA pro nástroj pro vyrovnávání zatížení](https://aka.ms/lbsla). Nástroj pro vyrovnávání zatížení úrovně Basic nemá žádnou smlouvu SLA.

## <a name="whats-new"></a>Co je nového

Přihlaste se k odběru informačního kanálu RSS a zobrazte nejnovější Azure Load Balancer aktualizace funkcí na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat nástroj pro vyrovnávání zatížení, přečtěte si téma [vytvoření veřejného standardního nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-portal.md) .

Další informace o omezeních Azure Load Balancer a součástech najdete v tématu [Azure Load Balancer Components](./components.md) a [Azure Load Balancer koncepty](./concepts.md) .