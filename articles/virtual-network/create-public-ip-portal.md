---
title: Vytvoření veřejné IP adresy – Azure Portal
description: Naučte se vytvářet veřejné IP adresy v Azure Portal
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694865"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Vytvoření veřejné IP adresy pomocí Azure Portal

Tento článek ukazuje, jak vytvořit prostředek veřejné IP adresy pomocí Azure Portal. 

Další informace o prostředcích, ke kterým se tato veřejná IP adresa dá přidružit, a rozdíl mezi základními a standardními SKU najdete v tématu [veřejné IP adresy](./public-ip-addresses.md). 

Tento článek se zaměřuje na adresy IPv4. Další informace o adresách IPv6 najdete v tématu [IPv6 pro virtuální síť Azure](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-create-public-ip-standard-zones)

Pomocí následujících kroků vytvořte standardní veřejnou IP adresu redundantní v zóně s názvem **myStandardZRPublicIP**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte **veřejnou IP adresu**. Ve výsledcích hledání vyberte **Veřejná IP adresa** .
4. Na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **vytvořit veřejnou IP adresu** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **Standard**         |
    | Vrstva                   | Vybrat **oblastní**         |
    | Název                    | Zadejte **myStandardZRPublicIP**          |
    | Přiřazení IP adresy   | Poznámka: Tento výběr je zamčený jako "static".                                        |
    | Předvolby směrování      | Ponechte výchozí **síť Microsoft**. </br> Další informace o předvolbách směrování najdete v tématu [co je předvolby směrování (Preview)?](./routing-preference-overview.md). |
    | Časový limit nečinnosti (minuty)  | Ponechte výchozí hodnotu **4**.        |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový**, zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | Umístění                | Vyberte **východní USA 2**      |
    | Zóna dostupnosti       | Vybrat zónu **– redundantní**, žádná zóna nebo vybrat konkrétní zónu (viz poznámka níže) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Vytvořit standardní IP adresu v Azure Portal" border="false":::

> [!NOTE]
> Tyto výběry jsou platné v oblastech s [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
V těchto oblastech můžete vybrat konkrétní zónu, i když nebudete odolná vůči selhání oblasti. </br> Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Úroveň se vztahuje k funkcím [Nástroje pro vyrovnávání zatížení mezi oblastmi](../load-balancer/cross-region-overview.md) , která je aktuálně ve verzi Preview.

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-create-public-ip-basic)

V této části vytvoříte základní veřejnou IP adresu s názvem **myBasicPublicIP**. 

> [!NOTE]
> Veřejné IP adresy úrovně Basic nepodporují zóny dostupnosti.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte **veřejnou IP adresu**. Ve výsledcích hledání vyberte **Veřejná IP adresa** .
4. Na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. Na stránce **vytvořit veřejnou IP adresu** zadejte nebo vyberte následující informace: 

    | Nastavení                 | Hodnota                       |
    | ---                     | ---                         |
    | Verze protokolu IP              | Vybrat IPv4                 |    
    | SKU                     | Vybrat **základní**         |
    | Název                    | Zadejte *myBasicPublicIP*          |
    | Přiřazení IP adresy   | Vybrat **statickou** (viz poznámka níže)                                     |
    | Časový limit nečinnosti (minuty)  | Ponechte výchozí hodnotu **4**.       |
    | Popisek názvu DNS          | Ponechte hodnotu prázdnou.    |
    | Předplatné            | Vyberte své předplatné.   |
    | Skupina prostředků          | Vyberte **vytvořit nový**, zadejte **myResourceGroup**. </br> Vyberte **OK**. |
    | Umístění                | Vyberte **východní USA 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Vytvořit standardní IP adresu v Azure Portal" border="false":::

Pokud je přijatelné, aby se IP adresa změnila v čase, můžete vybrat **dynamické** přiřazení IP adresy.

---

## <a name="additional-information"></a>Další informace 

Další informace o jednotlivých polích uvedených výše najdete v tématu [Správa veřejných IP adres](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Další kroky
- Přidružení [veřejné IP adresy k virtuálnímu počítači](./associate-public-ip-address-vm.md#azure-portal)
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).