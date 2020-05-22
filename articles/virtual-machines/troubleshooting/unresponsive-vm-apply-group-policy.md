---
title: Virtuální počítač Azure při použití zásad nereaguje.
description: Tento článek popisuje kroky pro řešení problémů, při kterých se při použití zásady během spouštění na virtuálním počítači Azure zablokuje obrazovka Load.
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
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749274"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Virtuální počítač přestane reagovat při použití zásad Zásady skupiny místní uživatelé & skupiny.

Tento článek popisuje kroky pro řešení problémů, při kterých se při použití zásady během spouštění na virtuálním počítači Azure zablokuje obrazovka Load.

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) zobrazíte snímek obrazovky virtuálního počítače, obrazovka se zablokuje načítání s touto zprávou: '*použití zásady skupiny místních uživatelů a zásad skupiny*'.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Snímek obrazovky s použitím Zásady skupiny načítání zásad místních uživatelů a skupin (Windows Server 2012 R2)":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Snímek obrazovky s použitím Zásady skupiny načítání zásad místních uživatelů a skupin (Windows Server 2012)":::

## <a name="cause"></a>Příčina

V případě, že se zásady pokusí vyčistit staré profily uživatelů, dochází ke konfliktům zámků.

> [!NOTE]
> To platí jenom pro systémy Windows Server 2012 a Windows Server 2012 R2.

Tady je problematické zásady:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. [Vytvoření a přístup k opravnému virtuálnímu počítači](#step-1-create-and-access-a-repair-vm)
2. [Zakázat zásadu](#step-2-disable-the-policy)
3. [Povolit kolekci sériového stavu a výpisu paměti](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Opětovné sestavení virtuálního počítače](#step-4-rebuild-the-vm)

> [!NOTE]
> Pokud dojde k této chybě spuštění, hostovaný operační systém není funkční. Je nutné řešit potíže v offline režimu.

### <a name="step-1-create-and-access-a-repair-vm"></a>Krok 1: vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) Připravte opravný virtuální počítač.
2. Použijte Připojení ke vzdálené ploše Připojte se k opravnému virtuálnímu počítači.

### <a name="step-2-disable-the-policy"></a>Krok 2: zakázání zásady

1. V okně opravit virtuální počítač otevřete Editor registru.
2. Vyhledejte klíč **HKEY_LOCAL_MACHINE** a v nabídce vyberte možnost načíst **soubor**  >  **...**

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Snímek obrazovky ukazuje zvýrazněný HKEY_LOCAL_MACHINE a nabídku obsahující podregistr Load.":::

    - Možnost Načíst podregistr umožňuje načíst klíče registru z offline systému, v tomto případě poškozený disk připojený k opravnému virtuálnímu počítači.
    - Nastavení pro systém je uloženo v systému `HKEY_LOCAL_MACHINE` a lze jej zkrátit jako "HKLM".
3. Na připojeném disku přejdete do `\windows\system32\config\SOFTWARE` souboru a otevřete ho.

    1. Zobrazí se výzva k zadání názvu. Zadejte BROKENSOFTWARE.<br/>
    2. Chcete-li ověřit, zda byl BROKENSOFTWARE načten, rozbalte **HKEY_LOCAL_MACHINE** a vyhledejte přidaný BROKENSOFTWARE klíč.
4. Přejděte na BROKENSOFTWARE a ověřte, jestli v načteném podregistru existuje klíč CleanupProfile.

    1. Pokud klíč existuje, pak je nastavená zásada CleanupProfile, její hodnota představuje zásady uchovávání informací ve dnech. Pokračujte v odstraňování klíče.<br/>
    2. Pokud klíč neexistuje, zásada CleanupProfile se nenastaví. [Odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), včetně souboru Memory. dmp umístěného v adresáři systému Windows připojeného disku s operačním systémem.

5. Odstraňte klíč CleanupProfiles pomocí tohoto příkazu:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Uvolněte podregistr BROKENSOFTWARE pomocí tohoto příkazu:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Krok 3: povolení kolekce sériového výpisu a výpisu paměti

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte tento skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tyto příkazy:

    **Povolit sériovou konzolu**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Ověřte, zda je volné místo na disku s operačním systémem přinejmenším rovno velikosti paměti virtuálního počítače (RAM).

    Pokud není dostatek místa na disku s operačním systémem, změňte umístění výpisu paměti a Projděte ho na připojený datový disk s dostatkem volného místa. Chcete-li změnit umístění, nahraďte "% SystemRoot%" písmenem jednotky (např. "F:") datového disku v následujících příkazech.

    **Navrhovaná konfigurace pro povolení výpisu operačního systému**:

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

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) znovu sestavte virtuální počítač.

Pokud je problém vyřešen, zásada byla zakázána místně. V případě trvalého řešení nepoužívejte zásady CleanupProfiles na virtuálních počítačích. K provedení vyčištění profilu použijte jinou metodu.

Nepoužívat tyto zásady:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Další kroky

Pokud narazíte na problémy při použití web Windows Update, přečtěte si článek o tom, že [virtuální počítač nereaguje s chybou "C01A001D" při použití web Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).