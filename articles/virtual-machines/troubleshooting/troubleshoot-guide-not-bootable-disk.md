---
title: Chyba při spuštění – "toto není spouštěcí disk"
description: Tento článek obsahuje postup řešení problémů, kdy se disk nezavádí ve virtuálním počítači Azure
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
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300976"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Chyba při spuštění – toto není spouštěcí disk

Tento článek obsahuje postup k vyřešení problémů, kdy disk není zaváděcí ve virtuálním počítači (VM) Azure.

## <a name="symptoms"></a>Příznaky

Při použití [boot diagnostiky](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku virtuálního počítače, uvidíte, že snímek obrazovky zobrazí výzvu se zprávou "Toto není spouštěcí disk. Vložte spouštěcí disketu a stisknutím libovolné klávesy akci opakujte.'.

   Obrázek 1

   ![Obrázek 1 znázorňuje zprávu *"Toto není spouštěcí disk. Vložte spouštěcí disketu a stisknutím libovolné klávesy akci opakujte."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Příčina

Tato chybová zpráva znamená, že spouštěcí proces operačního systému nemohl najít aktivní systémový oddíl. Tato chyba může také znamenat, že je chybějící odkaz v úložišti schránkovací data (BCD), brání v ylokací oddíl systému Windows.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesů

1. Vytvoření a přístup k virtuálnímu virtuálnímu mísu opravy.
2. Nastavte stav oddílu na aktivní.
3. Opravte diskový oddíl.
4. **Doporučeno**: Před sestavením virtuálního virtuálního zařízení povolte sériovou konzolu a kolekci výpisu stavu paměti.
5. Znovu vytvořit původní virtuální hod.

   > [!NOTE]
   > Při výskytu této chyby při spuštění není hostovaný operační systém funkční. Budete řešit problémy v režimu offline k vyřešení tohoto problému.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu mněmu pro opravy

1. Pomocí kroků 1-3 [příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) připravte virtuální počítač.
2. Pomocí připojení ke vzdálené ploše se připojte k virtuálnímu počítači pro opravu.

### <a name="set-partition-status-to-active"></a>Nastavení stavu oddílu na aktivní

Virtuální klíče generace 1 by měly nejprve ověřit, zda je oddíl operačního systému, který obsahuje úložiště BCD, označen jako *aktivní*. Pokud máte virtuální modul generace 2, přeskočte dopředu na [opravu oddílu disku](#fix-the-disk-partition), protože příznak *Stav* byl v pozdější generaci zastaral.

1. Otevřete příkazový řádek se zvýšenými oprávněními *(cmd.exe).*
2. Zadejte *diskpart* a spusťte nástroj DISKPART.
3. Zadáním *seznamu disků* zobrazíte seznam disků v systému a identifikujete připojený virtuální pevný disk operačního systému.
4. Po umístění připojeného virtuálního pevného disku operačního systému vyberte disk zadáním *sel disku #* .  Viz obrázek 2, kde disk 1 je připojený virtuální disk operačního systému.

   Obrázek 2

   ![Obrázek 2 znázorňuje okno *DISKPART*, které zobrazuje výstup příkazu diskového listu, disk0 a disk 1 zobrazený v tabulce.  Také zobrazuje výstup příkazu sel disk 1, disk 1 je vybraný disk](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Po výběru disku zadejte *oddíl seznamu* a uveďte oddíly vybraného disku.
6. Jakmile je spouštěcí oddíl identifikován, vyberte oddíl *sel oddíl #* .  Obvykle zaváděcí oddíl bude mít velikost kolem 350 MB.  Viz obrázek 3, kde oddíl 1 je spouštěcí oddíl.

   Obrázek 3

   ![Obrázek 3 znázorňuje okno *DISKPART* s výstupem příkazu *list partition*. Oddíl 1 a oddíl 2 jsou zobrazeny v tabulce. Zobrazuje také výstup příkazu *sel partition 1*, když je vybraný disk oddíl 1.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Zadejte 'detail oddíl' zkontrolovat stav oddílu. Viz obrázek 4, kde je oddíl *aktivní: Ne*nebo Obrázek 5, kde je oddíl "Aktivní: Ano".

   Obrázek 4

   ![Obrázek 4 znázorňuje okno *DISKPART* s výstupem příkazu *detail partition* při nastavení oddílu 1 na *Active: Ne*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Obrázek 5

   ![Obrázek 5 znázorňuje okno *DISKPART* s výstupem příkazu *detail partition* při nastavení oddílu 1 na *Active: Ano*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Pokud oddíl **není aktivní**, zadejte *aktivní* a změňte příznak *Aktivní.*
9. Zkontrolujte, zda byla změna stavu provedena správně zadáním *oddílu podrobností*.

   Obrázek 6

   ![Obrázek 6 znázorňuje okno diskpart s výstupem příkazu *detail partition* při nastavení oddílu 1 na *Active: Ano*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Zadáním *příkazu exit* zavřete nástroj DISKPART a uložte změny konfigurace.

### <a name="fix-the-disk-partition"></a>Oprava oddílu disku

1. Otevřete příkazový řádek se zvýšenými oprávněními (cmd.exe).
2. Pomocí následujícího příkazu spusťte *nástroj CHKDSK* na disky a opravte chyby:

   `chkdsk <DRIVE LETTER>: /f`

   Přidáním možnosti příkazu '/f' opravíte všechny chyby na disku. Ujistěte se, že nahradit <DRIVE LETTER> písmenem připojeného operačního virtuálního pevného disku.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: Před sestavením virtuálního virtuálního montovace povolte kolekci sériové konzole a výpisu stavu paměti

Chcete-li povolit kolekci výpisů stavu paměti a konzolu Serial Console, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   Povolení sériové konzoly

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
