---
title: 'Azure ExpressRoute: přidání brány do virtuální sítě: portál'
description: Tento článek vás provede procesem přidání brány virtuální sítě do již vytvořené Správce prostředků VNet pro ExpressRoute pomocí Azure Portal.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388081"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí webu Azure portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání brány virtuální sítě pro už existující virtuální síť. Tento článek vás provede kroky k přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Tady jsou kroky pro tuto konfiguraci speciálně pro virtuální sítě vytvořené pomocí modelu nasazení Resource Manageru, který se použije v konfiguraci služby ExpressRoute. Další informace o branách virtuální sítě a nastaveních konfigurace brány pro ExpressRoute najdete v tématu [informace o branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením

Kroky pro tuto úlohu použijte na základě hodnot v seznamu následující odkaz Konfigurace virtuální sítě. Tento seznam použijeme v našem příkladu krocích. Můžete zkopírovat seznam, aby používal jako odkaz, nahraďte hodnoty vlastními.

**Seznam odkazů konfigurace**

* Název virtuální sítě = "TestVNet"
* Adresní prostor virtuální sítě = 192.168.0.0/16
* Název podsítě = "FrontEnd" 
    * Adresní prostor podsítě = "192.168.1.0/24"
* Skupina prostředků = "TestRG"
* Umístění = "East US"
* Název podsítě brány: "GatewaySubnet" musíte vždycky pojmenovat podsíť brány *GatewaySubnet*.
    * Adresní prostor podsítě brány = "192.168.200.0/26"
* Název brány = "ERGW"
* Název veřejné IP adresy brány = "MyERGWVIP"
* Typ brány "ExpressRoute" = Tento typ je požadován pro konfiguraci služby ExpressRoute.

Před zahájením konfigurace si můžete zobrazit [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) o těchto krocích.

## <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na [portálu](https://portal.azure.com) přejděte na virtuální síť Resource Manageru, pro kterou chcete vytvořit bránu virtuální sítě.
2. V okně vaší virtuální sítě v části **Nastavení** klikněte na **Podsítě** a rozbalte okno Podsítě.
3. V okně **Podsítě** kliknutím na **+Podsíť brány** otevřete okno **Přidat podsíť**. 
   
    ![Přidat podsíť brány](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Přidání podsítě brány")


4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Doporučujeme vytvořit podsíť brány/27 nebo větší (/ 26, / 25 atd.). Pak kliknutím na **OK** uložte hodnoty a vytvořte podsíť brány.

    ![Přidání podsítě](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Přidání podsítě")

## <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na levé straně portálu klikněte na **+** a do vyhledávacího pole zadejte text „Brána virtuální sítě“. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. V dolní části okna **Brána virtuální sítě** klikněte na **Vytvořit**. Tím otevřete okno **Vytvořit bránu virtuální sítě**.
2. V okně **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

    ![Okno vytvořit bránu virtuální sítě](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Vytvoření polí okna brány virtuální sítě")
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
14. Kliknutím na **Vytvořit** zahajte proces vytváření brány. Nastavení se ověří a provede se nasazení brány. Vytvoření brány virtuální sítě může trvat až 45 minut.

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Viz [propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
