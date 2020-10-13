---
title: Chyba spuštění – "Toto není spustitelný disk"
description: Tento článek popisuje kroky pro řešení problémů, které neběží na virtuálním počítači Azure na disku.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509031"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Chyba spuštění – toto není spouštěcí disk.

Tento článek popisuje kroky pro řešení problémů, které neběží na virtuálním počítači Azure (VM).

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje výzvu se zprávou ' Toto není spouštěcí disk. Vložte prosím spouštěcí disketu a stiskněte libovolnou klávesu a zkuste to znovu...

   Obrázek 1

   ![Obrázek 1 ukazuje zprávu * "Toto není spustitelný disk. Vložte prosím spouštěcí disketu a stiskněte libovolnou klávesu a zkuste to znovu... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Příčina

Tato chybová zpráva znamená, že proces spouštění operačního systému nemohl najít aktivní systémový oddíl. Tato chyba by mohla také znamenat, že v úložišti konfigurační data spouštění (BCD) chybí odkaz a brání tak tomu, aby vyhledá oddíl Windows.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
2. Nastavte stav oddílu na aktivní.
3. Opravte diskový oddíl.
4. **Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.
5. Znovu sestavte původní virtuální počítač.

   > [!NOTE]
   > Při výskytu této chyby spuštění není hostovaný operační systém funkční. Tento problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="set-partition-status-to-active"></a>Nastavit stav oddílu na aktivní

Virtuální počítače 1. generace by měly nejdřív ověřit, že oddíl s operačním systémem, který obsahuje úložiště BCD, je označený jako *aktivní*. Pokud máte virtuální počítač 2. generace, přeskočte před [opravou diskového oddílu](#fix-the-disk-partition), protože příznak *stavu* byl v pozdější generaci zastaralý.

1. Otevřete příkazový řádek se zvýšenými oprávněními *(cmd.exe)*.
2. Zadáním příkazu *DiskPart* spusťte nástroj Diskpart.
3. Zadejte *disk se seznamem* pro výpis disků v systému a identifikaci připojeného virtuálního pevného disku s operačním systémem.
4. Po umístění připojeného virtuálního pevného disku s operačním systémem zadejte k výběru disku položku *sel disk #* .  Viz obrázek 2, kde disk 1 je připojený virtuální pevný disk s operačním systémem.

   Obrázek 2

   ![Obrázek 2 ukazuje okno * DISKPART * znázorňující výstup příkazu výpis disku, disk 0 a disk 1 zobrazené v tabulce.  Zobrazuje také výstup příkazu sel disk 1, disk 1 je vybraný disk.](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Po výběru disku zadejte *seznam* oddílů pro vypsání oddílů vybraného disku.
6. Po zjištění spouštěcího oddílu zadejte k výběru oddílu *SEL partition #* .  Velikost spouštěcího oddílu obvykle bude přibližně 350 MB.  Viz obrázek 3, kde oddíl 1 je spouštěcí oddíl.

   Obrázek 3

   ![Na obrázku 3 se zobrazuje okno * DISKPART * s výstupem příkazu * list partition *. V tabulce se zobrazí oddíl 1 a oddíl 2. Zobrazuje také výstup příkazu * sel partition 1 *, pokud je oddíl 1 vybraným diskem.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Pokud chcete zjistit stav oddílu, zadejte "detail partition". Viz obrázek 4, kde je oddíl *aktivní: ne*, nebo obrázek 5, kde oddíl je aktivní: Ano.

   Obrázek 4

   ![Obrázek 4 znázorňuje okno * DISKPART * s výstupem příkazu * detail partition *, pokud je oddíl 1 nastavený na * aktivní: No *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Obrázek 5

   ![Obrázek 5 znázorňuje okno * DISKPART * s výstupem příkazu * detail partition *, pokud je oddíl 1 nastavený na * aktivní: Ano *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Pokud oddíl není **aktivní**, zadejte *aktivní* , aby se změnil *aktivní* příznak.
9. Zadáním *oddílu podrobností*ověřte, zda byla změna stavu provedena správně.

   Obrázek 6

   ![Obrázek 6 znázorňuje okno nástroje DiskPart s výstupem příkazu * detail partition *, pokud je oddíl 1 nastaven na hodnotu * aktivní: Ano *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Pokud chcete nástroj DISKPART zavřít a uložit změny konfigurace, zadejte *Exit* .

### <a name="fix-the-disk-partition"></a>Opravit diskový oddíl

1. Otevřete příkazový řádek se zvýšenými oprávněními (cmd.exe).
2. Pomocí následujícího příkazu spusťte na discích *příkaz Chkdsk* a opravte chyby:

   `chkdsk <DRIVE LETTER>: /f`

   Přidání možnosti příkazu/f vyřeší všechny chyby na disku. Nezapomeňte nahradit <DRIVE LETTER> písmeno připojeného virtuálního pevného disku s operačním systémem.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: před opětovným sestavením virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   Povolit sériovou konzolu

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
