---
title: Řešení potíží s agentem Azure Backup
description: V tomto článku se dozvíte, jak řešit potíže s instalací a registrací agenta Azure Backup.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518180"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Řešení potíží s agentem Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak vyřešit chyby, které se můžou zobrazit během konfigurace, registrace, zálohování a obnovení.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme, abyste před zahájením řešení potíží s agentem služby Azure Recovery Services (MARS) zkontrolovali následující:

- [Ujistěte se, že je agent Mars aktuální](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Ujistěte se, že máte síťové připojení mezi agentem Mars a Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Ujistěte se, že je Služba MARS spuštěná (v konzole služby). Pokud potřebujete, restartujte operaci a zkuste operaci zopakovat.
- [Zajistěte, aby v umístění pomocné složky bylo k dispozici 5 až 10% volného místa na disku](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-).
- [Zkontrolujte, jestli službě Azure Backup nepřekáží jiný proces nebo antivirový software](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Pokud se úloha zálohování dokončí s upozorněními, přečtěte si téma [úlohy zálohování dokončené s upozorněním](#backup-jobs-completed-with-warning) .
- Pokud se naplánované zálohování nepovede, ale ruční zálohování funguje, přečtěte si téma [zálohování neběží podle plánu](#backups-dont-run-according-to-schedule).
- Zajistěte, aby měl váš operační systém nejnovější aktualizace.
- [Zajistěte, aby nepodporované jednotky a soubory s nepodporovanými atributy byly ze zálohy vyloučené](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Zajistěte, aby byly hodiny v chráněném systému nakonfigurované na správné časové pásmo.
- [Zajistěte, aby byl na serveru nainstalován .NET Framework 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653).
- Pokud se pokoušíte znovu zaregistrovat server do trezoru:
  - Zajistěte, aby byl agent na serveru odinstalován a aby se odstranil z portálu.
  - Použijte stejné heslo, které se původně použilo k registraci serveru.
- V případě offline zálohování zajistěte, aby se na zdrojovém i kopírovacím počítači nainstalovala Azure PowerShell 3.7.0 před zahájením zálohování.
- Pokud Agent zálohování běží na virtuálním počítači Azure, přečtěte si [Tento článek](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Zadané neplatné přihlašovací údaje trezoru

**Chybová zpráva**: byly zadány neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo k němu ve službě obnovení nejsou přiřazené nejnovější přihlašovací údaje. (ID: 34513)

| Příčina | Doporučené akce |
| ---     | ---    |
| **Přihlašovací údaje trezoru nejsou platné.** <br/> <br/> Soubory s přihlašovacími údaji trezoru můžou být poškozené, možná vypršela platnost nebo mají jinou příponu souboru než *. vaultCredentials*. (Například je možné, že byly staženy více než 10 dní před registrací.)| [Stáhněte si nové přihlašovací údaje](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) z trezoru Recovery Services na Azure Portal. Pak podle potřeby proveďte tyto kroky: <ul><li> Pokud jste již nainstalovali a zaregistrovali MARS, otevřete konzolu konzoly MMC Microsoft Azure Backup agenta. Pak v podokně **Akce** vyberte **Registrovat Server** a dokončete registraci s novými přihlašovacími údaji. <br/> <li> Pokud se nová instalace nezdařila, zkuste ji znovu nainstalovat pomocí nových přihlašovacích údajů.</ul> **Poznámka**: Pokud se stáhlo více souborů přihlašovacích údajů úložiště, bude platný jenom poslední soubor v příštích 10 dnech. Doporučujeme, abyste si stáhli nový soubor s přihlašovacími údaji úložiště.
| **Proxy server/Brána firewall blokuje registraci** <br/>nebo <br/>**Bez připojení k Internetu** <br/><br/> Pokud má váš počítač nebo proxy server omezené připojení k Internetu a nezajistíte přístup k potřebným adresám URL, registrace se nezdaří.| Proveďte tyto kroky:<br/> <ul><li> Spolupracujte s vaším IT týmem a ujistěte se, že je systém připojen k Internetu.<li> Pokud nemáte proxy server, zajistěte, aby při registraci agenta nebyla vybraná možnost proxy. [Ověřte nastavení proxy serveru](#verifying-proxy-settings-for-windows).<li> Pokud máte bránu firewall nebo proxy server, pracujte s týmem sítě, abyste zajistili přístup k těmto adresám URL a IP adresám:<br/> <br> **Adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP adresy**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Zkuste provést registraci znovu po dokončení předchozích kroků pro řešení potíží.<br></br> Pokud je vaše připojení prostřednictvím Azure ExpressRoute, ujistěte se, že nastavení jsou nakonfigurovaná podle pokynů v tématu [Podpora Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Antivirový software blokuje registraci** | Pokud máte na serveru nainstalovaný antivirový software, přidejte potřebná pravidla vyloučení do kontroly antivirového programu pro tyto soubory a složky: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Pomocná složka Výchozí umístění je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Složka Bin v adresáři C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Další doporučení

- Přejít na adresu C:/Windows/Temp a ověřte, zda je k dispozici více než 60 000 nebo 65 000 souborů s příponou. tmp. Pokud existují, odstraňte tyto soubory.
- Zajistěte, aby datum a čas počítače odpovídaly místnímu časovému pásmu.
- Zajistěte, aby byly [tyto weby](install-mars-agent.md#verify-internet-access) přidány do vašich důvěryhodných webů v aplikaci Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Ověřují se nastavení proxy serveru pro Windows.

1. Stáhněte si PsExec ze stránky [Sysinternals](/sysinternals/downloads/psexec) .
1. Spusťte `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` z příkazového řádku se zvýšenými oprávněními.

   Tento příkaz spustí aplikaci Internet Explorer.
1. Přejít na **nástroje**  >  **Možnosti Internetu**  >  **připojení**  >  **nastavení sítě LAN**.
1. Ověřte nastavení proxy serveru pro účet System.
1. Pokud není nakonfigurovaný žádný proxy server a poskytnou se podrobnosti o proxy serveru, odeberte podrobnosti.
1. Pokud je nakonfigurován proxy server a podrobnosti o serveru proxy nejsou správné, ujistěte se, že podrobnosti o **IP adrese proxy** a **portech** jsou správné.
1. Zavřete Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nejde stáhnout soubor s přihlašovacími údaji trezoru.

| Chyba   | Doporučené akce |
| ---     | ---    |
|Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru. (ID: 403) | <ul><li> Zkuste stáhnout přihlašovací údaje trezoru pomocí jiného prohlížeče nebo proveďte tyto kroky: <ul><li> Spusťte aplikaci Internet Explorer. Vyberte F12. </li><li> Přejdete na kartu **síť** a vymažte mezipaměť a soubory cookie. </li> <li> Aktualizujte stránku.<br></li></ul> <li> Ověřte, jestli je předplatné zakázané nebo vypršela jeho platnost.<br></li> <li> Zkontroluje, jestli nějaké pravidlo brány firewall neblokuje stahování. <br></li> <li> Ujistěte se, že jste vyčerpali limit úložiště (50 počítačů na trezor).<br></li>  <li> Zajistěte, aby měl uživatel Azure Backup oprávnění, která jsou nutná ke stažení přihlašovacích údajů trezoru a registraci serveru v trezoru. Viz téma [použití řízení přístupu na základě role Azure ke správě Azure Backup bodů obnovení](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent Microsoft Azure Recovery Services se nemohl připojit ke službě Microsoft Azure Backup

| Chyba  | Možná příčina | Doporučené akce |
| ---     | ---     | ---    |
| <br /><ul><li>Agent Microsoft Azure Recovery Services se nemohl připojit k Microsoft Azure Backup. (ID: 100050) Zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k Internetu.<li>(407) je vyžadováno ověřování proxy serveru. |Proxy blokuje připojení. |  <ul><li>V Internet Exploreru přejdete na **nástroje**  >  **Internet Options Internet možnosti**  >  **zabezpečení**  >  **Internetu**. Vyberte **vlastní úroveň** a přejděte dolů k části **Stažení souboru** . Vyberte **Povolit**.<p>Je také možné, že budete muset přidat do důvěryhodných webů v aplikaci Internet Explorer [adresy URL a IP adresy](install-mars-agent.md#verify-internet-access) .<li>Změňte nastavení na použití proxy server. Pak zadejte podrobnosti o proxy server.<li> Pokud má počítač omezený přístup k Internetu, zajistěte, aby nastavení brány firewall na počítači nebo proxy umožňovalo tyto [adresy URL a IP adresy](install-mars-agent.md#verify-internet-access). <li>Pokud máte na serveru nainstalovaný antivirový software, vylučte tyto soubory z antivirového vyhledávání: <ul><li>CBEngine.exe (místo dpmra.exe).<li>CSC.exe (souvisí s .NET Framework). Pro každou verzi .NET Framework nainstalovanou na serveru existuje CSC.exe. Vylučte soubory CSC.exe pro všechny verze .NET Framework na příslušném serveru. <li>Umístění pomocné složky nebo mezipaměti. <br>Výchozí umístění pro pomocnou složku nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Složka Bin v adresáři C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Zadaný soubor s přihlašovacími údaji trezoru se nedá použít, protože není stažený z trezoru přidruženého k tomuto serveru.

| Chyba  | Možná příčina | Doporučené akce |
| ---     | ---     | ---    |
| Zadaný soubor s přihlašovacími údaji trezoru nejde použít, protože není stažený z trezoru přidruženého k tomuto serveru. (ID: 100110) Zadejte prosím příslušné přihlašovací údaje trezoru. | Soubor s přihlašovacími údaji trezoru pochází z jiného trezoru, než je ten, na který tento server je už zaregistrovaný. | Ujistěte se, že cílový počítač a zdrojový počítač jsou zaregistrované ve stejném trezoru Recovery Services. Pokud je cílový server již zaregistrován v jiném trezoru, zaregistrujte se do správného trezoru pomocí možnosti **Registrace serveru** .  

## <a name="backup-jobs-completed-with-warning"></a>Úlohy zálohování se dokončily s upozorněním.

- Když Agent MARS během zálohování prochází soubory a složkami, může dojít k různým podmínkám, které můžou způsobit, že se záloha označí jako dokončená s upozorněními. Během těchto podmínek se úloha zobrazí jako dokončená s upozorněními. To je dobré, ale to znamená, že nejméně jeden soubor se nepodařilo zálohovat. Takže úloha tento soubor přeskočila, ale zálohuje všechny ostatní soubory v daném zdroji dat.

  ![Úloha zálohování se dokončila s upozorněními.](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Mezi podmínky, které můžou způsobit, že zálohy mají přeskočit soubory:
  - Nepodporované atributy souboru (například: ve složce OneDrive, komprimovaný datový proud, spojovací body). Úplný seznam najdete v tématu [matice podpory](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Problém systému souborů
  - Jiný proces koliduje (například: obslužné rutiny antivirového softwaru souborů můžou zabránit agentovi MARS v přístupu k souborům)
  - Soubory uzamčené aplikací  

- Služba Backup označí tyto soubory jako neúspěšné v souboru protokolu s následujícími zásadami vytváření názvů: *LastBackupFailedFilesxxxx.txt* v adresáři *C:\Program Files\Microsoft Azure Recovery Service Agent\temp* .
- Chcete-li tento problém vyřešit, zkontrolujte soubor protokolu, abyste pochopili povahu problému:

  | Kód chyby             | Hlediska                                             | Doporučení                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Soubor nebo adresář jsou poškozené a nečitelný. | Na zdrojovém svazku spusťte **Nástroj Chkdsk** .                             |
  | 0x80070002, 0x80070003 | Systém nemůže najít zadaný soubor.         | [Ujistěte se, že pomocná složka není plná.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Ověřte, jestli svazek, na kterém je nakonfigurované pomocné místo, existuje (neodstraněno).  <br><br>   [Zajistěte, aby byl agent MARS vyloučený z antivirového programu nainstalovaného v počítači.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Přístup byl odepřen.                                    | [Zjistit, jestli antivirový software nebo jiný software třetí strany blokuje přístup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Přístup k souboru v cloudu byl odepřen.                | Soubory OneDrive, soubory Git nebo jakékoli jiné soubory, které můžou být ve stavu offline v počítači |

- Pomocí [Přidat pravidla vyloučení do existujících zásad](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) můžete vyloučit nepodporované, chybějící nebo odstraněné soubory ze zásad zálohování, abyste zajistili úspěšné zálohování.

- Vyhněte se odstranění a opětovnému vytvoření chráněných složek se stejnými názvy ve složce nejvyšší úrovně. To by mohlo vést k tomu, že se zálohování dokončí s upozorněními s chybou *, že byla zjištěna kritická nekonzistence, takže změny nelze replikovat.*  Pokud potřebujete složky odstranit a znovu vytvořit, zvažte tak jejich použití v podsložkách chráněné složky nejvyšší úrovně.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepodařilo se nastavit šifrovací klíč pro bezpečné zálohy

| Chyba | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| <br />Nepovedlo se nastavit šifrovací klíč pro zabezpečené zálohy. Aktivace nebyla úspěšně dokončena, ale šifrovací heslo bylo uloženo do následujícího souboru. |<li>Server je už zaregistrovaný v jiném trezoru.<li>Během konfigurace bylo heslo poškozeno.| Zrušte registraci serveru z trezoru a znovu ho Zaregistrujte pomocí nového hesla.

## <a name="the-activation-did-not-complete-successfully"></a>Aktivace nebyla úspěšně dokončena.

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|<br />Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Po nějaké době zkuste operaci zopakovat. Pokud problém přetrvá, obraťte se na podporu Microsoftu.     | <li> Pomocná složka se nachází na svazku, který nemá dost místa. <li> Pomocná složka byla nesprávně přesunuta. <li> Chybí soubor OnlineBackup. KEK.         | <li>Upgradujte na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta Mars.<li>Přesuňte pomocné složky nebo umístění mezipaměti do svazku s volným místem, které je mezi 5% a 10% celkové velikosti zálohovaných dat. Pro správné přesunutí umístění mezipaměti použijte postup v tématu [běžné otázky k zálohování souborů a složek](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder).<li> Zajistěte, aby byl k dispozici soubor OnlineBackup. KEK. <br>*Výchozí umístění pro pomocnou složku nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Šifrovací heslo není správně nakonfigurované

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
| <br />Chyba 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurováno.    | <li> Pomocná složka se nachází na svazku, který nemá dost místa. <li> Pomocná složka byla nesprávně přesunuta. <li> Chybí soubor OnlineBackup. KEK.        | <li>Upgradujte na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta Mars.<li>Přesuňte pomocné složky nebo umístění mezipaměti do svazku s volným místem, které je mezi 5% a 10% celkové velikosti zálohovaných dat. Pro správné přesunutí umístění mezipaměti použijte postup v tématu [běžné otázky k zálohování souborů a složek](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder).<li> Zajistěte, aby byl k dispozici soubor OnlineBackup. KEK. <br>*Výchozí umístění pro pomocnou složku nebo cestu k mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Zálohování se nespouští podle plánu

Pokud se naplánované zálohy nespouštějí automaticky, ale ruční zálohování funguje správně, zkuste tyto akce:

- Zajistěte, aby plán zálohování Windows serveru nebyl v konfliktu s plánem zálohování souborů a složek Azure.

- Zajistěte, aby byl stav online zálohování nastavený na **Povolit**. Chcete-li ověřit stav, proveďte následující kroky:

  1. V Plánovač úloh rozbalte položku **Microsoft** a vyberte možnost **online zálohování**.
  1. Dvakrát klikněte na **Microsoft-OnlineBackup** a přejděte na kartu **triggery** .
  1. Ověřte, jestli je stav nastavený na **povoleno**. Pokud není, vyberte **Upravit**, vyberte **povoleno** a pak vyberte **OK**.

- Ujistěte se, že uživatelský účet vybraný pro spuštění úlohy je skupina **System** nebo **Local Administrators** na serveru. Uživatelský účet ověříte tak, že přejdete na kartu **Obecné** a zkontrolujete možnosti **zabezpečení** .

- Ujistěte se, že je na serveru nainstalovaný PowerShell 3,0 nebo novější. Pokud chcete zkontrolovat verzi PowerShellu, spusťte tento příkaz a ověřte, jestli `Major` je číslo verze 3 nebo novější:

  `$PSVersionTable.PSVersion`

- Zajistěte, aby tato cesta byla součástí `PSMODULEPATH` proměnné prostředí:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pokud je zásada spouštění PowerShellu pro `LocalMachine` nastavená na `restricted` , může dojít k selhání rutiny PowerShellu, která spustí úlohu zálohování. Spusťte tyto příkazy v režimu zvýšené úrovně, abyste zkontrolovali a nastavili zásady spouštění na hodnotu `Unrestricted` nebo `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Zajistěte, aby nebyly k dispozici žádné nebo poškozené soubory MSOnlineBackup modulu PowerShellu. V případě chybějících nebo poškozených souborů proveďte tyto kroky:

  1. Z libovolného počítače, který má agenta MARS, který funguje správně, zkopírujte složku MSOnlineBackup ze složky C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na problematickém počítači vložte zkopírované soubory do stejného umístění složky (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Pokud je na počítači již složka MSOnlineBackup, vložte do ní soubory nebo nahraďte všechny existující soubory.

> [!TIP]
> Chcete-li zajistit, aby se změny používaly konzistentně, restartujte server po provedení předchozích kroků.

## <a name="resource-not-provisioned-in-service-stamp"></a>Prostředek není zřízený na razítku služby.

Chyba | Možné příčiny | Doporučené akce
--- | --- | ---
Aktuální operace selhala kvůli vnitřní chybě služby (prostředek není zřízený na razítku služby). Zkuste prosím tuto operaci za chvíli znovu. (ID: 230006) | Chráněný Server se přejmenoval. | <li> Přejmenujte Server zpátky na původní název, který je zaregistrovaný v trezoru. <br> <li> Znovu zaregistrujte Server do trezoru s novým názvem.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Úlohu se nepovedlo spustit, protože probíhala jiná úloha.

Pokud se v historii úloh **konzoly Mars** zobrazí zpráva s upozorněním  >  , že úloha nemohla být spuštěna, protože probíhala jiná úloha, může to být způsobeno duplicitní instancí úlohy aktivované Plánovač úloh.

![Úlohu se nepovedlo spustit, protože probíhala jiná úloha.](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Řešení tohoto problému:

1. Spusťte modul snap-in Plánovač úloh tak, že v okně Spustit zadáte *Taskschd. msc.*
1. V levém podokně přejděte do **knihovny Plánovač úloh**  ->  **Microsoft**  ->  **OnlineBackup**.
1. Pro každý úkol v této knihovně poklikejte na úkol a otevřete vlastnosti a proveďte následující kroky:
    1. Přepněte na kartu **Nastavení**.

         ![Karta nastavení](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **Pokud je úloha už spuštěná, použijte následující pravidlo**. Vyberte **nespustit novou instanci**.

         ![Změnit pravidlo na nespouštět novou instanci](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Řešení potíží s obnovením

Azure Backup nemusí úspěšně připojit svazek pro obnovení, a to ani po několika minutách. A v průběhu procesu můžete obdržet chybové zprávy. Chcete-li začít obnovovat normálně, proveďte tyto kroky:

1. Zrušte proces připojení, pokud je spuštěn několik minut.

2. Podívejte se, jestli máte nejnovější verzi agenta zálohování. Chcete-li zjistit verzi, vyberte v podokně **Akce** v konzole Mars možnost **o Microsoft Azure Recovery Services agenta**. Ověřte, že číslo **verze** je rovno nebo vyšší než verze uvedená v [tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Kliknutím na tento odkaz [Stáhněte nejnovější verzi](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Přejděte na **Správce zařízení**  >  **řadiče úložiště** a najděte **iniciátor iSCSI společnosti Microsoft**. Pokud ho najdete, přejděte přímo ke kroku 7.

4. Pokud nemůžete najít službu iniciátoru iSCSI společnosti Microsoft, zkuste najít položku v části **Správce zařízení**  >  **řadiče úložiště** s názvem **neznámé zařízení** s ID hardwaru **ROOT\ISCSIPRT**.

5. Klikněte pravým tlačítkem na **neznámé zařízení** a vyberte **aktualizovat software ovladače**.

6. Aktualizujte ovladač tak, že vyberete možnost pro  **Automatické hledání aktualizovaného softwaru ovladače**. Tato aktualizace by měla změnit **neznámé zařízení** **iniciátoru iSCSI společnosti Microsoft**:

    ![Snímek obrazovky Azure Backup Správce zařízení se zvýrazněnými řadiči úložiště](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Přejít na služby **Správce úloh**  >  **(místní)**  >  **Služba iniciátoru iSCSI společnosti Microsoft**:

    ![Snímek obrazovky Azure Backup Správce úloh se zvýrazněnými službami (místní)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Restartujte službu iniciátoru iSCSI společnosti Microsoft. Provedete to tak, že kliknete pravým tlačítkem na službu a vyberte **zastavit**. Pak znovu klikněte na něj pravým tlačítkem myši a vyberte **Spustit**.

9. Opakujte obnovení pomocí [okamžitého obnovení](backup-instant-restore-capability.md).

Pokud obnovení ještě neproběhne úspěšně, restartujte server nebo klienta. Pokud se nechcete restartovat, nebo pokud se obnovení stále nedaří i po restartování serveru, zkuste obnovit [z jiného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Řešení potíží s mezipamětí

Operace zálohování může selhat, pokud je složka mezipaměti (také označovaná jako pomocná složka) nesprávně nakonfigurovaná, chybějící předpoklady nebo má omezený přístup.

### <a name="prerequisites"></a>Požadavky

Aby operace agenta MARS uspěly ve složce mezipaměti, musí splňovat následující požadavky:

- [Zajistěte, aby v umístění pomocné složky bylo k dispozici 5 až 10% volného místa na disku.](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Zajistěte, aby bylo umístění pomocné složky platné a dostupné.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Zajistěte, aby byly podporovány atributy souborů ve složce mezipaměti.](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Zajistěte, aby byl přidělený úložný prostor stínové kopie pro proces zálohování dostatečný.](#increase-shadow-copy-storage)
- [Zajistěte, aby nedocházelo k žádným jiným procesům (např. antivirový software), který omezuje přístup ke složce mezipaměti.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zvýšení úložiště stínové kopie

Pokud není k dispozici dostatek úložného prostoru stínové kopie, který je nutný k ochraně zdroje dat, operace zálohování by mohly selhat. Chcete-li tento problém vyřešit, zvyšte velikost úložiště stínové kopie na chráněném svazku pomocí nástroje **vssadmin** , jak je znázorněno níže:

- Ověřte aktuální prostor stínového úložiště z příkazového řádku se zvýšenými oprávněními:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zvětšete prostor pro stín úložiště pomocí následujícího příkazu:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Jiný proces nebo antivirový software blokuje přístup ke složce mezipaměti.

Pokud máte na serveru nainstalovaný antivirový software, přidejte potřebná pravidla vyloučení do kontroly antivirového programu pro tyto soubory a složky:  

- Pomocná složka Výchozí umístění je `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Složka Bin v `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Běžné problémy

Tato část se zabývá běžnými chybami, ke kterým dojde při použití agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Chybová zpráva | Doporučená akce
--|--
Agentovi Microsoft Azure Recovery Services se nepodařilo získat přístup ke kontrolnímu součtu zálohování uloženému na pomocném místě. | Pokud chcete tento problém vyřešit, proveďte následující kroky a restartujte server. <br/> - [Ověřte, jestli existuje antivirový nebo jiný proces, který by uzamknul soubory s pomocným umístěním.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Zkontroluje, jestli je pomocné umístění platné a dostupné pro agenta MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Chybová zpráva | Doporučená akce
--|--
Agentovi Microsoft Azure Recovery Services se nepodařilo získat přístup k pomocnému místu za účelem inicializace virtuálního pevného disku. | Pokud chcete tento problém vyřešit, proveďte následující kroky a restartujte server. <br/> - [Zjistit, jestli antivirová nebo jiné procesy uzamyká soubory s pomocným umístěním](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Zkontroluje, jestli je pomocné umístění platné a dostupné pro agenta MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Chybová zpráva | Doporučená akce
--|--
Zálohování se nepovedlo kvůli nedostatku úložiště na svazku, kde se nachází pomocná složka. | Chcete-li tento problém vyřešit, ověřte následující kroky a opakujte operaci:<br/>- [Zajistěte, aby byl agent MARS nejnovější.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Ověření a řešení problémů s úložištěm, které mají vliv na pomocné místo zálohy](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Chybová zpráva | Doporučená akce
--|--
Nepodařilo se najít změny v souboru. Důvodů může být několik. Zkuste operaci zopakovat. | Chcete-li tento problém vyřešit, ověřte následující kroky a opakujte operaci:<br/> - [Zajistěte, aby byl agent MARS nejnovější.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Ověření a řešení problémů s úložištěm, které mají vliv na pomocné místo zálohy](#prerequisites)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další podrobnosti o [tom, jak zálohovat Windows Server pomocí agenta Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Pokud potřebujete obnovit zálohu, přečtěte si téma [obnovení souborů do počítače s Windows](backup-azure-restore-windows-server.md).
