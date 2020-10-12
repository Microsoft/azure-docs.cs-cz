---
title: Virtuální počítač Azure při použití zásad nereaguje.
description: Tento článek popisuje kroky pro řešení problémů, při kterých obrazovka Load nereaguje při použití zásady při spuštění ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526006"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>Virtuální počítač nereaguje při použití Zásady skupiny zásady místních uživatelů a skupin.

Tento článek popisuje kroky pro řešení problémů, při kterých obrazovka Load nereaguje, když virtuální počítač Azure používá zásadu při spuštění.

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, obrazovka se zablokuje načítání s touto zprávou: "použití zásady skupiny místních uživatelů a skupin."

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Snímek obrazovky s použitím Zásady skupiny načítání zásad místních uživatelů a skupin (Windows Server 2012 R2)":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Snímek obrazovky s použitím Zásady skupiny načítání zásad místních uživatelů a skupin (Windows Server 2012 R2)":::

## <a name="cause"></a>Příčina

V případě, že se zásady pokusí vyčistit staré profily uživatelů, dochází ke konfliktům zámků.

> [!NOTE]
> To platí jenom pro systémy Windows Server 2012 a Windows Server 2012 R2.

Tady je problematické zásady:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. [Vytvoření a přístup k opravnému virtuálnímu počítači](#step-1-create-and-access-a-repair-vm)
1. [Zakázat zásadu](#step-2-disable-the-policy)
1. [Povolit kolekci sériového stavu a výpisu paměti](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Opětovné sestavení virtuálního počítače](#step-4-rebuild-the-vm)

> [!NOTE]
> Pokud dojde k této chybě spuštění, hostovaný operační systém nebude funkční. Je nutné řešit potíže v offline režimu.

### <a name="step-1-create-and-access-a-repair-vm"></a>Krok 1: vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) Připravte opravný virtuální počítač.
2. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.

### <a name="step-2-disable-the-policy"></a>Krok 2: zakázání zásady

1. V okně opravit virtuální počítač otevřete Editor registru.
1. Vyhledejte klíč **HKEY_LOCAL_MACHINE** a v nabídce **File**vyberte příkaz  >  **načíst** soubor.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Snímek obrazovky s použitím Zásady skupiny načítání zásad místních uživatelů a skupin (Windows Server 2012 R2)" /v CleanupProfiles /f
    ```
1.  Uvolněte podregistr BROKENSOFTWARE pomocí tohoto příkazu:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Krok 3: povolení kolekce sériového výpisu a výpisu paměti

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte tento skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními. (Spustit jako správce.)
1. Spusťte tyto příkazy, aby se aktivovala konzola sériového portu:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Ověřte, zda je volné místo na disku s operačním systémem přinejmenším rovno velikosti paměti virtuálního počítače (RAM).

    Pokud není dostatek místa na disku s operačním systémem, změňte umístění výpisu paměti a Projděte ho na připojený datový disk s dostatkem volného místa. Chcete-li změnit umístění, nahraďte "% SystemRoot%" písmenem jednotky (například "F:") datového disku v následujících příkazech.

    **Navrhovaná konfigurace pro povolení výpisu operačního systému**

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

### <a name="step-4-rebuild-the-vm"></a>Krok 4: opětovné sestavení virtuálního počítače

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač.

Pokud je problém opravený, zásada je teď zakázaná místně. V případě trvalého řešení nepoužívejte zásady CleanupProfiles na virtuálních počítačích. K provedení vyčištění profilu použijte jinou metodu.

Nepoužívat tyto zásady:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Další kroky

Pokud máte při použití web Windows Update problémy, přečtěte si článek o tom, že [virtuální počítač nereaguje s chybou "C01A001D" při použití web Windows Update](./unresponsive-vm-apply-windows-update.md).
