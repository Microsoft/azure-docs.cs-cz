---
title: 'Azure ExpressRoute: přidání brány do virtuální sítě: portál'
description: Tento článek vás provede procesem přidání brány virtuální sítě do již vytvořené Správce prostředků VNet pro ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 06f7e5d28017ee618adfeeec52c6f1226e1ae82c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396349"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí Azure Portal
> [!div class="op_single_selector"]
> * [Správce prostředků – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede jednotlivými kroky přidání brány virtuální sítě pro existující virtuální síť. Tento článek vás provede kroky pro přidání, změnu velikosti a odebrání brány virtuální sítě (VNet) pro stávající virtuální síť. Kroky pro tuto konfiguraci jsou určené konkrétně pro virtuální sítě vytvořené pomocí modelu nasazení Správce prostředků, který se použije v konfiguraci ExpressRoute. Další informace o branách virtuální sítě a nastaveních konfigurace brány pro ExpressRoute najdete v tématu [informace o branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením

Postup pro tuto úlohu používá virtuální síť na základě hodnot v následujícím seznamu odkazů konfigurace. Tento seznam používáme v našem ukázkovém postupu. Seznam můžete zkopírovat pro použití jako odkaz a nahradit hodnoty vlastními.

**Seznam odkazů konfigurace**

* Virtual Network název = "TestVNet"
* Virtual Network adresního prostoru = 192.168.0.0/16
* Název podsítě = "front-end" 
    * Adresní prostor podsítě = "192.168.1.0/24"
* Skupina prostředků = "TestRG"
* Location = "Východní USA"
* Název podsítě brány: "GatewaySubnet" musíte vždycky pojmenovat podsíť brány *GatewaySubnet*.
    * Adresní prostor podsítě brány = "192.168.200.0/26"
* Název brány = "ERGW"
* Název veřejné IP adresy brány = "MyERGWVIP"
* Typ brány = "ExpressRoute" Tento typ je vyžadován pro konfiguraci ExpressRoute.

Před zahájením konfigurace si můžete zobrazit [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) o těchto krocích.

## <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na [portálu](https://portal.azure.com)přejděte na virtuální síť správce prostředků, pro kterou chcete vytvořit bránu virtuální sítě.
2. V okně vaší virtuální sítě v části **Nastavení** klikněte na **Podsítě** a rozbalte okno Podsítě.
3. V okně **Podsítě** kliknutím na **+Podsíť brány** otevřete okno **Přidat podsíť**. 
   
    ![Přidání podsítě brány](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Přidání podsítě brány")


4. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **Rozsah adres** tak, aby odpovídaly požadavkům vaší konfigurace. Doporučujeme vytvořit podsíť brány s rozmezím/27 nebo větším (/26,/25 atd.). Pak kliknutím na **OK** uložte hodnoty a vytvořte podsíť brány.

    ![Přidání podsítě](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Přidání podsítě")

## <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na portálu klikněte na levé straně na **+** a do pole Hledat zadejte ' Virtual Network Gateway '. Ve výsledcích hledání vyhledejte položku **Brána virtuální sítě** a klikněte na ni. V dolní části okna **Brána virtuální sítě** klikněte na **Vytvořit**. Tím otevřete okno **Vytvořit bránu virtuální sítě**.
2. V okně **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

    ![Vytvoření polí okna brány virtuální sítě](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Vytvoření polí okna brány virtuální sítě")
3. **Název**: Zadejte pro bránu název. Toto není název podsítě brány. Jedná se o název objektu brány, který vytváříte.
4. **Typ brány**: vyberte **ExpressRoute**.
5. **SKU**: Z rozevíracího seznamu vyberte SKU brány.
6. **Umístění**: Upravte pole **Umístění** tak, aby odkazovalo na umístění, ve kterém se nachází vaše virtuální síť. Pokud umístění neukazuje na oblast, ve které je vaše virtuální síť umístěná, tato síť se nezobrazí v rozevírací nabídce Zvolit virtuální síť.
7. Zvolte virtuální síť, do které chcete tuto bránu přidat. Kliknutím na **virtuální síť** otevřete okno **zvolit virtuální síť** . Vyberte virtuální síť. Pokud svou virtuální síť nevidíte, ujistěte se, že pole **umístění** odkazuje na oblast, ve které se nachází vaše virtuální síť.
9. Zvolte veřejnou IP adresu. Kliknutím na **Veřejná IP adresa** otevřete okno **Zvolte veřejnou IP adresu**. Kliknutím na **+Vytvořit novou** otevřete okno **Vytvořit veřejnou IP adresu**. Zadejte název veřejné IP adresy. Prostřednictvím tohoto okna můžete vytvořit objekt veřejné IP adresy, ke kterému bude dynamicky přiřazena veřejná IP adresa. Kliknutím na tlačítko **OK** uložte změny v tomto okně.
10. **Předplatné**: Zkontrolujte, jestli je vybráno správné předplatné.
11. **Skupina prostředků**: Toto nastavení je určeno vámi vybranou virtuální sítí.
12. Po zadání předchozích nastavení už hodnotu **Umístění** neupravujte.
13. Ověřte nastavení. Pokud chcete, aby se brána zobrazovala na řídicím panelu, můžete v dolní části okna vybrat **Připnout na řídicí panel**.
14. Kliknutím na **Vytvořit** zahajte proces vytváření brány. Nastavení se ověří a provede se nasazení brány. Dokončení vytváření brány virtuální sítě může trvat až 45 minut.

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete připojit virtuální síť k okruhu ExpressRoute. Viz [propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
