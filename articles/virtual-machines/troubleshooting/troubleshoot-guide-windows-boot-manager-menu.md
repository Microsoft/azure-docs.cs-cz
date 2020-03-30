---
title: Virtuální počítač se systémem Windows nelze spustit z důvodu správce spouštění systému Windows
description: Tento článek obsahuje postup k vyřešení problémů, kdy Správce spouštění systému Windows zabraňuje spuštění virtuálního počítače Azure.
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
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373346"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Virtuální přístup systému Windows nelze spustit z důvodu Správce spouštění systému Windows

Tento článek obsahuje postup k vyřešení problémů, kdy Správce spouštění systému Windows zabraňuje spuštění virtuálního počítače (VM) Azure.

## <a name="symptom"></a>Příznak

Virtuální ho virtuálního uživatele se zasekne čekání na výzvu uživatele a nespustí se, pokud ručně pokyn.

Když pomocí [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) zobrazíte snímek obrazovky virtuálního aplikace, uvidíte, že snímek obrazovky zobrazuje Správce spouštění Windows se *zprávou Zvolte operační systém ke spuštění, nebo stisknutím klávesy TAB vyberte nástroj:*.

Obrázek 1
 
![Správce spouštění systému Windows s uvedením možnosti "Vyberte operační systém ke spuštění nebo stisknutím klávesy TAB vyberte nástroj:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Příčina

Chyba je způsobena spouštěcí *nabídkou příznaku* BCD ve Správci spouštění systému Windows. Je-li příznak povolen, správce spouštění systému Windows vyzve uživatele během procesu spouštění k výběru zavaděče, který chce spustit, což způsobí zpoždění spuštění. V Azure může tato funkce přidat čas potřebný ke spuštění virtuálního počítače.

## <a name="solution"></a>Řešení

Přehled procesu:

1. Nakonfigurujte rychlejší spouštění pomocí konzoly Serial Console.
2. Vytvoření a přístup k virtuálnímu virtuálnímu mísu opravy.
3. Konfigurace pro rychlejší spuštění na virtuálním počítači opravy.
4. **Doporučeno**: Před sestavením virtuálního virtuálního zařízení povolte sériovou konzolu a kolekci výpisu stavu paměti.
5. Znovu sestavit virtuální ho dispozice.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurace rychlejšího spuštění pomocí konzoly Serial Console

Pokud máte přístup k sériové konzoli, existují dva způsoby, jak dosáhnout rychlejšího spuštění. Buď zkraťte dobu čekání *v nabídce displaybootmenu* nebo příznak úplně odeberte.

1. Postupujte podle pokynů pro přístup k [konzoli Azure Serial Console pro Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) a získejte přístup k textové konzoli.

   > [!NOTE]
   > Pokud se vám nedaří získat přístup k sériové konzole, přeskočte na [vytvořit a získat přístup k virtuálnímu virtuálnímu virtuálnímu mandu opravy](#create-and-access-a-repair-vm).

2. **Možnost A**: Zkrácení čekací doby

   a. Čekací doba je ve výchozím nastavení nastavena na 30 sekund, ale lze ji změnit na rychlejší čas (např. 5 sekund).

   b. Pomocí následujícího příkazu v konzole sériového konzole nastavte hodnotu časového času:

      `bcdedit /set {bootmgr} timeout 5`

3. **Možnost B:** Odebrání příznaku BCD

   a. Chcete-li zcela zabránit zobrazení výzvy spuštění nabídky, zadejte následující příkaz:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Pokud se vám nepodařilo použít konzolu sériové ho spouštění ke konfiguraci rychlejšího spuštění ve výše uvedených krocích, můžete místo toho pokračovat následujícími kroky. Nyní budete řešit problémy v režimu offline k vyřešení tohoto problému.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu mněmu pro opravy

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) připravte virtuální počítač.
2. Použijte připojení ke vzdálené ploše k virtuálnímu počítači pro opravu.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurace rychlejšího spuštění na virtuálním počítači opravy

1. Otevřete příkazový řádek se zvýšenými oprávněními.
2. Chcete-li povolit displaybootmenu, zadejte následující:

   Tento příkaz použijte pro **virtuální chod generace 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Tento příkaz použijte pro **virtuální chod generace 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Nahraďte větší nebo menší než symboly, stejně jako text v nich, např > <.

3. Změňte hodnotu časového času na 5 sekund:

   Tento příkaz použijte pro **virtuální chod generace 1**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Tento příkaz použijte pro **virtuální chod generace 2**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Nahraďte větší nebo menší než symboly, stejně jako text v nich, např > <.

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