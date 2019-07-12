---
title: Běžné otázky při zálohování souborů a složek pomocí Azure Backup
description: Řeší běžné otázky týkající se zálohování souborů a složek s Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: dd800c0eeb18fe45b44a72aeb58b500623b2b366
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705080"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Běžné dotazy týkající se zálohování souborů a složek

Tento článek obsahuje odpovědi na běžné dotazy abound zálohování souborů a složek pomocí agenta Microsoft Azure Recovery Services (MARS) v [Azure Backup](backup-overview.md) služby.

## <a name="general"></a>Obecné

## <a name="configure-backups"></a>Konfigurace zálohování

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Kde lze stáhnout nejnovější verzi agenta MARS?
Nejnovější verzi agenta MARS při zálohování počítačů s Windows serverem, System Center DPM a Microsoft Azure Backup server je k dispozici pro [Stáhnout](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Jak dlouho jsou platné přihlašovací údaje trezoru?
Platnost přihlašovacích údajů trezoru vyprší po 48 hodinách. Pokud vyprší platnost soubor s přihlašovacími údaji, stáhněte soubor znovu z portálu Azure portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z jaké jednotek můžu zálohovat soubory a složky?

Nemůžete zálohovat následující typy jednotek a svazků:

* Vyměnitelné médium: Všechny zdroje položek k zálohování se musí hlásit jako pevné.
* Svazky jen pro čtení: Svazek musí být zapisovatelný služby Stínová kopie svazku (VSS) fungovat.
* Offline svazky: Svazek musí být online služby VSS, aby funkce.
* Sdílené síťové složky: Svazek musí být místní pro server zálohování pomocí online zálohování.
* Svazky chráněné nástrojem BitLocker: Předtím, než může dojít k zálohování, musí být svazek odemčený.
* Identifikace systému souborů: Je jediným podporovaným systémem souborů NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jaké typy souborů a složek se podporují?

[Další informace](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typech souborů a složek, které jsou podporovány pro zálohování.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Můžete pomocí agenta MARS zálohovat soubory a složky na Virtuálním počítači Azure?  
Ano. Azure Backup poskytuje zálohování na úrovni virtuálního počítače pro virtuální počítače Azure pomocí rozšíření virtuálního počítače pro agenta virtuálního počítače Azure. Pokud chcete zálohovat soubory a složky na hostovaném operačním systému Windows na virtuálním počítači, můžete nainstalovat agenta MARS na to.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Můžete pomocí agenta MARS zálohovat soubory a složky na dočasné úložiště pro virtuální počítač Azure?
Ano. Nainstalujte agenta MARS a zálohovat soubory a složky na hostovaném operačním systému Windows do dočasného úložiště.

- Úlohy zálohování neúspěšné při dat na dočasném úložišti dojde k vymazání.
- Pokud dojde k odstranění dat na dočasném úložišti, lze obnovit pouze na stálé úložiště.

### <a name="how-do-i-register-a-server-to-another-region"></a>Jak se zaregistrovat server do jiné oblasti?

Zálohovaná data se odesílají do datového centra trezoru, ve kterém je server zaregistrovaný. Nejjednodušší způsob, jak změnit datové centrum je potřeba odinstalovat a znovu nainstalujte agenta a pak zaregistrovat počítače do nového trezoru v oblasti, které potřebujete.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Odstranění duplicit podporu agenta MARS Windows serveru 2012?
Ano. Agenta MARS převádí Odstraněná duplicitní data na normální data během přípravy operace zálohování. Potom optimalizuje data pro zálohování, zašifruje je a potom zašifrovaná data odešle do trezoru.

## <a name="manage-backups"></a>Správa záloh

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co se stane, když přejmenuji nakonfigurovaných pro zálohování počítače s Windows?

Při přejmenování počítače s Windows, všechna stávající nastavená zálohování se zastaví.

- Budete muset zaregistrovat nový název počítače k trezoru služby Backup.
- Když zaregistrujete nový název trezoru, je první operace *úplné* zálohování.
- Pokud potřebujete obnovit data zálohovaná do trezoru se starým názvem serveru, použijte možnost obnovit do alternativního umístění v Průvodci obnovením dat. [Další informace](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaký je maximální délka cesty pro zálohu?
Agenta MARS spoléhá na systém souborů NTFS a používá specifikace délky cesty k souboru omezený [rozhraní Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Pokud jsou delší než povolená hodnota soubory, které chcete chránit, zálohujte nadřazenou složku nebo diskovou jednotku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jaké znaky jsou povolené v cestách k souborům?

Agenta MARS spoléhá na systém souborů NTFS a umožňuje [podporované znaky](/windows/desktop/FileIO/naming-a-file#naming-conventions) v názvy cest.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Zobrazí se upozornění "Zálohování Azure nebyly nakonfigurovány pro tento server".
Toto upozornění se může zobrazit i v případě, že nakonfigurujete zásady zálohování, nastavení plánu zálohování uložené na místním serveru neshodují s nastavením uloženým v trezoru služby backup.
- Když serveru nebo nastavení bylo obnoveno do známého funkčního stavu, se může stát nesynchronizované plánů zálohování.
- Pokud se zobrazí toto upozornění [konfigurace](backup-azure-manage-windows-server.md) znovu zásady zálohování a pak spustit na vyžádání zálohování opakovanou synchronizaci místního serveru s Azure.


## <a name="manage-the-backup-cache-folder"></a>Spravovat složku mezipaměť zálohování

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaký je požadavek na minimální velikost složky mezipaměti?
Velikost složky mezipaměti určuje množství dat, která zálohujete.
- Svazky složka mezipaměti musí mít volné místo, které se rovná aspoň 5 až 10 % celkové velikosti dat zálohy.
- Pokud svazek obsahuje méně než 5 % volné místo, zvětšete svazek nebo přesuňte složku mezipaměti na svazek s dostatkem místa.
- Pokud jste zálohu stavu systému Windows, bude nutné dalších 30 – 35 GB volného místa ve svazku, který obsahuje složku mezipaměti.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak zkontrolovat, zda je pomocnou složku platná a dostupná?

1. Ve výchozím nastavení se nachází v odkládací složce `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Ujistěte se, že cesta umístění odkládací složky se shoduje s hodnotami klíče položky registru je uvedeno níže:

  | Cesta k registru | Klíč registru | Value |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Změna umístění mezipaměti pro agenta MARS

1. V příkazovém řádku se zvýšenými oprávněními na zastavte modul zálohování spuštěním tohoto příkazu:

    ```PS C:\> Net stop obengine```

2. Soubory nepřesouvejte. Místo toho zkopírujte složku mezipaměti místo na jinou jednotku, která má dost volného místa.
3. Aktualizujte následující položky registru cesty nové složky mezipaměti.<br/>

    | Cesta k registru | Klíč registru | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

4. Restartujte modul Backup na příkazovém řádku se zvýšenými oprávněními:

    ```PS C:\> Net start obengine```

5. Po dokončení zálohování úspěšně pomocí nové umístění, můžete původní složku mezipaměti odebrat.


### <a name="where-should-the-cache-folder-be-located"></a>Pokud třeba složky mezipaměti umístěné?

Pro složku mezipaměti nedoporučujeme používat následující umístění:

* Síťové sdílené složky a vyměnitelné médium: Složka mezipaměti musí být místní pro server, který potřebuje zálohování pomocí online zálohování. Síťová umístění a vyměnitelná média jako jednotky USB nejsou podporovány.
* Offline svazky: Složka mezipaměti musí být online pro očekávané zálohování pomocí agenta Azure Backup

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Jsou nějaké atributy složky mezipaměti, které nejsou podporované?
Složka mezipaměti nepodporuje následující atributy nebo jejich kombinace:

* Šifrované
* S odstraněním duplicit
* Komprimované
* Řídké
* Bod rozboru

Složka mezipaměti ani virtuální pevný disk s metadaty nemají atributy vyžadované pro agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existuje způsob, jak nastavit šířku pásma používanou pro zálohování?

Ano, můžete použít **změnit vlastnosti** možnost v agenta MARS k úpravě šířky pásma a časování. [Další informace](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Obnovení

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším úlohu probíhající obnovení?

Pokud se zruší úlohu obnovení probíhající, zastaví proces obnovení. Všechny soubory obnovit před zrušením zůstat v nakonfigurovaných cíl (původního nebo alternativního umístění), bez jakékoli vrácení zpět.


## <a name="next-steps"></a>Další postup

[Přečtěte si](tutorial-backup-windows-server-to-azure.md) jak zálohovat počítač s Windows.
