---
title: Přidání nebo odebrání delegování podsítě ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Naučte se, jak přidat nebo odebrat delegovanou podsíť pro službu v Azure v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938496"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Přidání nebo odebrání delegování podsítě

Delegování podsítě dává službě explicitní oprávnění k vytváření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru při nasazování služby. Tento článek popisuje, jak přidat nebo odebrat delegovanou podsíť pro službu Azure.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k Azure Portal v https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť, které budete později delegovat na službu Azure.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavením | Hodnota |
    | ------- | ----- |
    | Name | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | formě | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **EastUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Rozsah adres podsítě | Zadejte *10.0.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a pak vyberte **vytvořit**.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, do služby Azure.

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Pokud se ve výsledcích hledání zobrazí **myVirtualNetwork** , vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení**vyberte **podsítě**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* vyberte v seznamu **delegování podsítě** ze služeb uvedených v části **delegovat podsíť na službu** (například **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Pokud se ve výsledcích hledání zobrazí **myVirtualNetwork** , vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení**vyberte **podsítě**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* v seznamu **delegování podsítě** vyberte možnost **žádné** ze služeb uvedených v části **delegovat podsíť na službu**. 

## <a name="next-steps"></a>Další kroky
- Naučte se [Spravovat podsítě v Azure](virtual-network-manage-subnet.md).
