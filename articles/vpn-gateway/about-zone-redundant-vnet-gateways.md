---
title: O branách zónově redundantní virtuálních sítí v zónách dostupnosti Azure | Dokumentace Microsoftu
description: Další informace o branách VPN Gateway nebo ExpressRoute v zónách dostupnosti.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 27bac5265a5e884b808c4ccb58fda0b2fffeb774
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975646"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>O branách zónově redundantní virtuálních sítí v zónách dostupnosti Azure

Nasazením brány sítě VPN a ExpressRoute v [zóny dostupnosti Azure](../availability-zones/az-overview.md). To přináší odolnost proti chybám, škálovatelnosti a vysoké dostupnosti do brány virtuální sítě. Nasazení brány v zónách dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, současně připojení k místní síti Azure byla chráněná před výpadky na úrovni zóny.

### <a name="zrgw"></a>Zónově redundantní brány

Pokud chcete automaticky nasadit vaší brány virtuální sítě napříč zónami dostupnosti, zónově redundantní virtuální sítě brány můžete použít. Zónově redundantní brány vám může přinést odolnost zóny pro přístup k vaší kritických pro chod, škálovatelných služeb v Azure.

<br>
<br>

![Obrázek zóny redunant brány](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Oblastmi brány

Oblastmi brány můžete použít k nasazení brány v konkrétní zóně. Při nasazení oblastmi brány všechny instance brány jsou nasazené ve stejné zóně dostupnosti.

<br>
<br>

![Obrázek oblastmi brány](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU brány

Zónové a zónově redundantní brány jsou k dispozici jako nové SKU brány. Přidali jsme nové SKU brány virtuální sítě v Azure AZ oblastech. Tyto SKU jsou podobné odpovídající existující SKU pro ExpressRoute a VPN Gateway, s tím rozdílem, že jsou specifické pro zónové a zónově redundantní brány.

Nová brána se tyto skladové položky:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKU veřejné IP adresy

Zónově redundantní brány a zónové brány využívají Azure prostředek veřejné IP adresy *standardní* SKU. Konfigurace Azure prostředek veřejné IP adresy určuje, zda je brána, který nasadíte zónově redundantní, nebo oblastmi. Pokud vytvoříte prostředek s veřejnou IP *základní* SKU, brána nebude mít žádné redundanci zón a bude místní prostředky brány.

### <a name="pipzrg"></a>Zónově redundantní brány

Když vytvoříte a veřejné IP adresy s použitím **standardní** SKU veřejné IP bez zadání zónu, chování se liší v závislosti na tom, jestli je brána VPN gateway nebo ExpressRoute gateway. 

* Pro bránu sítě VPN se nasadí v aspoň 2 mimo tyto tři zóny poskytnout redundanci zón instance dvě brány. 
* Pro bránu ExpressRoute protože může existovat více než dvě instance, brána lze pracovat nad více všechny tři zóny.

### <a name="pipzg"></a>Oblastmi brány

Když vytvoříte a veřejné IP adresy s použitím **standardní** SKU veřejné IP a určení zóny (1, 2 nebo 3), všechny instance brány se nasadí ve stejné zóně.

### <a name="piprg"></a>Místní brány

Když vytvoříte a veřejné IP adresy s použitím **základní** veřejné IP SKU brány je nasazená jako místní brány a nemá žádné zóny redundance integrovaný do brány.

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní, když nasadím tyto nové SKU?

Z pohledu můžete nasadit bran s redundanci zón. To znamená, že všechny instance brány nasadí napříč zónami dostupnosti Azure a každá zóna dostupnosti je jiné selhání a aktualizační domény. Díky tomu vaše brány spolehlivé, dostupné a odolné vůči selhání zóny.

### <a name="can-i-use-the-azure-portal"></a>Můžete použít na webu Azure portal?

Ano, můžete na webu Azure portal k nasazení nové SKU. Nicméně zobrazí se tyto nové SKU pouze v těchto oblastech Azure, které mají zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Které oblasti jsou k dispozici pro mě nejlepší používat na nové SKU?

Nové SKU jsou k dispozici v oblasti Azure, které mají zóny dostupnosti Azure – USA (střed), Francie – střed a západní Evropa. Od této chvíle jsme zpřístupníte zónově redundantní brány je v jiných veřejných oblastech Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Je možné změnit/migrace a upgrade mé existující brány virtuální sítě k bránám zónové a zónově redundantní?

Migrace vaší existující brány virtuální sítě pro zónové a zónově redundantní brány není aktuálně podporováno. Můžete však odstraňte existující bránu a znovu vytvořit bránu zónové a zónově redundantní.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžete nasadit brány sítě VPN a Expressroute ve stejné virtuální síti?

Se podporuje koexistence bran VPN a Expressroute ve stejné virtuální síti. Nicméně, měli byste si rezervovat velikost/27 rozsah IP adres pro podsíť brány.

## <a name="next-steps"></a>Další kroky

[Vytvoření zónově redundantní brány virtuální sítě](create-zone-redundant-vnet-gateway.md)