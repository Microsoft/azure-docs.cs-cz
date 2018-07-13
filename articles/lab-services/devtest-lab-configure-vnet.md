---
title: Konfigurace virtuální sítě ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat stávající virtuální síť a podsíť a jejich použití ve virtuálním počítači s Azure DevTest Labs
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687784"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurace virtuální sítě ve službě Azure DevTest Labs
Jak je popsáno v článku [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md), při vytváření virtuálního počítače v testovacím prostředí, můžete zadat virtuální síť nakonfigurované. Například můžete potřebovat pro přístup k prostředkům podnikové sítě z vašich virtuálních počítačů pomocí virtuální sítě, který byl nakonfigurován s využitím ExpressRoute nebo VPN typu site-to-site.

Tento článek vysvětluje, jak přidat existující virtuální sítě do nastavení virtuální sítě testovacího prostředí tak, aby se si můžete vybrat při vytváření virtuálních počítačů.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Konfigurace virtuální sítě pro testovací prostředí pomocí webu Azure portal
Následující postup vás provede přidáním stávající virtuální síť (a podsítě) do testovacího prostředí tak, aby ho lze použít při vytváření virtuálního počítače ve stejném prostředí. 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte požadované prostředí. 
1. V hlavním podokně testovacího prostředí, vyberte **konfigurace a zásad**.

    ![Přístup ke konfiguraci a zásady testovacího prostředí](./media/devtest-lab-configure-vnet/policies-menu.png)
1. V **EXTERNÍM PROSTŘEDKŮM** vyberte **virtuální sítě**. Zobrazí se seznam virtuálních sítí nakonfigurovaných pro aktuální testovací prostředí a také výchozí virtuální síť vytvořili pro testovací prostředí. 
1. Vyberte **+ Přidat**.
   
    ![Přidat existující virtuální sítě do testovacího prostředí](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Na **virtuální síť** vyberte **[vyberte virtuální síť]**.
   
    ![Vyberte existující virtuální sítě](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Na **zvolte virtuální síť** podokně, vyberte požadovanou virtuální síť. Zobrazí seznam zobrazující všechny virtuální sítě, které jsou ve stejné oblasti v rámci předplatného jako testovacího prostředí.
1. Po výběru virtuální sítě, budete přesměrováni **virtuální síť** podokně. V seznamu dole vyberte podsíť.

    ![Seznam podsítí](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zobrazí se podokno podsíť v testovacím prostředí.

    ![Podokno podsíť testovacího prostředí](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Zadejte **název podsítě testovacího prostředí**.
   - Chcete-li povolit podsíť, která se použije v testovacím prostředí vytvoření virtuálního počítače, vyberte **použijte při vytváření virtuálních počítačů**.
   - Povolit [sdílené veřejné IP adresy](devtest-lab-shared-ip.md)vyberte **povolit sdílenou veřejnou IP adresu**.
   - Chcete-li povolit veřejné IP adresy v podsíti, vyberte **povolit vytvoření veřejné IP adresy**.
   - V **maximální počet virtuálních počítačů na uživatele** zadejte maximální virtuálních počítačů na uživatele pro každou podsíť. Pokud chcete, aby neomezený počet virtuálních počítačů, ponechte toto pole prázdné.
1. Vyberte **OK** zavřete podokno podsíť v testovacím prostředí.
1. Vyberte **Uložit** zavřete podokno virtuální sítě.

Teď, když je nakonfigurovaná ve virtuální síti, můžete vybrat při vytváření virtuálního počítače. Chcete-li zjistit, jak vytvořit virtuální počítač a zadáte virtuální sítě, najdete v článku [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md). 

Azure [dokumentace k Virtual Network](https://docs.microsoft.com/azure/virtual-network) poskytuje další informace o tom, jak používat virtuální sítě, jak nastavit a spravovat virtuální síť a připojení k místní síti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Po přidání požadované virtuální sítě pro testovací prostředí, dalším krokem je [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

