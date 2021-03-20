---
title: Informace o branách redundantní virtuální sítě v zóně Zóny dostupnosti Azure
description: Nasaďte brány VPN a ExpressRoute v Zóny dostupnosti Azure a zajistěte tak odolnost, škálovatelnost a vyšší dostupnost bran virtuální sítě.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89401108"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informace o branách redundantní virtuální sítě v zóně Zóny dostupnosti Azure

V [zóny dostupnosti Azure](../availability-zones/az-overview.md)můžete nasadit brány VPN a ExpressRoute. To přináší odolnost proti chybám, škálovatelnost a vyšší dostupnost bran virtuálních sítí. Nasazování bran v rámci Zón dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, přičemž zároveň chrání připojení vaší místní sítě k Azure před výpadky na úrovni zóny.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zónově redundantní brány

Pokud chcete automaticky nasadit brány virtuální sítě napříč zónami dostupnosti, můžete použít brány virtuální sítě redundantní v zóně. U redundantních bran v zóně můžete využívat výhody zóny – odolnost pro přístup k důležitým a škálovatelným službám v Azure.

<br>
<br>

![grafika – redundantní brány – grafika](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Brány pro oblast

Chcete-li nasadit brány v určité zóně, můžete použít brány oblastí. Když nasadíte bránu pro oblast, budou všechny instance brány nasazené ve stejné zóně dostupnosti.

<br>
<br>

![grafika v bráně oblast](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Skladové položky brány

Brány redundantní a oblasti oblastí jsou k dispozici jako nové SKU brány. Přidali jsme nové skladové položky brány virtuální sítě v Azure v oblastech AZ. Tyto SKU jsou podobné odpovídajícím existujícím SKU pro ExpressRoute a VPN Gateway s tím rozdílem, že jsou specifické pro brány redundantní v zóně a oblasti. Tyto SKU můžete identifikovat pomocí příkazu AZ v názvu SKU.

Informace o SKU brány najdete v tématu SKU [brány VPN](vpn-gateway-about-vpngateways.md#gwsku) a [SKU brány ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>SKU veřejných IP adres

Sítě redundantní brány a brány oblastí jsou závislé na *standardu* SKU prostředků veřejné IP adresy Azure. Konfigurace prostředku veřejné IP adresy Azure určuje, jestli je brána, kterou nasazujete, redundantní v zóně nebo v oblasti. Pokud vytvoříte prostředek veřejné IP adresy se *základní* skladovou jednotkou, brána nebude mít žádnou redundanci zóny a prostředky brány budou regionální.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zónově redundantní brány

Když vytvoříte veřejnou IP adresu pomocí **standardní** SKU veřejné IP adresy, aniž byste určili zónu, chování se liší v závislosti na tom, jestli je brána bránou VPN nebo bránou ExpressRoute. 

* Pro bránu sítě VPN se tyto dvě instance brány nasadí do každé 2 z těchto tří zón, aby se zajistila redundance zóny. 
* Pro bránu ExpressRoute, protože může být víc než dvě instance, může Brána zahrnovat celou tři zóny.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Brány pro oblast

Když vytvoříte veřejnou IP adresu pomocí **standardní** SKU veřejné IP adresy a zadáte zónu (1, 2 nebo 3), všechny instance brány se nasadí ve stejné zóně.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionální brány

Když vytvoříte veřejnou IP adresu pomocí **základní** veřejné IP adresy (SKU), brána se nasadí jako místní brána a nebude mít do brány vestavěnou redundanci zón.

## <a name="faq"></a><a name="faq"></a>Časté otázky

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní při nasazení těchto nových SKU?

Z perspektivy můžete nasadit brány s využitím redundance zón. To znamená, že všechny instance bran budou nasazeny v rámci Zóny dostupnosti Azure a každá zóna dostupnosti je jinou chybou a aktualizační doménou. Díky tomu jsou brány spolehlivější, dostupné a odolné vůči selháním zón.

### <a name="can-i-use-the-azure-portal"></a>Můžu použít Azure Portal?

Ano, můžete použít Azure Portal k nasazení nových SKU. Tyto nové SKU se ale zobrazí jenom v oblastech Azure, které mají Zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jaké oblasti máte k dispozici pro použití nových SKU?

Nové SKU jsou dostupné v oblastech Azure, které mají Zóny dostupnosti Azure-Střed USA, Francii Central, Severní Evropa, Západní Evropa a Západní USA 2 oblasti, Východní USA, Východní USA 2, jihovýchodní Asie, Japonsko – východ, Velká Británie – jih. V dalších veřejných oblastech Azure budeme mít k dispozici redundantní brány pro zóny, které máte k dispozici.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžu změnit/migrovat nebo upgradovat stávající brány virtuální sítě na brány, které mají zóny redundantní nebo rozbrané?

Migrace stávajících bran virtuální sítě na brány, které nejsou v současné době podporované, se nepodporují. Můžete ale odstranit stávající bránu a znovu vytvořit bránu, která je nadbytečná nebo se zónou.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžu ve stejné virtuální síti nasazovat brány sítě VPN i Express?

Podporuje se souběžná existence bran sítě VPN i Express Route ve stejné virtuální síti. Měli byste si však vyhradit rozsah IP adres a/27 pro podsíť brány.

## <a name="next-steps"></a>Další kroky

[Vytvoření zónově redundantní brány virtuální sítě](create-zone-redundant-vnet-gateway.md)
