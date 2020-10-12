---
title: Řešení potíží při spuštění – došlo k chybě čtení z disku
description: Tento článek popisuje kroky pro řešení problémů, při kterých nelze číst disky ve virtuálním počítači Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069141"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Řešení potíží při spuštění – došlo k chybě čtení z disku

Tento článek popisuje kroky pro řešení problémů, při kterých není možné číst disk ve virtuálním počítači Azure (VM).

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje výzvu se zprávou "došlo k chybě při čtení disku. Stiskněte Ctrl+Alt+Del a proveďte restartování.

   ![Chybová zpráva: došlo k chybě čtení disku. Stisknutím kombinace kláves Ctrl + Alt + Del restartujte počítač.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Příčina

Tato chybová zpráva znamená, že je struktura disku poškozená a není čitelná. Pokud používáte virtuální počítač 1. generace, je také možné, že diskový oddíl obsahující konfigurační data spouštění není nastavený na **aktivní**.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Vyberte řešení:
   - [Nastavit stav oddílu na aktivní](#set-partition-status-to-active)
   - [Opravit diskový oddíl](#fix-the-disk-partition)
1. Povolte kolekci sériového výpisu a výpisu paměti.
1. Znovu sestavte virtuální počítač.

> [!NOTE]
> Při výskytu této chyby spuštění není hostovaný operační systém (OS) v provozu. Tento problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.

### <a name="set-partition-status-to-active"></a>Nastavit stav oddílu na aktivní

Virtuální počítače 1. generace by měly nejdřív ověřit, že oddíl s operačním systémem, který obsahuje úložiště BCD, je označený jako **aktivní**. Pokud máte virtuální počítač 2. generace, přeskočte před [opravou diskového oddílu](#fix-the-disk-partition), protože příznak stavu byl v pozdější generaci zastaralý.

1. Otevřete příkazový řádek se zvýšenými oprávněními (cmd.exe).
1. Zadáním příkazu **DiskPart** spusťte nástroj **DiskPart** .
1. Zadejte **disk se seznamem** pro výpis disků v systému a identifikaci připojeného virtuálního pevného disku operačního systému (VHD).
1. Po umístění připojeného virtuálního pevného disku s operačním systémem zadejte k výběru disku položku **sel disk #** . Na následujícím obrázku najdete příklad, kdy je disk 1 připojeným virtuálním pevným diskem operačního systému.

   ![Okno nástroje DiskPart s výstupem příkazu * * list disku * *, kde se v tabulce zobrazí disk 0 a disk 1. V okně se zobrazí také výstup příkazu * * sel disk 1 * *, kde disk 1 je vybraný disk.](./media/disk-read-error-occurred/2.png)

1. Po výběru disku zadejte **seznam** oddílů pro vypsání oddílů vybraného disku.
1. Po zjištění spouštěcího oddílu zadejte k výběru oddílu **SEL partition #** . Velikost spouštěcího oddílu je často přibližně 350 MB.  Podívejte se na následující obrázek, například oddíl 1 je spouštěcí oddíl.

   ![Okno nástroje DiskPart s výstupem příkazu * * list partition * *, kde oddíl 1 a oddíl 2 jsou zobrazeny v tabulce. V okně se zobrazí také výstup příkazu * * sel partition 1 * *, kde oddíl 1 je vybraný disk.](./media/disk-read-error-occurred/3.png)

1. Zadejte **detailní oddíl** ke kontrole stavu oddílu. V následujících snímcích obrazovky najdete příklady oddílu nastaveného na **aktivní: ne** nebo **aktivní: Ano**.

   **Aktivní: ne**

   ![Okno nástroje DiskPart s výstupem příkazu * * detail partition * *, kde oddíl 1 je nastaven na * * aktivní: No * *.](./media/disk-read-error-occurred/4.png)

   **Aktivní: Ano**

   ![Okno nástroje DiskPart s výstupem příkazu * * detail partition * *, kde oddíl 1 je nastaven na * * aktivní: Ano * *.](./media/disk-read-error-occurred/5.png)

1. Pokud oddíl není nastavený na **aktivní**, zadejte **aktivní** , aby se změnil aktivní příznak.
1. Zadejte **oddíl Details** pro kontrolu správného dokončení změny stavu a ověřte, že výstup zahrnuje **aktivní: Ano**. 
1. Pokud chcete nástroj DISKPART zavřít a uložit změny konfigurace, zadejte **Exit** .

### <a name="fix-the-disk-partition"></a>Opravit diskový oddíl

1. Otevřete příkazový řádek se zvýšenými oprávněními (cmd.exe).
1. Pomocí následujícího příkazu spusťte na discích **příkaz Chkdsk** a proveďte opravy chyb:

   `chkdsk <DRIVE LETTER>: /f`

   Přidání možnosti příkazu **/f** vyřeší všechny chyby na disku. Nezapomeňte ** >písmeno jednotky< ** nahradit písmenem připojeného virtuálního pevného disku s operačním systémem.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Povolení kolekce sériové konzoly a výpisu paměti

**Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériového stavu a výpisu paměti spuštěním následujícího skriptu:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními jako správce.
1. Spusťte následující příkazy:

   **Povolte konzolu sériového portu**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Ověřte, zda je volné místo na disku s operačním systémem větší než velikost paměti (RAM) na virtuálním počítači.

   Pokud není dostatek místa na disku s operačním systémem, změňte umístění, kde se vytvoří soubor s výpisem paměti, a přečtěte si toto umístění na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte **% systemroot%** písmenem jednotky datového disku, například **F:**, v následujících příkazech.

   Navrhovaná konfigurace pro povolení výpisu operačního systému:

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
   
### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
