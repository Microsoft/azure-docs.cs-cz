---
title: 'Azure ExpressRoute: Přidání brány do virtuální sítě: portál'
description: Tento článek vás provede přidáním brány virtuální sítě do již vytvořené virtuální sítě Správce prostředků pro ExpressRoute pomocí portálu Azure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264827"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Správce prostředků – portál Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání brány virtuální sítě pro již existující virtuální síť. Tento článek vás provede kroky pro přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro již existující virtuální síť. Kroky pro tuto konfiguraci jsou speciálně pro virtuální sítě, které byly vytvořeny pomocí modelu nasazení Resource Manager, který se bude používat v konfiguraci ExpressRoute. Další informace o branách virtuálnísítě a nastavení konfigurace brány pro ExpressRoute najdete v [tématu O branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením

Kroky pro tuto úlohu používají virtuální síť na základě hodnot v následujícím seznamu odkazů na konfiguraci. Tento seznam používáme v našich příkladech kroků. Seznam můžete zkopírovat a použít jako odkaz a nahradit je vlastními hodnotami.

**Seznam odkazů na konfiguraci**

* Název virtuální sítě = "Testvnet"
* Adresní prostor virtuální sítě = 192.168.0.0/16
* Název podsítě = "FrontEnd" 
    * Adresní prostor podsítě = "192.168.1.0/24"
* Skupina prostředků = "TestRG"
* Umístění = "Východní USA"
* Název podsítě brány: "GatewaySubnet" Vždy musíte pojmenovat gateway podsíť *GatewaySubnet*.
    * Adresní prostor podsítě brány = "192.168.200.0/26"
* Název brány = "ERGW"
* Název veřejné IP brány = "MyERGWVIP"
* Typ brány = "ExpressRoute" Tento typ je vyžadován pro konfiguraci ExpressRoute.

Můžete zobrazit [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) z těchto kroků před zahájením konfigurace.

## <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na [portálu](https://portal.azure.com)přejděte do virtuální sítě Správce prostředků, pro kterou chcete vytvořit bránu virtuální sítě.
2. V okně vaší virtuální sítě v části **Nastavení** klikněte na **Podsítě** a rozbalte okno Podsítě.
3. V okně **Podsítě** kliknutím na **+Podsíť brány** otevřete okno **Přidat podsíť**. 
   
    ![Přidání podsítě brány](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Přidání podsítě brány")


4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Doporučujeme vytvořit podsíť brány s /27 nebo větší (/26, /25 atd.). Potom kliknutím na **OK** uložte hodnoty a vytvořte podsíť brány.

    ![Přidání podsítě](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Přidání podsítě")

## <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na portálu, na levé **+** straně, klikněte a zadejte 'Brána virtuální sítě' ve vyhledávání. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. V dolní části okna **Brána virtuální sítě** klikněte na **Vytvořit**. Tím otevřete okno **Vytvořit bránu virtuální sítě**.
2. V okně **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

    ![Vytvoření polí okna brány virtuální sítě](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Vytvoření polí okna brány virtuální sítě")
3. **Název**: Zadejte pro bránu název. Toto není název podsítě brány. Jedná se o název objektu brány, který vytváříte.
4. **Typ brány**: Vyberte **ExpressRoute**.
5. **SKU**: Z rozevíracího seznamu vyberte SKU brány.
6. **Umístění**: Upravte pole **Umístění** tak, aby odkazovalo na umístění, ve kterém se nachází vaše virtuální síť. Pokud umístění neukazuje na oblast, ve které je vaše virtuální síť umístěná, tato síť se nezobrazí v rozevírací nabídce Zvolit virtuální síť.
7. Zvolte virtuální síť, do které chcete tuto bránu přidat. Kliknutím na **Virtuální síť** otevřete okno Zvolit **virtuální síť.** Vyberte virtuální síť. Pokud virtuální síť nevidíte, ujistěte se, že pole **Umístění** ukazuje na oblast, ve které se virtuální síť nachází.
9. Zvolte veřejnou IP adresu. Kliknutím na **Veřejná IP adresa** otevřete okno **Zvolte veřejnou IP adresu**. Kliknutím na **+Vytvořit novou** otevřete okno **Vytvořit veřejnou IP adresu**. Zadejte název veřejné IP adresy. Prostřednictvím tohoto okna můžete vytvořit objekt veřejné IP adresy, ke kterému bude dynamicky přiřazena veřejná IP adresa. Kliknutím na tlačítko **OK** uložte změny v tomto okně.
10. **Předplatné**: Zkontrolujte, jestli je vybráno správné předplatné.
11. **Skupina prostředků**: Toto nastavení je určeno vámi vybranou virtuální sítí.
12. Po zadání předchozích nastavení už hodnotu **Umístění** neupravujte.
13. Ověřte nastavení. Pokud chcete, aby se brána zobrazovala na řídicím panelu, můžete v dolní části okna vybrat **Připnout na řídicí panel**.
14. Kliknutím na **Vytvořit** zahajte proces vytváření brány. Nastavení se ověří a provede se nasazení brány. Vytvoření brány virtuální sítě může trvat až 45 minut.

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete virtuální síť propojit s okruhem ExpressRoute. Viz [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
