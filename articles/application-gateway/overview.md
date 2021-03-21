---
title: Co je Azure Application Gateway?
description: Přečtěte si, jak můžete pomocí aplikační brány Azure spravovat webové přenosy do vaší aplikace.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 4344cd38d9a58eec27c6202e81b8ef678a510681
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176004"
---
# <a name="what-is-azure-application-gateway"></a>Co je Azure Application Gateway?

Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Nástroje pro vyrovnávání zatížení obvykle fungují na přenosové vrstvě (vrstva OSI 4 – TCP a UDP) a směrují provoz na základě zdrojové IP adresy a portu do cílové IP adresy a portu.

Application Gateway mohou provádět rozhodování o směrování na základě dalších atributů požadavku HTTP, například cesty k identifikátoru URI nebo hlaviček hostitele. Můžete například směrovat provoz na základě příchozí adresy URL. Pokud tedy příchozí adresa URL obsahuje `/images`, můžete směrovat provoz na konkrétní sadu serverů (označovanou jako fond), která je nakonfigurovaná pro obrázky. Pokud `/video` je v adrese URL, provoz se směruje do jiného fondu optimalizovaného pro videa.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Tento typ směrování se označuje jako vyrovnávání zatížení aplikační vrstvy (vrstva OSI 7). Azure Application Gateway umí směrování na základě adresy URL, ale to není vše.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. 
> * Pokud chcete provést globální směrování na základě DNS **a nemáte požadavky** na ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL"), požadavky na protokol HTTP/HTTPS nebo zpracování aplikační vrstvy, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Pokud potřebujete optimalizovat globální směrování webového provozu a optimalizovat výkon a spolehlivost koncového uživatele nejvyšší úrovně prostřednictvím rychlého globálního převzetí služeb při selhání, přečtěte si část [přední dveře](../frontdoor/front-door-overview.md).
> * Pro vyrovnávání zatížení síťové vrstvy si přečtěte [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Vaše ucelené scénáře můžou v případě potřeby těžit z kombinace těchto řešení.
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="features"></a>Funkce

Další informace o funkcích Application Gateway najdete v tématu [funkce Azure Application Gateway](features.md).

## <a name="pricing-and-sla"></a>Ceny a smlouvy SLA

Informace o cenách Application Gateway najdete v tématu [Application Gateway ceny](https://azure.microsoft.com/pricing/details/application-gateway/).

Informace o Application Gateway smlouvě SLA najdete v tématu [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Novinky

Informace o tom, co je nového u Azure Application Gateway, najdete v tématu [Aktualizace Azure](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Další kroky

V závislosti na vašich požadavcích a prostředí můžete vytvořit testovací Application Gateway pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.

- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI](quick-create-cli.md)