---
title: Odstraňování potíží Azure Backup Serveru
description: Poradce při potížích s instalací, registrací serveru Azure Backup A zálohováním a obnovením úloh aplikací.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: cc0cf7e91c1aacbc637d33ab1e5546cc54836b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673117"
---
# <a name="troubleshoot-azure-backup-server"></a>Odstraňování potíží Azure Backup Serveru

Informace v následujících tabulkách slouží k řešení chyb, ke kterým dochází při používání serveru Azure Backup Server.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Doporučujeme provést níže uvedené ověření, než začnete řešit potíže se serverem Microsoft Azure Backup Server (MABS):

- [Zajistěte, aby byl agent služby Microsoft Azure Recovery Services (MARS) aktuální](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ujistěte se, že existuje síťové propojení mezi agentem MARS a Azure.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby restartujte a opakujte operaci
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Pokud registrace selhává, ujistěte se, že server, na který se pokoušíte nainstalovat Server Zálohování Azure, ještě není zaregistrován v jiném úložišti.
- Pokud nabízená instalace selže, zkontrolujte, jestli už agent DPM neexistuje. Pokud agent existuje, odinstalujte ho a pak instalaci opakujte.
- [Zajištění, aby službě Azure Backup nepřekáží jiný proces nebo antivirový software](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Ujistěte se, že je na serveru MAB spuštěná služba agenta SQL a že je nastavená na automatický režim.<br>

## <a name="invalid-vault-credentials-provided"></a>Byla poskytnuta neplatná pověření trezoru.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Registrace do trezoru | Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozen nebo nemá nejnovější pověření přidružená ke službě pro obnovení. | Doporučená akce: <br> <ul><li> Stáhněte si nejnovější soubor pověření z úložiště a akci opakujte. <br>(NEBO)</li> <li> Pokud předchozí akce nefungovala, zkuste přihlašovací údaje stáhnout do jiného místního adresáře nebo vytvořit nový trezor. <br>(NEBO)</li> <li> Zkuste aktualizovat nastavení data a času, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided). <br>(NEBO)</li> <li> Zkontrolujte, zda c:\windows\temp má více než 65000 souborů. Přesuňte zastaralé soubory do jiného umístění nebo odstraňte položky ve složce Temp. <br>(NEBO)</li> <li> Zkontrolujte stav certifikátů. <br> a. Otevřete **spravovat certifikáty počítače** (v Ovládacích panelech). <br> b. Rozbalte **osobní** uzel a jeho podřízené **certifikáty**uzlu .<br> c.  Odeberte certifikát **nástroje Windows Azure**Tools . <br> d. Opakujte registraci v klientovi Azure Backup. <br> (NEBO) </li> <li> Zkontrolujte, zda jsou zavedeny nějaké zásady skupiny. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika je nekonzistentní

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Zálohování | Replika je nekonzistentní | Ověřte, zda je zapnuta možnost automatické kontroly konzistence v Průvodci skupinou ochrany. Další informace o příčinách nekonzistence replik y a relevantní návrhy naleznete v článku [Replika je nekonzistentní](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10)).<br> <ol><li> V případě zálohování stavu systému nebo bmr ověřte, zda je na chráněném serveru nainstalovánprogram Zálohování serveru.</li><li> Zkontrolujte problémy související s prostorem ve fondu úložiště DPM na zálohovacím serveru DPM/Microsoft Azure a přidělte úložiště podle potřeby.</li><li> Zkontrolujte stav služby Stínová kopie svazku na chráněném serveru. Pokud je v zakázaném stavu, nastavte jej spustit ručně. Spusťte službu na serveru. Potom se vraťte ke konzoli DPM/Microsoft Azure Backup Server a spusťte synchronizaci s úlohou kontroly konzistence.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Vytvoření bodu obnovení online se nezdařilo

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Zálohování | Vytvoření bodu obnovení online se nezdařilo | **Chybová zpráva**: Agent zálohování Windows Azure nemohl vytvořit snímek vybraného svazku. <br> **Řešení:** Zkuste zvětšit místo v repliky a svazku bodu obnovení.<br> <br> **Chybová zpráva:** Agent zálohování Windows Azure se nemůže připojit ke službě OBEngine. <br> **Řešení**: ověřte, zda modul OBEngine existuje v seznamu spuštěných služeb v počítači. Pokud služba OBEngine není spuštěna, použijte příkaz "net start OBEngine" ke spuštění služby OBEngine. <br> <br> **Chybová zpráva**: Šifrovací heslo pro tento server není nastaveno. Nakonfigurujte heslo pro šifrování. <br> **Řešení:** Zkuste nakonfigurovat heslo pro šifrování. Pokud se nezdaří, postupujte takto: <br> <ol><li>Ověřte, zda je odkládací místo. Toto je umístění, které je uvedeno v registru **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, s názvem **ScratchLocation** by měl existovat.</li><li> Pokud odkládací umístění existuje, zkuste znovu zaregistrovat pomocí staré přístupové fráze. *Kdykoli nakonfigurujete šifrovací přístupové heslo, uložte je na bezpečném místě.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Původní a externí servery DPM musí být zaregistrovány do stejného trezoru.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Obnovení | **Kód chyby:** CbPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Chybová zpráva**: Původní a externí servery DPM musí být zaregistrovány do stejného trezoru. | **Příčina**: K tomuto problému dochází, když se pokoušíte obnovit soubory na alternativní server z původního serveru pomocí externího obnovení DPM možnost a pokud server, který je obnovován a původní server, ze kterého jsou data zálohována nejsou přidruženy ke stejnému úložiště služby obnovení.<br/> <br/>**Řešení** Chcete-li tento problém vyřešit, ujistěte se, že původní i alternativní server je registrován do stejného trezoru.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Úlohy vytváření bodů obnovení online pro virtuální vm v systému VMware se nezdaří

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Zálohování | Úlohy vytváření bodů obnovení online pro virtuální vm v systému VMware se nezdaří. Aplikace DPM zjistila chybu ze společnosti VMware při pokusu o získání informací changetracking. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Resetujte CTK na VMware pro ovlivněné virtuální počítač.</li> <li>Zkontrolujte, zda není ve společnosti VMware konto nezávislý disk.</li> <li>Zastavte ochranu postižených virtuálních počítače a znovu chraňte tlačítkem **Aktualizovat.** </li><li>Spusťte CC pro ovlivněné virtuální chod.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operace agenta se nezdařila z důvodu chyby komunikace se službou koordinátora agenta DPM na serveru.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Vytlačování agentů na chráněné servery | Operace agenta se nezdařila z důvodu chyby \<komunikace se službou Koordinátor agenta DPM na> ServerName. | **Pokud doporučená akce uvedená v produktu nefunguje, proveďte následující kroky**: <ul><li> Pokud připojujete počítač z nedůvěryhodné domény, postupujte [takto](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> (NEBO) </li><li> Pokud připojujete počítač z důvěryhodné domény, odstraňte potíže pomocí kroků popsaných v [tomto blogu](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(NEBO)</li><li> Zkuste zakázat antivirový program jako krok řešení potíží. Pokud se problém vyřeší, upravte nastavení antivirového programu, jak je navrženo v [tomto článku](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalační program nemohl aktualizovat metadata registru

| Operace | Podrobnosti o chybě | Alternativní řešení |
|-----------|---------------|------------|
|Instalace | Instalační program nemohl aktualizovat metadata registru. Tato chyba aktualizace může vést k nadměrnému využití spotřeby úložiště. Chcete-li se této aktualizaci vyhnout, položka registru oříznutí refs. | Upravte klíč registru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Nastavte hodnotu Dword na 1. |
|Instalace | Instalační program nemohl aktualizovat metadata registru. Tato chyba aktualizace může vést k nadměrnému využití spotřeby úložiště. Chcete-li tomu zabránit, aktualizujte položku registru Programu SnapOptimization svazku. | Vytvořte klíč registru **SOFTWARE\Správce ochrany dat společnosti Microsoft\Configuration\VolSnapOptimization\WriteIds** s prázdnou hodnotou řetězce. |

## <a name="registration-and-agent-related-issues"></a>Registrace a problémy související s agentem

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Vytlačování agentů na chráněné servery | Pověření zadaná pro server jsou neplatná. | **Pokud doporučená akce zobrazená v produktu nefunguje, postupujte takto**: <br> Pokuste se nainstalovat agenta ochrany ručně na produkční server, jak je uvedeno v [tomto článku](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| Agent zálohy Azure se nemohl připojit ke službě Azure Backup (ID: 100050) | Agent zálohování Azure se nemohl připojit ke službě Azure Backup. | **Pokud doporučená akce zobrazená v produktu nefunguje, postupujte takto**: <br>1. Spusťte následující příkaz ze zvýšené výzvy: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Otevře se okno aplikace Internet Explorer. <br/> 2. Přejděte na **nástroje** > **Možnosti Internetu Připojení** > **LAN** > **nastavení**. <br/> 3. Změňte nastavení pro použití proxy serveru. Poté zadejte podrobnosti o serveru proxy.<br/> 4. Pokud má váš počítač omezený přístup k internetu, ujistěte se, že nastavení brány firewall na počítači nebo proxy umožňuje tyto [adresy URL](install-mars-agent.md#verify-internet-access) a [IP adresu](install-mars-agent.md#verify-internet-access).|
| Instalace agenta zálohování Azure se nezdařila | Instalace služby Microsoft Azure Recovery Services se nezdařila. Všechny změny, které byly provedeny v systému instalací služby Microsoft Azure Recovery Services byly vráceny zpět. (ID: 4024) | Ručně nainstalujte Azure Agent.

## <a name="configuring-protection-group"></a>Konfigurace skupiny ochrany

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Konfigurace skupin ochrany | Aplikace DPM nemohla vytvořit výčet součásti aplikace v chráněném počítači (chráněný název počítače). | Na obrazovce konfigurace skupiny ochrany vyberte **možnost Aktualizovat** na obrazovce ui skupiny ochrany na příslušné úrovni zdroje dat nebo komponenty. |
| Konfigurace skupin ochrany | Ochranu nejde nakonfigurovat. | Pokud je chráněný server server SQL, ověřte, zda byla oprávnění role sysadmin poskytnuta systémovému účtu (NTAuthority\System) v chráněném počítači, jak je popsáno v [tomto článku](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Konfigurace skupin ochrany | Pro tuto skupinu ochrany není ve fondu úložiště dostatek volného místa. | Disky, které jsou přidány do fondu úložiště [by neměl obsahovat oddíl](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Odstraňte všechny existující svazky na discích. Pak je přidejte do fondu úložiště.|
| Změna zásad |Zásady zálohování nelze změnit. Chyba: Aktuální operace se nezdařila z důvodu vnitřní chyby služby [0x29834]. Opakujte operaci po uplynutí určité doby. Pokud potíže trvají, obraťte se na podporu Microsoftu. | **Způsobit:**<br/>K této chybě dochází za tří podmínek: pokud jsou povolena nastavení zabezpečení, při pokusu o snížení rozsahu uchování pod minimální hodnoty zadané dříve a pokud jste na nepodporovanou verzi. (Nepodporované verze jsou verze pod Microsoft Azure Backup Server verze 2.0.9052 a Azure Backup Server update 1.) <br/>**Doporučená akce:**<br/> Chcete-li pokračovat v aktualizacích souvisejících se zásadami, nastavte dobu uchování nad minimální dobu uchovávání zadanou. (Minimální retenční doba je sedm dní pro denní, čtyři týdny pro týdenní, tři týdny pro měsíční nebo jeden rok pro rok.) <br><br>Volitelně je dalším upřednostňovaným přístupem aktualizace agenta zálohování a serveru Azure Backup Server, aby byly využity všechny aktualizace zabezpečení. |

## <a name="backup"></a>Zálohování

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Zálohování | Při spuštění úlohy došlo k neočekávané chybě. Zařízení není připraveno. | **Pokud doporučená akce zobrazená v produktu nefunguje, postupujte takto:** <br> <ul><li>Nastavte prostor úložiště stínové kopie na neomezené položky ve skupině ochrany a spusťte kontrolu konzistence.<br></li> (NEBO) <li>Zkuste snímat existující skupinu ochrany a vytvořit více nových skupin. Každá nová skupina ochrany by měla mít v sobě samostatnou položku.</li></ul> |
| Zálohování | Pokud zálohujete pouze stav systému, ověřte, zda je v chráněném počítači dostatek volného místa pro uložení zálohy stavu systému. | <ol><li>Ověřte, zda je v chráněném počítači nainstalována aplikace Zálohování serveru.</li><li>Ověřte, zda je v chráněném počítači dostatek místa pro stav systému. Nejjednodušší způsob, jak to ověřit, je přejít do chráněného počítače, otevřít program Zálohování serveru, proklikat výběry a vybrat bmr. UI pak řekne, kolik místa je potřeba. Otevřete > plán**místního** > **zálohování** **WSB** > **Vyberte možnost Konfigurace konfigurace** > **full serveru** (zobrazí se velikost). Tuto velikost použijte pro ověření.</li></ol>
| Zálohování | Selhání zálohování pro BMR | Pokud je velikost BMR velká, přesuňte některé soubory aplikace na jednotku operačního systému a opakujte akci. |
| Zálohování | Možnost znovu chránit virtuální počítač VMware na novém microsoft azure zálohovacím serveru se nezobrazuje jako dostupná k přidání. | Vlastnosti vmware jsou zaměřeny na starou, vyřazenou instanci microsoft azure backup serveru. Řešení tohoto problému:<br><ol><li>V programu VCenter (ekvivalent SC-VMM) přejděte na kartu **Souhrn** a potom na **vlastní atributy**.</li>  <li>Odstraňte starý název serveru Microsoft Azure Backup Server z hodnoty **DPMServer.**</li>  <li>Vraťte se na nový microsoft azure backup server a upravte PG.  Po zaškrtnutí tlačítka **Aktualizovat** se virtuální ho dohledu zobrazí se zaškrtávacím políčkem, které je k dispozici pro přidání do ochrany.</li></ol> |
| Zálohování | Při přístupu k souborům/sdíleným složkám došlo k chybě | Zkuste upravit nastavení antivirového programu, jak je navrženo v tomto článku [Spusťte antivirový software na serveru DPM](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).|

## <a name="change-passphrase"></a>Změna přístupové fráze

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Změna přístupové fráze |Zadaný kód PIN zabezpečení je nesprávný. Zadejte správný bezpečnostní kód PIN k dokončení této operace. |**Způsobit:**<br/> K této chybě dochází, když zadáte neplatný bezpečnostní kód PIN nebo kód PIN s ukončenou platností při provádění kritické operace (například změny přístupového hesla). <br/>**Doporučená akce:**<br/> Chcete-li operaci dokončit, musíte zadat platný bezpečnostní kód PIN. Pokud chcete získat PIN kód, přihlaste se na portál Azure a přejděte do trezoru služby Recovery Services. Potom přejděte na **nastavení** > **vlastnosti** > **generovat kód PIN zabezpečení**. Pomocí tohoto KÓDU PIN můžete změnit přístupové heslo. |
| Změna přístupové fráze |Operace se nezdařila. ID: 120002 |**Způsobit:**<br/>K této chybě dochází, pokud jsou povolena nastavení zabezpečení nebo když se pokusíte změnit přístupové heslo, když používáte nepodporovanou verzi.<br/>**Doporučená akce:**<br/> Chcete-li změnit přístupové heslo, musíte nejprve aktualizovat agenta zálohování na minimální verzi, která je 2.0.9052. Je také potřeba aktualizovat Azure Backup Server na minimum aktualizace 1 a pak zadat platný pin zabezpečení. Pokud chcete získat PIN kód, přihlaste se na portál Azure portal a přejděte do trezoru služby Recovery Services. Potom přejděte na **nastavení** > **vlastnosti** > **generovat kód PIN zabezpečení**. Pomocí tohoto KÓDU PIN můžete změnit přístupové heslo. |

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Nastavení e-mailových oznámení pomocí účtu Office 365 |ID chyby: 2013| **Způsobit:**<br> Pokus o použití účtu Office 365 <br>**Doporučená akce:**<ol><li> První věc, kterou je třeba zajistit, je, že "Povolit anonymní přenos na přijímacím konektoru" pro server DPM je nastavena na serveru Exchange. Další informace o konfiguraci naleznete v tématu [Povolit anonymní přenos na přijímacím konektoru](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Pokud nemůžete použít interní přenos SMTP a potřebujete ho nastavit pomocí serveru Office 365, můžete službu IIS nastavit jako relé. Nakonfigurujte server DPM tak, aby [předával protokol SMTP na O365 pomocí služby IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Nezapomeňte použít formát\@uživatele domain.com a *nikoli* doménu\uživatel.<br><br><li>Bod DPM použít název místního serveru jako server SMTP, port 587. Pak najet na e-mail uživatele, že e-maily by měly pocházet z.<li> Uživatelské jméno a heslo na stránce nastavení DPM SMTP by mělo být pro účet domény v doméně, ve které se aplikace DPM nachází. </li><br> Při změně adresy serveru SMTP proveďte změnu nového nastavení, zavřete pole nastavení a znovu jej otevřete, abyste se ujistili, že odráží novou hodnotu.  Jednoduše změna a testování nemusí vždy způsobit, že se nové nastavení projeví, takže testování tímto způsobem je osvědčeným postupem.<br><br>Kdykoli během tohoto procesu můžete tato nastavení vymazat zavřením konzoly DPM a úpravou následujících klíčů registru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword a SMTPUserName .** Můžete je přidat zpět do ui, když jej znovu spustíte.

## <a name="common-issues"></a>Běžné problémy

Tato část popisuje běžné chyby, se kterými se můžete setkat při používání Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Chybová zpráva | Doporučená akce |
-- | --
Zálohování selhalo kvůli neplatné nebo chybějící replice zálohy disku. | Chcete-li tento problém vyřešit, ověřte níže uvedené kroky a opakujte operaci: <br/> 1. Vytvoření bodu obnovení disku<br/> 2. Spustit kontrolu konzistence na zdroji dat <br/> 3. Zastavte ochranu zdroje dat a poté překonfigurujte ochranu tohoto zdroje dat

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Chybová zpráva | Doporučená akce |
-- | --
Snímek zdrojového svazku se nezdařil, protože metadata v replice jsou neplatná. | Vytvoření bodu obnovení disku tohoto zdroje dat a opakování zálohování online znovu

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>Chyba CBPSourceSnapshotFailedReplicaInconsistent

Chybová zpráva | Doporučená akce |
-- | --
Snímek zdrojového svazku se nezdařil z důvodu nekonzistentní repliky zdroje dat. | Spuštění kontroly konzistence tohoto zdroje dat a akci opakujte.

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Chybová zpráva | Doporučená akce |
-- | --
Zálohování selhalo, protože se nepodařilo naklonovat repliku zálohy disku.| Ujistěte se, že jsou všechny předchozí soubory replik y zálohování disku (.vhdx) odpojeny a během zálohování online neprobíhá žádné zálohování disku a že neprobíhá žádné zálohování disku.
