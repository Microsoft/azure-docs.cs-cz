---
title: Vytvoření veřejné IP adresy – Azure Portal
description: Naučte se vytvářet veřejné IP adresy v Azure Portal
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 49a89ee90d28c9c7a3f59424b773ee0f221381e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89301947"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Rychlý Start: vytvoření veřejné IP adresy pomocí Azure Portal

V tomto článku se dozvíte, jak vytvořit prostředek veřejné IP adresy pomocí Azure Portal. Další informace o tom, k jakým prostředkům může být přidružen, rozdíl mezi základní a standardní SKU a dalšími souvisejícími informacemi, najdete v tématu [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  V tomto příkladu se zaměříme jenom na adresy IPv4. Další informace o adresách IPv6 najdete v tématu [IPv6 pro virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**Standardní SKU – používání zón**](#tab/option-create-public-ip-standard-zones)

Pomocí následujících kroků vytvořte standardní veřejnou IP adresu redundantní v zóně s názvem **myStandardZRPublicIP**.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
4. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **Vytvoření veřejné IP adresy** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **Standard**         |
    | Name                    | Zadejte *myStandardZRPublicIP*          |
    | Přiřazení IP adresy   | Všimněte si, že tato možnost bude uzamčena jako statická.                                        |
    | Časový limit nečinnosti (minuty)  | Ponechte hodnotu 4.        |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový** , zadejte myResourceGroup a pak vyberte **OK** . |
    | Umístění                | Vyberte **východní USA 2**      |
    | Zóna dostupnosti       | Vyberte **zónu – redundantní** nebo vyberte konkrétní zónu (viz poznámka níže). |

Všimněte si, že se jedná o platné výběry pouze v oblastech s [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  (Můžete také vybrat konkrétní zónu v těchto oblastech, i když nebude odolná vůči selhání oblasti.)

# <a name="standard-sku---no-zones"></a>[**Standardní SKU – žádné zóny**](#tab/option-create-public-ip-standard)

Pomocí následujících kroků vytvořte standardní veřejnou IP adresu jako prostředek mimo oblast s názvem **myStandardPublicIP**.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
4. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **Vytvoření veřejné IP adresy** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **Standard**         |
    | Name                    | Zadejte *myStandardPublicIP*          |
    | Přiřazení IP adresy   | Všimněte si, že tato možnost bude uzamčena jako statická.                                        |
    | Časový limit nečinnosti (minuty)  | Ponechte hodnotu 4.        |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový** , zadejte myResourceGroup a pak vyberte **OK** . |
    | Umístění                | Vyberte **východní USA 2**      |
    | Zóna dostupnosti       | Vyberte možnost **žádná zóna** (a viz poznámka níže). |

Tento výběr je platný ve všech oblastech a je výchozí volbou pro standardní veřejné IP adresy v oblastech bez nutnosti [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-create-public-ip-basic)

Pomocí následujících kroků vytvořte základní statickou veřejnou IP adresu s názvem **myBasicPublicIP**.  Základní veřejné IP adresy nemají koncept zón dostupnosti.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
4. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **Vytvoření veřejné IP adresy** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **Standard**         |
    | Name                    | Zadejte *myBasicPublicIP*          |
    | Přiřazení IP adresy   | Zvolit **statickou** (viz poznámka níže)                                     |
    | Časový limit nečinnosti (minuty)  | Ponechte hodnotu 4.        |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový** , zadejte myResourceGroup a pak vyberte **OK** . |
    | Umístění                | Vyberte **východní USA 2**      |

Pokud je přijatelné, aby se IP adresa změnila v čase, můžete vybrat **dynamické** přiřazení IP adresy.

---

## <a name="additional-information"></a>Další informace 

Další podrobnosti o jednotlivých polích uvedených výše najdete v tématu [Správa veřejných IP adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Další kroky
- Přidružení [veřejné IP adresy k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).