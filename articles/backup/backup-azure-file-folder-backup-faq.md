---
title: Zálohování souborů a složek – běžné otázky
description: Řeší běžné otázky týkající se zálohování souborů a složek pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 6e9f265672ff15e40444a46a3e440e73a0051a5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254746"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Běžné otázky týkající se zálohování souborů a složek

Tento článek odpovídá na běžné otázky, které jsou hojné zálohování souborů a složek agentem Microsoft Azure Recovery Services (MARS) ve službě [Azure Backup.](backup-overview.md)

## <a name="configure-backups"></a>Konfigurace zálohování

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Kde mohu stáhnout nejnovější verzi agenta MARS?

Nejnovější agent MARS používaný při zálohování počítačů se systémem Windows Server, system center DPM a serveru Microsoft Azure Backup je k dispozici [ke stažení](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Jak dlouho jsou pověření trezoru platná?

Platnost přihlašovacích údajů trezoru vyprší po 10 dnech. Pokud vyprší platnost souboru přihlašovacích údajů, stáhněte soubor znovu z webu Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Z jakých jednotek lze zálohovat soubory a složky?

Nelze zálohovat následující typy jednotek a svazků:

* Vyměnitelné médium: Všechny zdroje položek zálohování musí vykazovat jako opravené.
* Svazky jen pro čtení: Aby služba stínové kopie svazku (VSS) fungovala, musí být zapisovatelná.
* Offline svazky: Svazek musí být online, aby vSS fungovat.
* Sdílené složky v síti: Svazek musí být místní pro server, aby byl zálohován pomocí zálohování online.
* Svazky chráněné nástrojem BitLocker: Svazek musí být před zálohováním odemčen.
* Identifikace systému souborů: Jediným podporovaným systémem souborů je NTFS.

### <a name="what-file-and-folder-types-are-supported"></a>Jaké typy souborů a složek jsou podporovány?

[Přečtěte si další informace](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) o typech souborů a složek podporovaných pro zálohování.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Můžu použít agenta MARS k zálohování souborů a složek na virtuálním počítači Azure?  

Ano. Azure Backup poskytuje zálohování na úrovni virtuálních počítačů pro virtuální počítače Azure pomocí rozšíření virtuálních počítače pro agenta virtuálního počítače Azure. Pokud chcete zálohovat soubory a složky v hostovaném operačním systému Windows na virtuálním počítači, můžete k tomu nainstalovat agenta MARS.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Můžu použít agenta MARS k zálohování souborů a složek v dočasném úložišti pro virtuální počítač Azure?

Ano. Nainstalujte agenta MARS a zálohujte soubory a složky v hostovaném operačním systému Windows do dočasného úložiště.

* Úlohy zálohování se nezdaří při vymazání dat dočasného úložiště.
* Pokud jsou data dočasného úložiště odstraněna, můžete obnovit pouze nestálé úložiště.

### <a name="how-do-i-register-a-server-to-another-region"></a>Jak mohu zaregistrovat server do jiné oblasti?

Záložní data jsou odesílána do datového centra úložiště, ve kterém je server registrován. Nejjednodušší způsob, jak změnit datové centrum, je odinstalovat a znovu nainstalovat agenta a potom počítač zaregistrovat do nového trezoru v oblasti, kterou potřebujete.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Podporuje agent MARS odstranění duplicit systému Windows Server 2012?

Ano. Agent MARS převede data deduplikovaných deduplikovaných na normální data při přípravě operace zálohování. Poté optimalizuje data pro zálohování, šifruje data a poté odešle šifrovaná data do úložiště.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Potřebuji oprávnění správce k instalaci a konfiguraci agenta MARS?

Ano, instalace agenta MARS a konfigurace záloh pomocí konzoly MARS vyžadují, aby byl uživatel místním správcem chráněného serveru.

## <a name="manage-backups"></a>Správa záloh

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co se stane, když přejmenuju počítač se systémem Windows nakonfigurovaný pro zálohování?

Při přejmenování počítače se systémem Windows jsou zastaveny všechny aktuálně nakonfigurované zálohy.

* Nový název počítače je třeba zaregistrovat v trezoru zálohování.
* Při registraci nového názvu s trezorem je první operace *úplnou* zálohou.
* Pokud potřebujete obnovit data zálohovaná do úložiště se starým názvem serveru, použijte možnost obnovení do alternativního umístění v Průvodci obnovením dat. [Další informace](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaká je maximální délka cesty k souboru pro zálohování?

Agent MARS spoléhá na systém souborů NTFS a používá specifikaci délky cesty souboru omezenou [rozhraním API systému Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Pokud jsou soubory, které chcete chránit, delší než povolená hodnota, zálohujte nadřazenou složku nebo diskovou jednotku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jaké znaky jsou povoleny v cestách souborů?

Agent MARS spoléhá na systém souborů NTFS a umožňuje [podporované znaky](/windows/desktop/FileIO/naming-a-file#naming-conventions) v názvech souborů nebo cestách.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Zobrazí se upozornění, že zálohy Azure nebyly pro tento server nakonfigurovány.

Toto upozornění se může zobrazit i v případě, že jste nakonfigurovali zásady zálohování, pokud nastavení plánu zálohování uložené na místním serveru nejsou stejná jako nastavení uložená v úložišti záloh.

* Pokud byl server nebo nastavení obnovendo známého dobrého stavu, mohou být plány zálohování nesynchronizované.
* Pokud se zobrazí toto upozornění, [nakonfigurujte](backup-azure-manage-windows-server.md) zásady zálohování znovu a pak spusťte zálohu na vyžádání, abyste znovu synchronizovali místní server s Azure.

## <a name="manage-the-backup-cache-folder"></a>Správa složky záložní mezipaměti

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaký je požadavek na minimální velikost složky mezipaměti?

Velikost složky mezipaměti určuje množství dat, která zálohujete.

* Svazky složek mezipaměti by měly mít volné místo, které se rovná alespoň 5-10 % celkové velikosti záložních dat.
* Pokud má svazek méně než 5 % volného místa, zvětšete velikost svazku nebo přesuňte složku mezipaměti na svazek s dostatkem místa [pomocí následujících kroků](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Pokud zálohujete stav systému Windows, budete potřebovat dalších 30 až 35 GB volného místa na svazku obsahujícím složku mezipaměti.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak zkontrolovat, zda je stírací složky platná a přístupná?

1. Ve výchozím nastavení je odkládací složka umístěna na adrese`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Ujistěte se, že cesta umístění odkládací složky odpovídá hodnotám položek klíče registru, které jsou uvedeny níže:

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Jak změním umístění mezipaměti agenta MARS?

1. Spusťte tento příkaz v příkazovém řádku se zvýšenými oprávněními k zastavení modulu zálohování:

    ```Net stop obengine```
2. Pokud jste nakonfigurovali zálohování stavu systému, otevřete nástroj Správa `"CBSSBVol_<ID>"`disků a odpojte disky s názvy ve formátu .
3. Ve výchozím nastavení je odkládací složka umístěna`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Zkopírujte `\Scratch` celou složku na jinou jednotku, která má dostatek místa. Ujistěte se, že obsah je zkopírován, nikoli přesunut.
5. Aktualizujte následující položky registru cestou nově přesunuté poškrábané složky.

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění odkládací složky* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění odkládací složky* |

6. Restartujte modul zálohování na příkazovém řádku se zvýšenými oprávněními:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Spusťte zálohu na vyžádání. Po úspěšném dokončení zálohování pomocí nového umístění můžete odebrat původní složku mezipaměti.

### <a name="where-should-the-cache-folder-be-located"></a>Kde by měla být umístěna složka mezipaměti?

Následující umístění pro složku mezipaměti se nedoporučuje:

* Sdílená nebo vyměnitelná média sítě: Složka mezipaměti musí být místní pro server, který potřebuje zálohování pomocí zálohování online. Síťová umístění nebo vyměnitelná média, jako jsou jednotky USB, nejsou podporována.
* Offline svazky: Složka mezipaměti musí být online pro očekávané zálohování pomocí Agenta zálohování Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existují nějaké atributy složky mezipaměti, které nejsou podporovány?

Složka mezipaměti nepodporuje následující atributy nebo jejich kombinace:

* Šifrované
* S odstraněním duplicit
* Komprimované
* Řídké
* Bod rozboru

Složka mezipaměti a virtuální pevný disk metadat nemají potřebné atributy pro agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existuje způsob, jak upravit velikost šířky pásma použité pro zálohování?

Ano, možnost **Změnit vlastnosti** v agentovi MARS můžete upravit šířku pásma a časování. [Další informace](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Obnovení

### <a name="manage"></a>Spravovat

**Mohu se zotavit, když zapomenu přístupové heslo?**
Agent Azure Backup vyžaduje přístupové heslo (které jste zadali při registraci) k dešifrování zálohovaných dat během obnovení. Projděte si níže uvedené scénáře, abyste pochopili možnosti zpracování ztracené přístupové fráze:

| Originální stroj <br> *(zdrojový počítač, kde byly zálohy pořízeny)* | Heslo | Dostupné možnosti |
| --- | --- | --- |
| K dispozici. |Ztracené |Pokud je původní počítač (kde byly zálohy pořízeny) k dispozici a stále registrován ve stejném trezoru služby Recovery Services, můžete heslo znovu vygenerovat pomocí [následujících kroků](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Ztracené |Ztracené |Není možné obnovit data nebo data není k dispozici |

Zvažte následující podmínky:

* Pokud odinstalujete a znovu zaregistrujete agenta na stejném původním počítači s
  * *Stejné přístupové heslo*, pak budete moci obnovit zálohovaná data.
  * *Jiné přístupové heslo*, pak nebudete moci obnovit zálohovaná data.
* Pokud nainstalujete agenta do *jiného počítače* s
  * *Stejné přístupové heslo* (použité v původním počítači), pak budete moci obnovit zálohovaná data.
  * *Jiné přístupové heslo*, nebudete moci obnovit zálohovaná data.
* Pokud je původní počítač poškozen (brání vám v regeneraci přístupového hesla prostřednictvím konzoly MARS), ale můžete obnovit nebo získat přístup k původní odkládací složce používané agentem MARS, pak můžete být schopni obnovit (pokud jste zapomněli heslo). Další pomoc získáte od zákaznické podpory.

**Jak se obnovím, pokud ztratím původní počítač (kde byly zálohy odebrány)?**

Pokud máte stejné přístupové heslo (které jste zadali při registraci) původního počítače, můžete zálohovaná data obnovit do alternativního počítače. Projděte si níže uvedené scénáře, abyste pochopili možnosti obnovení.

| Originální stroj | Heslo | Dostupné možnosti |
| --- | --- | --- |
| Ztracené |K dispozici. |Agenta MARS můžete nainstalovat a zaregistrovat na jiném počítači se stejným přístupovým heslem, které jste zadali při registraci původního počítače. Zvolte **možnost** > obnovení**Jiné umístění** k provedení obnovení. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Ztracené |Ztracené |Není možné obnovit data nebo data není k dispozici |

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je trvalá úloha obnovení zrušena, proces obnovení se zastaví. Všechny soubory obnovené před zrušením zůstávají v nakonfigurovaném cíli (původní nebo alternativní umístění), bez vrácení zpět.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Zálohovat a obnovovat seznamy AC nastavené na soubory, složky a svazky agenta MARS?

* Agent MARS zálohovat seznamy AC nastavené na soubory, složky a svazky
* V případě obnovení svazku poskytuje agent MARS možnost přeskočit obnovení oprávnění seznamu ACL k obnovenému souboru nebo složce.
* Pro možnost obnovení jednotlivých souborů a složek se agent MARS obnoví s oprávněními Seznamu řízení (neexistuje žádná možnost přeskočit obnovení seznamu ACL).

## <a name="next-steps"></a>Další kroky

[Přečtěte si,](tutorial-backup-windows-server-to-azure.md) jak zálohovat počítač se systémem Windows.
