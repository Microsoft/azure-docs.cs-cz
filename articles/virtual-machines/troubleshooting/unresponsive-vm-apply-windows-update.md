---
title: Virtuální počítač Azure při použití web Windows Update nereaguje s chybou C01A001D
description: Tento článek popisuje kroky pro řešení problémů, kdy Windows Update vygeneruje chybu a ve virtuálním počítači Azure přestane reagovat.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 145d58013b259bf14c26d0840b8cd4299cfe85f0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632618"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Při použití web Windows Update virtuální počítač neodpovídá chybě "C01A001D"

Tento článek popisuje kroky pro řešení problémů, které web Windows Update (KB) vygeneruje chybu a ve virtuálním počítači Azure přestane reagovat.

## <a name="symptoms"></a>Příznaky

Při použití [diagnostiky spouštění](./boot-diagnostics.md) k zobrazení snímku obrazovky virtuálního počítače se zobrazí web Windows Update (KB), ale dojde k chybě s kódem chyby: ' C01A001D '.

![nereagující web Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Příčina

V systému souborů nelze vytvořit základní soubor. Operační systém nemůže zapsat soubory na disk.

## <a name="resolution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

1. [Vytvořte a získejte přístup k opravnému virtuálnímu počítači](#create-and-access-a-repair-vm).
2. [Uvolněte místo na pevném disku](#free-up-space-on-the-hard-disk).
3. [Doporučené: před opětovným sestavením virtuálního počítače povolte kolekci sériového výpisu a výpisu paměti](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Znovu sestavte virtuální počítač](#rebuild-the-vm).

> [!NOTE]
> Pokud k této chybě dojde, hostovaný operační systém nebude funkční. Chcete-li tento problém vyřešit, je nutné řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Připojte se k opravnému virtuálnímu počítači pomocí Připojení ke vzdálené ploše.

### <a name="free-up-space-on-the-hard-disk"></a>Uvolněte místo na pevném disku

Pokud disk ještě není 1 TB, je nutné změnit jeho velikost. Jakmile je disk 1 TB, proveďte Vyčištění disku a defragmentaci jednotky.

1. Ověřte, zda je disk plný. Pokud je disk menší než 1 TB, [rozbalte ho do velikosti 1 TB pomocí PowerShellu](../windows/expand-os-disk.md).
2. Jakmile je disk 1 TB, proveďte Vyčištění disku.
    - [Odpojte datový disk od poškozeného virtuálního počítače](../windows/detach-disk.md).
    - [Připojte datový disk k fungujícímu virtuálnímu počítači](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Uvolněte místo pomocí [nástroje Vyčištění disku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
3. Po změně velikosti a vyčištění jednotky defragmentujte:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    V závislosti na úrovni fragmentace může to trvat hodiny.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: před opětovným sestavením virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

    Povolit sériovou konzolu:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Zajistěte, aby bylo volné místo na disku s operačním systémem přinejmenším stejné jako velikost paměti virtuálního počítače (RAM).

    Pokud není dostatek místa na disku s operačním systémem, změňte umístění, kde se vytvoří soubor s výpisem paměti, a uveďte ho na datový disk připojený k virtuálnímu počítači a s dostatkem volného místa. Chcete-li změnit umístění, nahraďte `%SystemRoot%` písmeno jednotky (například F:) datového disku v následujících příkazech:

    **Povolit navrhovanou konfiguraci výpisu operačního systému:**

    Načíst poškozený disk s operačním systémem:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Povolit na ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Povolit na ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Uvolnit poškozený disk s operačním systémem:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač.
