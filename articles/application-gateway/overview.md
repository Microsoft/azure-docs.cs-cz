---
title: Co je Azure Application Gateway?
description: Přečtěte si, jak můžete pomocí aplikační brány Azure spravovat webové přenosy do vaší aplikace.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78302572"
---
# <a name="what-is-azure-application-gateway"></a>Co je Azure Application Gateway?

Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Nástroje pro vyrovnávání zatížení obvykle fungují na přenosové vrstvě (vrstva OSI 4 – TCP a UDP) a směrují provoz na základě zdrojové IP adresy a portu do cílové IP adresy a portu.

Aplikační brána může provádět rozhodování o směrování na základě dalších atributů požadavku HTTP, například cesty URI nebo záhlaví hostitele. Můžete například směrovat provoz na základě příchozí adresy URL. Pokud tedy příchozí adresa URL obsahuje `/images`, můžete směrovat provoz na konkrétní sadu serverů (označovanou jako fond), která je nakonfigurovaná pro obrázky. Pokud `/video` je v adrese URL, tento provoz je směrován do jiného fondu, který je optimalizován pro videa.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Tento typ směrování se označuje jako vyrovnávání zatížení aplikační vrstvy (vrstva OSI 7). Azure Application Gateway umí směrování na základě adresy URL, ale to není vše.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud potřebujete vysoce výkonné vyrovnávání zatížení vrstvy 4 s nízkou latencí, přečtěte si část [Co je nástroj pro vyrovnávání zatížení Azure?](../load-balancer/load-balancer-overview.md) Pokud hledáte globální vyrovnávání zatížení DNS, přečtěte si část [Co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Kombinace těchto řešení může mít prospěch z jejich komplexních scénářů.
>
> Porovnání možností vyrovnávání zatížení Azure najdete [v tématu Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funkce

Další informace o funkcích brány aplikací najdete [v tématu Funkce Brány aplikací Azure](features.md).

## <a name="pricing-and-sla"></a>Ceny a SLA

Informace o cenách application gateway najdete v [tématu Ceny aplikační brány](https://azure.microsoft.com/pricing/details/application-gateway/).

Informace o sla aplikační brány naleznete v [tématu SLA aplikační brány](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Další kroky

V závislosti na vašich požadavcích a prostředí můžete vytvořit testovací aplikační bránu pomocí portálu Azure, Azure PowerShellu nebo Azure CLI.

- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI](quick-create-cli.md)
