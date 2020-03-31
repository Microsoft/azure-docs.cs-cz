---
title: Spuštění virtuálního počítače se zaseklo na "Příprava Windows. Nevypínat počítač" v Azure | Dokumenty společnosti Microsoft
description: Zaveďte kroky k řešení problému, ve kterém se zaseklo spuštění virtuálního počítače na "Příprava systému Windows.Introduce the steps to troubleshoot to troubleshoot the problem in which VM startup is stuck on "Getting Windows ready. Nevypínejte počítač.
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
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749636"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Spuštění virtuálního počítače se zaseklo na "Příprava Windows. Nevypínat počítač" v Azure

Tento článek popisuje obrazovky Příprava a Příprava systému Windows, se kterými se můžete setkat při spuštění virtuálního počítače (VM) windows v Microsoft Azure. Poskytuje kroky, které vám pomohou shromažďovat data pro lístek podpory.

 

## <a name="symptoms"></a>Příznaky

Virtuální ho virtuálního ms systému Windows se nespustí. Při použití **boot diagnostiky** získat snímek obrazovky virtuálního virtuálního soudu, může se zobrazit, že virtuální ho dispozičního programu se zobrazí zpráva "Příprava" nebo "Příprava systému Windows".

![Příklad zprávy pro Systém Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Příklad zprávy](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Příčina

Obvykle k tomuto problému dochází, když server provádí konečné restartování po změně konfigurace. Změna konfigurace může být inicializována aktualizacemi systému Windows nebo změnami rolí nebo funkcí serveru. Pokud byla velikost aktualizací velká, potřebuje operační systém v systému Windows Update více času na změnu konfigurace.

## <a name="collect-an-os-memory-dump"></a>Shromáždění výpisu stavu paměti operačního systému

Pokud se problém nevyřeší po čekání na změny procesu, budete muset shromáždit soubor s výpisem stavu paměti a podporu kontaktu. Chcete-li soubor s výpisem stavu paměti shromáždit, postupujte takto:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. Pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
3. Vzdálená plocha k virtuálnímu počítači pro obnovení. 
4. Pokud je disk operačního systému zašifrován, musíte před přechodem k dalšímu kroku šifrování vypnout. Další informace naleznete [v tématu Dešifrování šifrovaného disku operačního systému ve virtuálním počítače, který nelze spustit](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Vyhledání souboru s výpisem stavu paměti a odeslání lístku podpory

1. Na virtuálním počítači pro obnovení přejděte do složky Windows na připojeném disku operačního systému. Pokud je písmeno ovladače přiřazené k připojenému disku operačního systému F, musíte přejít na f:\Windows.
2. Vyhledejte soubor memory.dmp a [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti. 

Pokud nemůžete najít soubor s výpisem stavu paměti, přesuňte další krok a povolte protokol výpisu a konzolu Serial Console.

### <a name="enable-dump-log-and-serial-console"></a>Povolení protokolu výpisu a sériové konzoly

Chcete-li povolit protokol s výpisem stavu paměti a konzolu Serial Console, spusťte následující skript.

1. Otevřete relaci příkazu Se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F.  Nahraďte ji příslušnou hodnotou ve virtuálním počítači.

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

    1. Ujistěte se, že je dostatek místa na disku přidělit tolik paměti jako RAM, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.
    2. Pokud není dostatek místa nebo je to velký virtuální počítače (G, GS nebo E řady), můžete pak změnit umístění, kde bude vytvořen tento soubor a odkazovat, že na jakýkoli jiný datový disk, který je připojen k virtuálnímu počítače. Chcete-li to provést, budete muset změnit následující klíč:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odpojte disk operačního systému a potom znovu připojte disk operačního systému k ovlivněnému virtuálnímu počítače](../windows/troubleshoot-recovery-disks-portal.md).
4. Spusťte virtuální ho spouštěč a získejte přístup k konzole Serial Console.
5. Chcete-li aktivovat výpis stavu paměti, vyberte možnost **Odeslat nemaskovatelné přerušení (NMI).**
    ![obrázek o tom, kam odeslat nemaskovatelné přerušení](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení znovu, shromažďovat soubor s výpisem stavu paměti.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Po shromáždění souboru s výpisem stavu paměti se obraťte na [podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a analyzujte hlavní příčinu.