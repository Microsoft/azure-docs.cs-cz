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
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937430"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Jak vytvořit klasickou virtuální síť na webu Azure Portal
Vytváření klasický virtuální sítě v závislosti na předchozím scénáři, postupujte podle těchto kroků.

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Všimněte si, **vybrat model nasazení** seznam již obsahuje **Classic**. 3. Klikněte na tlačítko **vytvořit** jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Na **virtuální síť** podokně, zadejte **název** virtuální sítě a pak klikněte na tlačítko **adresní prostor**. Nakonfigurujte nastavení prostoru adres pro virtuální síť a jeho první podsíť a pak klikněte na tlačítko **OK**. Následující obrázek ukazuje nastavení blok CIDR pro náš scénář.
   
    ![Podokno adresa místa](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klikněte na tlačítko **skupiny prostředků** a vyberte skupinu prostředků, přidejte k virtuální síti, nebo klikněte na **vytvořit novou skupinu prostředků** přidat síť VNet do nové skupiny prostředků. Následující obrázek ukazuje na prostředek nastavení skupiny pro novou skupinu prostředků s názvem **TestRG**. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Vytvořit podokno skupiny prostředků](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. V případě potřeby změňte nastavení **Předplatné** a **Umístění** sítě VNet. 
7. Pokud nechcete tuto síť VNet zobrazovat jako dlaždici v zobrazení **Úvodní panel**, zakažte možnost **Připnout na Úvodní panel**. 
8. Klikněte na tlačítko **vytvořit** a Všimněte si dlaždice s názvem **vytváří se virtuální síť** jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Počkejte pro virtuální síť, který se má vytvořit a pokud se zobrazí dlaždice, kliknutím na Přidat další podsítě.
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Měli byste vidět **konfigurace** pro vaši síť VNet, jak je znázorněno. 
   
    ![Vytvoření sítě VNet na portálu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klikněte na tlačítko **podsítě** > **přidat**, zadejte **název** a zadat **rozsah adres (blok CIDR)** pro vaši podsíť a pak Klikněte na tlačítko **OK**. Následující obrázek ukazuje nastavení pro naše aktuální situaci.
    
    ![Vytvoření virtuální sítě VNet na portálu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

