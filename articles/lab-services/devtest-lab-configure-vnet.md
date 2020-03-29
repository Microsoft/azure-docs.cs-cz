---
title: Konfigurace virtuální sítě v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat existující virtuální síť a podsíť a použít je ve virtuálním počítači s Azure DevTest Labs
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390037"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurace virtuální sítě v Azure DevTest Labs
Jak je vysvětleno v článku [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md), při vytváření virtuálního počítače v testovacím prostředí můžete zadat nakonfigurovanou virtuální síť. Například budete muset získat přístup k prostředkům corpnet z virtuálních počítačů pomocí virtuální sítě, která byla nakonfigurovaná pomocí ExpressRoute nebo sítě VPN.

Tento článek vysvětluje, jak přidat existující virtuální síť do nastavení virtuální sítě testovacího prostředí tak, aby byla k dispozici pro výběr při vytváření virtuálních počítačů.

> [!NOTE]
> Další informace o nákladech spojených se službou Virtuální síť Azure najdete v [tématu Ceny za virtuální síť Azure](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurace virtuální sítě pro testovací prostředí pomocí portálu Azure
Následující kroky vás provedou přidáním existující virtuální sítě (a podsítě) do testovacího prostředí, aby ji bylo možné použít při vytváření virtuálního počítače ve stejném testovacím prostředí. 

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí. 
1. V hlavním podokně testovacího prostředí vyberte **Možnost Konfigurace a zásady**.

    ![Přístup ke konfiguraci a zásadám testovacího prostředí](./media/devtest-lab-configure-vnet/policies-menu.png)
1. V části **EXTERNÍ ZDROJE** vyberte **virtuální sítě**. Zobrazí se seznam virtuálních sítí nakonfigurovaných pro aktuální testovací prostředí a také výchozí virtuální síť vytvořená pro testovací prostředí. 
1. Vyberte **+ Přidat**.
   
    ![Přidání existující virtuální sítě do testovacího prostředí](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. V podokně **Virtuální síť** vyberte **možnost [Vybrat virtuální síť]**.
   
    ![Výběr existující virtuální sítě](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. V podokně **Zvolit virtuální síť** vyberte požadovanou virtuální síť. Zobrazí se seznam zobrazující všechny virtuální sítě, které jsou ve stejné oblasti v rámci předplatného jako testovací prostředí.
1. Po výběru virtuální sítě se vrátíte do podokna **Virtuální síť.** Vyberte podsíť v seznamu v dolní části.

    ![Seznam podsítí](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zobrazí se podokno Podsítě v laboratoři.

    ![Podokno podsítě Lab](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Zadejte **název podsítě Lab**.
   - Pokud chcete povolit použití podsítě při vytváření virtuálních počítačích v testovacím prostředí, vyberte **Použít při vytváření virtuálních strojů**.
   - Chcete-li povolit [sdílenou veřejnou IP adresu](devtest-lab-shared-ip.md), vyberte **Povolit sdílenou veřejnou IP adresu**.
   - Chcete-li povolit veřejné IP adresy v podsíti, vyberte **Povolit vytváření veřejné ip adresy**.
   - V poli **Maximální počet virtuálních počítačů na uživatele** zadejte maximální počet virtuálních počítačů na uživatele pro každou podsíť. Pokud chcete neomezený počet virtuálních stránek, ponechte toto pole prázdné.
1. Chcete-li zavřít podokno Podsítě v laboratoři, vyberte **ok.**
1. Výběrem **možnosti Uložit** zavřete podokno Virtuální síť.

Teď, když je virtuální síť nakonfigurovaná, může být vybrána při vytváření virtuálního počítače. Pokud chcete zjistit, jak vytvořit virtuální počítač a určit virtuální síť, najdete v článku [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md). 

[Dokumentace k virtuální síti](https://docs.microsoft.com/azure/virtual-network) Azure poskytuje další informace o tom, jak používat virtuální sítě, včetně toho, jak nastavit a spravovat virtuální síť a připojit ji k místní síti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
Po přidání požadované virtuální sítě do testovacího prostředí je dalším krokem [přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

