---
title: Přesunout veřejný partnerský vztah na Azure ExpressRoute pro partnerský vztah Microsoftu | Dokumentace Microsoftu
description: Tento článek ukazuje postup přesunutí váš veřejný partnerský vztah Microsoftu na ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 579f8874459004ef6bfa0d0794ab09333e053acb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966106"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Přesunout veřejného partnerského vztahu k partnerskému vztahu Microsoftu

ExpressRoute podporuje použití partnerských vztahů s filtry tras pro služby Azure PaaS, jako je Azure Storage a Azure SQL Database. Teď potřebujete k přístupu ke službám PaaS a SaaS Microsoftu jenom jednu doménu směrování. Pomocí filtrů tras můžete selektivně inzerovat předpony služby PaaS do oblastí Azure, které chcete používat.

Tento článek pomáhá přesunout konfiguraci veřejného partnerského vztahu Microsoftu partnerského vztahu bez výpadků. Další informace o směrování domény a vztahy, naleznete v tématu [ExpressRoute okruzích a doménách směrování](expressroute-circuit-peerings.md).


## <a name="before"></a>Než začnete

* Pro připojení k partnerskému vztahu Microsoftu, budete muset nastavit a spravovat NAT. Váš poskytovatel připojení může nastavit a spravovat NAT jako spravovanou službu. Pokud máte v úmyslu přístup k Azure PaaS a Azure SaaS services na partnerský vztah Microsoftu, je důležité pro nastavení velikosti fondu adres IP pro NAT správně. Další informace o NAT pro ExpressRoute najdete v článku [požadavky NAT pro partnerský vztah Microsoftu](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Pokud používáte veřejný partnerský vztah a aktuálně máte pravidel sítě protokolu IP pro veřejné IP adresy, které se používají pro přístup k [služby Azure Storage](../storage/common/storage-network-security.md) nebo [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), budete potřebovat, abyste měli jistotu, že fond IP adres NAT nakonfigurovat s Microsoftem partnerského vztahu je součástí seznamu veřejné IP adresy pro účet služby Azure storage nebo účet služby Azure SQL.

* Aby bylo možné přesunout do partnerského vztahu bez výpadků Microsoftu, postupujte podle kroků v tomto článku v pořadí, ve kterém jsou uvedeny.

## <a name="create"></a>1. Vytvoření partnerského vztahu Microsoftu

Pokud ještě nebyl vytvořen partnerský vztah Microsoftu, použijte některý z následujících článků vytvoření partnerského vztahu Microsoftu. Pokud spravované nabídek poskytovatele připojení vrstvy 3 služby, požádejte poskytovatele připojení, povolte partnerského vztahu Microsoftu pro váš okruh.

  * [Vytvoření partnerského vztahu Microsoftu pomocí webu Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Vytvoření partnerského vztahu Microsoftu s využitím Azure Powershellu](expressroute-howto-routing-arm.md#msft)
  * [Vytvoření partnerského vztahu Microsoftu pomocí rozhraní příkazového řádku Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Ověření Microsoft partnerského vztahu je povoleno

Ověřte, že partnerského vztahu Microsoftu je povoleno a inzerované veřejné předpony jsou ve stavu nakonfigurované.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Nakonfigurujete a připojíte filtr tras k okruhu

Ve výchozím nastavení nastavení nové partnerské vztahy Microsoftu neprovádějí všechny předpony, dokud se filtr tras je připojen k okruhu. Když vytvoříte pravidlo filtru tras, můžete zadat seznam komunit služeb pro oblasti Azure, které chcete používat pro služby Azure PaaS, jak je znázorněno na následujícím snímku obrazovky:

![Sloučit veřejného partnerského vztahu](./media/how-to-move-peering/public.png)

Konfigurace filtrů směrování pomocí některého z následujících článků:

  * [Konfigurace filtrů směrování pro partnerský vztah Microsoftu s využitím webu Azure portal](how-to-routefilter-portal.md)
  * [Konfigurace filtrů směrování pro partnerský vztah Microsoftu s využitím Azure Powershellu](how-to-routefilter-powershell.md)
  * [Konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Odstranění veřejného partnerského vztahu

Po ověření, že je nakonfigurován partnerský vztah Microsoftu a předpony, které chcete využívat jsou správně inzerované na partnerský vztah Microsoftu, pak můžete odstranit veřejný partnerský vztah. K odstranění veřejného partnerského vztahu, použijte některou z následujících článcích:

  * [Odstranění veřejného partnerského vztahu Azure pomocí webu Azure portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Odstranění veřejného partnerského vztahu Azure pomocí Azure Powershellu](expressroute-howto-routing-arm.md#deletepublic)
  * [Odstranění veřejného partnerského vztahu Azure pomocí rozhraní příkazového řádku](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Zobrazení partnerských vztahů
  
Zobrazí se seznam všech okruhy ExpressRoute a partnerské vztahy na webu Azure Portal. Další informace najdete v tématu [podrobností partnerského vztahu Microsoftu zobrazení](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
