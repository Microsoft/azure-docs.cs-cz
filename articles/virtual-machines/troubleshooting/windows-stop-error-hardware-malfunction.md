---
title: Chyba stop systému Windows – selhání hardwaru
description: Tento článek popisuje kroky pro řešení problémů, při kterých došlo k chybě virtuálních počítačů s Windows Serverem 2008 s chybovou zprávou, která uvádí, že došlo k selhání hardwaru.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: eb4e0a246d6a33c3fad5f44b99a37997e4462f05
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663628"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Chyba stop systému Windows – selhání hardwaru

Tento článek popisuje kroky pro řešení problémů, při kterých došlo k chybě virtuálních počítačů s Windows Serverem 2008 s chybovou zprávou, která uvádí, že došlo k selhání hardwaru.

## <a name="symptoms"></a>Příznaky

Když k zobrazení obrazovky virtuálního počítače použijete [diagnostiku spouštění](./boot-diagnostics.md) , uvidíte, že snímek obrazovky zobrazuje modrou obrazovku s touto zprávou:

**\*\*\* Selhání hardwaru**

**Požádat dodavatele o podporu**

**\*\*\* Systém se zastavil. \*\*\***

#### <a name="blue-screen"></a>Modrá obrazovka

![Snímek obrazovky ukazuje chybu při selhání hardwaru modré obrazovky.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Konzola sériového portu

![Pokud je povolená sériová konzola, zobrazí se na snímku obrazovka zpráva selhání hardwaru v konzole sériového portu.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Příčina

Tato obrazovka se zobrazí v případě, že hostovaný operační systém není správně nastavený a pošle se nemaskovatelné přerušení (NMI). Chybová zpráva označuje, že program v režimu jádra vygeneroval výjimku, kterou obslužná rutina nezachytává. Můžete určit, která výjimka byla vygenerována shromážděním výpisu paměti.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu 

1. Nastavení klíče registru nemaskované přerušení (NMI) 
2. Vytvoření a přístup k opravnému virtuálnímu počítači 
3. Povolit kolekci sériového stavu a výpisu paměti 
4. Opětovné sestavení virtuálního počítače 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Nastavení klíče registru nemaskované přerušení (NMI)

1. Pomocí Azure Portal restartujte virtuální počítač, aby se operační systém Host spouštěl normálně. 
2. Po obnovení nějakého přístupu k virtuálnímu počítači otevřete příkazový řádek se zvýšenými oprávněními (Spustit jako správce). 
3. Klíč registru NMI nastavte pomocí následujícího příkazu:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Zobrazit další informace o příkazu REG ADD](https://docs.microsoft.com/windows-server/administration/windows-commands/reg-add)
4. *(Volitelné)* Kolekce výpisu paměti instalace:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Volitelné)* Nastavit přístup ke konzole sériového portu:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Zobrazit další informace o příkazu BCDEDIT](https://docs.microsoft.com/windows-server/administration/windows-commands/bcdedit)
6. Restartujte virtuální počítač pomocí následujícího příkazu:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Zobrazit další informace o příkazu pro vypnutí](https://docs.microsoft.com/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Problém by se teď měl opravit!

> [!NOTE]
> Po restartování otestujte svůj virtuální počítač, abyste se ujistili, že funguje jako normální. Pokud stále dochází k problémům, můžete pokračovat v další části, kde najdete další pokyny.

> [!TIP]
> Doporučujeme, abyste v části výše nastavili klíč registru nemaskované přerušení (NMI). Pokud se ale váš virtuální počítač nerestartuje normálně, pak se nemusí uskutečnit zamýšlené změny v registru hostovaného operačního systému. Pokud je to tento případ, můžete místo toho ručně přidat nastavení registru podle pokynů níže.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Připojte se k opravnému virtuálnímu počítači pomocí Připojení ke vzdálené ploše.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Povolit kolekci sériového stavu a výpisu paměti

Před opětovným sestavením virtuálního počítače se doporučuje povolit shromažďování výpisů paměti a sériová konzola. Uděláte to tak, že spustíte tento skript: 

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce). 
2. Seznamte se s daty úložiště BCD a určete identifikátor zaváděcího programu, který použijete v dalším kroku. 
    1. Pro virtuální počítač 1. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        V příkazu nahraďte `<BOOT PARTITON>` písmenem oddílu připojeným diskem, který obsahuje spouštěcí složku. 

        ![Snímek obrazovky ukazuje výstup výpisu úložiště BCD na virtuálním počítači generace 1, který obsahuje seznam v části spouštěcí zavaděč Windows číslo identifikátoru.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. U virtuálního počítače 2. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * V příkazu nahraďte `<LETTER OF THE EFI SYSTEM PARTITION>` písmenem systémového oddílu EFI.
        * Může být užitečné spustit konzolu pro správu disků a identifikovat příslušný systémový oddíl označený jako *systémový oddíl EFI*.
        * Identifikátor může být jedinečný identifikátor GUID, nebo může být výchozím *Bootmgr*.
3. Spusťte následující příkazy, aby se aktivovala sériová konzola:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * V příkazu nahraďte `<VOLUME LETTER WHERE THE BCD FOLDER IS>` písmenem složky BCD.
    * V příkazu nahraďte `<BOOT LOADER IDENTIFIER>` identifikátorem, který jste našli v předchozím kroku.
4. Ověřte, zda je volné místo na disku s operačním systémem větší než velikost paměti (RAM) na virtuálním počítači. 
    1. Pokud není dostatek místa na disku s operačním systémem, měli byste změnit umístění, kde se vytvoří soubor s výpisem paměti. Místo vytvoření souboru na disku s operačním systémem ho můžete odkazovat na jakýkoli jiný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte **% systemroot%** písmenem jednotky (například **F:**) datového disku v níže uvedených příkazech. 
    2. Zadejte následující příkazy (navrhovaná konfigurace výpisu paměti):

    **Načíst podregistr registru z poškozeného disku s operačním systémem:**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **Povolit na ControlSet001:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Povolit na ControlSet002:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Uvolnit poškozený disk s operačním systémem:**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>Znovu sestavit virtuální počítač

* K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení chyb při spouštění virtuálních počítačů Azure](./boot-error-troubleshoot.md)