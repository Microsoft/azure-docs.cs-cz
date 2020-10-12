---
title: Řešení potíží s Windows stop – Chyba při inicializaci adresářové služby
description: Vyřešte problémy, při kterých se virtuální počítač řadiče domény služby Active Directory v Azure zablokuje ve smyčce, která uvádí, že se musí restartovat.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 909481964f8aa3272715e235fa011562225a9422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028358"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Řešení potíží s Windows stop – Chyba při inicializaci adresářové služby

Tento článek popisuje kroky pro řešení problémů, při kterých se virtuální počítač řadiče domény služby Active Directory v Azure zablokuje ve smyčce a uvádí, že se musí restartovat.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, snímek obrazovky ukazuje, že se virtuální počítač musí restartovat kvůli chybě, a to zobrazení stop Code **0XC00002E1** v systému Windows Server 2008 R2 nebo **0xC00002E2** v systému Windows Server 2012 nebo novějším.

![Stav úvodní obrazovky systému Windows Server 2012: Váš počítač byl příčinou problému a je třeba jej restartovat. Jenom shromažďujeme nějaké informace o chybách a pak se za vás restartuje...](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Příčina

Kód chyby **0xC00002E2** představuje **STATUS_DS_INIT_FAILURE**a kód chyby **0xC00002E1** představuje **STATUS_DS_CANT_START**. K oběma chybám dochází, když dojde k potížím s adresářovou službou.

Při spuštění operačního systému se pak vynuceně automaticky restartuje místní server pro ověření zabezpečení (**LSASS.exe**), který ověřuje přihlášení uživatelů. Ověřování se nemůže stát, když je operační systém na virtuálním počítači řadič domény, který nemá přístup pro čtení a zápis do místní databáze služby Active Directory. Z důvodu nedostatku přístupu ke **službě Active Directory (AD)**, LSASS.exe nelze ověřit a je vynuceno restartovat operační systém.

Tato chyba může být způsobena některou z následujících podmínek:

- Nemáte přístup k disku, který je v místní databázi AD (**NTDS. DIT**).
- Disk, který je v místní databázi AD (NTDS. DIT) nemá dostatek volného místa.
- Místní databáze služby AD (NTDS. Soubor DIT) chybí.
- Virtuální počítač má několik disků a zásady sítě SAN (Storage Area Network) jsou nesprávně nakonfigurované. Zásady sítě SAN nejsou nastavené na **ONLINEALL**a disky s jinou než operačním systémem jsou připojené ke Správci disků v režimu offline.
- Místní databáze služby AD (NTDS. Soubor DIT) je poškozen.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu:

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Volné místo na disku.
1. Ověřte, že je připojená jednotka, která obsahuje databázi AD.
1. Povolte režim obnovení adresářových služeb.
1. **Doporučené**: před opětovným SESTAVENÍM virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.
1. Znovu sestavte virtuální počítač.
1. Překonfigurujte zásady sítě SAN.

> [!NOTE]
> Při výskytu této chyby hostující operační systém není funkční. Problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) Připravte opravný virtuální počítač.
1. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="free-up-space-on-disk"></a>Uvolněte místo na disku.

Když je disk připojený k opravnému virtuálnímu počítači, zkontrolujte, jestli má disk s interní databází Active Directory dostatek místa pro správné provedení.

