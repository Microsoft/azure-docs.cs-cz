---
title: Chyba zastavení Windows – stav nedostatku paměti
description: Tento článek popisuje kroky pro řešení problémů, které se nepodařilo spustit Windows, a zobrazuje stav "stav bez paměti".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005901"
---
# <a name="windows-stop-error---status-no-memory"></a>Chyba zastavení Windows – stav nedostatku paměti

Tento článek popisuje kroky pro řešení problémů, které se nepodařilo spustit, a zobrazuje stav nebo kód chyby #0xC0000017, označované také jako "stav bez paměti".

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače (VM), uvidíte, že snímek obrazovky zobrazuje kód chyby: `0xC0000017` . V závislosti na verzi Windows, kterou používáte, se může tento kód zobrazit buď ve **Správci spouštění systému Windows** , nebo na **obrazovce pro obnovení**.

   **Správce spouštění systému Windows**

   ![Správce spouštění systému Windows, který uvádí, že systém Windows se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. Když se posunete dolů, zobrazí se stavový kód 0xC0000017 a informace o tom, že se nepodařilo načíst aplikaci nebo operační systém, protože požadovaný soubor chybí nebo obsahuje chyby.](./media/troubleshoot-windows-stop-error/1.png)

   **Obrazovka pro obnovení**
 
   ![Obrazovka pro obnovení informující o tom, že je potřeba opravit počítač/zařízení. K vytvoření zařízení RAM není k dispozici dostatek paměti. Měl by se zobrazit také kód chyby "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Příčina

Disk s operačním systémem je buď plný, moc fragmentován, nebo operační systém (OS) nemá přístup k paměti nebo stránkovacímu souboru nebo k oběma možnostem.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

1. Vytvoření a přístup k opravnému virtuálnímu počítači
1. Uvolněte místo na disku.
1. Vyčištění chybné paměti z úložiště BCD
1. Obnovit výchozí umístění stránkovacího souboru
1. Povolit kolekci sériového stavu a výpisu paměti
1. Opětovné sestavení virtuálního počítače

> [!NOTE]
> Při výskytu této chyby hostující operační systém není funkční. Tento problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>V případě virtuálních počítačů 2. generace Přiřaďte písmeno oddílu EFI (Extensible Firmware Interface):

Pokud používáte virtuální počítač 2. generace, k oddílu EFI připojeného disku se nemusí přiřadit písmeno. Před pokračováním v této příručce pro odstraňování potíží budete muset použít následující postup, abyste přiřadili písmeno k oddílu.

1. Ve Windows Search zadejte `diskmgmt` a otevřete **konzolu pro správu disků**.
1. Identifikujte poškozený disk připojený k opravnému virtuálnímu počítači. Obvykle je tento disk v konzole uveden jako poslední a má nejvyšší číselnou hodnotu.
1. Poznámka: Pokud je v tomto disku oddíl, který obsahuje **systémový oddíl EFI**, ke kterému není přiřazena žádná hodnota písmen (například jednotka *F:*). Pokud jsou přiřazené všechny oddíly, můžete přeskočit k volnému místu na disku. V opačném případě pokračujte a přiřaďte k tomuto disku písmeno.

   ![Konzola Správa disků s připojeným diskem "disk 2" a také nepřiřazený oddíl, který je 100 MB a je "systémový oddíl EFI".](./media/troubleshoot-windows-stop-error/3.png)

1. Otevřete příkazový řádek se zvýšenými oprávněními jako správce a `diskpart` Spusťte nástroj **DiskPart** .
1. Zadejte následující příkazy:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - V příkazu nahraďte `<NUMBER OF THE ATTACHED DISK>` číslem disku, který jste identifikovali v kroku 2.
   - V příkazu nahraďte `<NUMBER OF THE SYSTEM PARTION>` číslem oddílu systémového oddílu EFI. Tomuto oddílu se ještě nepřiřadilo písmeno, ale měl by být v **systému** typů a měl by mít velikost 100 MB.

   > [!NOTE]
   > Porovnání oddílů v konzole správy disků pomocí těch, které jsou uvedeny v nástroji DISKPART, může být užitečné při určování, které číslo oddílu odpovídá systémovému oddílu EFI na připojeném disku.

1. Zavřete okno příkazového řádku.

### <a name="free-up-space-on-the-disk"></a>Uvolněte místo na disku.

Když je teď poškozený disk připojený k opravnému virtuálnímu počítači, měli byste ověřit, jestli má operační systém na tomto disku dostatek místa pro správné fungování. 

1. Ověřte, jestli je disk plný, a to tak, že kliknete pravým tlačítkem na jednotku připojeného disku a vyberete **vlastnosti**.
1. Pokud disk má **méně než 300 MB volného místa**, [rozbalte ho do velikosti 1 TB pomocí PowerShellu](../windows/expand-os-disk.md).
1. Až bude velikost disku **1 TB**, budete muset provést vyčištění disku. K uvolnění místa můžete použít [Nástroj pro vyčištění disku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
1. Otevřete příkazový řádek se zvýšenými oprávněními (Spustit jako správce) a proveďte na jednotce defragmentaci:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - V závislosti na úrovni fragmentace může defragmentace trvat hodiny.
   - V příkazu nahraďte `<LETTER ASSIGNED TO THE OS DISK>` písmenem disku operačního systému (například Drive *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Vyčištění chybné paměti z úložiště konfigurační data spouštění (BCD)

1. Otevřete příkazový řádek se zvýšenými oprávněními (Spustit jako správce).
1. Pomocí následujícího příkazu spusťte dotaz na konfigurační soubor spouštění, aby byly příznaky chybné paměti:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - V příkazu nahraďte `<LETTER ASSIGNED TO THE OS DISK>` písmenem disku operačního systému (například Drive *F:*).

1. Pokud dotaz nezobrazuje žádné chybné bloky paměti, přeskočte dopředu k dalšímu úkolu. V opačném případě pokračujte krokem 4.
1. Pokud jsou zjištěny chybné bloky paměti, tyto bloky nebrání vytvoření disku RAM a měly by být odstraněny pomocí následujícího příkazu:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - V příkazu nahraďte `<LETTER ASSIGNED TO THE OS DISK>` písmenem disku operačního systému (například Drive *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Obnovit výchozí umístění stránkovacího souboru

Stránkovací soubor ukládá data, která nemůže být držena pamětí paměti Random Access (RAM) vašeho počítače, jako forma přetečení a zálohování. Je možné, že se soubor hostuje na virtuálním pevném disku, a ne na dočasné jednotce, což je výchozí umístění Azure. Pokud má hodnotu true, je možné, že soubor nebude přístupný a měl by být obnoven do výchozího umístění.

Před provedením jakýchkoli kroků byste měli vytvořit kopii složky **\Windows\System32\Config** na disku, který je v pořádku. Tento krok zajistí, že může vrátit zpět všechny nechtěné změny. Budete pracovat na důležitých systémových souborech, takže tato preventivní opatrnost se důrazně doporučuje.

1. Ve Windows Search zadejte **Regedit** a otevřete aplikaci Editor registru.
1. V editoru registru zvýrazněte klíč **HKEY_LOCAL_MACHINE** a v nabídce vyberte **soubor > Načíst podregistr...** .

   ![Nabídka Načíst podregistr v editoru registru.](./media/troubleshoot-windows-stop-error/4.png)

1. V dialogovém okně Načíst podregistr vyberte **\windows\system32\config\SYSTEM** a klikněte na otevřít.
   1. Zobrazí se výzva k zadání názvu, který byste měli zadat **BROKENSYSTEM**. Tento název vám pomůže rozlišovat ovlivněné podregistry při odstraňování potíží.
   1. Rozbalením **HKEY_LOCAL_MACHINE** zobrazíte nový klíč BROKENSYSTEM, který jste přidali.
1. Pomocí Editoru registru určete, ze kterého ControlSet se počítač spouští.
   1. Přejděte na **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> vybrat**.
   1. V seznamu klíčů si všimněte hodnoty dat Current. Například pokud je tato hodnota **1** nebo **0x00000001 (1)**, sada ovládacích prvků bude CONTROLSET001.
1. Ověřte umístění, kde je nakonfigurováno vytváření stránkovacího souboru.
   1. V HKEY_LOCAL_MACHINE\BROKENSYSTEM rozbalte adresář, který odpovídá ControlSet číslu, které jste zjistili v kroku 4, jako je například **CONTROLSET001**.
   1. Přejděte k **ovládacímu prvku >> správce relací >> správu paměti** a poznamenejte si umístění klíče **ExistingPageFiles** .
   1. Tento klíč by měl být ve výchozím umístění Azure na dočasné jednotce. Pokud tam není a je v nějakém virtuálním pevném disku, jako je například jednotka datového disku nebo jednotka operačního systému, bude nutné ji odstranit.
   1. Přejděte do tohoto umístění v Průzkumníkovi souborů a pak odstraňte soubor **pagefile.sys** .

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Povolení kolekce sériové konzoly a výpisu paměti

**Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériového stavu a výpisu paměti spuštěním následujícího skriptu:

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními jako správce.
1. Seznamte se s daty úložiště BCD a určete identifikátor zaváděcího programu, který použijete v dalším kroku.

   1. Pro virtuální počítač 1. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - V příkazu nahraďte `<BOOT PARTITON>` písmenem oddílu připojeným diskem, který obsahuje spouštěcí složku.

      ![Výstup z výpisu úložiště BCD na virtuálním počítači generace 1, který obsahuje seznam pod spouštěcím zavaděčem Windows číslo identifikátoru.](./media/troubleshoot-windows-stop-error/5.png)

   1. U virtuálního počítače 2. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - V příkazu nahraďte `<LETTER OF THE EFI SYSTEM PARTITION>` písmenem systémového oddílu EFI.
   - Může být užitečné spustit konzolu pro správu disků a identifikovat příslušný systémový oddíl označený jako **systémový oddíl EFI**.
   - Identifikátor může být jedinečný identifikátor GUID, nebo může být výchozím **Bootmgr**.

1. Spusťte následující příkazy, aby se aktivovala sériová konzola:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - V příkazu nahraďte `<VOLUME LETTER WHERE THE BCD FOLDER IS>` písmenem složky BCD.
   - V příkazu nahraďte `<BOOT LOADER IDENTIFIER>` identifikátorem, který jste našli v předchozím kroku.

1. Ověřte, zda je volné místo na disku s operačním systémem větší než velikost paměti (RAM) na virtuálním počítači.

   Pokud není dostatek místa na disku s operačním systémem, změňte umístění, kde se vytvoří soubor s výpisem paměti, a přečtěte si toto umístění na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte **% systemroot%** písmenem jednotky datového disku, například Drive **F:**, v následujících příkazech.

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
