---
title: 'Kurz: Azure ExpressRoute – přidání brány do virtuální sítě (Azure Portal)'
description: Tento kurz vás provede procesem přidání brány virtuální sítě do virtuální sítě pro ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "91761776"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Kurz: Konfigurace brány virtuální sítě pro ExpressRoute pomocí Azure Portal
> [!div class="op_single_selector"]
> * [Správce prostředků – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Tento kurz vás provede jednotlivými kroky přidání brány virtuální sítě pro existující virtuální síť. Tento článek vás provede kroky pro přidání, změnu velikosti a odebrání brány virtuální sítě (VNet) pro stávající virtuální síť. Kroky pro tuto konfiguraci jsou určené konkrétně pro virtuální sítě vytvořené pomocí modelu nasazení Správce prostředků, který se použije v konfiguraci ExpressRoute. Další informace o branách virtuální sítě a nastaveních konfigurace brány pro ExpressRoute najdete v tématu [informace o branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořte podsíť brány.
> - Vytvořte bránu Virtual Network.

## <a name="prerequisites"></a>Předpoklady

Postup pro tuto úlohu používá virtuální síť na základě hodnot v následujícím seznamu odkazů konfigurace. Tento seznam používáme v našem ukázkovém postupu. Seznam můžete zkopírovat pro použití jako odkaz a nahradit hodnoty vlastními.

**Seznam odkazů konfigurace**

* Virtual Network název = "TestVNet"
* Virtual Network adresního prostoru = 192.168.0.0/16
* Název podsítě = "front-end" 
    * Adresní prostor podsítě = "192.168.1.0/24"
* Skupina prostředků = "TestRG"
* Location = "Východní USA"
* Název podsítě brány: "GatewaySubnet" musíte vždycky pojmenovat podsíť brány *GatewaySubnet* .
    * Adresní prostor podsítě brány = "192.168.200.0/26"
* Název brány = "ERGW"
* Název veřejné IP adresy brány = "MyERGWVIP"
* Typ brány = "ExpressRoute" Tento typ je vyžadován pro konfiguraci ExpressRoute.

Před zahájením konfigurace si můžete zobrazit [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) o těchto krocích.

## <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na [portálu](https://portal.azure.com)přejděte na virtuální síť správce prostředků, pro kterou chcete vytvořit bránu virtuální sítě.
1. V části **Nastavení** vaší virtuální sítě vyberte **podsítě** a rozbalte nastavení podsítě.
1. V nastavení **podsítě** vyberte **+ podsíť brány** a přidejte tak podsíť brány. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Přidání podsítě brány":::

1. **Název** podsítě se automaticky vyplní hodnotou GatewaySubnet. To je požadovaná hodnota, aby služba Azure podsíť rozpoznala jako podsíť brány. Upravte automaticky vyplněné hodnoty **rozsahu adres** tak, aby odpovídaly vašim požadavkům na konfiguraci. Doporučujeme vytvořit podsíť brány s příponou/27 nebo větší (/26,/25 atd.). Pak vyberte **OK** a uložte hodnoty a vytvořte podsíť brány.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Přidání podsítě brány":::

## <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na portálu na levé straně vyberte **vytvořit prostředek** a do pole Hledat zadejte ' Virtual Network Gateway '. Vyhledejte **bránu virtuální sítě** ve vyhledávání a vyberte ji. Na stránce **Brána virtuální sítě** vyberte **vytvořit** .
1. Na stránce **vytvořit bránu virtuální sítě** zadejte nebo vyberte Tato nastavení:

    | Nastavení | Hodnota |
    | --------| ----- |
    | Předplatné | Ověřte, zda je vybráno správné předplatné. |
    | Skupina prostředků | Skupina prostředků se automaticky vybere po výběru virtuální sítě. | 
    | Name | Pojmenujte bránu. To není totéž jako pojmenování podsítě brány. Jedná se o název objektu brány, který vytváříte.|
    | Oblast | Změňte pole **region** tak, aby odkazovalo na umístění, kde se nachází vaše virtuální síť. Pokud umístění neukazuje na oblast, ve které je vaše virtuální síť, tato virtuální síť se nezobrazí v rozevírací nabídce zvolit virtuální síť. |
    | Typ brány | Vybrat **ExpressRoute**|
    | SKU | Z rozevíracího seznamu vyberte SKU brány. |
    | Virtuální síť | Vyberte *TestVNet* . |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu.|
    | Název veřejné IP adresy | Zadejte název veřejné IP adresy. |

1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořte** a začněte vytvářet bránu. Nastavení se ověří a provede se nasazení brány. Dokončení vytváření brány virtuální sítě může trvat až 45 minut.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Přidání podsítě brány":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už bránu ExpressRoute nepotřebujete, vyhledejte bránu ve skupině prostředků virtuální sítě a vyberte **Odstranit** . Ujistěte se, že brána nemá žádná připojení k okruhu.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Přidání podsítě brány":::

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete připojit virtuální síť k okruhu ExpressRoute. 

> [!div class="nextstepaction"]
> [Propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
