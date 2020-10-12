---
title: Při použití zásad konfigurace zásad auditu nereaguje virtuální počítač.
description: Tento článek popisuje kroky pro řešení problémů, které virtuální počítač (VM) přestane reagovat při aplikování zásad konfigurace zásad auditu, které brání spuštění virtuálního počítače Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: bc41783bf977806b5f9bba5b953f1f581ad07f18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299520"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>Při použití zásad konfigurace zásad auditu nereaguje virtuální počítač.

Tento článek popisuje kroky pro řešení problémů, které virtuální počítač (VM) přestane reagovat při aplikování zásad konfigurace zásad auditu, které brání spuštění virtuálního počítače Azure.

## <a name="symptom"></a>Příznak

Při použití [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku obrazovky virtuálního počítače se zobrazí obrazovka s oznámením, že operační systém (OS) přestal při spuštění reagovat pomocí zprávy s **použitím zásad konfigurace zásad auditu**.

  ![Spouštění operačního systému pomocí této zprávy: "použití zásad konfigurace zásad auditu"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Spouštění operačního systému v systému Windows Server 2012 s touto zprávou: "použití zásad konfigurace zásad auditu"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Příčina

V případě, že se zásady pokusí vyčistit staré profily uživatelů, dochází ke konfliktům zámků.

> [!NOTE]
> To platí jenom pro systémy Windows Server 2012 a Windows Server 2012 R2.

Tady je problematické zásady: *počítač \ šablony \ systémové/Uživatel Profiles\Delete profily uživatelů starší než zadaný počet dní při restartování systému.*

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Zakažte tuto zásadu.
1. Povolte kolekci sériového výpisu a výpisu paměti.
1. Znovu sestavte virtuální počítač.
1. Shromážděte soubor výpisu paměti a odešlete lístek podpory.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.

### <a name="disable-the-policy"></a>Zakázat zásadu

1. V okně opravit virtuální počítač otevřete **Editor registru**.
1. Vyhledejte klíč **HKEY_LOCAL_MACHINE** a v nabídce vyberte **soubor > Načíst podregistr** .

   ![Navigace v editoru registru pro načtení podregistru.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Pomocí funkce Load podregistr můžete načíst klíče registru z offline systému. V tomto případě je systém poškozeným diskem připojeným k opravnému virtuálnímu počítači.
   - Nastavení systému je uloženo na **HKEY_LOCAL_MACHINE** a lze je zkrátit jako **HKLM**.

1. V připojeném disku otevřete `\windows\system32\config\SOFTWARE` soubor.

   - Po zobrazení výzvy k zadání názvu zadejte **BROKENSOFTWARE**.
   - Chcete-li ověřit, zda byl **BROKENSOFTWARE** načten, rozbalte **HKEY_LOCAL_MACHINE** a vyhledejte přidaný **BROKENSOFTWARE** klíč.

1. Přejít na **BROKENSOFTWARE** a ověřte, jestli v načteném podregistru existuje klíč **CleanupProfiles** .

   - Pokud klíč existuje, zásada **CleanupProfiles** se nastaví. Jeho hodnota představuje zásady uchovávání informací měřené ve dnech.
   - Pokud klíč neexistuje, zásada **CleanupProfiles** se nenastaví. V takové situaci můžete přeskočit k [odeslání lístku podpory se souborem s výpisem paměti](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Odstraňte klíč **CleanupProfiles** pomocí tohoto příkazu:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Uvolněte podregistr **BROKENSOFTWARE** pomocí tohoto příkazu:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Povolení kolekce sériové konzoly a výpisu paměti

**Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériového stavu a výpisu paměti spuštěním následujícího skriptu:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními jako správce.
1. Seznamte se s daty úložiště BCD a určete identifikátor zaváděcího programu, který použijete v dalším kroku.

   1. Pro virtuální počítač 1. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - V příkazu nahraďte `<BOOT PARTITON>` písmenem oddílu připojeným diskem, který obsahuje spouštěcí složku.

        ![Obrázek 4 ukazuje výstup výpisu úložiště BCD na virtuálním počítači generace 1, který obsahuje seznam v části spouštěcí zavaděč Windows číslo identifikátoru.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. U virtuálního počítače 2. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - V příkazu nahraďte `<LETTER OF THE EFI SYSTEM PARTITION>` písmenem systémového oddílu EFI.
      - Může být užitečné spustit konzolu pro správu disků a identifikovat příslušný systémový oddíl označený jako **systémový oddíl EFI**.
      - Identifikátor může být jedinečný identifikátor GUID, nebo může být výchozím **Bootmgr**.

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
   
### <a name="rebuild-the-virtual-machine"></a>Znovu sestavit virtuální počítač

1. K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

1. Otestujte, jestli se virtuální počítač spouští normálně, a zjistěte, jestli problém vyřešil problém.

   - Pokud se problém nevyřeší, pokračujte tím, že [shromáždíte soubor s výpisem paměti a odešlete lístek podpory](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Pokud byl problém vyřešen, není nutné provádět žádné další kroky.

Pokud byl problém vyřešen, zásada je nyní zakázána místně. V případě trvalého řešení nepoužívejte zásady CleanupProfiles na virtuálních počítačích, protože budou automaticky odstraňovat profily uživatelů. K provádění úklidů profilů, jako je například naplánovaná úloha nebo skript, použijte jinou metodu.

**Nepoužívat tyto zásady:** 
 *Machine\Admin složce Profiles\Delete profily uživatelů starší než zadaný počet dní při restartování systému.*

### <a name="the-issue-should-now-be-fixed"></a>Problém by se teď měl opravit.

Otestujte svůj virtuální počítač, abyste se ujistili, že funguje jako normální. Pokud stále dochází k problémům, můžete pokračovat v další části, kde najdete další pomoc.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Shromáždění souboru s výpisem paměti a odeslání lístku podpory

Chcete-li tento problém vyřešit, je třeba nejprve shromáždit soubor výpisu paměti pro danou chybu a pak kontaktovat podporu pomocí souboru s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. K přípravě nového opravného virtuálního počítače použijte kroky 1-3 [příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) .
1. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem označeno jako *F*, pak musíte přejít na `F:\Windows` .
1. Vyhledejte `memory.dmp` soubor a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem výpisu paměti.
1. Pokud se vám nedaří najít `memory.dmp` soubor, použijte místo toho [volání NMI (nemaskovaná přerušení) v sériové konzole](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Postupujte podle pokynů průvodce a [vygenerujte soubor s výpisem stavu systému pomocí volání NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).
