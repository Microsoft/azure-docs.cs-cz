---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: edfcd4d59eceada9e1c6014dad9afa7fe8ab3c8f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Postup vytvoření klasické virtuální sítě na portálu Azure
Pokud chcete vytvořit virtuální síť v závislosti na předchozím scénáři klasický, postupujte podle těchto kroků.

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Všimněte si, že **vybrat model nasazení** seznam již ukazuje **Classic**. 3. Klikněte na tlačítko **vytvořit** jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Na **virtuální síť** podokně, zadejte **název** virtuální síť a potom klikněte na **adresní prostor**. Nakonfigurujte nastavení prostoru adres pro síť VNet a jeho první podsíť a pak klikněte na tlačítko **OK**. Následující obrázek ukazuje nastavení blok CIDR pro náš scénář.
   
    ![Podokno prostoru adres](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klikněte na tlačítko **skupiny prostředků** a vyberte skupinu prostředků přidat síť VNet, nebo kliknutím na tlačítko **vytvořit novou skupinu prostředků** přidat síť VNet do nové skupiny prostředků. Následující obrázek znázorňuje prostředku nastavení skupiny pro novou skupinu prostředků s názvem **TestRG**. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Vytvoření podokně skupiny prostředků](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. V případě potřeby změňte nastavení **Předplatné** a **Umístění** sítě VNet. 
7. Pokud nechcete tuto síť VNet zobrazovat jako dlaždici v zobrazení **Úvodní panel**, zakažte možnost **Připnout na Úvodní panel**. 
8. Klikněte na tlačítko **vytvořit** a Všimněte si dlaždice s názvem **vytváření virtuální sítě** jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Počkejte pro virtuální síť, který se má vytvořit a pokud se zobrazí na dlaždici, kliknutím na Přidat další podsítě.
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Měli byste vidět **konfigurace** pro vaši virtuální síť, jak je vidět. 
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klikněte na tlačítko **podsítě** > **přidat**, zadejte **název** a zadejte **rozsahu (blok CIDR) adres** pro podsíť a potom Klikněte na tlačítko **OK**. Následující obrázek ukazuje nastavení pro náš scénář aktuální.
    
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

