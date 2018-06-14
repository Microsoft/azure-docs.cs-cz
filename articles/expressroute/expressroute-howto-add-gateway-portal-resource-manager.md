---
title: 'Přidat bránu virtuální sítě k virtuální síti pro ExpressRoute: portál: Azure | Microsoft Docs'
description: Tento článek vás provede přidáním bránu virtuální sítě k již vytvořené virtuální sítě Resource Manageru pro ExpressRoute.
documentationcenter: na
services: expressroute
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
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850865"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí webu Azure portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – prostředí PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání brány virtuální sítě pro už existující virtuální síť. Tento článek vás provede kroky k přidání, změně velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Kroky pro tuto konfiguraci jsou speciálně určené pro virtuální sítě vytvořené pomocí modelu nasazení Resource Manager, který se použije v konfiguraci ExpressRoute. Další informace o brány virtuální sítě a nastavení konfigurace brány ExpressRoute najdete v tématu [o brány virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením

Kroky pro tuto úlohu použijte virtuální sítě na základě hodnot v následujícím seznamu odkaz konfigurace. V našem příkladu kroky jsme pomocí tohoto seznamu. Seznam, aby používal jako odkaz, můžete zkopírovat nahraďte hodnoty vlastními.

**Seznam odkazů konfigurace**

* Název virtuální sítě = "TestVNet"
* Virtuální adresní prostor sítě = 192.168.0.0/16
* Název podsítě = "FrontEnd" 
    * Adresního prostoru podsítě = "192.168.1.0/24"
* Skupina prostředků = "TestRG"
* Umístění = "East US"
* Název podsítě brány: "GatewaySubnet" název musí být vždy podsíť brány *GatewaySubnet*.
    * Adresního prostoru podsítě brány = "192.168.200.0/26"
* Název brány = "ERGW"
* Název brány IP = "MyERGWVIP"
* Typ brány = "ExpressRoute" Tento typ je požadovaná konfigurace ExpressRoute.
* Název veřejné IP adresy brány = "MyERGWVIP"

Můžete zobrazit [Video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) z těchto kroků před zahájením konfigurace.

## <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na [portálu](http://portal.azure.com) přejděte na virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.
2. V okně vaší virtuální sítě v části **Nastavení** klikněte na **Podsítě** a rozbalte okno Podsítě.
3. V okně **Podsítě** kliknutím na **+Podsíť brány** otevřete okno **Přidat podsíť**. 
   
    ![Přidání podsítě brány](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Přidání podsítě brány")


4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Doporučujeme vytvořit podsíť brány/27 nebo větší (/ 26, / 25 atd.). Potom klikněte na **OK** uložte hodnoty a vytvořit podsíť brány.

    ![Přidání podsítě](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Přidání podsítě")

## <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na levé straně portálu klikněte na **+** a do vyhledávacího pole zadejte text „Brána virtuální sítě“. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. V dolní části okna **Brána virtuální sítě** klikněte na **Vytvořit**. Tím otevřete okno **Vytvořit bránu virtuální sítě**.
2. V okně **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

    ![Pole v okně Vytvořit bránu virtuální sítě](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Pole v okně Vytvořit bránu virtuální sítě")
3. **Název**: Zadejte pro bránu název. Toto není název podsítě brány. Jedná se o název objektu brány, který vytváříte.
4. **Typ brány**: vyberte **ExpressRoute**.
5. **SKU**: Z rozevíracího seznamu vyberte SKU brány.
6. **Umístění**: Upravte pole **Umístění** tak, aby odkazovalo na umístění, ve kterém se nachází vaše virtuální síť. Pokud umístění neukazuje na oblast, ve které je vaše virtuální síť umístěná, tato síť se nezobrazí v rozevírací nabídce Zvolit virtuální síť.
7. Zvolte virtuální síť, do které chcete tuto bránu přidat. Klikněte na **Virtuální síť** a otevře se okno **Zvolit virtuální síť**. Vyberte virtuální síť. Pokud svou virtuální síť nevidíte, zkontrolujte, že pole **Umístění** odkazuje na oblast, ve které se nachází vaše virtuální síť.
9. Zvolte veřejnou IP adresu. Kliknutím na **Veřejná IP adresa** otevřete okno **Zvolte veřejnou IP adresu**. Kliknutím na **+Vytvořit novou** otevřete okno **Vytvořit veřejnou IP adresu**. Zadejte název veřejné IP adresy. Prostřednictvím tohoto okna můžete vytvořit objekt veřejné IP adresy, ke kterému bude dynamicky přiřazena veřejná IP adresa. Kliknutím na tlačítko **OK** uložte změny v tomto okně.
10. **Předplatné**: Zkontrolujte, jestli je vybráno správné předplatné.
11. **Skupina prostředků**: Toto nastavení je určeno vámi vybranou virtuální sítí.
12. Po zadání předchozích nastavení už hodnotu **Umístění** neupravujte.
13. Ověřte nastavení. Pokud chcete, aby se brána zobrazovala na řídicím panelu, můžete v dolní části okna vybrat **Připnout na řídicí panel**.
14. Kliknutím na **Vytvořit** zahajte proces vytváření brány. Nastavení se ověří a provede se nasazení brány. Vytvoření brány virtuální sítě může trvat až 45 minut na dokončení.

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě, můžete se propojit virtuální sítě k okruhu ExpressRoute. V tématu [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).