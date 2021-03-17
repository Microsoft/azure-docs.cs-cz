---
title: Povolení akcelerované sítě pro zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Popisuje, jak povolit akcelerované síťové služby s Azure Site Recovery pro zotavení po havárii virtuálního počítače Azure.
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000245"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Urychlené používání sítě pomocí zotavení po havárii virtuálních počítačů Azure

Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z DataPath, snižuje latenci, kolísání a využití CPU a používá se u nejnáročnějších síťových úloh na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními počítači s a bez urychlení sítě:

![Porovnání](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery vám umožní využívat výhody akcelerované sítě pro virtuální počítače Azure, u kterých došlo k převzetí služeb při selhání do jiné oblasti Azure. Tento článek popisuje, jak můžete povolit akcelerované síťové služby pro virtuální počítače Azure replikované pomocí Azure Site Recovery.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte:
-   [Architektura replikace](azure-to-azure-architecture.md) virtuálních počítačů Azure
-   [Nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure
-   [Převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) Virtuální počítače Azure

## <a name="accelerated-networking-with-windows-vms"></a>Urychlení sítě s virtuálními počítači s Windows

Azure Site Recovery podporuje povolování akcelerovaných sítí pro replikované virtuální počítače pouze v případě, že má zdrojový virtuální počítač aktivované akcelerované síťové služby. Pokud váš zdrojový virtuální počítač nemá povolené urychlené síťové služby, můžete [zjistit, jak](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)povolit akcelerované sítě pro virtuální počítače s Windows.

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure se podporují následující distribuce:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované sítě se podporují na většině účelových a výpočetních instancí optimalizovaných pro výpočty s 2 nebo více vCPU.  Tyto podporované řady: D/DSv2 a F/FS

Na instancích, které podporují multithreading, se zrychluje síť pro instance virtuálních počítačů se 4 nebo více vCPU. Podporované řady: D/DSv3, E/ESv3, Fsv2 a MS/MMS

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Urychlení sítě pomocí virtuálních počítačů se systémem Linux

Azure Site Recovery podporuje povolování akcelerovaných sítí pro replikované virtuální počítače pouze v případě, že má zdrojový virtuální počítač aktivované akcelerované síťové služby. Pokud váš zdrojový virtuální počítač nemá povolené urychlené síťové služby, můžete zjistit, jak povolit akcelerované sítě pro [virtuální počítače se](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)systémem Linux.

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure se podporují následující distribuce:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "roztažení" s jádrem pro porty**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované sítě se podporují na většině účelových a výpočetních instancí optimalizovaných pro výpočty s 2 nebo více vCPU.  Tyto podporované řady: D/DSv2 a F/FS

Na instancích, které podporují multithreading, se zrychluje síť pro instance virtuálních počítačů se 4 nebo více vCPU. Podporované řady jsou: D/DSv3, E/ESv3, Fsv2 a MS/MMS.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů se systémem Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Povolení akcelerovaných síťových služeb pro replikované virtuální počítače

Pokud [povolíte replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure, Site Recovery automaticky zjistí, jestli jsou v síťových rozhraních virtuálních počítačů povolené urychlení sítě. Pokud je již urychlené síťové služby povoleno, Site Recovery bude automaticky konfigurovat urychlené síťové rozhraní replikovaného virtuálního počítače.

Stav akcelerované sítě se dá ověřit v části **Síťová rozhraní** v nastavení **výpočty a síť** pro replikovaný virtuální počítač.

![Nastavení akcelerované sítě](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Pokud jste na zdrojovém virtuálním počítači povolili akcelerované sítě po povolení replikace, můžete pro síťová rozhraní replikovaných virtuálních počítačů povolit akcelerované síťové rozhraní následujícím postupem:
1. Otevřete nastavení **výpočty a síť** pro replikovaný virtuální počítač.
2. V části **Síťová rozhraní** klikněte na název síťového rozhraní.
3. V rozevíracím seznamu vyberte možnost **povoleno** pro akcelerované síťové služby v rámci **cílového** sloupce.

![Povolit akcelerované síťové služby](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

U stávajících replikovaných virtuálních počítačů, u kterých se předtím nepoužívaly urychlené síťové sítě, by se měly provést i výše uvedené procesy Site Recovery.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [výhodách akcelerovaných síťových](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)služeb.
- Přečtěte si další informace o omezeních a omezeních akcelerované sítě pro [virtuální počítače s Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) a [virtuální počítače](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)se systémem Linux.
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikace.