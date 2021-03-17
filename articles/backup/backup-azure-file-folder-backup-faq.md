---
title: Agent Microsoft Azure Recovery Services (MARS) – nejčastější dotazy
description: Řeší běžné otázky týkající se zálohování souborů a složek pomocí Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 1edfaed99e60409774496c5ae75df8be99a8fe1f
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917354"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>Nejčastější dotazy – agent Microsoft Azure Recovery Services (MARS)

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování dat pomocí agenta Microsoft Azure Recovery Services (MARS) ve službě [Azure Backup](backup-overview.md) .

## <a name="configure-backups"></a>Konfigurace zálohování

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Kde můžu stáhnout nejnovější verzi agenta MARS?

Nejnovější agent MARS, který se používá při zálohování počítačů s Windows serverem, aplikace System Center DPM a serveru Microsoft Azure Backup, je k dispozici ke [stažení](https://aka.ms/azurebackup_agent).

### <a name="where-can-i-download-the-vault-credentials-file"></a>Kde můžu stáhnout soubor s přihlašovacími údaji trezoru?

V Azure Portal přejděte na **vlastnosti** vašeho trezoru. V části **přihlašovací údaje pro zálohování** zaškrtněte políčko pro **použití nejnovějšího agenta Recovery Services**. Vyberte **Stáhnout**.

![Stáhnout přihlašovací údaje](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>Jak dlouho jsou přihlašovací údaje trezoru platné?

Platnost přihlašovacích údajů trezoru vyprší po 10 dnech. Pokud platnost souboru přihlašovacích údajů vyprší, Stáhněte soubor znovu z Azure Portal.

### <a name="what-characters-are-allowed-for-the-passphrase"></a>Jaké znaky jsou pro přístupové heslo povoleny?

Přístupové heslo by mělo používat znaky ze znakové sady ASCII, přičemž [hodnoty ASCII jsou menší nebo rovny 127](/office/vba/language/reference/user-interface-help/character-set-0127).

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

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Potřebuji pro instalaci a konfiguraci agenta MARS oprávnění správce?

Ano, instalace agenta MARS a konfigurace záloh pomocí konzoly MARS vyžaduje, aby uživatel byl místním správcem na chráněném serveru.

### <a name="what-is-the-impact-on-mars-agent-backups-of-transferring-the-vault-subscription-to-a-different-azure-ad-directory"></a>Jaký má dopad na zálohy agenta MARS na převod předplatného trezoru do jiného adresáře služby Azure AD?

Změna adresáře služby Azure AD nebude mít žádný vliv na zálohy agenta MARS. 

## <a name="manage-backups"></a>Správa záloh

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Co se stane, když přejmenujem počítač se systémem Windows nakonfigurovaný pro zálohování?

Při přejmenování počítače se systémem Windows se zastaví všechna aktuálně nakonfigurovaná zálohování.

* Nový název počítače je potřeba zaregistrovat v úložišti záloh.
* Když zaregistrujete nový název trezoru, první operace je *Úplná* záloha.
* Pokud potřebujete obnovit data zálohovaná do trezoru se starým názvem serveru, použijte možnost obnovení do alternativního umístění v Průvodci obnovením dat. [Přečtěte si další informace](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Jaká je maximální délka cesty k souboru pro zálohování?

Agent MARS spoléhá na systém souborů NTFS a použije specifikaci délky FilePath, která je omezená [rozhraním API systému Windows](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Pokud soubory, které chcete chránit, jsou delší než povolená hodnota, zálohujte nadřazenou složku nebo diskovou jednotku.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Jaké znaky jsou povoleny v cestách k souborům?

Agent MARS spoléhá na NTFS a v názvech souborů nebo cestách umožňuje [podporované znaky](/windows/win32/FileIO/naming-a-file#naming-conventions) .

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Zobrazí se upozornění "zálohy Azure nebyly pro tento server nakonfigurovány".

Toto upozornění se může objevit i v případě, že jste nakonfigurovali zásady zálohování, protože nastavení plánu zálohování uložené na místním serveru se neshoduje s nastavením uloženým v trezoru záloh.

* Když se server nebo nastavení obnovily do známého funkčního stavu, plány zálohování se můžou stát nesynchronizovanými.
* Pokud se zobrazí toto upozornění, [nakonfigurujte](backup-azure-manage-windows-server.md) znovu zásady zálohování a pak spusťte zálohování na vyžádání, aby se místní server znovu synchronizoval s Azure.

## <a name="manage-the-backup-cache-folder"></a>Správa složky mezipaměti pro zálohování

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Jaký je požadavek na minimální velikost složky mezipaměti?

Velikost složky mezipaměti určuje množství zálohovaných dat.

* Svazky složek mezipaměti by měly mít volné místo, které se rovná minimálně 5-10% celkové velikosti zálohovaných dat.
* Pokud svazek obsahuje méně než 5% volného místa, zvětšete velikost svazku nebo přesuňte složku mezipaměti na svazek s dostatkem místa pomocí následujících [kroků](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Pokud zálohujete stav systému Windows, budete potřebovat dalších 30-35 GB volného místa ve svazku, který obsahuje složku mezipaměti.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Jak zjistit, jestli je pomocná složka platná a dostupná?

1. Ve výchozím nastavení se složka pro pomocné složky nachází v `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Ujistěte se, že cesta k umístění pomocné složky odpovídá hodnotám klíčů registru, které jsou uvedené níže:

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění složky mezipaměti* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění složky mezipaměti* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Návody změnit umístění mezipaměti pro agenta MARS?

1. Spuštěním tohoto příkazu v příkazovém řádku se zvýšenými oprávněními zastavte modul zálohování:

    ```Net stop obengine```
2. Pokud jste nakonfigurovali zálohování stavu systému, otevřete správu disků a odpojte disky s názvy ve formátu `"CBSSBVol_<ID>"` .
3. Ve výchozím nastavení se složka pro pomocné složky nachází v `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Zkopírujte celou `\Scratch` složku na jinou jednotku, která má dostatek místa. Přesvědčte se, zda je obsah zkopírován, nikoli přesunut.
5. Aktualizujte následující položky registru s cestou nově přesunuté pomocné složky.

    | Cesta k registru | Klíč registru | Hodnota |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nové umístění pomocné složky* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nové umístění pomocné složky* |

6. Restartujte zálohovací stroj na příkazovém řádku se zvýšenými oprávněními:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Spuštění zálohování na vyžádání Po úspěšném dokončení zálohování pomocí nového umístění můžete odebrat původní složku mezipaměti.

### <a name="where-should-the-cache-folder-be-located"></a>Kde by měla být složka mezipaměti umístěna?

Pro složku mezipaměti nedoporučujeme používat následující umístění:

* Sdílená síťová složka nebo vyměnitelná média: Složka mezipaměti musí být místní pro server, který potřebuje zálohování pomocí online zálohování. Síťová umístění nebo vyměnitelná média jako jednotky USB nejsou podporována.
* Offline svazky: Složka mezipaměti musí být online pro očekávané zálohování pomocí agenta Azure Backup

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existují nějaké atributy složky mezipaměti, které nejsou podporované?

Pro složku mezipaměti nejsou podporovány následující atributy nebo jejich kombinace:

* Šifrované
* S odstraněním duplicit
* Komprimované
* Řídké
* Bod rozboru

Složka mezipaměti a virtuální pevný disk metadat nemají potřebné atributy pro agenta Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existuje způsob, jak upravit šířku pásma používanou pro zálohování?

Ano, pomocí možnosti **změnit vlastnosti** v agentovi Mars můžete upravit šířku pásma a časování. [Přečtěte si další informace](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Obnovení

### <a name="manage"></a>Spravovat

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>Můžu obnovit heslo?

Agent Azure Backup vyžaduje heslo (které jste zadali během registrace) k dešifrování zálohovaných dat během obnovování. Přečtěte si níže uvedené scénáře, abyste porozuměli vašim možnostem zpracování ztraceného hesla:

| Původní počítač <br> *(zdrojový počítač, ve kterém proběhlo zálohování)* | Hesel | Dostupné možnosti |
| --- | --- | --- |
| K dispozici |Ztráty |Pokud je váš původní počítač (kde bylo vygenerováno zálohování) dostupný a je stále zaregistrován ve stejném úložišti Recovery Services, můžete heslo znovu vygenerovat pomocí následujících [kroků](./backup-azure-manage-mars.md#re-generate-passphrase).  |
| Ztráty |Ztráty |Není možné obnovit data nebo data nejsou k dispozici. |

Vezměte v úvahu následující podmínky:

* Pokud agenta odinstalujete a znovu zaregistrujete na stejný původní počítač s
  * *Stejné heslo*, pak můžete zálohovaná data obnovit.
  * *Jiné heslo*, potom nemůžete obnovit zálohovaná data.
* Pokud nainstalujete agenta na *jiný počítač* s
  * *Stejné přístupové heslo* (používá se v původním počítači), můžete obnovit zálohovaná data.
  * *Jiné heslo*, nemůžete obnovit zálohovaná data.
* Pokud je původní počítač poškozený (bráníte opětovnému generování hesla přes konzolu MARS), ale můžete obnovit nebo získat přístup k původní pomocné složce používané agentem Mars, pak budete moct obnovit (Pokud jste zapomněli heslo). Pokud potřebujete další pomoc, obraťte se na zákaznickou podporu.

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>Návody obnovení po ztrátě původního počítače (kde se prováděly zálohy)?

Pokud máte stejné heslo (které jste zadali během registrace) původního počítače, pak můžete zálohovaná data obnovit na jiný počítač. Další informace o možnostech obnovení najdete v následujících scénářích.

| Původní počítač | Hesel | Dostupné možnosti |
| --- | --- | --- |
| Ztráty |K dispozici |Agenta MARS můžete nainstalovat a zaregistrovat na jiném počítači se stejným heslem, které jste zadali během registrace původního počítače. **Recovery Option**  >  Chcete-li provést obnovení, vyberte možnost obnovení **jiné umístění** . Další informace najdete v tomto [článku](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Ztráty |Ztráty |Není možné obnovit data nebo data nejsou k dispozici. |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>Úlohy zálohování se nezdařily nebo nejsou spuštěny po dlouhou dobu. Jsem po dobu uchování. Můžu I nadále obnovit?

V rámci bezpečnostního opatření Azure Backup zachová nejaktuálnější bod obnovení, i když je po dobu uchování mimo dobu uchování. Jakmile budou zálohy obnoveny a budou k dispozici nové body obnovení, bude starší bod obnovení odebrán podle zadaného uchovávání.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je probíhající úloha obnovení zrušena, proces obnovení se zastaví. Všechny soubory obnovené před zrušením zůstávají v nakonfigurovaném cíli (původní nebo alternativní umístění) bez vrácení zpět.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Zálohuje agent MARS a obnoví seznamy řízení přístupu (ACL) nastavené u souborů, složek a svazků?

* Agent MARS zálohuje seznamy řízení přístupu (ACL) nastavené na soubory, složky a svazky.
* U možnosti obnovení svazku pro obnovení svazku nabízí agent MARS možnost přeskočit obnovení oprávnění ACL k souboru nebo složce, která se obnovuje.
* V případě možnosti obnovení jednotlivých souborů a složek se agent MARS obnoví s oprávněním seznamu ACL (není k dispozici možnost přeskočit obnovení seznamu ACL).

## <a name="next-steps"></a>Další kroky

[Naučte](tutorial-backup-windows-server-to-azure.md) se zálohovat počítač s Windows.
