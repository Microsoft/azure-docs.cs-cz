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
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175953"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Přidání nebo odebrání delegování podsítě

Delegování podsítě dává službě explicitní oprávnění k vytváření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru při nasazování služby. Tento článek popisuje, jak přidat nebo odebrat delegovanou podsíť pro službu Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť, které budete později delegovat na službu Azure.

1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **EastUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.0.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a pak vyberte **vytvořit**.

## <a name="permissons"></a>Nemáte

Pokud jste nevytvořili podsíť, kterou byste chtěli delegovat na službu Azure, budete potřebovat následující oprávnění: `Microsoft.Network/virtualNetworks/subnets/write`.

Integrovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) obsahuje taky potřebná oprávnění.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, do služby Azure.

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení**vyberte **podsítě**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* vyberte v seznamu **delegování podsítě** ze služeb uvedených v části **delegovat podsíť na službu** (například **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení**vyberte **podsítě**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* v seznamu **delegování podsítě** vyberte možnost **žádné** ze služeb uvedených v části **delegovat podsíť na službu**. 

## <a name="next-steps"></a>Další kroky
- Naučte se [Spravovat podsítě v Azure](virtual-network-manage-subnet.md).
