---
title: Povolení zrychlené sítě pro zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Popisuje, jak povolit zrychlené sítě pomocí Azure Site Recovery pro azure virtuální počítač zotavení po havárii
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73622432"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Zrychlené vytváření sítí s obnovením infrastruktury virtuálních strojů Azure

Akcelerovaná síť umožňuje virtualizaci vstupně-videa jednoho kořene (SR-IOV) do virtuálního počítače, což výrazně zlepšuje jeho síťový výkon. Tato cesta vysokého výkonu obchází hostitele z cesty data, snižuje latenci, kolísání a využití procesoru pro použití s nejnáročnějšími síťovými úlohami na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními zařízeními se zrychleným vytvářením sítí a bez něj:

![Srovnání](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery umožňuje využívat výhody akcelerované sítě pro virtuální počítače Azure, které se nezdaří v jiné oblasti Azure. Tento článek popisuje, jak můžete povolit zrychlené sítě pro virtuální počítače Azure replikované pomocí Azure Site Recovery.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte:
-   [Architektura replikace virtuálních](azure-to-azure-architecture.md) strojů Azure
-   [Nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure
-   [Převzetí selhání](azure-to-azure-tutorial-failover-failback.md) Virtuální počítače Azure

## <a name="accelerated-networking-with-windows-vms"></a>Zrychlené vytváření sítí pomocí virtuálních aplikací Windows

Azure Site Recovery podporuje povolení akcelerované sítě pro replikované virtuální počítače pouze v případě, že zdrojový virtuální počítač má povoleno uzuracené sítě. Pokud váš zdrojový virtuální počítač nemá povolenou funkci Accelerated Networking, můžete se zde naučit povolit virtuální [počítače](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)Accelerated Networking for Windows .

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure jsou podporované následující distribuce:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních ms
Akcelerovaná síť je podporována pro většinu velikostí instancí pro obecné účely a optimalizované pro výpočetní výkon y se 2 nebo více virtuálními procesory.  Tyto podporované řady jsou: D/DSv2 a F/Fs

V instancích, které podporují hyperthreading, akcelerované sítě je podporována na instancích virtuálních počítačích se 4 nebo více virtuálních procesorů. Podporované řady jsou: D/DSv3, E/ESv3, Fsv2 a Ms/Mms

Další informace o instancích virtuálních počítačů najdete v [tématu Velikosti virtuálních počítačových sítí v systému Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Zrychlené vytváření sítí pomocí virtuálních počítačů s Linuxem

Azure Site Recovery podporuje povolení akcelerované sítě pro replikované virtuální počítače pouze v případě, že zdrojový virtuální počítač má povoleno uzuracené sítě. Pokud váš zdrojový virtuální počítač nemá povolenou funkci Accelerated Networking, můžete se naučit povolit virtuální počítače Accelerated Networking for Linux [zde](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure jsou podporované následující distribuce:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" s jádrem backports**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních ms
Akcelerovaná síť je podporována pro většinu velikostí instancí pro obecné účely a optimalizované pro výpočetní výkon y se 2 nebo více virtuálními procesory.  Tyto podporované řady jsou: D/DSv2 a F/Fs

V instancích, které podporují hyperthreading, akcelerované sítě je podporována na instancích virtuálních počítačích se 4 nebo více virtuálních procesorů. Podporované řady jsou: D/DSv3, E/ESv3, Fsv2 a Ms/Mms.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačích v Linuxu](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Povolení zrychlené sítě pro replikované virtuální hody

Když [povolíte replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure, site recovery automaticky zjistí, jestli mají síťová rozhraní virtuálních počítačů povolenou zrychlenou síť. Pokud je akcelerovaná síť již povolena, obnovení sítě automaticky nakonfiguruje akcelerované sítě v síťových rozhraních replikovaného virtuálního počítače.

Stav akcelerované sítě lze ověřit v části **Síťová rozhraní** v nastavení **výpočetních prostředků a sítě** pro replikovaný virtuální počítač.

![Zrychlené nastavení sítě](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Pokud jste po povolení replikace povolili funkci Akcelerované sítě ve zdrojovém virtuálním počítači, můžete povolit akcelerované síťové připojení pro síťová rozhraní replikovaného virtuálního počítače následujícím postupem:
1. Otevření **nastavení Výpočetních prostředků a sítě** pro replikovaný virtuální počítač
2. Klikněte na název síťového rozhraní v části **Síťová rozhraní**
3. V rozevíracím seznamu pro akcelerované sítě ve sloupci **Cíl** vyberte **Možnost Povoleno.**

![Povolení zrychlených sítí](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Výše uvedený proces by měl být také dodržen pro existující replikované virtuální počítače, které dříve neměly akcelerované sítě automaticky povoleny site recovery.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace [o výhodách akcelerované sítě](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Další informace o omezeních a omezeních akcelerovaných sítí pro [virtuální počítače se systémem Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) a [virtuálních počítačích s Linuxem](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikací.
