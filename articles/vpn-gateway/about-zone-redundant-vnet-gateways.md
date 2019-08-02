---
title: Informace o branách redundantní virtuální sítě v zóně Zóny dostupnosti Azure | Microsoft Docs
description: Seznamte se s VPN Gateway a ExpressRoute branami v Zóny dostupnosti.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: d076e2b0057f0ba666fa47ffd0b3d7d1fcc14631
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725584"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informace o branách redundantní virtuální sítě v zóně Zóny dostupnosti Azure

V [zóny dostupnosti Azure](../availability-zones/az-overview.md)můžete nasadit brány VPN a ExpressRoute. Tím se zvýší odolnost, škálovatelnost a vyšší dostupnost bran virtuální sítě. Nasazování bran v Zóny dostupnosti Azure fyzicky a logicky odděluje brány v rámci určité oblasti a zároveň chrání vaše místní síťové připojení k Azure ze selhání na úrovni zóny.

### <a name="zrgw"></a>Redundantní brány v zóně

Pokud chcete automaticky nasadit brány virtuální sítě napříč zónami dostupnosti, můžete použít brány virtuální sítě redundantní v zóně. U redundantních bran v zóně můžete využívat výhody zóny – odolnost pro přístup k důležitým a škálovatelným službám v Azure.

<br>
<br>

![grafika – redundantní brány – grafika](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Brány pro oblast

Chcete-li nasadit brány v určité zóně, můžete použít brány oblastí. Když nasadíte bránu pro oblast, budou všechny instance brány nasazené ve stejné zóně dostupnosti.

<br>
<br>

![grafika v bráně oblast](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU brány

Brány redundantní a oblasti oblastí jsou k dispozici jako nové SKU brány. Přidali jsme nové skladové položky brány virtuální sítě v Azure v oblastech AZ. Tyto SKU jsou podobné odpovídajícím existujícím SKU pro ExpressRoute a VPN Gateway s tím rozdílem, že jsou specifické pro brány redundantní v zóně a oblasti.

Nové SKU brány:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKU veřejných IP adres

Sítě redundantní brány a brány oblastí jsou závislé na standardu SKU prostředků veřejné IP adresy Azure. Konfigurace prostředku veřejné IP adresy Azure určuje, jestli je brána, kterou nasazujete, redundantní v zóně nebo v oblasti. Pokud vytvoříte prostředek veřejné IP adresy se *základní* skladovou jednotkou, brána nebude mít žádnou redundanci zóny a prostředky brány budou regionální.

### <a name="pipzrg"></a>Redundantní brány v zóně

Když vytvoříte veřejnou IP adresu pomocí **standardní** SKU veřejné IP adresy, aniž byste určili zónu, chování se liší v závislosti na tom, jestli je brána bránou VPN nebo bránou ExpressRoute. 

* Pro bránu sítě VPN se tyto dvě instance brány nasadí do každé 2 z těchto tří zón, aby se zajistila redundance zóny. 
* Pro bránu ExpressRoute, protože může být víc než dvě instance, může Brána zahrnovat celou tři zóny.

### <a name="pipzg"></a>Brány pro oblast

Když vytvoříte veřejnou IP adresu pomocí **standardní** SKU veřejné IP adresy a zadáte zónu (1, 2 nebo 3), všechny instance brány se nasadí ve stejné zóně.

### <a name="piprg"></a>Regionální brány

Když vytvoříte veřejnou IP adresu pomocí **základní** veřejné IP adresy (SKU), brána se nasadí jako místní brána a nebude mít do brány vestavěnou redundanci zón.

## <a name="faq"></a>Nejčastější dotazy

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
