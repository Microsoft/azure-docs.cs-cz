---
title: Modrá obrazovka chyby při dalším spuštění virtuálního počítače Azure | Dokumentace Microsoftu
description: Zjistěte, jak problém vyřešit modrá obrazovka chyby přijaté při dalším spuštění | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 00/19/2018
ms.author: genli
ms.openlocfilehash: 6525067ec7a7a3154d1439f15736a1038ab0408c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167884"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows se zobrazuje chyba modrá obrazovka při dalším spuštění virtuálního počítače Azure
Tento článek popisuje modrá obrazovka chyby, které můžete narazit při spuštění Windows virtuální počítač (VM) v Microsoft Azure. Poskytuje kroky při shromažďování dat pro lístek podpory. 

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptom"></a>Příznak 

Virtuální počítač Windows nespustí. Když vrátíte se změnami na snímcích obrazovky spouštěcí [Diagnostika spouštění](./boot-diagnostics.md), najdete v jednom z následujících chybových zpráv v modrá obrazovka:

- Naše PC narazili na problém a vyžaduje restartování. Shromáždíme jenom některé informace o chybě, a potom restartujete.
- Váš počítač narazili na problém a vyžaduje restartování.

Tato část uvádí běžné chybové zprávy, se můžete setkat při správě virtuálních počítačů:

## <a name="cause"></a>Příčina

Může dojít z několika příčin jako proč získali byste chybu stop. Nejčastější příčiny patří:

- Problém s ovladačem
- Poškozená systémový soubor nebo paměti
- Zakázané sektoru paměti přistupuje k aplikaci

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, je třeba prvními, kdo se shromažďování výpisu stavu systému souboru při chybě a obraťte se na podporu pomocí souboru s výpisem paměti. Shromažďování výpisu stavu systému souborů, postupujte podle těchto kroků:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. Pořízení snímku disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal.md). 
3. K obnovení virtuálního počítače pomocí vzdálené plochy.

### <a name="collect-dump-file-and-submit-a-support-ticket"></a>Shromažďování výpisu stavu systému souborů a vyplňte lístek podpory

1. Na virtuální počítač pro obnovení přejděte do složky systému windows v připojeném disku s operačním systémem. Pokud písmeno jednotky, která je přiřazena připojeném disku s operačním systémem je F, budete muset přejít do F:\Windows.
2. Vyhledejte soubor memory.dmp th a potom [vyplňte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pomocí souboru s výpisem paměti. 

Pokud nemůže najít soubor s výpisem paměti, přesuňte následující krok, povolení protokolu výpisu stavu systému a konzoly sériového portu.

### <a name="enable-dump-log-and-serial-console"></a>Povolení protokolu výpisu stavu systému a konzoly sériového portu

Pokud chcete povolit protokol s výpisem paměti a konzoly sériového portu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F.  Nahraďte ji odpovídající hodnotou ve virtuálním počítači.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Ujistěte se, zda je dostatek místa na disku přidělit tolik paměti, jako paměť RAM, které závisí na velikosti, který vyberete pro tento virtuální počítač.
    2. Pokud není dostatek místa nebo je to velká velikost virtuálního počítače (řady G a GS E), může pak změnit umístění, kde tento soubor se vytvoří a, která odkazují na libovolný datový disk, který je připojen k virtuálnímu počítači. K tomuto účelu bude nutné změnit následující klíč:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odpojit disk s operačním systémem a znovu připojte disk s operačním systémem k virtuálnímu počítači ovlivněné](troubleshoot-recovery-disks-portal.md).
4. Spusťte virtuální počítač pro reprodukci problému a vygeneruje se soubor s výpisem paměti.
5. Připojení disku s operačním systémem k obnovení virtuálního počítače, shromažďování výpisu stavu systému souborů a potom [vyplňte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pomocí souboru s výpisem paměti.



