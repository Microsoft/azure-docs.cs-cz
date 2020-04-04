---
title: Virtuální počítač Azure nereaguje s chybou C01A001D při použití služby Windows Update
description: Tento článek obsahuje postup k vyřešení problémů, kdy aktualizace systému Windows generuje chybu a přestane reagovat ve virtuálním počítači Azure.
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
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633954"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Virtuální měsíč nereaguje s chybou C01A001D při použití služby Windows Update

Tento článek obsahuje kroky k vyřešení problémů, kde Windows Update (KB) generuje chybu a přestane reagovat ve virtuálním počítači Azure.

## <a name="symptoms"></a>Příznaky

Při použití [boot diagnostiky](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku z virtuálního zařízení, Windows Update (KB) probíhá se zobrazí, ale selže s kódem chyby: 'C01A001D'.

![nereagující služba Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Příčina

Základní soubor nelze vytvořit v systému souborů. Operační systém nemůže zapisovat soubory na disk.

## <a name="resolution"></a>Řešení

### <a name="process-overview"></a>Přehled procesů

1. [Vytvořte a získejte přístup k opravnému virtuálnímu virtuálnímu montovně](#create-and-access-a-repair-vm).
2. [Uvolněte místo na pevném disku](#free-up-space-on-the-hard-disk).
3. [Doporučeno: Před opětovnou sestavení virtuálního zařízení povolte sériovou konzolu a kolekci výpisu stavu paměti](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Znovu vytvořit virtuální hod](#rebuild-the-vm).

> [!NOTE]
> Když dojde k této chybě, hostovaný operační systém není funkční. Chcete-li tento problém vyřešit, je nutné tento problém vyřešit v režimu offline.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu montovi opravě

1. Postupujte [podle kroků 1-3 příkazů pro opravu virtuálních počítačích](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) a připravte si virtuální počítač.
2. Připojte se k virtuálnímu počítači pro opravu pomocí připojení ke vzdálené ploše.

### <a name="free-up-space-on-the-hard-disk"></a>Uvolnění místa na pevném disku

Pokud disk ještě není 1 Tb, je nutné změnit jeho velikost. Jakmile je disk 1 TB, proveďte vyčištění disku a defragmentaci jednotky.

1. Zkontrolujte, zda je disk plný. Pokud je disk nižší než 1 Tb, [rozbalte jej na maximálně 1 Tb pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Jakmile je disk 1 Tb, proveďte vyčištění disku.
    - [Odpojte datový disk od poškozeného virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Připojení datového disku k funkčnímu virtuálnímu virtuálnímu počítače](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - K uvolnění místa použijte [nástroj Vyčištění disku.](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)
3. Po změna velikosti a vyčištění defragmentujte jednotku:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    V závislosti na úrovni fragmentace to může trvat hodiny.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučeno: Před opětovnou sestavení virtuálního zařízení povolte sériovou konzolu a kolekci výpisu stavu paměti

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

    Povolit sériovou konzolu:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Ujistěte se, že volné místo na disku operačního systému je alespoň rovno velikosti paměti virtuálního počítače (RAM).

    Pokud není dostatek místa na disku operačního systému, změňte umístění, kde bude vytvořen soubor výpisu stavu paměti a odkazovat jej na datový disk připojený k virtuálnímu počítači a s dostatečným volným místem. Chcete-li změnit `%SystemRoot%` umístění, nahraďte písmenem jednotky (například "F:") datového disku v následujících příkazech:

    **Povolit konfiguraci s výpisem stavu operačního systému:**

    Načíst přerušený disk operačního systému:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Povolit na Ovládacích sadach001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Povolit na Ovládacím prvkuSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Uvolnění poškozeného disku operačního systému:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního virtuálního mísy

Pomocí [kroku 5 příkazů opravy virtuálních montovny](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) znovu sestavit virtuálního mísu.
