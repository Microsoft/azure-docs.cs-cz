---
title: Konfigurace škálovací sady virtuálních strojů pomocí existujícího nástroje Azure Load Balancer – portál Azure
description: Zjistěte, jak nakonfigurovat škálovací sadu virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349725"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Konfigurace škálovací sady virtuálních strojů pomocí existujícího nástroje Provyrovnávání zatížení Azure pomocí portálu Azure

V tomto článku se dozvíte, jak nakonfigurovat škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Existující standardní nástroj pro vyrovnávání zatížení sku v předplatném, kde se nasadí škálovací sada virtuálního počítače.
- Virtuální síť Azure pro škálovací sadu virtuálních strojů.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení škálovací sady virtuálních strojů pomocí existujícího nástroje pro vyrovnávání zatížení

V této části vytvoříte škálovací sadu virtuálních strojů na webu Azure Portal s existujícím nástrojem pro vyrovnávání zatížení Azure.

> [!NOTE]
> Následující kroky předpokládají virtuální síť s názvem **myVNet** a nástroj pro vyrovnávání zatížení Azure s názvem **myLoadBalancer** byl dříve nasazen.

1. V levém horním rohu obrazovky klikněte na **Vytvořit sadu měřítka** > **výpočetního** > **virtuálního počítače** nebo vyhledejte **škálovací sadu virtuálních strojů** ve vyhledávání na marketplace.

2. Vyberte **Vytvořit**.

3. V **části Vytvoření škálovací sady virtuálních strojů**zadejte nebo vyberte tyto informace na kartě **Základy:**

    | Nastavení                        | Hodnota                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Podrobnosti o projektu**            |                                                                                                       |
    | Předplatné                   | Vyberte své předplatné Azure.                                                                        |
    | Skupina prostředků                 | Vyberte Vytvořit nový, zadejte **myResourceGroup**, pak vyberte OK nebo vyberte existující skupinu prostředků. |
    | **Podrobnosti škálovací sady**          |                                                                                                       |
    | Název škálovací sady virtuálních počítačů | Zadejte **myVMSS**                                                                                      |
    | Region (Oblast)                         | Vybrat **východní USA 2**                                                                                    |
    | Zóna dostupnosti              | Vybrat **možnost Žádné**                                                                                       |
    | **Podrobnosti instance**           |                                                                                                       |
    | Image                          | Vyberte **Ubuntu Server 18.04 LTS**                                                                    |
    | Instance Azure Spot            | Vybrat **ne**                                                                                         |
    | Velikost                           | Ponechat ve výchozím nastavení                                                                                      |
    | **Účet správce**      |                                                                                                       |
    | Typ ověřování            | Vybrat **heslo**                                                                                   |
    | Uživatelské jméno                       | Zadejte své uživatelské jméno správce.        |
    | Heslo                       | Zadejte heslo správce.    |
    | Potvrzení hesla               | Opětovné zadání hesla správce |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Vytvořte škálovací sadu virtuálních strojů." border="true":::

4. Vyberte kartu **Síť.**

5. Zadejte nebo vyberte tyto informace na kartě **Síť:**

     Nastavení                           | Hodnota                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfigurace virtuální sítě** |                                                          |
    | Virtuální síť                   | Vyberte **myVNet** nebo stávající virtuální síť.      |
    | **Vyrovnávání zatížení**                |                                                          |
    | Použití odvykaču zatížení               | Vybrat **ano**                                           |
    | **Nastavení vyrovnávání zatížení**       |                                                          |
    | Možnosti vyrovnávání zatížení            | Vyberte **Azure balancer**                           |
    | Výběr odvykaču zatížení            | Vyberte **myLoadBalancer** nebo stávající vyvažovač zatížení |
    | Výběr back-endfondu             | Vyberte **myBackendPool** nebo existující back-endový fond.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Vytvořte škálovací sadu virtuálních strojů." border="true":::

6. Vyberte kartu **Správa.**

7. Na kartě **Správa** nastavte **diagnostiku spouštění** na **Vypnuto**.

8. Vyberte modré tlačítko **Revize + vytvořit.**

9. Zkontrolujte nastavení a vyberte tlačítko **Vytvořit.**

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili škálovací sadu virtuálních strojů s existujícím nástrojem Pro vyrovnávání zatížení Azure.  Další informace o škálovacích sadách virtuálních strojů a nástroj pro vyrovnávání zatížení najdete v tématu:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
