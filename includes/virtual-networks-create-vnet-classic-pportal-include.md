---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: ac5599a38c3fcbc4a6c331d2b14c6b6c55c31d82
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850313"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Jak vytvořit klasickou virtuální síť v Azure Portal
Pokud chcete vytvořit klasickou virtuální síť v závislosti na předchozím scénáři, postupujte podle těchto kroků.

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Všimněte si, že seznam **Vyberte model nasazení** již zobrazuje **klasický**. 3. Klikněte na **vytvořit** , jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. V podokně **virtuální síť** zadejte **název** virtuální sítě a klikněte na **adresní prostor**. Nakonfigurujte nastavení adresního prostoru pro virtuální síť a její první podsíť a pak klikněte na **OK**. Následující obrázek ukazuje nastavení bloku CIDR pro náš scénář.
   
    ![Podokno adresního prostoru](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klikněte na **Skupina prostředků** a vyberte skupinu prostředků, do které chcete virtuální síť přidat, nebo kliknutím na **vytvořit novou skupinu prostředků** přidejte virtuální síť do nové skupiny prostředků. Následující obrázek ukazuje nastavení skupiny prostředků pro novou skupinu prostředků s názvem **TestRG**. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Podokno vytvořit skupinu prostředků](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. V případě potřeby změňte nastavení **Předplatné** a **Umístění** sítě VNet. 
7. Pokud nechcete tuto síť VNet zobrazovat jako dlaždici v zobrazení **Úvodní panel**, zakažte možnost **Připnout na Úvodní panel**. 
8. Klikněte na **vytvořit** a Všimněte si dlaždice s názvem **vytváření virtuální sítě** , jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření virtuální sítě na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Počkejte, až se virtuální síť vytvoří a když se zobrazí dlaždice, klikněte na ni a přidejte další podsítě.
   
    ![Vytvoření virtuální sítě na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Měla by se zobrazit **Konfigurace** vaší virtuální sítě, jak je znázorněno na obrázku. 
   
    ![Vytvoření virtuální sítě na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klikněte na **podsítě** > **Přidat**, zadejte **název** a určete **Rozsah adres (blok CIDR)** pro vaši podsíť a pak klikněte na **OK**. Následující obrázek ukazuje nastavení pro náš aktuální scénář.
    
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

