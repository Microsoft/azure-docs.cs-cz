---
title: Informace o zónově redundantních privátních síťových branách v zónách dostupnosti Azure
description: Další informace o branách VPN Gateway a ExpressRoute v zónách dostupnosti.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864294"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informace o zónově redundantních privátních síťových branách v zónách dostupnosti Azure

Brány VPN a ExpressRoute můžete nasadit v [zónách dostupnosti Azure](../availability-zones/az-overview.md). To přináší odolnost proti chybám, škálovatelnost a vyšší dostupnost bran virtuálních sítí. Nasazování bran v rámci Zón dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, přičemž zároveň chrání připojení vaší místní sítě k Azure před výpadky na úrovni zóny.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zónově redundantní brány

Chcete-li automaticky nasadit brány virtuální sítě napříč zónami dostupnosti, můžete použít zóny redundantní brány virtuální sítě. Díky zónově redundantním bránám můžete využívat odolnost zón pro přístup k důležitým a škálovatelným službám v Azure.

<br>
<br>

![zónově redundantní brány – grafika](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zonální brány

Chcete-li nasadit brány v určité zóně, můžete použít zónové brány. Když nasadíte zónovou bránu, všechny instance brány jsou nasazeny ve stejné zóně dostupnosti.

<br>
<br>

![zonální brány – grafika](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKU brány

Zónově redundantní a zónové brány jsou k dispozici jako nové brány sloky brány. Přidali jsme nové virtuální síťové brány skutáleny v oblastech Azure AZ. Tyto sloky jsou podobné odpovídající existující sku pro ExpressRoute a VPN gateway, s tím rozdílem, že jsou specifické pro zóny redundantní a zónonální brány. Tyto skladové položky můžete identifikovat podle "AZ" v názvu skladové položky.

Informace o su su brány naleznete v tématu [VPN gateway SKU](vpn-gateway-about-vpngateways.md#gwsku) a [ExpressRoute gateway SKU](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Veřejná IP SKUs

Zóny redundantní brány a zónové brány spoléhají na standardní skladovou položku *pro* veřejné IP prostředky Azure. Konfigurace prostředku veřejné IP azure určuje, zda je brána, kterou nasadíte, zónově redundantní nebo zónová. Pokud vytvoříte veřejný prostředek IP se *základní* skladovou položkou, brána nebude mít žádnou redundanci zóny a prostředky brány budou místní.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zónově redundantní brány

Když vytvoříte veřejnou IP adresu pomocí **standardní** veřejné ip skladové položky bez určení zóny, chování se liší v závislosti na tom, zda je brána VPN brána nebo brána ExpressRoute. 

* Pro bránu VPN budou dvě instance brány nasazeny v libovolném 2 z těchto tří zón, aby byla zajištěna redundance zóny. 
* Pro bránu ExpressRoute, protože může existovat více než dvě instance, brána může protábovat všechny tři zóny.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zonální brány

Když vytvoříte veřejnou IP adresu pomocí **standardní** veřejné IP skladové položky a určíte zónu (1, 2 nebo 3), všechny instance brány budou nasazeny ve stejné zóně.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionální brány

Když vytvoříte veřejnou IP adresu pomocí **základní** veřejné IP skladové položky, brána se nasadí jako místní brána a nemá do brány zabudovanou žádnou redundanci zóny.

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní, když nasadím tyto nové souku?

Z vašeho pohledu můžete nasadit brány s redundancí zóny. To znamená, že všechny instance bran se nasadí napříč zónami dostupnosti Azure a každá zóna dostupnosti je jiná doména poruchy a aktualizace. Díky tomu jsou vaše brány spolehlivější, dostupnější a odolnější vůči selhání zóny.

### <a name="can-i-use-the-azure-portal"></a>Můžu používat portál Azure?

Ano, na webu Azure Portal můžete nasadit nové sku- tu. Tyto nové souny však uvidíte pouze v těch oblastech Azure, které mají zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jaké oblasti jsou k dispozici pro použití nových skutých.

Nové skum jsou dostupné v oblastech Azure, které mají zóny dostupnosti Azure – střední USA, Francie – střed, Severní Evropa, Západní Evropa a Západní USA 2 oblasti, Východní USA, Východní USA 2, Jihovýchodní Asie, Japonsko – východ, Velká Británie – jih. Do budoucna vám zpřístupníme zóny redundantní brány v jiných veřejných oblastech Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžu změnit/migrovat/upgradovat své stávající brány virtuální sítě na zónově redundantní nebo zónové brány?

Migrace existujících bran virtuální sítě do zónově redundantních nebo zónových bran není momentálně podporována. Můžete však odstranit existující bránu a znovu vytvořit zónovou nebo zónovou bránu.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžu nasadit brány VPN i Express Route ve stejné virtuální síti?

Koexistence bran VPN i Express Route ve stejné virtuální síti je podporována. Měli byste však rezervovat rozsah IP adres /27 pro podsíť brány.

## <a name="next-steps"></a>Další kroky

[Vytvoření zónově redundantní brány virtuální sítě](create-zone-redundant-vnet-gateway.md)
