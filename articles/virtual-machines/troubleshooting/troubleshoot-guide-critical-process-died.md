---
title: Chyba stop systému Windows –
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
ms.openlocfilehash: c04f3b27c7214dcf821c7698796bfaea399b947d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509099"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Chyba stop systému Windows – #0x000000EF "nepostradatelný proces zemřel"

Tento článek popisuje kroky pro řešení problémů, při kterých během spouštění na virtuálním počítači Azure zemře nepostradatelný proces.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu *#0x000000EF* se *nezpracovaným nepostradatelným procesem*zprávy.

!["Váš počítač byl příčinou problému a je nutné ho restartovat. Jenom shromažďujeme nějaké informace o chybách a pak se můžete restartovat. (# #% dokončeno) Pokud se chcete dozvědět víc, můžete k této chybě vyhledat online v online režimu: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Příčina

Obvykle je to kvůli selhání kritického systémového procesu během spouštění. Další informace o problémech s kritickými procesy najdete v tématu "[kontroly chyb 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
2. Opravte poškození operačního systému.
3. **Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.
4. Znovu sestavte virtuální počítač.

> [!NOTE]
> Při výskytu této chyby spuštění není hostovaný operační systém funkční. Tento problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="fix-any-os-corruption"></a>Oprava poškození operačního systému

1. Otevřete příkazový řádek se zvýšenými oprávněními.
2. Spusťte následující příkaz pro kontrolu systémových souborů (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Kde < spouštěcí DISKová jednotka > je spouštěcím svazkem opravného virtuálního počítače (obvykle "C:") a <m >m disku, bude k připojenému disku z poškozeného virtuálního počítače písmeno jednotky. Nahraďte symboly větší než/menší než a text obsažený v nich, například "< text >" s příslušným písmenem.

3. V dalším [kroku pomocí příkazu pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač a zjistěte, jestli se spustí.
4. Pokud se virtuální počítač stále nespouští, pokračujte ve shromažďování souboru s výpisem paměti.

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Pokud potíže potrvají po spuštění nástroje SFC, bude nutné provést analýzu souboru s výpisem paměti, aby bylo možné určit příčinu problému. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. K přípravě nového opravného virtuálního počítače použijte [kroky 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) .
2. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

3. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem *F*, musíte přejít na *F:\Windows*.
4. Vyhledejte soubor *Memory. dmp* a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.

   > [!NOTE]
   > Pokud soubor výpisu paměti nemůžete najít, dokončete následující kroky, abyste povolili shromažďování výpisu paměti a sériovou konzolu, pak se vrátíte do této části a zopakujete postup uvedený výše v úloze, abyste mohli shromáždit soubor výpisu paměti.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: před opětovným sestavením virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   Povolit sériovou konzolu

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Nahraďte symboly větší než nebo menší než a text v nich, třeba text < sem >.

3. Ověřte, že volné místo na disku s operačním systémem je ve virtuálním počítači ve velikosti paměti (RAM).

Pokud na disku operačního systému není dostatek místa, měli byste změnit umístění, kde se vytvoří soubor s výpisem paměti, a odkazovat na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte "% SystemRoot%" písmenem jednotky (například "F:") datového disku v následujících příkazech.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Navrhovaná konfigurace pro povolení výpisu operačního systému

**Načíst poškozený disk s operačním systémem**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Povolit na ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Povolit na ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Uvolnit poškozený disk s operačním systémem:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Znovu sestavte původní virtuální počítač.

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač.
