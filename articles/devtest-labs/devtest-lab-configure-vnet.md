---
title: Konfigurace virtuální sítě v Azure DevTest Labs | Microsoft Docs
description: Naučte se konfigurovat existující virtuální síť a podsíť a používat je na virtuálním počítači s Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283550"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurace virtuální sítě v Azure DevTest Labs
Jak je vysvětleno v článku [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md), když vytvoříte virtuální počítač v testovacím prostředí, můžete zadat nakonfigurovanou virtuální síť. Například můžete potřebovat přístup k prostředkům Corpnet z virtuálních počítačů pomocí virtuální sítě, která byla nakonfigurovaná se ExpressRoute nebo VPN typu Site-to-site.

Tento článek vysvětluje, jak přidat existující virtuální síť do nastavení Virtual Network testovacího prostředí tak, aby bylo dostupné pro výběr při vytváření virtuálních počítačů.

> [!NOTE]
> Další informace o nákladech souvisejících se službou Azure Virtual Network najdete v tématu [ceny za Azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurace virtuální sítě pro testovací prostředí pomocí Azure Portal
Následující kroky vás provedou přidáním existující virtuální sítě (a podsítě) do testovacího prostředí, aby se mohly použít při vytváření virtuálních počítačů ve stejném testovacím prostředí. 

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte požadované testovací prostředí. 
1. V hlavním podokně testovacího prostředí vyberte **Konfigurace a zásady**.

    ![Přístup ke konfiguraci a zásadám testovacího prostředí](./media/devtest-lab-configure-vnet/policies-menu.png)
1. V části **externí prostředky** vyberte **virtuální sítě**. Zobrazí se seznam virtuálních sítí konfigurovaných pro aktuální testovací prostředí a také výchozí virtuální síť vytvořená pro vaše testovací prostředí. 
1. Vyberte **+ Přidat**.
   
    ![Přidat existující virtuální síť do testovacího prostředí](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. V podokně **virtuální síť** vyberte **[Vybrat virtuální síť]**.
   
    ![Vyberte existující virtuální síť.](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. V podokně **Zvolte virtuální síť** vyberte požadovanou virtuální síť. Zobrazí se seznam všech virtuálních sítí, které jsou ve stejné oblasti v předplatném jako testovací prostředí.
1. Po výběru virtuální sítě se vrátíte do podokna **virtuální síť** . V seznamu dole vyberte podsíť.

    ![Seznam podsítí](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zobrazí se podokno podsíť testovacího prostředí.

    ![Podokno podsítě testovacího prostředí](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Zadejte **název podsítě testovacího prostředí**.
   - Pokud chcete, aby se podsíť použila při vytváření testovacího virtuálního počítače, vyberte **použít při vytváření virtuálního počítače**.
   - Pokud chcete povolit [sdílenou veřejnou IP adresu](devtest-lab-shared-ip.md), vyberte **Povolit sdílenou veřejnou IP** adresu.
   - Pokud chcete v podsíti dovolit veřejné IP adresy, vyberte možnost **Povolení vytvoření veřejné IP** adresy.
   - V poli **maximální počet virtuálních počítačů na uživatele** zadejte maximální počet virtuálních počítačů na uživatele pro každou podsíť. Pokud chcete neomezený počet virtuálních počítačů, ponechte toto pole prázdné.
1. Kliknutím na **tlačítko OK** zavřete podokno podsíť testovacího prostředí.
1. Výběrem **Uložit** zavřete podokno virtuální síť.

Teď, když je virtuální síť nakonfigurovaná, dá se při vytváření virtuálního počítače vybrat. Pokud chcete zjistit, jak vytvořit virtuální počítač a zadat virtuální síť, přečtěte si článek o [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md). 

V dokumentaci k Azure [Virtual Network](../virtual-network/index.yml) najdete další informace o tom, jak používat virtuální sítě, včetně postupu nastavení a správy virtuální sítě a její připojení k místní síti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po přidání požadované virtuální sítě do testovacího prostředí je dalším krokem [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).
