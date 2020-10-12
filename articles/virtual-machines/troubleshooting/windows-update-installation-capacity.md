---
title: Řešení potíží s startem operačního systému – web Windows Update kapacity instalace
description: Kroky k vyřešení problémů, kdy web Windows Update (KB) vrátí chybu a přestane reagovat na virtuálním počítači Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 596303223554589ef26938486ccfd2281ccd46f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999101"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Řešení potíží s startem operačního systému – web Windows Update kapacity instalace

Tento článek popisuje kroky pro řešení problémů s virtuálním počítačem Azure (VM), kde web Windows Update (KB) vrátí chybu a přestane reagovat.

## <a name="symptom"></a>Příznak

Když k zobrazení obrazovky virtuálního počítače použijete diagnostiku spouštění, uvidíte, že se na snímku obrazovky zobrazí web Windows Update (KB), ale neúspěšná chyba s kódem chyby: **C01A001D**. Následující obrázek ukazuje web Windows Update (KB) zablokované se zprávou "Chyba C01A001D při aplikování operace Update # # # # # # # # # # (# # # # # #)":

![Web Windows Update (KB) se zablokuje ve zprávě, "Error C01A001D při použití aktualizační operace X z Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Příčina

V této situaci operační systém (OS) nemůže dokončit instalaci web Windows Update (KB), protože v systému souborů nelze vytvořit základní soubor. Podle tohoto kódu chyby operační systém nemůže na disk zapisovat žádné soubory.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Volné místo na disku.
1. Povolte kolekci sériového výpisu a výpisu paměti.
1. Znovu sestavte virtuální počítač.

> [!NOTE]
> Při výskytu této chyby hostující operační systém není funkční. Pokud chcete tento problém vyřešit, odstraňte tento problém v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše se připojte k opravnému virtuálnímu počítači.

### <a name="free-up-space-on-the-disk"></a>Uvolněte místo na disku.

Řešení problému:

- Změňte velikost disku na 1 TB, pokud již není maximální velikost 1 TB.
- Proveďte Vyčištění disku.
- Zrušte fragmentaci jednotky.

1. Ověřte, zda je disk plný. Pokud je velikost disku menší než 1 TB, rozbalte ji do velikosti až 1 TB [pomocí PowerShellu](../windows/expand-os-disk.md).
1. Pokud je disk již 1 TB, bude nutné provést vyčištění disku.
   1. Odpojte datový disk [od poškozeného virtuálního počítače](../windows/detach-disk.md).
   1. Připojte datový disk [k fungujícímu virtuálnímu počítači](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
   1. Uvolněte místo pomocí [nástroje Vyčištění disku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
1. Po dokončení změny velikosti a vyčištění pomocí tohoto příkazu zrušte fragmentaci jednotky:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
V závislosti na úrovni fragmentace může defragmentace trvat několik hodin.

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

    **Načtěte poškozený disk s operačním systémem:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Povolit na CONTROLSET001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Povolit na ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Uvolnit poškozený disk s operačním systémem**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
