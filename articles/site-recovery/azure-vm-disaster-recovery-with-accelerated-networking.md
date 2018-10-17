---
title: Urychlení sítí pomocí zotavení po havárii virtuálních počítačů Azure | Dokumentace Microsoftu
description: Popisuje, jak povolit Akcelerovanými síťovými službami službou Azure Site Recovery pro zotavení po havárii virtuálních počítačů Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: bb4a3f433f213984c4c351439760ddd3327cda73
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353132"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Akcelerované síťové služby s zotavení po havárii virtuálních počítačů Azure

Akcelerované síťové služby umožňuje virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, výrazně zlepšit sítě. Tato cesta výkonné obchází hostitele z datapath, snížení latence, zpoždění a využití procesoru pro použití s nejnáročnějších úloh sítě na podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikace mezi dvěma virtuálními počítači a nemusíte akcelerované síťové služby:

![porovnání](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery umožňuje využívat výhod Accelerated Networking pro Azure virtual machines, které při selhání do jiné oblasti Azure. Tento článek popisuje, jak můžete zajistit Akcelerovanými síťovými službami pro virtuální počítače Azure pomocí služby Azure Site Recovery replikovat.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte:
-   Virtuální počítač Azure [architektura replikace](azure-to-azure-architecture.md)
-   [Nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure
-   [Převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) virtuálních počítačů Azure

## <a name="accelerated-networking-with-windows-vms"></a>Akcelerované síťové služby s virtuálními počítači Windows

Azure Site Recovery podporuje, povolení Akcelerovanými síťovými službami pro replikované virtuální počítače pouze v případě, že zdrojový virtuální počítač má Akcelerovanými síťovými službami povolena. Pokud zdrojový virtuální počítač nemá akcelerované síťové povolena, se dozvíte, jak povolit Accelerated Networking pro Windows virtuálních počítačů [tady](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Podporované operační systémy
Připravených v galerii Azure jsou podporovány následující distribuce:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované síťové služby se podporuje na nejvíce obecné účely a velikostí optimalizovaných pro výpočetní instance s 2 nebo více virtuálních procesorů.  Jsou tyto podporované řady: D/DSv2 a F/Fs

U instancí, které podporují hyperthreadingem Akcelerovanými síťovými službami se podporuje na instancích virtuálních počítačů s 4 nebo více virtuálních procesorů. Jsou podporované řady: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Akcelerované síťové služby s Linuxovými virtuálními počítači

Azure Site Recovery podporuje, povolení Akcelerovanými síťovými službami pro replikované virtuální počítače pouze v případě, že zdrojový virtuální počítač má Akcelerovanými síťovými službami povolena. Pokud zdrojový virtuální počítač nemá akcelerované síťové povolena, se dozvíte, jak povolit Akcelerovanými síťovými službami pro virtuální počítače s Linuxem [tady](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Podporované operační systémy
Připravených v galerii Azure jsou podporovány následující distribuce:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" s zpětné jádra**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované síťové služby se podporuje na nejvíce obecné účely a velikostí optimalizovaných pro výpočetní instance s 2 nebo více virtuálních procesorů.  Jsou tyto podporované řady: D/DSv2 a F/Fs

U instancí, které podporují hyperthreadingem Akcelerovanými síťovými službami se podporuje na instancích virtuálních počítačů s 4 nebo více virtuálních procesorů. Jsou podporované řady: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Povoluje se pro replikované virtuální počítače s Akcelerovanými síťovými službami

Pokud jste [povolit replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure, Site Recovery automaticky zjistí, jestli síťová rozhraní virtuálního počítače mají Akcelerovanými síťovými službami povolena. Pokud Akcelerovanými síťovými službami je už povolená, Site Recovery automaticky nakonfigurují Akcelerovanými síťovými službami v síťovém rozhraní replikovaného virtuálního počítače.

V části se dá ověřit stav Akcelerovanými síťovými službami **síťová rozhraní** část **výpočty a síť** nastavení replikovaný virtuální počítač.

![Akcelerované síťové nastavení](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Pokud jste povolili Akcelerovanými síťovými službami na zdrojovém virtuálním počítači po povolení replikace, můžete povolit Akcelerovanými síťovými službami pro rozhraní sítí replikovaného virtuálního počítače následujícím procesem:
1. Otevřít **výpočty a síť** nastavení replikovaný virtuální počítač
2. Klikněte na název síťového rozhraní v části **síťová rozhraní** oddílu
3. Vyberte **povoleno** z rozevíracího seznamu pro Akcelerovanými síťovými službami v rámci **cílové** sloupec

![Povolit akcelerované síťové služby](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Výše uvedené procesu byste měli dodržet také existující replikované virtuální počítače, které dříve neměla Akcelerovanými síťovými službami povolí automaticky službou Site Recovery.

## <a name="next-steps"></a>Další postup
- Další informace o [výhody Akcelerovanými síťovými službami](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Další informace o omezení a omezení akcelerované síťové služby pro [Windows virtual machines](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) a [virtuální počítače s Linuxem](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
