---
title: Konfigurace virtuální sítě v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak nakonfigurovat existující virtuální síť a podsíť a využít ve virtuálním počítači s Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 0141ea8a88c0322e6f56cbea56d3a43c923769af
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787406"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurace virtuální sítě v Azure DevTest Labs
Jak je popsáno v článku [přidat virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md), při vytváření virtuálních počítačů v testovacím prostředí, můžete zadat nakonfigurované virtuální sítě. Například může potřebujete přístup k prostředkům corpnet z virtuálních počítačů pomocí virtuální sítě, která byla konfigurovaná s ExpressRoute nebo VPN typu site-to-site.

Tento článek vysvětluje, jak přidat existující virtuální sítě do testovacího prostředí na nastavení virtuální sítě, aby bylo možné zvolit při vytváření virtuálních počítačů.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurace virtuální sítě pro testovacím prostředí pomocí portálu Azure
Následující postup vás provede procesem přidání existující virtuální síť (a podsítě) do testovacího prostředí, aby se můžete použít při vytváření virtuálního počítače ve stejné testovací prostředí. 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte požadované testovací prostředí. 
1. V hlavním podokně v prostředí, vyberte **konfiguraci a zásady**.

    ![Přístup v prostředí konfiguraci a zásady](./media/devtest-lab-configure-vnet/policies-menu.png)
1. V **EXTERNÍM PROSTŘEDKŮM** vyberte **virtuální sítě**. Zobrazí se seznam virtuálních sítí, které jsou nakonfigurované pro aktuální prostředí a také výchozí virtuální síti, které jsou vytvořené pro testovací prostředí. 
1. Vyberte **+ Přidat**.
   
    ![Přidat existující virtuální síť do testovacího prostředí](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Na **virtuální síť** podokně, vyberte **[vyberte virtuální síť]**.
   
    ![Vyberte existující virtuální síť](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Na **zvolte virtuální síť** podokně, vyberte požadované virtuální sítě. Zobrazí seznam zobrazuje všechny virtuální sítě, které jsou ve stejné oblasti v rámci předplatného jako testovací prostředí.
1. Po výběru virtuální sítě, vrátíte se **virtuální síť** podokně. V seznamu dole vyberte podsíť.

    ![Seznam podsítí](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zobrazí se podokno podsíť testovacího prostředí.

    ![Podokno podsíť testovacího prostředí](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Zadejte **název podsítě testovacího prostředí**.
   - Chcete-li povolit podsíť, kterou chcete použít v testovacím prostředí vytvoření virtuálního počítače, vyberte **použití v vytvoření virtuálního počítače**.
   - Chcete-li povolit [sdílené veřejnou IP adresu](devtest-lab-shared-ip.md), vyberte **povolení sdíleného veřejnou IP adresu**.
   - Chcete-li povolit veřejné IP adresy v podsíti, vyberte **povolit vytvoření veřejné IP**.
   - V **maximální počet virtuálních počítačů na uživatele** pole zadejte maximální virtuálních počítačů na uživatele pro každou podsíť. Pokud chcete neomezený počet virtuálních počítačů, nechte pole prázdné.
1. Vyberte **OK** zavřete podokno podsíť testovacího prostředí.
1. Vyberte **Uložit** zavřete podokně virtuální sítě.

Teď, když je nakonfigurovaný virtuální sítě, můžete vybrat při vytváření virtuálního počítače. Informace o tom, jak vytvořit virtuální počítač a zadejte virtuální síť, naleznete v článku [přidat virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md). 

Azure [dokumentace k virtuálním sítím](https://docs.microsoft.com/azure/virtual-network) poskytuje další informace o tom, jak používat virtuální sítě, včetně toho, jak nastavit a spravovat virtuální sítě a připojení k síti na pracovišti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Po přidání požadované virtuální sítě do vašeho testovacího prostředí, dalším krokem je [přidat virtuální počítač do testovacího prostředí](devtest-lab-add-vm.md).

