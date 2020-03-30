---
title: Chyba zastavení systému Windows -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373359"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Chyba zastavení systému Windows - #0x000000EF "Kritický proces zemřel"

Tento článek obsahuje kroky k vyřešení problémů, kdy kritický proces zemře při spuštění ve virtuálním počítači Azure.

## <a name="symptom"></a>Příznak

Při použití [boot diagnostiky](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku z virtuálního aplikace, uvidíte, že snímek obrazovky zobrazí chybu *#0x000000EF* se zprávou kritický *proces zemřel*.

!["Váš počítač narazil na problém a je třeba restartovat. Právě shromažďujeme nějaké informace o chybách a pak můžete restartovat. (##% dokončeno) Pokud byste chtěli vědět více, můžete hledat on-line později pro tuto chybu: 0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Příčina

Obvykle je to způsobeno selháním kritického systémového procesu během spuštění. Další informace o kritických problémech s procesem naleznete na adrese "[Kontrola chyb 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

1. Vytvoření a přístup k virtuálnímu virtuálnímu mísu opravy.
2. Opravte jakékoli poškození operačního spo.
3. **Doporučeno**: Před sestavením virtuálního virtuálního zařízení povolte sériovou konzolu a kolekci výpisu stavu paměti.
4. Znovu sestavit virtuální ho dispozice.

> [!NOTE]
> Při výskytu této chyby při spuštění není hostovaný operační systém funkční. Tento problém vyřešíte v režimu offline.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu mněmu pro opravy

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) připravte virtuální počítač.
2. Pomocí připojení ke vzdálené ploše se připojte k virtuálnímu počítači pro opravu.

### <a name="fix-any-os-corruption"></a>Oprava poškození operačního spoje

1. Otevřete příkazový řádek se zvýšenými oprávněními.
2. Spusťte následující příkaz Kontrola systémových souborů (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Ve < boot diskové jednotky > je spouštěcí svazek operačního systému Opravy (obvykle "C:") a < BROKEN DISK DRIVE > bude písmeno jednotky pro připojený disk z poškozeného virtuálního počítače. Nahraďte symboly větší než / méně, stejně jako text, který je v nich obsažen, například "< text zde >", příslušným písmenem.

3. Dále použijte [krok 5 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) k opětovnému sestavení virtuálního počítače a uvidíme, jestli se spustí.
4. Pokud virtuální hod stále není bootování, pak nadále shromažďovat soubor s výpisem stavu paměti.

### <a name="collect-the-memory-dump-file"></a>Shromáždění souboru s výpisem stavu paměti

Pokud problém přetrvává po spuštění SFC, bude nutné analýzu souboru výpisu stavu paměti k určení příčiny problému. Chcete-li shromáždit soubor s výpisem stavu paměti, postupujte takto:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojení disku operačního systému k novému virtuálnímu virtuálnímu počítače pro opravu

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) připravte nový virtuální počítač.
2. Pomocí připojení ke vzdálené ploše se připojte k virtuálnímu počítači pro opravu.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem stavu paměti a odešlete lístek podpory

3. Na virtuálním počítači opravy přejděte do složky Windows na připojeném disku operačního systému. Pokud je písmeno ovladače přiřazené k připojenému disku operačního systému *F*, je třeba přejít na *f:\Windows*.
4. Vyhledejte soubor *memory.dmp* a [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti.

   > [!NOTE]
   > Pokud nemůžete najít soubor s výpisem stavu paměti, proveďte níže uvedené kroky a povolte kolekci výpisu stavu paměti a konzolu Serial Console, vraťte se do této části a opakujte kroky ve výše uvedené úloze a shromážděte soubor s výpisem stavu paměti.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: Před sestavením virtuálního virtuálního montovace povolte kolekci sériové konzole a výpisu stavu paměti

Chcete-li povolit kolekci výpisů stavu paměti a konzolu Serial Console, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   Povolení sériové konzoly

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Nahraďte větší nebo menší než symboly, stejně jako text v nich, např > <.

3. Ověřte, že volné místo na disku operačního systému je stejně jako velikost paměti (RAM) na virtuálním počítači.

Pokud není dostatek místa na disku operačního systému, měli byste změnit umístění, kde bude vytvořen soubor výpisu stavu paměti a odkazovat, že na všechny datové disky připojené k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte "%SystemRoot%" písmenem jednotky (například "F:") datového disku v níže uvedených příkazech.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Navrhovaná konfigurace pro povolení výpisu operačního systému

**Načíst poškozený disk operačního systému**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Povolit na Ovládacích sadach001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Povolit na Ovládacím prvkuSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Uvolnit přerušený disk operačního systému:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Znovu sestavit původní virtuální ms

Pomocí [kroku 5 příkazů pro opravu virtuálních vod](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) znovu sestavte virtuální ho.