1. Ověřte, jestli je disk plný, a to tak, že kliknete pravým tlačítkem na jednotku a vyberete **vlastnosti**.
1. Pokud disk má méně než 300 MB volného místa, [rozbalte ho do velikosti 1 TB pomocí PowerShellu](../windows/expand-os-disk.md).
1. Pokud disk dosáhl 1 TB využitého místa, proveďte Vyčištění disku.

   1. Pomocí PowerShellu [odpojte datový disk](../windows/detach-disk.md#detach-a-data-disk-using-powershell) od PŘERUŠENÉHO virtuálního počítače.
   1. Po odpojení od poškozeného virtuálního počítače [Připojte datový disk](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm) k FUNGUJÍCÍmu virtuálnímu počítači.
   1. Uvolněte další místo pomocí [nástroje Vyčištění disku](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .

1. **Volitelné** – Pokud je potřeba více místa, otevřete instanci cmd a zadejte `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` příkaz pro provedení defragmentace na jednotce:

  * V příkazu nahraďte `<LETTER ASSIGNED TO THE OS DISK>` písmenem disku s operačním systémem. Například pokud je písmeno disku `F:` , pak bude příkaz `defrag F: /u /x /g` .

  * V závislosti na úrovni fragmentace může defragmentace trvat hodiny.

Pokud je na disku dostatek místa, pokračujte dalším úkolem.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Ověřte, že je připojená jednotka obsahující databázi služby Active Directory.

1. Otevřete instanci příkazového řádku se zvýšenými oprávněními a spusťte následující příkazy:

   1. Načíst soubor registru:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      Označení `f:` předpokládá, že disk je jednotka `F:` . Použijte písmeno jednotky patřící do jednotky, která obsahuje disk s operačním systémem.

   1. Určete písmeno jednotky a složku **NTDS. DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Uvolnit soubor registru:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Pomocí Azure Portal ověřte, zda jednotka, kde je NTDS. Na virtuálním počítači se přidá DIT.
1. Pomocí konzoly pro správu disků z hostovaného operačního systému ověřte, zda disk obsahuje NTDS. DIT je online.
   1. Nástroj Správa disků najdete v části **Nástroje pro správu > Správa počítače > úložiště**nebo k nim můžete získat pøístup pomocí `diskmgmt.msc` příkazu v instanci cmd.
1. Pokud disk není připojený k virtuálnímu počítači, znovu připojte datový disk, aby se problém vyřešil.

   Pokud byl disk připojen normálně, pokračujte dalším úkolem.

### <a name="enable-directory-services-restore-mode"></a>Povolit režim obnovení adresářových služeb

Nastavte virtuální počítač tak, aby se spouštěl v režimu **obnovení adresářových služeb** , abyste mohli obejít kontrolu existence NTDS. Soubor DIT během spouštění.

1. Než budete pokračovat, ověřte, že jste dokončili předchozí úlohy pro připojení disku k opravnému virtuálnímu počítači, a zjistili jste, který disk NTDS. Soubor DIT se nachází v.
1. Pomocí instance CMD se zvýšenými oprávněními uveďte v tomto úložišti informace o spouštěcím oddílu, abyste našli identifikátor z aktivního oddílu:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Nahraďte `< Drive Letter >` písmenem určeným v předchozích krocích.

   ![Po zadání příkazu bcdedit/Store <Drive>: \boot\bcd/enum zobrazí snímek obrazovky, který zobrazuje správce spouštění systému Windows s identifikátorem.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Povolit `safeboot DsRepair` příznak pro spouštěcí oddíl:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Nahraďte `< Drive Letter >` a hodnotou `< Identifier >` určenou v předchozích krocích.

1. Spusťte znovu dotaz na možnosti spuštění, abyste měli jistotu, že vaše změna byla správně nastavena.

   ![Po povolení příznaku DsRepair SafeBoot zobrazí snímek obrazovky instanci CMD se zvýšenými oprávněními.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Doporučené: před opětovným sestavením virtuálního počítače povolte kolekci sériové konzoly a výpisu paměti.

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte následující skript otevřením relace příkazového řádku se zvýšenými oprávněními (Spustit jako správce) a spusťte následující příkazy.

1. Povolte konzolu sériového portu:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Ověřte, že volné místo na disku s operačním systémem je aspoň stejné jako velikost paměti (RAM) na virtuálním počítači.

  1. Pokud není dostatek místa na disku s operačním systémem, změňte umístění, ve kterém se vytvoří soubor s výpisem paměti, a přečtěte si to na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa.

     Chcete-li změnit umístění, nahraďte ho `%SystemRoot%` písmenem jednotky (například, `F:` ) datového disku v následujících příkazech.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Pro povolení výpisu operačního systému je doporučena následující konfigurace:

  **Načíst poškozený disk s operačním systémem**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

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

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

1. Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač.

### <a name="reconfigure-the-storage-area-network-policy"></a>Překonfigurujte zásady sítě oblasti úložiště.

1. Při spuštění v režimu DSRM je jediným uživatelem dostupným pro přihlášení správce obnovení, který se použil v případě, kdy byl virtuální počítač povýšen na řadič domény. Všem ostatním uživatelům se zobrazí chyba ověřování.

   1. Pokud není k dispozici žádný jiný řadič domény, musíte se přihlásit místně pomocí `.\administrator` nebo `machinename\administrator` a hesla DSRM.

1. Nastavte zásady sítě SAN tak, aby všechny disky byly online.

   1. Otevřete instanci příkazového řádku se zvýšenými oprávněními a zadejte `DISKPART` .
   1. Dotaz na seznam disků

      `DISKPART> list disk`

   1. Zadáním následujících příkazů vyberte disk, který je nutné uvést do režimu online, a změňte zásady sítě SAN:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Po vyřešení problému se ujistěte, že je tento příznak `DsRepair safeboot` odebraný:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Restartujte virtuální počítač.

   > [!NOTE]
   > Pokud byl váš virtuální počítač právě migrován z místního prostředí a chcete migrovat více řadičů domény z místního prostředí do Azure, měli byste zvážit postup v následujících článcích, abyste zabránili tomu, že se tento problém v budoucích migracích neděje:
   >
   > [Postup nahrání stávajících místních řadičů domény Hyper-V do Azure pomocí Azure PowerShell](https://support.microsoft.com/help/2904015)
