---
title: Spuštění virtuálního počítače se zablokuje při přípravě Windows. Nevypínejte počítač v Azure | Microsoft Docs
description: Zaveďte kroky pro řešení potíží, při kterých se spuštění virtuálního počítače zablokuje při přípravě na Windows. Nevypínejte počítač.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: f3e4d51b4d41fa0dc23e9b12ac0251c14215de5c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633006"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Spuštění virtuálního počítače se zablokuje při přípravě Windows. Nevypínejte počítač v Azure

Tento článek popisuje úvodní a přípravné obrazovky Windows, se kterými se můžete setkat při spuštění virtuálního počítače s Windows v Microsoft Azure. Poskytuje kroky, které vám pomůžou shromáždit data pro lístek podpory.

 

## <a name="symptoms"></a>Příznaky

Virtuální počítač s Windows se nespustí. Když pomocí **diagnostiky spouštění** získáte snímek obrazovky virtuálního počítače, může se stát, že se na virtuálním počítači zobrazí zpráva "Příprava na přípravu" nebo "Příprava Windows na přípravu".

![Příklad zprávy pro Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Příklad zprávy](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Příčina

K tomuto problému obvykle dochází, když server provádí konečné restartování po změně konfigurace. Změna konfigurace může být inicializována aktualizacemi systému Windows nebo změnami rolí/funkcí serveru. Pro web Windows Update, pokud je velikost aktualizací velká, operační systém potřebuje více času na změnu konfigurace změn.

## <a name="collect-an-os-memory-dump"></a>Shromažďování výpisu paměti operačního systému

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud se problém nevyřeší po čekání na zpracování změn, budete potřebovat shromáždit soubor s výpisem paměti a kontaktovat podporu. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
3. Vzdálená plocha do virtuálního počítače pro obnovení. 
4. Pokud je disk s operačním systémem zašifrovaný, musíte před přechodem k dalšímu kroku vypnout šifrování. Další informace najdete v tématu [dešifrujte zašifrovaný disk s operačním systémem ve virtuálním počítači, který se nedá spustit](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Vyhledat soubor s výpisem paměti a odeslat lístek podpory

1. Na virtuálním počítači pro obnovení otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, je nutné přejít na F:\Windows.
2. Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti. 

Pokud soubor s výpisem paměti nemůžete najít, přejděte k dalšímu kroku a Povolte protokol výpisu a sériovou konzolu.

### <a name="enable-dump-log-and-serial-console"></a>Povolit protokol výpisu paměti a sériová konzola

Pokud chcete povolit protokol výpisu a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F.  Nahraďte ji odpovídající hodnotou ve vašem VIRTUÁLNÍm počítači.

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

    1. Ujistěte se, že na disku je dostatek místa pro přidělení tolika paměti jako paměti RAM, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.
    2. Pokud není dostatek místa nebo se jedná o virtuální počítač velké velikosti (G, GS nebo E-series), můžete změnit umístění, kde se tento soubor vytvoří, a odkazovat na jakýkoli jiný datový disk, který je připojený k virtuálnímu počítači. K tomu budete muset změnit následující klíč:
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [Odpojte disk s operačním systémem a pak znovu připojte disk s operačním systémem k ovlivněnému virtuálnímu počítači](./troubleshoot-recovery-disks-portal-windows.md).
4. Spusťte virtuální počítač a otevřete konzolu sériového portu.
5. Chcete-li aktivovat výpis paměti, vyberte možnost **Odeslat nemaskovatelné přerušení (NMI)** .
    ![obrázek, kde se má poslat nemaskovatelné přerušení](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Znovu připojte disk s operačním systémem k virtuálnímu počítači pro obnovení, shromážděte soubor výpisu paměti.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Po shromáždění souboru s výpisem paměti se obraťte na [podporu Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby se provedla analýza hlavní příčiny.
