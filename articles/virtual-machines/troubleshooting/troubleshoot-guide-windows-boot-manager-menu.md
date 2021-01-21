---
title: Virtuální počítač s Windows se nedá spustit kvůli Správci spouštění Windows.
description: Tento článek popisuje kroky pro řešení problémů, kdy správce spouštění systému Windows zabraňuje spuštění virtuálního počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5f83f4871d5cde23194ff51a90a22031b526cf91
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632559"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Virtuální počítač s Windows se nedá spustit kvůli Správci spouštění Windows.

Tento článek popisuje kroky pro řešení problémů, při kterých správce spouštění systému Windows zabraňuje spuštění virtuálního počítače Azure (VM).

## <a name="symptom"></a>Příznak

Virtuální počítač se zablokuje při čekání na výzvu uživatele a nespustí se, pokud se nezadá ručně.

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazí správce spouštění systému Windows se zprávou *zvolit operační systém, který se má spustit, nebo stiskněte klávesu TAB a vyberte nástroj:*.

Obrázek 1
 
![Správce spouštění systému Windows s informací o tom, jak zvolit operační systém, který se má spustit, nebo stiskněte klávesu Tabulátor pro výběr nástroje:](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Příčina

K této chybě dochází z důvodu příznaku BCD *DISPLAYBOOTMENU* ve Správci spouštění systému Windows. Když je příznak povolený, správce spouštění systému Windows během procesu spouštění vyzve uživatele, aby vybral zavaděč, který chce spustit, což způsobí zpoždění při spuštění. V Azure může tato funkce přičíst k době potřebné ke spuštění virtuálního počítače.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Přehled procesu:

1. Nakonfigurujte pro rychlejší spouštění pomocí sériové konzoly.
2. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
3. Nakonfigurujte pro rychlejší spouštění na opravném virtuálním počítači.
4. **Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.
5. Znovu sestavte virtuální počítač.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurace pro rychlejší spouštění pomocí sériové konzoly

Pokud máte přístup ke konzole sériového portu, existují dva způsoby, jak můžete dosáhnout rychlejšího spouštění. Buď snižte dobu čekání *DISPLAYBOOTMENU* , nebo příznak odeberte úplně.

1. Podle pokynů pro přístup ke [konzole Azure Serial Console pro Windows](./serial-console-windows.md) získáte přístup k textové konzole.

   > [!NOTE]
   > Pokud nemůžete získat přístup k sériové konzole, přeskočte před [vytvořením a přístupem k opravnému virtuálnímu počítači](#create-and-access-a-repair-vm).

2. **Možnost A**: Snižte čas čekání

   a. Doba čekání se ve výchozím nastavení 30 sekund nastaví, ale dá se změnit na kratší dobu (např. 5 sekund).

   b. Pomocí následujícího příkazu v sériové konzole upravte hodnotu timeout:

      `bcdedit /set {bootmgr} timeout 5`

3. **Možnost B**: Odebrání příznaku BCD

   a. Pokud chcete zabránit tomu, aby se zobrazila výzva k zobrazení spouštěcí nabídky, zadejte následující příkaz:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Pokud jste nedokázali použít sériovou konzolu pro konfiguraci rychlejšího spuštění v krocích výše, můžete místo toho pokračovat podle následujících kroků. Teď budete řešit tento problém v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Použijte Připojení ke vzdálené ploše Připojte se k opravnému virtuálnímu počítači.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurace pro rychlejší spouštění na opravném virtuálním počítači

1. Otevřete příkazový řádek se zvýšenými oprávněními.
2. Pokud chcete povolit DisplayBootMenu, zadejte následující:

   Tento příkaz použijte pro **virtuální počítače 1. generace**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Tento příkaz použijte pro **virtuální počítače 2. generace**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Nahraďte symboly větší než nebo menší než a text v nich, třeba text < sem >.

3. Změňte hodnotu timeout na 5 sekund:

   Tento příkaz použijte pro **virtuální počítače 1. generace**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Tento příkaz použijte pro **virtuální počítače 2. generace**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Nahraďte symboly větší než nebo menší než a text v nich, třeba text < sem >.

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
