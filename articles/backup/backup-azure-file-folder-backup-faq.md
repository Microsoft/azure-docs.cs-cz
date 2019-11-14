---
title: Zálohování souborů a složek pomocí Azure Backup – běžných otázek
description: Řeší běžné otázky týkající se zálohování souborů a složek pomocí Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: c65c04a67b92642d3664293dd666236919142f12
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074198"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Běžné dotazy týkající se zálohování souborů a složek

Tento článek obsahuje odpovědi na běžné dotazy Abound zálohování souborů a složek pomocí agenta služby Microsoft Azure Recovery Services (MARS) ve službě [Azure Backup](backup-overview.md) .

## <a name="configure-backups"></a>Konfigurace zálohování

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Kde můžu stáhnout nejnovější verzi agenta MARS?

Nejnovější agent MARS, který se používá při zálohování počítačů s Windows serverem, aplikace System Center DPM a serveru Microsoft Azure Backup, je k dispozici ke [stažení](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Jak dlouho jsou přihlašovací údaje trezoru platné?

Platnost přihlašovacích údajů trezoru vyprší po 48 hodinách. Pokud platnost souboru přihlašovacích údajů vyprší, Stáhněte soubor znovu z Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z jakých jednotek můžu zálohovat soubory a složky?

Nemůžete zálohovat následující typy jednotek a svazků:

* Vyměnitelná média: všechny zdroje zálohované položky musí hlásit jako pevné.
* Svazky jen pro čtení: svazek musí být zapisovatelný, aby služba Stínová kopie svazku (VSS) fungovala.
* Offline svazky: svazek musí být online, aby služba VSS mohla fungovat.
* Sdílené síťové složky: svazek musí být místní pro server, který se má zálohovat pomocí online zálohování.
* Svazky chráněné nástrojem BitLocker: svazek musí být odemčen před tím, než může dojít k zálohování.
* Identifikace systému souborů: Jediným podporovaným systémem souborů je NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jaké typy souborů a složek jsou podporované?

[Přečtěte si další informace](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typech souborů a složek podporovaných pro zálohování.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Můžu použít agenta MARS k zálohování souborů a složek na virtuálním počítači Azure?  

Ano. Azure Backup poskytuje zálohování na úrovni virtuálního počítače pro virtuální počítače Azure pomocí rozšíření virtuálního počítače pro agenta virtuálního počítače Azure. Pokud chcete zálohovat soubory a složky na hostovaném operačním systému Windows na VIRTUÁLNÍm počítači, můžete k tomu nainstalovat agenta MARS.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Můžu použít agenta MARS k zálohování souborů a složek v dočasném úložišti pro virtuální počítač Azure?

Ano. Nainstalujte agenta MARS a zálohujte soubory a složky v hostovaném operačním systému Windows do dočasného úložiště.

* Úlohy zálohování selžou, když se vymažou data dočasného úložiště.
* Pokud jsou data dočasného úložiště odstraněna, lze obnovit pouze do úložiště, které není volatile.

### <a name="how-do-i-register-a-server-to-another-region"></a>Návody zaregistrovat server do jiné oblasti?

Zálohovaná data se odesílají do datacentra trezoru, ve kterém je server zaregistrovaný. Nejjednodušší způsob, jak změnit datové centrum, je odinstalovat a znovu nainstalovat agenta a pak tento počítač zaregistrovat do nového trezoru v oblasti, kterou potřebujete.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Podporuje agent MARS odstranění duplicitních dat ve Windows Serveru 2012?

Ano. Po přípravě operace zálohování převede agent MARS data s odstraněnými duplicitními daty na běžná data. Pak optimalizuje data pro zálohování, šifruje data a pak zašifrovaná data pošle do trezoru.

## <a name="manage-backups"></a>Správa záloh

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co se stane, když přejmenujem počítač se systémem Windows nakonfigurovaný pro zálohování?

Při přejmenování počítače se systémem Windows se zastaví všechna aktuálně nakonfigurovaná zálohování.

* Nový název počítače je potřeba zaregistrovat v úložišti záloh.
* Když zaregistrujete nový název trezoru, první operace je *Úplná* záloha.
* Pokud potřebujete obnovit data zálohovaná do trezoru se starým názvem serveru, použijte možnost obnovení do alternativního umístění v Průvodci obnovením dat. [Další informace](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaká je maximální délka cesty k souboru pro zálohování?

Agent MARS spoléhá na systém souborů NTFS a použije specifikaci délky FilePath, která je omezená [rozhraním API systému Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Pokud soubory, které chcete chránit, jsou delší než povolená hodnota, zálohujte nadřazenou složku nebo diskovou jednotku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jaké znaky jsou povoleny v cestách k souborům?

Agent MARS spoléhá na NTFS a v názvech souborů nebo cestách umožňuje [podporované znaky](/windows/desktop/FileIO/naming-a-file#naming-conventions) .

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Zobrazí se upozornění "zálohy Azure nebyly pro tento server nakonfigurovány".

Toto upozornění se může objevit i v případě, že jste nakonfigurovali zásady zálohování, protože nastavení plánu zálohování uložené na místním serveru se neshoduje s nastavením uloženým v trezoru záloh.

* Když se server nebo nastavení obnovily do známého funkčního stavu, plány zálohování se můžou stát nesynchronizovanými.
* Pokud se zobrazí toto upozornění, [nakonfigurujte](backup-azure-manage-windows-server.md) znovu zásady zálohování a pak spusťte zálohování na vyžádání, aby se místní server znovu synchronizoval s Azure.

## <a name="manage-the-backup-cache-folder"></a>Správa složky mezipaměti pro zálohování

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaký je požadavek na minimální velikost složky mezipaměti?

Velikost složky mezipaměti určuje množství dat, která zálohujete.

* Svazky složek mezipaměti by měly mít volné místo, které se rovná minimálně 5-10% celkové velikosti zálohovaných dat.
* Pokud svazek obsahuje méně než 5% volného místa, zvětšete velikost svazku nebo přesuňte složku mezipaměti na svazek s dostatkem místa.
* Pokud budete zálohovat stav systému Windows, budete potřebovat dalších 30-35 GB volného místa ve svazku, který obsahuje složku mezipaměti.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak zjistit, jestli je pomocná složka platná a dostupná?

1. Ve výchozím nastavení je pomocná složka umístěna na adrese `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Ujistěte se, že cesta k umístění pomocné složky odpovídá hodnotám klíčů registru, které jsou uvedené níže:

  | Cesta k registru | Klíč registru | Hodnota |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Návody změnit umístění mezipaměti pro agenta MARS?

1. Spuštěním tohoto příkazu v příkazovém řádku se zvýšenými oprávněními zastavte modul zálohování:

    ```Net stop obengine```

2. Pokud jste nakonfigurovali zálohování stavu systému, otevřete správu disků a odpojte disky s názvy ve formátu `"CBSSBVol_<ID>"`.
3. Nepřesouvat soubory. Místo toho zkopírujte složku místo v mezipaměti na jinou jednotku s dostatkem místa.
4. Aktualizujte následující položky registru s cestou nové složky mezipaměti.

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

5. Restartujte zálohovací stroj na příkazovém řádku se zvýšenými oprávněními:

  ```command
  Net stop obengine

  Net start obengine
  ```

6. Spusťte zálohování na vyžádání. Po úspěšném dokončení zálohování pomocí nového umístění můžete odebrat původní složku mezipaměti.

### <a name="where-should-the-cache-folder-be-located"></a>Kde by měla být složka mezipaměti umístěna?

Pro složku mezipaměti nedoporučujeme používat následující umístění:

* Sdílená síťová složka nebo vyměnitelná média: Složka mezipaměti musí být místní pro server, který potřebuje zálohování pomocí online zálohování. Síťová umístění nebo vyměnitelná média jako jednotky USB nejsou podporovaná.
* Offline svazky: Složka mezipaměti musí být online pro očekávané zálohování pomocí agenta Azure Backup

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existují nějaké atributy složky mezipaměti, které nejsou podporované?

Složka mezipaměti nepodporuje následující atributy nebo jejich kombinace:

* Šifrované
* S odstraněním duplicit
* Komprimované
* Řídké
* Bod rozboru

Složka mezipaměti ani virtuální pevný disk s metadaty nemají atributy vyžadované pro agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existuje způsob, jak upravit šířku pásma používanou pro zálohování?

Ano, pomocí možnosti **změnit vlastnosti** v agentovi Mars můžete upravit šířku pásma a časování. [Další informace](backup-configure-vault.md#enable-network-throttling)

## <a name="restore"></a>Obnovení

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je probíhající úloha obnovení zrušena, proces obnovení se zastaví. Všechny soubory obnovené před zrušením zůstávají v nakonfigurovaném cíli (původní nebo alternativní umístění) bez vrácení zpět.

## <a name="next-steps"></a>Další kroky

[Naučte](tutorial-backup-windows-server-to-azure.md) se zálohovat počítač s Windows.
