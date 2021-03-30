---
title: Konfigurace sady škálování virtuálních počítačů pomocí existující Azure Load Balancer-Azure Portal
description: Naučte se konfigurovat sadu škálování virtuálního počítače s existujícím Azure Load Balancer pomocí Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91439515"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Konfigurace sady škálování virtuálních počítačů pomocí existující Azure Load Balancer pomocí Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat sadu škálování virtuálních počítačů s existujícím Azure Load Balancer. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Existující Nástroj pro vyrovnávání zatížení Standard SKU v předplatném, kde se bude nasazovat sada škálování virtuálního počítače.
- Virtual Network Azure pro sadu škálování virtuálního počítače.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Nasazení sady škálování virtuálních počítačů s existujícím nástrojem pro vyrovnávání zatížení

V této části vytvoříte v Azure Portal sadu škálování virtuálního počítače s existujícím nástrojem pro vyrovnávání zatížení Azure.

> [!NOTE]
> Následující kroky předpokládají virtuální síť s názvem **myVNet** a služba Azure Load Balancer s názvem **myLoadBalancer** byla dřív nasazená.

1. V levém horním rohu obrazovky klikněte na **vytvořit prostředek**  >  **COMPUTE**  >  **Virtual Machine Scale set** nebo ve vyhledávání na webu Marketplace vyhledejte **sadu škálování virtuálního počítače** .

2. Vyberte **Vytvořit**.

3. V části **vytvořit sadu škálování virtuálního počítače** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení                        | Hodnota                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Podrobnosti o projektu**            |                                                                                                       |
    | Předplatné                   | Vyberte své předplatné Azure.                                                                        |
    | Skupina prostředků                 | Vyberte vytvořit novou, zadejte **myResourceGroup**, vyberte OK nebo vyberte existující skupinu prostředků. |
    | **Podrobnosti o škále sady**          |                                                                                                       |
    | Název škálovací sady virtuálních počítačů | Zadejte **myVMSS**                                                                                      |
    | Oblast                         | Vyberte **východní USA 2**                                                                                    |
    | Zóna dostupnosti              | Vybrat **žádné**                                                                                       |
    | **Podrobnosti o instancích**           |                                                                                                       |
    | Image                          | Vyberte **Ubuntu Server 18,04 LTS**                                                                    |
    | Instance Azure Spot            | Vybrat **ne**                                                                                         |
    | Velikost                           | Ponechat ve výchozím nastavení                                                                                      |
    | **Účet správce**      |                                                                                                       |
    | Typ ověřování            | Vybrat **heslo**                                                                                   |
    | Uživatelské jméno                       | Zadejte uživatelské jméno správce.        |
    | Heslo                       | Zadejte heslo správce.    |
    | Potvrzení hesla               | Zadejte znovu heslo správce. |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Snímek obrazovky se zobrazí na kartě vytvořit základy pro sadu škálování virtuálního počítače." border="true":::

4. Vyberte kartu **Sítě**.

5. Zadejte nebo vyberte tyto informace na kartě **síť** :

     Nastavení                           | Hodnota                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfigurace virtuální sítě** |                                                          |
    | Virtuální síť                   | Vyberte **myVNet** nebo existující virtuální síť.      |
    | **Vyrovnávání zatížení**                |                                                          |
    | Použití nástroje pro vyrovnávání zatížení               | Vyberte **Ano**.                                           |
    | **Nastavení vyrovnávání zatížení**       |                                                          |
    | Možnosti vyrovnávání zatížení            | Výběr **služby Azure Load Balancer**                           |
    | Vyberte nástroj pro vyrovnávání zatížení.            | Vyberte **myLoadBalancer** nebo existující Nástroj pro vyrovnávání zatížení. |
    | Vybrat back-end fond             | Vyberte **myBackendPool** nebo svůj stávající back-end fond.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Snímek obrazovky se zobrazí karta vytvořit síť sady škálování virtuálního počítače." border="true":::

6. Vyberte kartu **Správa** .

7. Na kartě **Správa** nastavte **diagnostiku spouštění** na **vypnuto**.

8. Vyberte tlačítko modrá **Revize + vytvořit** .

9. Zkontrolujte nastavení a klikněte na tlačítko **vytvořit** .

## <a name="next-steps"></a>Další kroky

V tomto článku jste nasadili sadu škálování virtuálního počítače s existujícím Azure Load Balancer.  Další informace o službě Virtual Machine Scale Sets a nástroji pro vyrovnávání zatížení najdete v těchto tématech:

- [Co je Azure Load Balancer?](load-balancer-overview.md)
- [Co jsou škálovací sady virtuálních počítačů?](../virtual-machine-scale-sets/overview.md)
