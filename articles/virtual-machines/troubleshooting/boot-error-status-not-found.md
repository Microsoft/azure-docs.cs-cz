---
title: Řešení potíží s Windows Boot Managerem – chyba – 0xC0000225 "status nenalezen"
description: Postup řešení potíží, při kterých se kód chyby 0xC0000225 ve virtuálním počítači Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 3677d67f55cfccdc80245b2ec870ffa76b0a1940
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088661"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Řešení potíží s Windows Boot Managerem – chyba – 0xC0000225 "status nenalezen"
 
Tento článek popisuje kroky pro řešení problémů, při kterých se kód chyby 0xC0000225 ve virtuálním počítači Azure. Tato chyba uvádí, že se nenašel stav nebo objekt.

## <a name="symptoms"></a>Příznaky

Když použijete [diagnostiku spouštění](./boot-diagnostics.md) k zobrazení snímku obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu systému Windows, která selhala s kódem stavu *0xc0000225*.

Soubor přidružený k tomuto kódu chyby vás bude informovat o krocích, které je potřeba provést, aby se problém vyřešil. Vyhledejte **soubor:** text oddílu a určete příslušný postup.

### <a name="drivers-os-related-or-third-party"></a>Ovladače, související s operačním systémem nebo třetí strana

Pokud soubor existuje, ale odkazuje na ovladač (jak je zobrazen), nebo je v souladu s operačním systémem nebo třetí stranou, postupujte podle kroků v části [Oprava systémového souboru](#repair-the-system-file).
 
Na následujícím obrázku jsou stavy správce spouštění systému Windows, které se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. " Obrázek také zobrazuje stav "0xc0000225", **soubor:** as `\windows\System32\drivers\atapi.sys` a **info:** "operační systém nelze načíst, protože důležitý systémový ovladač chybí nebo obsahuje chyby".

![Stavy správce spouštění systému Windows se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. Zobrazuje také stav "0xc0000225", soubor jako "\windows\System32\drivers\atapi.sys" a v informačním oddílu, který uvádí: "operační systém nelze načíst, protože důležitý systémový ovladač chybí nebo obsahuje chyby".](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Žádný soubor

Pokud se zobrazí se stavovým kódem, ale není zobrazen žádný soubor, postupujte podle kroků v části [Přidání proměnné OSDEVICE](#add-the-osdevice-variable).

Na následujícím obrázku jsou stavy správce spouštění systému Windows, které se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. " Obrázek také zobrazuje stav "0xc0000225" a **informace:** jako "výběr spuštění se nezdařil, protože požadované zařízení je nedostupné."

![Stavy správce spouštění systému Windows se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. Zobrazuje také stav "0xc0000225" a v informačním oddílu, který uvádí: "výběr spuštění se nezdařil, protože požadované zařízení je nedostupné."](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Soubor registru

Pokud se odkazuje na některý ze souborů registru, například \WINDOWS\SYSTEM32\CONFIG\SYSTEM, postupujte podle kroků v části [Vytvoření lístku podpory](#contact-support).
 
Na následujícím obrázku jsou stavy správce spouštění systému Windows, které se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. " Obrázek taky zobrazuje stav "0xc0000225", soubor jako `\windows\System32\config\system` a **informace:** "operační systém nejde načíst, protože chybí soubor systémového registru nebo obsahuje chyby."

![Stavy správce spouštění systému Windows se nepodařilo spustit. Příčinou může být nedávný změna hardwaru nebo softwaru. Zobrazuje taky stav "0xc0000225", soubor jako "\windows\System32\config\system" a v informačním oddílu, který uvádí: "operační systém nebylo možné načíst, protože soubor registru systému chybí nebo obsahuje chyby."](./media/troubleshoot-boot-error-status-not-found/3.png)

Na následujícím obrázku je potřeba opravit obrazovku obnovení stavů počítač/zařízení. Operační systém se nepovedlo načíst, protože chybí soubor systémového registru nebo obsahuje chyby. Obrázek také zobrazuje kód chyby jako "0xc0000225" a soubor jako `\windows\System32\config\system` .

![Stavy obrazovky pro obnovení: počítač/zařízení je třeba opravit. Operační systém se nepovedlo načíst, protože chybí soubor systémového registru nebo obsahuje chyby. Zobrazuje také kód chyby "0xc0000225" a soubor jako "\windows\System32\config\system".](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Příčiny

### <a name="missing-binary"></a>Chybějící binární soubor

V souboru System **(. sys)** může docházet k chybějícímu nebo poškozenému binárnímu souboru.

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Poškození BCD nebo nesprávná migrace VHD

V takovém případě je buď služba **konfigurační data spouštění (BCD)** poškozená, nebo **virtuální pevný disk (VHD)** byl migrován z místního prostředí, ale nebyl správně připraven. Výsledkem je, že proměnná **OSDEVICE** chybí a bude nutné ji přidat.

### <a name="registry-hive-corruption"></a>Poškození podregistru registru

Poškození podregistru může mít následující příčiny:

- Podregistr se nezdařil.
- Podregistr se připojuje, ale je prázdný.
- Podregistr nebyl správně uzavřen.
## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Vyberte řešení:
   - [Opravte systémový soubor](#repair-the-system-file)
   - [Přidat proměnnou OSDevice](#add-the-osdevice-variable)
   - [Vytvoření lístku podpory](#contact-support)
1. Povolte kolekci sériového výpisu a výpisu paměti.
1. Znovu sestavte virtuální počítač.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.

### <a name="select-a-solution"></a>Vybrat řešení

1. Otevřete příkazový řádek se zvýšenými oprávněními.
1. Na základě příznaku, který jste identifikovali dříve, postupujte podle kroků v příslušném řešení. Můžete přeskočit kroky v dalších řešeních, protože se nebudou vztahovat k vašemu problému:

- [Opravte systémový soubor](#repair-the-system-file)
- [Přidat proměnnou OSDevice](#add-the-osdevice-variable)
- [Vytvoření lístku podpory](#contact-support)

### <a name="repair-the-system-file"></a>Opravte systémový soubor

1. Pomocí připojeného virtuálního pevného disku přejděte do umístění souboru binárního souboru (VM), který se zobrazuje na snímku obrazovky virtuálního počítače.
1. Klikněte na soubor pravým tlačítkem, vyberte **vlastnosti**a pak vyberte kartu **Podrobnosti** a zobrazte informace o souboru.
   1. Poznamenejte si verzi souboru, jak je znázorněno na následujícím obrázku:

      ![Okno vlastností souboru ' cng.sys ' se zvýrazněnou verzí souboru.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Přejmenujte soubor na **< BINARY.SYS >. old**a nahraďte **< BINARY.SYS >** názvem souboru.

   V případě obrázku v kroku výše by se **cng.sys** souboru přejmenoval na **cng.sys. old.**

   > [!NOTE]
   > Pokud se pokusíte přejmenovat soubor a dostat zprávu "soubor je poškozen a nečitelný", [požádejte o pomoc podporu](https://azure.microsoft.com/support/create-ticket/), protože toto řešení nebude fungovat.

1. Když se teď poškozený soubor přejmenoval, opravte ho tak, že ho obnovíte z jeho interního úložiště.
   1. Spusťte relaci **cmd** .
   1. Přejděte na **\windows\winsxs**.

   1. Vyhledejte binární soubor umístěný na začátku této části pomocí následujícího příkazu:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Tento příkaz zobrazí seznam všech verzí souboru, které má počítač, a poskytuje tak historii cest této součásti.
      
      Například **dir cng.sys** by se přejmenovala **dir cng.sys/s**

   1. V seznamu vyberte nejnovější verzi souboru (nebo všechny, které dáváte přednost), a zkopírujte soubor do složky **Windows\System32** pomocí předchozí cesty a následujícího příkazu:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Pokud poslední binární soubor nefungoval, vyzkoušejte verzi před tím, nebo kteroukoli z nich, na které víte, že je stabilní soubor, třeba verze před opravou.

      Například pokud je binární soubor, který hledáte, **cmimcext.sys**, vadná jednotka je jednotka **F:** a právě jste spustili hledání nejnovější verze, zobrazí se následující obrázek, ve kterém dotaz na příkazovém řádku `dir cmim* /s` vyhledá nejnovější verzi cmimcext.sys souboru.

      ![Dotaz na příkazovém řádku příkazu DIR CMIM */s pro vyhledání nejnovější verze cmimcext.sys souboru.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Na výše uvedeném obrázku se dotaz prováděl v **C:**, zatímco písmeno jednotky by mělo být na vadné jednotce **F:**, což je disk s operačním systémem připojený jako datový disk na opravném virtuálním počítači.

      Výsledný příkaz pro kopírování souboru by byl: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

Po dokončení této úlohy pokračujte [v povolení kolekce sériového a výpisu paměti](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Přidat proměnnou OSDEVICE

Shromážděte informace o instalaci aktuálního spuštění a poznamenejte si identifikátor v aktivním oddílu. Pomocí těchto informací pak přidáte proměnnou **OSDEVICE** podle pokynů pro generaci virtuálního počítače.

Pokud tato kolekce informací obsahuje chybu, pokud není k dispozici žádný soubor **\boot\bcd** , použijte místo toho pokyny v části [Oprava systémového souboru](#repair-the-system-file) . 

1. Pro virtuální počítače 1. generace otevřete příkazový řádek se zvýšenými oprávněními jako správce a zadejte tento příkaz:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Tento obrázek ukazuje zaváděcí program pro spouštění systému Windows na virtuálním počítači generace 1 s zvýrazněným atributem identifikátor. Zvýrazněný atribut identifikátoru ukazuje jedinečný alfanumerický řetězec.

   ![Spouštěcí zavaděč Windows zobrazený ve virtuálním počítači 1. generace s zvýrazněným atributem identifikátoru Zvýrazněný atribut identifikátoru ukazuje jedinečný alfanumerický řetězec.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Poznamenejte si identifikátor zaváděcího nástroje pro spouštění systému Windows, jehož cesta je **\windows\system32\winload.exe**.

1. U virtuálních počítačů 2. generace ověřte, že je disk s operačním systémem online a že byla přiřazena písmena jednotek oddílů. Po ověření Shromážděte informace o instalaci spuštění.
   1. Ve **Windows Search**zadejte **Správa disků** a otevřete konzolu Správa disků. Pomocí této konzoly můžete identifikovat číslo disku připojené k opravnému VIRTUÁLNÍmu počítači a oddíl EFI (Extensible Firmware Interface), který obsahuje úložiště BCD.

   Na následujícím obrázku je disk 2 číslo disku připojené k opravnému virtuálnímu počítači. V imagi se taky zobrazuje systémový oddíl EFI na disku 2, což je velikost 100 MB a nemá přiřazené písmeno.

   ![Disk 2 je zobrazený jako číslo disku připojené k opravnému virtuálnímu počítači. Zobrazuje také systémový oddíl EFI na disku 2, což je 100 MB a nemá přiřazené písmeno.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Otevřete příkazový řádek se zvýšenými oprávněními jako správce a zadejte následující příkazy:
      1. Pomocí příkazu spusťte **Nástroj DiskPart** `diskpart` .
      1. Vypíšete všechny disky a pak vyberete připojený disk, který jste identifikovali v předchozím kroku:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         Následující obrázek ukazuje výsledky výpisu a výběru disku. V seznamu je uveden disk 0 (127 GB/online), disk 1 (32 GB/online) a disk 2 (127 GB/online) s vybraným diskem 2 pomocí příkazu `sel disk 2` .

         ![Výsledky výpisu a následného výběru disku. Disk 0 (127 GB | Online), disk 1 (32 GB | Online) a disk 2 (127 GB | Online) jsou uvedeny s vybraným diskem 2.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Vypsat oddíly a vybrat systémový oddíl EFI identifikovaný v předchozím kroku:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         Následující obrázek znázorňuje výsledky výpisu a výběru oddílu. V seznamu oddíl 1 (rezervovaný/16), oddíl 2 (systém/100 MB) a oddíl 3 (primární/126 GB) se vybere oddíl 2 pomocí příkazu `sel part 2` .

         ![Výsledky výpisu a následného výběru oddílu. V seznamu oddíl 1 (rezervovaný | 16), oddíl 2 (systém | 100 MB) a oddíl 3 (primární | 126 GB) se vybere oddíl 2.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Pomocí příkazu přiřaďte k oddílu EFI písmeno `assign` .

         Na následujícím obrázku `assign` je příkaz a systém nové jednotky **(F:)** možné zobrazit v Průzkumníkovi souborů.

         ![Příkaz Assign a systém nového disku (F:) lze zobrazit v Průzkumníkovi souborů.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Seznamte se s daty úložiště BCD pomocí následujícího příkazu:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Na následujícím obrázku je zaváděcí program pro spouštění systému Windows ve virtuálním počítači 2. generace s zvýrazněným atributem identifikátoru. Zvýrazněný atribut identifikátoru má hodnotu **{Default}**.

         ![Spouštěcí zavaděč Windows se zobrazuje ve virtuálním počítači 2. generace s zvýrazněným atributem identifikátoru. Zvýrazněný atribut Identifier ukazuje jako hodnotu výchozí.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Poznamenejte si identifikátor zaváděcího programu pro spouštění systému Windows, jehož cesta je **\Windows\system32\winload.EFI**.

1. Všimněte si, že chybí proměnná OSDEVICE v aktivním oddílu:

   ![Správce spouštění systému Windows a atributy zavaděče spouštění systému Windows jsou uvedeny na příkazovém řádku s chybějícím atributem zařízení operačního systému.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   V této imagi jsou atributy Windows Boot Manageru a zavaděče spouštění Windows uvedené na příkazovém řádku, ale chybí atribut OSDEVICE.

1. Přidejte proměnnou OSDEVICE na základě následujících informací:

   Pro disky s operačním systémem s jedním oddílem přidejte `BOOT` .

   > [!NOTE]
   > Spouštěcí složka bude obsahovat stejný oddíl jako složka **\Windows**složky systému Windows.
   > - Spouštěcí složka pro virtuální počítače 1. generace je **(\boot\bcd složka)**.
   > - Spouštěcí složka pro virtuální počítače 2. generace je **EFI\Microsoft\boot\bcd**.

   Pro virtuální počítače 1. generace zadejte následující příkaz:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Pro virtuální počítače 2. generace zadejte tento příkaz:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Pro více disků s operačním systémem oddílů přidejte `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > Spouštěcí-možná složka bude pravděpodobně na jiném oddílu než složka **\Windows**složky systému Windows.
   > - Spouštěcí složka pro virtuální počítače 1. generace je **(\boot\bcd složka)**.
   > - Spouštěcí složka pro virtuální počítače 2. generace je **EFI\Microsoft\boot\bcd**.

   Pro virtuální počítače 1. generace zadejte následující příkaz:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Pro virtuální počítače 2. generace zadejte tento příkaz:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Po dokončení této úlohy pokračujte [v povolení kolekce sériového a výpisu paměti](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Kontaktování podpory

Chyba **souboru registru** obsahuje řešení, ale pro další pomoc budete muset [vytvořit lístek podpory](https://azure.microsoft.com/support/create-ticket/) .

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
