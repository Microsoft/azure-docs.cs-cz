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
ms.openlocfilehash: 52bc2a20be0d7113583b7f23cc569dbb9503d364
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397242"
---
# <a name="what-is-azure-application-gateway"></a>Co je Azure Application Gateway?

Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Nástroje pro vyrovnávání zatížení obvykle fungují na přenosové vrstvě (vrstva OSI 4 – TCP a UDP) a směrují provoz na základě zdrojové IP adresy a portu do cílové IP adresy a portu.

Application Gateway mohou provádět rozhodování o směrování na základě dalších atributů požadavku HTTP, například cesty k identifikátoru URI nebo hlaviček hostitele. Můžete například směrovat provoz na základě příchozí adresy URL. Pokud tedy příchozí adresa URL obsahuje `/images`, můžete směrovat provoz na konkrétní sadu serverů (označovanou jako fond), která je nakonfigurovaná pro obrázky. Pokud `/video` je v adrese URL, provoz se směruje do jiného fondu optimalizovaného pro videa.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Tento typ směrování se označuje jako vyrovnávání zatížení aplikační vrstvy (vrstva OSI 7). Azure Application Gateway umí směrování na základě adresy URL, ale to není vše.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud potřebujete vysoce výkonné, nízké latence, Vyrovnávání zatížení vrstvy 4, přečtěte si téma [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Pokud hledáte globální vyrovnávání zatížení DNS, přečtěte si téma [co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Vaše ucelené scénáře můžou využít kombinaci těchto řešení.
>
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