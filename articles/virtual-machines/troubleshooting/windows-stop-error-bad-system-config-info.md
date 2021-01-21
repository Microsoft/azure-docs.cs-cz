---
title: Chyba stop systému Windows – 0x00000074 chybné informace o konfiguraci systému
description: Tento článek popisuje kroky pro řešení problémů, při kterých se systém Windows nemůže spustit a který je potřeba restartovat kvůli chybovým informacím o konfiguraci systému na virtuálním počítači Azure (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 8d501bcc745ef19d15564951b8c0f29f9e2678ab
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661302"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Chyba stop systému Windows – 0x00000074 chybné informace o konfiguraci systému

Tento článek popisuje kroky pro řešení problémů, při kterých se systém Windows nemůže spustit a který je potřeba restartovat kvůli chybovým informacím o konfiguraci systému na virtuálním počítači Azure (VM).

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje kód zastavení Windows **#0x00000074** nebo **BAD_SYSTEM_CONFIG_INFO**.

*Váš počítač narazil na problém a je potřeba ho restartovat. Můžete restartovat.* 
 *Další informace o tomto problému a možných opravách najdete na http://windows.com/stopcode webu* 
 *Pokud zavoláte osobu podpory, poskytněte jim tyto informace:* 
 *Stop kód: BAD_SYSTEM_CONFIG_INFO*

  ![Kód systému Windows stop 0x00000074, který je také zobrazen jako "BAD_SYSTEM_CONFIG_INFO". Windows informuje uživatele o tom, že jeho počítač narazil na problém a musí se restartovat.](./media/windows-stop-error-bad-system-config-info/stop-code-0x00000074.png)

## <a name="cause"></a>Příčina

Kód **BAD_SYSTEM_CONFIG_INFO** stop dojde, pokud je podregistr **systémového** registru pravděpodobně poškozen. Tato chyba může být způsobena některým z těchto důvodů:

- Podregistr registru se nepovedlo správně zavřít.
- Podregistr registru je poškozen.
- Chybí klíče registru nebo hodnoty.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Vyhledejte poškození podregistru.
1. Povolte kolekci sériového výpisu a výpisu paměti.
1. Znovu sestavte virtuální počítač.

   > [!NOTE]
   > Při výskytu této chyby není hostovaný operační systém (OS) v provozu. Chcete-li tento problém vyřešit, vyřešte potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Vyhledejte poškození podregistru.
1. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.
1. Zkopírujte `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` složku a uložte ji buď v dobrém oddílu disku, nebo v jiném bezpečném umístění. Zazálohujte tuto složku jako preventivní, protože budete upravovat důležité soubory registru. 

   > [!NOTE]
   > Vytvořte kopii `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` složky jako zálohu pro případ, že budete potřebovat vrátit zpět všechny změny, které provedete v registru.

### <a name="check-for-hive-corruption"></a>Kontrolovat poškození podregistru

Následující pokyny vám pomůžou určit, jestli příčina byla způsobená poškozením podregistru, nebo jestli se podregistr nezavřel správně. Pokud se podregistr nezavřel správně, budete moct soubor odemknout a opravit svůj virtuální počítač.

1. Na VIRTUÁLNÍm počítači s opravou otevřete aplikaci **Editor registru** . Zadáním příkazu regedit na panelu hledání systému Windows ho vyhledejte.
1. V editoru registru vyberte **HKEY_LOCAL_MACHINE** pro zvýraznění a pak vyberte **soubor > Načíst podregistr...** z nabídky.
1. Vyhledejte `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` a vyberte **otevřít**.
1. Po zobrazení výzvy k zadání názvu zadejte **BROKENSYSTEM**.

   1. Pokud se podregistr nepovede otevřít, nebo pokud je prázdný, je podregistr poškozený. Pokud je podregistr poškozený, [otevřete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

      ![Při upozornění, že Editor registru nemůže načíst podregistr, dojde k chybě.](./media/windows-stop-error-bad-system-config-info/cannot-load-hive-error.png)

   1. Pokud se podregistr otevře normálně, podregistr se nezavřel správně. Pokračujte krokem 5.

1. Pokud chcete opravit podregistr, který se nezavřel správně, zvýrazněte **BROKENSYSTEM** a pak vyberte **soubor > uvolnit podregistr...** k odemknutí souboru.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Povolení kolekce sériové konzoly a výpisu paměti

**Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériového stavu a výpisu paměti spuštěním následujícího skriptu:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními jako správce.
1. Spusťte následující příkazy:

   **Povolte konzolu sériového portu**:
   
   ```ps
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Ověřte, zda je volné místo na disku s operačním systémem větší než velikost paměti (RAM) na virtuálním počítači.

   Pokud není dostatek místa na disku s operačním systémem, změňte umístění, kde se vytvoří soubor s výpisem paměti, a přečtěte si toto umístění na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, nahraďte **% systemroot%** písmenem jednotky datového disku, například **F:**, v následujících příkazech.

   Navrhovaná konfigurace pro povolení výpisu operačního systému:

   **Načíst podregistr registru z poškozeného disku s operačním systémem:**

   ```ps
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Povolit na ControlSet001:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Povolit na ControlSet002:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Uvolnit poškozený disk s operačním systémem:**

   ```ps
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .