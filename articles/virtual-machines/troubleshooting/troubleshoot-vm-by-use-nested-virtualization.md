---
title: Poradce při potížích s vadným virtuálním počítačem Azure pomocí vnořené virtualizace v Azure | Dokumenty společnosti Microsoft
description: Jak řešit problém virtuálního počítače Azure pomocí vnořené virtualizace v Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119614"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Poradce při potížích s vadným virtuálním počítačem Azure pomocí vnořené virtualizace v Azure

Tento článek ukazuje, jak vytvořit vnořené virtualizační prostředí v Microsoft Azure, takže můžete připojit disk vadného virtuálního počítače na hostiteli Hyper-V (Rescue VM) pro účely řešení potíží.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné připojit vadný virtuální hod, záchranný virtuální ho disponuje použít stejný typ účtu úložiště (standard nebo premium) jako vadný virtuální ho.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1: Vytvoření záchranného virtuálního virtuálního mísy a instalace role Hyper-V

1.  Vytvořte nový záchranný virtuální virtuální movitý virtuální mísa:

    -  Operační systém: Windows Server 2016 Datacenter

    -  Velikost: Všechny řady V3 s alespoň dvěma jádry, které podporují vnořenou virtualizaci. Další informace najdete [v tématu zavedení nové velikosti virtuálních počítače Dv3 a Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Stejné umístění, účet úložiště a skupina prostředků jako vadný virtuální účet.

    -  Vyberte stejný typ úložiště jako vadný virtuální virtuální účet (standardní nebo prémiový).

2.  Po vytvoření záchranného virtuálního počítače, vzdálená plocha k virtuálnímu počítači pro záchranu.

3.  Ve Správci serveru vyberte **Spravovat** > **přidat role a funkce**.

4.  V části **Typ instalace** vyberte **instalaci založenou na rolích nebo funkcích**.

5.  V části **Vybrat cílový server** zkontrolujte, zda je vybrán záchranný virtuální virtuální soud.

6.  Vyberte roli > Přidat**funkce** **technologie Hyper-V**.

7.  V části **Funkce** vyberte **Další.**

8.  Pokud je k dispozici virtuální přepínač, vyberte jej. V opačném případě vyberte **další**.

9.  V části **Migrace** vyberte **Další.**

10. V části **Výchozí obchody** vyberte **Další**.

11. Zaškrtněte políčko, chcete-li v případě potřeby server restartovat automaticky.

12. Vyberte **Install** (Nainstalovat).

13. Povolte serveru nainstalovat roli Hyper-V. To trvá několik minut a server se automaticky restartuje.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2: Vytvoření vadného virtuálního počítače na serveru Hyper-V záchranného virtuálního počítače

1.  [Vytvořte snímek disk](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) pro disk operačního systému virtuálního počítače, který má problém, a pak připojte snímek disk k opětovnému připojení virtuálního počítače.

2.  Vzdálená plocha k virtuálnímu počítači pro záchranu.

3.  Správa disku (diskmgmt.msc). Ujistěte se, že disk vadného virtuálního počítače je nastavena na **offline**.

4.  Otevřít Správce technologie Hyper-V: Ve **Správci serveru**vyberte **roli Hyper-V**. Klepněte pravým tlačítkem myši na server a vyberte **správce technologie Hyper-V**.

5.  Ve Správci technologie Hyper-V klikněte pravým tlačítkem myši na záchranný virtuální počítač a pak vyberte **nový** > **virtuální počítač** > **další**.

6.  Zadejte název virtuálního soudu a pak vyberte **Další**.

7.  Vyberte **generace 1**.

8.  Nastavte spouštěcí paměť na 1024 MB nebo více.

9. Pokud je to možné, vyberte síťový přepínač Hyper-V, který byl vytvořen. Jinak přejděte na další stránku.

10. Vyberte **Připojit virtuální pevný disk později**.

    ![obrázek o volbě Připojit virtuální pevný disk později](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Vyberte **Dokončit** při vytvoření virtuálního virtuálního soudu.

12. Klikněte pravým tlačítkem myši na vytvořený virtuální virtuální počítače a potom vyberte **Nastavení**.

13. Vyberte **řadič IDE 0**, vyberte **pevný disk**a klepněte na tlačítko **Přidat**.

    ![obrázek o přidává nový pevný disk](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Ve **fyzickém pevném disku**vyberte disk vadného virtuálního počítače, který jste připojili k virtuálnímu počítači Azure. Pokud nejsou uvedeny žádné disky, zkontrolujte, zda je disk nastaven na offline pomocí správy disků.

    ![obrázek o připojení disku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Vyberte **Apply** (Použít) a pak vyberte **OK**.

16. Poklikejte na virtuální počítač a spusťte ho.

17. Teď můžete pracovat na virtuálním počítači jako místní virtuální počítač. Můžete postupovat podle všech kroků řešení potíží, které potřebujete.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Krok 3: Nahrazení disku operačního systému používaného vadným virtuálním počítačem

1.  Po získání virtuálního virtuálního provozu zpět do režimu online, vypněte virtuální ho ve správci Hyper-V.

2.  [Odpojte a odpojte opravený disk operačního systému](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Nahraďte disk operačního systému používaný virtuálním počítačem opraveným diskem operačního systému](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálním počítači se systémem Windows](troubleshoot-app-connection.md).
