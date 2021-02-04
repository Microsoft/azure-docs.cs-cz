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
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550231"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Rychlý Start: vytvoření veřejné IP adresy pomocí Azure Portal

V tomto článku se dozvíte, jak vytvořit prostředek veřejné IP adresy pomocí Azure Portal. Další informace o tom, k jakým prostředkům může být přidružen, rozdíl mezi základní a standardní SKU a dalšími souvisejícími informacemi, najdete v tématu [veřejné IP adresy](./public-ip-addresses.md).  V tomto příkladu se zaměříme jenom na adresy IPv4. Další informace o adresách IPv6 najdete v tématu [IPv6 pro virtuální síť Azure](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-create-public-ip-standard-zones)

Pomocí následujících kroků vytvořte standardní veřejnou IP adresu redundantní v zóně s názvem **myStandardZRPublicIP**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
4. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **Vytvoření veřejné IP adresy** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **Standard**         |
    | Úroveň (Pokud je zobrazena *)                  | Vybrat **oblastní**         |
    | Name                    | Zadejte *myStandardZRPublicIP*          |
    | Přiřazení IP adresy   | Všimněte si, že tato možnost bude uzamčena jako statická.                                        |
    | Časový limit nečinnosti (minuty)  | Ponechte hodnotu 4.        |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový** , zadejte myResourceGroup a pak vyberte **OK** . |
    | Umístění                | Vyberte **východní USA 2**      |
    | Zóna dostupnosti       | Vybrat zónu **– redundantní**, žádná zóna nebo vybrat konkrétní zónu (viz poznámka níže) |

Všimněte si, že se jedná o platné výběry pouze v oblastech s [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  (Můžete také vybrat konkrétní zónu v těchto oblastech, i když nebude odolná vůči selhání oblasti.)  Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Úroveň se vztahuje k funkcím [Load Balancer mezi oblastmi](../load-balancer/cross-region-overview.md) , které jsou aktuálně ve verzi Preview.

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-create-public-ip-basic)

Pomocí následujících kroků vytvořte základní statickou veřejnou IP adresu s názvem **myBasicPublicIP**.  Základní veřejné IP adresy nemají koncept zón dostupnosti.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
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

Další podrobnosti o jednotlivých polích uvedených výše najdete v tématu [Správa veřejných IP adres](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Další kroky
- Přidružení [veřejné IP adresy k virtuálnímu počítači](./associate-public-ip-address-vm.md#azure-portal)
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
