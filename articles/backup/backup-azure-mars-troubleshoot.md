---
title: Poradce při potížích s agentem azure backup
description: V tomto článku se dozvíte, jak řešit potíže s instalací a registrací agenta Azure Backup.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673104"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Poradce při potížích s agentem služby Microsoft Azure Recovery Services (MARS)

Tento článek popisuje, jak vyřešit chyby, které se mohou zobrazit při konfiguraci, registraci, zálohování a obnovení.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme, abyste před zahájením řešení potíží s agentem služby Microsoft Azure Recovery Services (MARS) zkontrolovali následující:

- [Ujistěte se, že je agent MARS aktuální](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Ujistěte se, že máte připojení k síti mezi agentem MARS a Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Ujistěte se, že je spuštěn mars (v konzole služby). Pokud potřebujete, restartujte a opakujte operaci.
- [Ujistěte se, že v umístění odkládací složky je k dispozici 5 až 10 % volného místa na svazku](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Zkontrolujte, jestli jiný proces nebo antivirový software narušuje Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Pokud se plánované zálohování nezdaří, ale ruční zálohování funguje, přečtěte si informace o tom, [že zálohování se nespustí podle plánu](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Ujistěte se, že váš operační systém má nejnovější aktualizace.
- [Zajistěte, aby nepodporované jednotky a soubory s nepodporovanými atributy byly vyloučeny ze zálohy](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Ujistěte se, že hodiny v chráněném systému jsou nakonfigurovány na správné časové pásmo.
- [Zkontrolujte, zda je na serveru nainstalována architektura .NET Framework 4.5.2 nebo novější](https://www.microsoft.com/download/details.aspx?id=30653).
- Pokud se pokoušíte znovu zaregistrovat server do trezoru:
  - Ujistěte se, že agent je odinstalován na serveru a že je odstraněn z portálu.
  - Použijte stejné přístupové heslo, které bylo původně použito k registraci serveru.
- U offline záloh se ujistěte, že je Azure PowerShell 3.7.0 před spuštěním zálohování nainstalovaný ve zdrojovém i kopírovacím počítači.
- Pokud agent backup běží na virtuálním počítači Azure, přečtěte [si tento článek](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Byla poskytnuta neplatná pověření trezoru.

**Chybová zpráva**: Byla poskytnuta neplatná pověření trezoru. Soubor je poškozený nebo k němu ve službě obnovení nejsou přiřazené nejnovější přihlašovací údaje. (ID: 34513)

| Příčina | Doporučené akce |
| ---     | ---    |
| **Pověření trezoru nejsou platná.** <br/> <br/> Soubory přihlašovacích údajů aplikace Vault jsou pravděpodobně poškozeny nebo jejichž platnost vypršela. (Například mohly být staženy více než 48 hodin před časem registrace.)| Stáhněte si nová pověření z trezoru služby Recovery Services na webu Azure Portal. (Viz krok 6 v části [Stáhnout agenta MARS.)](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Pak podle potřeby proveďte tyto kroky: <ul><li> Pokud jste již nainstalovali a zaregistrovali mars, otevřete konzolu MMC agenta zálohování Microsoft Azure a pak vpodokně **Akce** vyberte **Register Server** a dokončete registraci s novými přihlašovacími údaji. <br/> <li> Pokud se nová instalace nezdaří, zkuste přeinstalovat s novými pověřeními.</ul> **Poznámka:** Pokud bylo staženo více souborů přihlašovacích údajů úschovny, je následujících 48 hodin platný pouze nejnovější soubor. Doporučujeme stáhnout nový soubor přihlašovacích údajů trezoru.
| **Proxy server/brána firewall blokuje registraci** <br/>– nebo – <br/>**Žádné připojení k internetu** <br/><br/> Pokud má váš počítač nebo proxy server omezené připojení k internetu a nezajistíte přístup k potřebným adresám URL, registrace se nezdaří.| Postupujte takto:<br/> <ul><li> Spolupracujte se svým IT týmem a zajistěte, aby systém byl připojen k internetu.<li> Pokud nemáte proxy server, ujistěte se, že při registraci agenta není vybrána možnost proxy serveru. [Zkontrolujte nastavení proxy serveru](#verifying-proxy-settings-for-windows).<li> Pokud máte bránu firewall/proxy server, spolupracujte se svým síťovým týmem a zajistěte přístup k těmto adresám URL a adresám IP:<br/> <br> **URL – adresy**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP adresy**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Po dokončení předchozích kroků řešení potíží zkuste registraci znovu zaregistrovat.<br></br> Pokud jste připojení přes Azure ExpressRoute, ujistěte se, že nastavení jsou nakonfigurované, jak je popsáno v [podpoře Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Antivirový software blokuje registraci** | Pokud máte na serveru nainstalovaný antivirový software, přidejte do antivirové kontroly těchto souborů a složek potřebná pravidla vyloučení: <br/><ul> <li> CBengine.exe <li> Csc.exe<li> Stírací složky. Jeho výchozí umístění je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Složka přihrádky na adrese C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Další doporučení

- Přejděte na c:/Windows/Temp a zkontrolujte, zda existuje více než 60 000 nebo 65 000 souborů s příponou TMP. Pokud existují, odstraňte tyto soubory.
- Ujistěte se, že datum a čas zařízení odpovídají místnímu časovému pásmu.
- Ujistěte [se, že tyto weby](install-mars-agent.md#verify-internet-access) jsou přidány do důvěryhodných serverů v aplikaci Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Ověření nastavení serveru proxy pro systém Windows

1. Stáhněte si PsExec ze stránky [Sysinternals.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Spusťte `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` příkazový řádek se zvýšenými oprávněními.

   Tento příkaz otevře aplikaci Internet Explorer.
1. Přejděte na **možnosti nástroje** > **Internet** > **Nastavení** > **připojení k síti LAN**.
1. Zkontrolujte nastavení proxy serveru pro systémový účet.
1. Pokud není nakonfigurován žádný proxy server a jsou k dispozici podrobnosti o serveru proxy, odeberte podrobnosti.
1. Pokud je nakonfigurován proxy server a podrobnosti o serveru proxy jsou nesprávné, ujistěte se, **že ip proxy** a **port** podrobnosti jsou správné.
1. Zavřete Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nelze stáhnout soubor pověření úschovny.

| Chyba   | Doporučené akce |
| ---     | ---    |
|Stažení souboru pověření úschovny se nezdařilo. (ID: 403) | <ul><li> Zkuste stáhnout přihlašovací údaje trezoru pomocí jiného prohlížeče nebo provést následující kroky: <ul><li> Spusťte aplikaci Internet Explorer. Vyberte F12. </li><li> Přejděte na kartu **Síť** a vymažte mezipaměť a soubory cookie. </li> <li> Aktualizujte stránku.<br></li></ul> <li> Zkontrolujte, zda je předplatné zakázáno nebo vypršela jeho platnost.<br></li> <li> Zkontrolujte, zda stahování neblokuje nějaké pravidlo brány firewall. <br></li> <li> Ujistěte se, že jste nevyčerpali limit trezoru (50 strojů na trezor).<br></li>  <li> Ujistěte se, že uživatel má oprávnění azure zálohování, které jsou nutné ke stažení přihlašovací údaje trezoru a zaregistrovat server s trezoru. Informace o [správě bodů obnovení azure backup najdete v tématu Použití řízení přístupu na základě rolí](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent Microsoft Azure Recovery Services se nemohl připojit ke službě Microsoft Azure Backup

| Chyba  | Možná příčina | Doporučené akce |
| ---     | ---     | ---    |
| <br /><ul><li>Agent služby Obnovení Microsoft Azure se nemohl připojit k microsoft azure backup. (ID: 100050) Zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k internetu.<li>(407) Je vyžadováno ověření serveru proxy. |Připojení blokuje proxy server. |  <ul><li>V aplikaci Internet Explorer přejděte na**možnosti** >  **nástroje Internetu:** > **Security** > **Internet**. Vyberte **Vlastní úroveň** a přejděte dolů do oddílu **Stažení souboru.** Vyberte **Povolit**.<p>V aplikaci Internet Explorer může být také vhodné přidat adresy [URL a adresy IP](install-mars-agent.md#verify-internet-access) do důvěryhodných serverů.<li>Změňte nastavení tak, aby používal proxy server. Poté zadejte podrobnosti o serveru proxy.<li> Pokud má váš počítač omezený přístup k internetu, ujistěte se, že nastavení brány firewall v počítači nebo proxy serveru povoluje tyto [adresy URL a IP adresy](install-mars-agent.md#verify-internet-access). <li>Pokud máte na serveru nainstalovaný antivirový software, vylučte tyto soubory z antivirové kontroly: <ul><li>CBEngine.exe (místo dpmra.exe).<li>CSC.exe (související s rozhraním .NET Framework). Pro každou verzi rozhraní .NET Framework nainstalovanou na serveru existuje soubor CSC.exe. Vylučte soubory CSC.exe pro všechny verze rozhraní .NET Framework na postiženém serveru. <li>Odkládací složka nebo umístění mezipaměti. <br>Výchozí umístění pro odkládací složku nebo cestu ke mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Složka přihrádky na adrese C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepodařilo se nastavit šifrovací klíč pro bezpečné zálohy

| Chyba | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| <br />Nastavení šifrovacího klíče pro zabezpečené zálohy se nezdařilo. Aktivace nebyla zcela úspěšná, ale heslo pro šifrování bylo uloženo do následujícího souboru. |<li>Server je již zaregistrován v jiném trezoru.<li>Během konfigurace bylo přístupové heslo poškozeno.| Odregistrute server z úložiště a znovu ho zaregistrujte pomocí nové přístupové fráze.

## <a name="the-activation-did-not-complete-successfully"></a>Aktivace nebyla úspěšně dokončena.

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|<br />Aktivace nebyla úspěšně dokončena. Aktuální operace se nezdařila z důvodu vnitřní chyby služby [0x1FC07]. Po nějaké době zkuste operaci zopakovat. Pokud problém přetrvá, obraťte se na podporu Microsoftu.     | <li> Odkládací složka je umístěna na svazku, který nemá dostatek místa. <li> Odkládací složka byla nesprávně přesunuta. <li> Chybí soubor OnlineBackup.KEK.         | <li>Upgradujte na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte odkládací složku nebo umístění mezipaměti na svazek s volným místem, které se pohybuje mezi 5 % a 10 % celkové velikosti záložních dat. Chcete-li správně přesunout umístění mezipaměti, přečtěte si kroky [v části Běžné otázky týkající se zálohování souborů a složek](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Ujistěte se, že je přítomen soubor OnlineBackup.KEK. <br>*Výchozí umístění pro odkládací složku nebo cestu ke mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Šifrovací heslo není správně nakonfigurované

| Chyba  | Možné příčiny | Doporučené akce |
|---------|---------|---------|
| <br />Chyba 34506. Heslo pro šifrování uložené v tomto počítači není správně nakonfigurováno.    | <li> Odkládací složka je umístěna na svazku, který nemá dostatek místa. <li> Odkládací složka byla nesprávně přesunuta. <li> Chybí soubor OnlineBackup.KEK.        | <li>Upgradujte na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte odkládací složku nebo umístění mezipaměti na svazek s volným místem, které se pohybuje mezi 5 % a 10 % celkové velikosti záložních dat. Chcete-li správně přesunout umístění mezipaměti, přečtěte si kroky [v části Běžné otázky týkající se zálohování souborů a složek](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Ujistěte se, že je přítomen soubor OnlineBackup.KEK. <br>*Výchozí umístění pro odkládací složku nebo cestu ke mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Zálohy se nespouštějí podle plánu.

Pokud se naplánované zálohy nespustí automaticky, ale ruční zálohování funguje správně, vyzkoušejte následující akce:

- Ujistěte se, že plán zálohování systému Windows Server není v konfliktu s plánem zálohování souborů a složek Azure.

- Ujistěte se, že je stav zálohování online nastaven na **povolit**. Chcete-li ověřit stav, postupujte takto:

  1. V Plánovači úloh rozbalte **položku Microsoft** a vyberte **možnost Zálohování online**.
  1. Poklepejte na **položku Microsoft-OnlineBackup** a přejděte na kartu **Aktivační události.**
  1. Zkontrolujte, zda je stav nastaven na **povoleno**. Pokud tomu tak není, vyberte **Upravit**, vyberte **Povoleno**a pak vyberte **OK**.

- Ujistěte se, že uživatelský účet vybraný pro spuštění úlohy je **buď SYSTEM,** nebo **skupina místních správců** na serveru. Chcete-li ověřit uživatelský účet, přejděte na kartu **Obecné** a zkontrolujte možnosti **zabezpečení.**

- Ujistěte se, že je na serveru nainstalováno prostředí PowerShell 3.0 nebo novější. Chcete-li zkontrolovat verzi prostředí PowerShell, `Major` spusťte tento příkaz a ověřte, zda je číslo verze 3 nebo novější:

  `$PSVersionTable.PSVersion`

- Ujistěte se, že `PSMODULEPATH` tato cesta je součástí proměnné prostředí:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pokud je nastavena `LocalMachine` zásada `restricted`spuštění prostředí PowerShell pro , rutina prostředí PowerShell, která spustí úlohu zálohování, může selhat. Spusťte tyto příkazy ve zvýšeném režimu `Unrestricted` a `RemoteSigned`zkontrolujte a nastavte zásady spuštění na buď nebo :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Ujistěte se, že neexistují žádné chybějící nebo poškozené soubory modulu PowerShell MSOnlineBackup. Pokud se jedná o chybějící nebo poškozené soubory, postupujte takto:

  1. Z libovolného počítače, který má správně fungujícího agenta MARS, zkopírujte složku MSOnlineBackup z c:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. V problematickém počítači vložte zkopírované soubory do stejného umístění složky (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Pokud již v počítači existuje složka MSOnlineBackup, vložte do ní soubory nebo nahraďte všechny existující soubory.

> [!TIP]
> Chcete-li zajistit, aby byly změny použity konzistentně, restartujte server po provedení předchozích kroků.

## <a name="troubleshoot-restore-problems"></a>Poradce při potížích s obnovením

Azure Backup nemusí úspěšně připojit svazek pro obnovení, i po několika minutách. A během procesu se mohou zobrazit chybové zprávy. Chcete-li začít obnovení normálně, postupujte takto:

1. Zrušte proces připojení, pokud byl spuštěn několik minut.

2. Zkontrolujte, zda máte nejnovější verzi agenta backup. Chcete-li zkontrolovat verzi, vyberte v podokně **Akce** konzoly MARS **možnost O agentovi služby Microsoft Azure Recovery Services**. Zkontrolujte, zda je číslo **verze** stejné nebo vyšší než verze uvedená v [tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Výběrem tohoto odkazu [stáhněte nejnovější verzi](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Přejděte na**řadiče úložiště** **Správce** > zařízení a vyhledejte **iniciátor microsoft iSCSI**. Pokud ji najdete, přejděte přímo ke kroku 7.

4. Pokud nemůžete najít službu INicIÁTOR Microsoft iSCSI, pokuste se najít položku v části**Řadiče úložiště** **Správce** > zařízení s názvem **Neznámé zařízení** s id hardwaru **ROOT\ISCSIPRT**.

5. Klepněte pravým tlačítkem myši na **položku Neznámé zařízení** a vyberte možnost **Aktualizovat software ovladače**.

6. Aktualizujte ovladač výběrem možnosti **automatického hledání aktualizovaného softwaru ovladače**. Tato aktualizace by měla změnit **možnost Neznámé zařízení** na **iniciátor iSCSI společnosti Microsoft**:

    ![Snímek obrazovky se Správcem zálohovacích zařízení Azure se zvýrazněnými řadiči úložiště](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Přejděte na web **Task Manager** > **Services (Local)** > **Microsoft iniciátor služby ISCSI**:

    ![Snímek obrazovky správce úloh zálohování Azure se zvýrazněnou možností Služby (Místní)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Restartujte službu INicIátoru iSCSI společnosti Microsoft. Chcete-li to provést, klepněte pravým tlačítkem myši na službu a vyberte příkaz **Zastavit**. Potom na něj znovu klikněte pravým tlačítkem myši a vyberte **spustit**.

9. Opakujte obnovení pomocí [funkce Okamžité obnovení](backup-instant-restore-capability.md).

Pokud obnovení stále selže, restartujte server nebo klienta. Pokud nechcete restartovat nebo pokud obnovení stále selže i po restartování serveru, zkuste [se zotavit z jiného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Poradce při potížích s mezipamětí

Operace zálohování může selhat, pokud je složka mezipaměti (označovaná také jako odkládací složka) nesprávně nakonfigurována, chybí požadavky nebo má omezený přístup.

### <a name="prerequisites"></a>Požadavky

Pro operace agenta MARS úspěšné složky mezipaměti musí dodržovat níže uvedené požadavky:

- [Zajistěte, aby v umístění stísněné složky bylo k dispozici 5 až 10 % volného místa na svazku.](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Ujistěte se, že umístění odkládací složky je platné a přístupné](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Zajištění podpory atributů souborů ve složce mezipaměti](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Ujistěte se, že přidělený úložný prostor stínové kopie je dostatečný pro proces zálohování](#increase-shadow-copy-storage)
- [Ujistěte se, že neexistují žádné jiné procesy (např. antivirový software), které by omezovaly přístup ke složce mezipaměti](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zvětšit úložiště stínové kopie

Operace zálohování může selhat, pokud není dostatek místa pro stínové kopírování úložiště potřebné k ochraně zdroje dat. Chcete-li tento problém vyřešit, zvyšte úložný prostor stínové kopie na chráněném svazku pomocí vssadmin, jak je znázorněno níže:

- Zkontrolujte aktuální úložný prostor stínového prostoru z příkazového řádku se zvýšenými oprávněními:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zvětšete úložný prostor stínů pomocí následujícího příkazu:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Jiný proces nebo antivirový software blokující přístup ke složce mezipaměti

Pokud máte na serveru nainstalovaný antivirový software, přidejte do antivirové kontroly těchto souborů a složek potřebná pravidla vyloučení:  

- Stírací složky. Jeho výchozí umístění je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- Složka přihrádky na adrese C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- Csc.exe

## <a name="common-issues"></a>Běžné problémy

Tato část popisuje běžné chyby, které narazíte při použití agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Chybová zpráva | Doporučená akce |
-- | --
Agentovi Microsoft Azure Recovery Services se nepodařilo získat přístup ke kontrolnímu součtu zálohování uloženému na pomocném místě. | Chcete-li tento problém vyřešit, proveďte níže a restartujte server <br/> - [Zkontrolujte, zda neexistuje antivirový nebo jiný proces, který zamyká soubory odkládacího umístění](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Zkontrolujte, zda je odškrtávací místo platné a přístupné agentovi Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Chybová zpráva | Doporučená akce |
-- | --
Agentovi Microsoft Azure Recovery Services se nepodařilo získat přístup k pomocnému místu za účelem inicializace virtuálního pevného disku. | Chcete-li tento problém vyřešit, proveďte níže a restartujte server <br/> - [Zkontrolujte, zda neexistuje antivirový nebo jiný proces, který zamyká soubory odkládacího umístění](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Zkontrolujte, zda je odškrtávací místo platné a přístupné agentovi Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Chybová zpráva | Doporučená akce |
-- | --
Zálohování se nezdařilo z důvodu nedostatečného úložiště na svazku, kde je umístěna odkládací složka | Chcete-li tento problém vyřešit, ověřte níže uvedené kroky a opakujte operaci:<br/>- [Ujistěte se, že agent MARS je nejnovější](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Ověření a vyřešení problémů s úložištěm, které mají vliv na mezeru v poškrábání záloh](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Chybová zpráva | Doporučená akce |
-- | --
Nepodařilo se najít změny v souboru. Důvodů může být několik. Zkuste operaci zopakovat. | Chcete-li tento problém vyřešit, ověřte níže uvedené kroky a opakujte operaci:<br/> - [Ujistěte se, že agent MARS je nejnovější](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Ověření a vyřešení problémů s úložištěm, které mají vliv na mezeru v poškrábání záloh](#prerequisites)

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o [tom, jak zálohovat Windows Server pomocí agenta Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Pokud potřebujete obnovit zálohu, přečtěte si informace o [obnovení souborů do počítače se systémem Windows](backup-azure-restore-windows-server.md).
