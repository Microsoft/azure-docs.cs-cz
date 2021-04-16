---
title: Odstraňování potíží Azure Backup Serveru
description: Řešení potíží s instalací, registrací Azure Backup Server a zálohováním a obnovením aplikačních úloh.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514746"
---
# <a name="troubleshoot-azure-backup-server"></a>Odstraňování potíží Azure Backup Serveru

Informace v následujících tabulkách použijte k řešení chyb, ke kterým dochází při použití Azure Backup Server.

## <a name="basic-troubleshooting"></a>Základní řešení potíží

Před zahájením řešení potíží s Microsoft Azure Backup serverem (MABS) doporučujeme provést následující ověření:

- [Zajistěte, aby byl agent Microsoft Azure Recovery Services (MARS) aktuální.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zajistěte, aby mezi agentem MARS a Azure bylo síťové připojení.](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Ujistěte se, že je spuštěná služba Microsoft Azure Recovery Services (v konzole služby). V případě potřeby restartujte operaci a operaci opakujte.
- [Ujistěte se, že je v umístění pomocné složky k dispozici 5 až 10 % volného místa.](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Pokud se registrace nedaří, ujistěte se, že server, na který se pokoušíte nainstalovat Azure Backup Server, už není zaregistrovaný v jiném trezoru.
- Pokud nabízená instalace selže, zkontrolujte, jestli už agent DPM neexistuje. Pokud agent existuje, odinstalujte ho a pak instalaci opakujte.
- [Zajištění, aby službě Azure Backup nepřekáží jiný proces nebo antivirový software](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Ujistěte se, že je služba SQL Agent spuštěná a na serveru MABS je nastavená na automatické.<br>

## <a name="configure-antivirus-for-mabs-server"></a>Konfigurace antivirového programu pro MABS Server

MABS je kompatibilní s nejoblíbenějšími antivirovými softwarovými produkty. Pro zamezení konfliktů doporučujeme následující postup:

1. **Zakázat monitorování v reálném čase** – antivirový software v reálném čase zakažte sledováním těchto možností:
    - `C:\Program Files<MABS Installation path>\XSD` složky
    - `C:\Program Files<MABS Installation path>\Temp` složky
    - Písmeno jednotky Moderní úložiště zálohování svazku
    - Replika a protokoly přenosů: Chcete-li to provést, zakažte sledování **dpmra.exe** v reálném čase, který je umístěn ve složce `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Monitorování v reálném čase snižuje výkon, protože antivirový software kontroluje repliky pokaždé, když se MABS synchronizuje s chráněným serverem, a kontroluje všechny zasažené soubory pokaždé, když MABS použije změny v replikách.
    - Konzola pro správu: Chcete-li se vyhnout dopadu na výkon, zakažte sledování **csc.exeho** procesu v reálném čase. **csc.exe** proces je kompilátor jazyka C \# a sledování v reálném čase může snížit výkon, protože antivirový software kontroluje soubory, které proces **csc.exe** generuje při generování zpráv XML. **CSC.exe** se nachází v následujících cestách:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Pro agenta MARS nainstalovaného na serveru MABS doporučujeme, abyste vyloučili následující soubory a umístění:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` jako proces
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Pomocné umístění (Pokud nepoužíváte standardní umístění)
2. **Zakázat sledování v reálném čase na chráněném serveru**: zakáže sledování **dpmra.exe** v reálném čase, které se nachází ve složce `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , na chráněném serveru.
3. **Konfigurace antivirového softwaru k odstranění nakažených souborů na chráněných serverech a na serveru MABS**: Pokud chcete zabránit poškození dat repliky a bodů obnovení, nakonfigurujte antivirový software tak, aby odstranil napadené soubory místo automatického čištění nebo umístění do karantény. Automatické čištění a umístění do karantény může způsobovat antivirovému softwaru upravovat soubory a provádět změny, které MABS nemůže detekovat.

Měli byste spustit ruční synchronizaci s konzistencí. Zkontroluje úlohu pokaždé, když antivirový software odstraní soubor z repliky, a to i v případě, že je replika označena jako nekonzistentní.

### <a name="mabs-installation-folders"></a>Instalační složky MABS

Výchozí instalační složky pro aplikaci DPM jsou následující:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Pokud chcete najít cestu k instalační složce, můžete taky spustit následující příkaz:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Zadané neplatné přihlašovací údaje trezoru

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Registrace do trezoru | Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá k dispozici nejnovější přihlašovací údaje přidružené ke službě obnovení. | Doporučená akce: <br> <ul><li> Stáhněte si nejnovější soubor s přihlašovacími údaji z trezoru a zkuste to znovu. <br>ANI</li> <li> Pokud předchozí akce nefungovala, zkuste stáhnout přihlašovací údaje do jiného místního adresáře nebo vytvořit nový trezor. <br>ANI</li> <li> Zkuste aktualizovat nastavení data a času, jak je popsáno v [tomto článku](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>ANI</li> <li> Zkontrolujte, jestli má c:\Windows\Temp víc než 65000 souborů. Přesuňte zastaralé soubory do jiného umístění nebo odstraňte položky v dočasné složce. <br>ANI</li> <li> Ověřte stav certifikátů. <br> a. Otevřete **spravovat certifikáty počítače** (v Ovládacích panelech). <br> b. Rozbalte **osobní** uzel a jeho **certifikáty** podřízeného uzlu.<br> c.  Odeberte certifikát **nástroje Windows Azure Tools**. <br> d. Opakujte registraci v klientovi Azure Backup. <br> ANI </li> <li> Zkontrolujte, jestli nejsou nastavené žádné zásady skupiny. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika je nekonzistentní.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Backup | Replika je nekonzistentní. | Ověřte, že je zapnutá možnost Automatická kontrola konzistence v průvodci skupinou ochrany. Další informace o možnostech replikace a kontrolách konzistence najdete v [tomto článku](/system-center/dpm/create-dpm-protection-groups) .<br> <ol><li> V případě zálohování stavu systému nebo BMR ověřte, že je na chráněném serveru nainstalovaná Zálohování Windows Serveru.</li><li> Ve fondu úložiště DPM na serveru DPM nebo Microsoft Azure Backup vyhledejte problémy související s místem a podle potřeby přidělte úložiště.</li><li> Ověřte stav služba Stínová kopie svazku na chráněném serveru. Pokud je v zakázaném stavu, nastavte ho tak, aby se spouštěl ručně. Spusťte službu na serveru. Pak se vraťte do konzoly DPM/Microsoft Azure Backup serveru a spusťte synchronizaci s úlohou kontroly konzistence.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Vytvoření bodu obnovení online se nezdařilo

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Backup | Vytvoření bodu obnovení online se nezdařilo | **Chybová zpráva**: Windows Azure Backup Agent nemohl vytvořit snímek vybraného svazku. <br> **Alternativní řešení**: Zkuste zvětšit místo ve svazku repliky a bodu obnovení.<br> <br> **Chybová zpráva**: Agent Windows Azure Backup se nemůže připojit ke službě OBEngine. <br> **Alternativní řešení**: Ověřte, že OBEngine existuje v seznamu spuštěných služeb v počítači. Pokud služba OBEngine není spuštěná, spusťte službu OBEngine pomocí příkazu net start OBEngine. <br> <br> **Chybová zpráva**: šifrovací heslo pro tento server není nastavené. Nakonfigurujte prosím šifrovací heslo. <br> **Alternativní řešení**: zkuste nakonfigurovat šifrovací heslo. Pokud dojde k chybě, proveďte následující kroky: <br> <ol><li>Ověřte, zda existuje pomocné umístění. Toto je umístění, které je uvedené v registru **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, kde **ScratchLocation** má název, který by měl existovat.</li><li> Pokud existuje pomocné umístění, zkuste ho znovu zaregistrovat pomocí starého přístupového hesla. *Pokaždé, když nakonfigurujete šifrovací heslo, uložte ho na bezpečné místo.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Původní a externí servery DPM musí být zaregistrované ve stejném trezoru.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Obnovení | **Kód chyby**: Chyba přihlašovacích údajů CBPServerRegisteredVaultDontMatchWithCurrent/trezoru: 100110 <br/> <br/>**Chybová zpráva**: původní a externí servery DPM musí být zaregistrované ve stejném trezoru. | **Příčina**: k tomuto problému dochází, když se pokoušíte obnovit soubory na alternativním serveru z původního serveru pomocí možnosti externí obnovení DPM a pokud se server, který obnovujete, a původní server, ze kterého se zálohují data, nevztahují ke stejnému trezoru Recovery Services.<br/> <br/>**Alternativní řešení** Chcete-li tento problém vyřešit, zajistěte, aby byl původní i alternativní server zaregistrován do stejného trezoru.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Úlohy Vytvoření bodu obnovení online pro virtuální počítač VMware selžou

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Backup | Úlohy Vytvoření bodu obnovení online pro virtuální počítač VMware selžou. Aplikace DPM narazila při pokusu o získání informací sledování změn ve na chybu od VMware. ErrorCode-FileFaultFault (ID 33621) |  <ol><li> Resetujte CTK na VMware pro ovlivněné virtuální počítače.</li> <li>Ověřte, že se na VMware nepoužívá nezávislý disk.</li> <li>Zastavte ochranu pro ovlivněné virtuální počítače a znovu **proveďte** ochranu pomocí tlačítka aktualizovat. </li><li>Spusťte kopii pro ovlivněné virtuální počítače.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operace agenta se nezdařila, protože došlo k chybě komunikace se službou DPM Agent Coordinator na serveru.

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Přenáší agenty na chráněné servery | Operace agenta se nezdařila z důvodu chyby komunikace se službou Koordinátor agenta DPM v systému \<ServerName> . | **Pokud Doporučená akce uvedená v produktu nefunguje, proveďte následující kroky**: <ul><li> Pokud připojujete počítač z nedůvěryhodné domény, postupujte podle [těchto kroků](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> ANI </li><li> Pokud připojujete počítač z důvěryhodné domény, vyřešte potíže pomocí kroků uvedených v [tomto blogu](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>ANI</li><li> Zkuste zakázat antivirovou ochranu jako krok pro odstraňování potíží. Pokud se problém vyřeší, upravte nastavení antivirového programu podle doporučení v [tomto článku](/system-center/dpm/run-antivirus-server).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalační program nemohl aktualizovat metadata registru

| Operace | Podrobnosti o chybě | Alternativní řešení |
|-----------|---------------|------------|
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohlo vést k přečerpání spotřeby úložiště. Aby se tato aktualizace nezobrazovala, proveďte položku registru oříznutí ReFS. | Upravte klíč registru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Nastavte hodnotu DWORD na 1. |
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohlo vést k přečerpání spotřeby úložiště. Pokud se tomu chcete vyhnout, aktualizujte položku registru Volume SnapOptimization. | Vytvořte klíč registru **SOFTWARE\Microsoft data Protection Manager\Configuration\VolSnapOptimization\WriteIds** s hodnotou prázdného řetězce. |

## <a name="registration-and-agent-related-issues"></a>Problémy související se registrací a agenty

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Přenáší agenty na chráněné servery | Přihlašovací údaje zadané pro server jsou neplatné. | **Pokud Doporučená akce, která je zobrazená v produktu, nefunguje, proveďte následující kroky**: <br> Pokuste se agenta ochrany nainstalovat ručně na provozní server, jak je uvedeno v [tomto článku](/system-center/dpm/deploy-dpm-protection-agent).|
| Agent Azure Backup se nemohl připojit ke službě Azure Backup (ID: 100050). | Agent Azure Backup se nemohl připojit ke službě Azure Backup. | **Pokud Doporučená akce, která je zobrazená v produktu, nefunguje, proveďte následující kroky**: <br>1. z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz: **PsExec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**. Otevře se okno Internet Explorer. <br/> 2. v **nabídce nástroje**  >  **Možnosti Internetu**  >  **připojení**  >  **nastavení sítě LAN**. <br/> 3. Změňte nastavení tak, aby se používalo proxy server. Pak zadejte podrobnosti o proxy server.<br/> 4. Pokud má počítač omezený přístup k Internetu, zajistěte, aby nastavení brány firewall na počítači nebo proxy umožňovalo tyto [adresy URL](install-mars-agent.md#verify-internet-access) a [IP adresy](install-mars-agent.md#verify-internet-access).|
| Nepovedlo se nainstalovat agenta Azure Backup | Instalace Microsoft Azure Recovery Services se nezdařila. Všechny změny, které byly provedeny v systému instalačním programem Microsoft Azure Recovery Services byly vráceny zpět. (ID: 4024) | Nainstalujte agenta Azure ručně.

## <a name="configuring-protection-group"></a>Konfigurace skupiny ochrany

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Konfigurace skupin ochrany | Aplikaci DPM se nepodařil výčet součásti aplikace v chráněném počítači (název chráněného počítače). | Na příslušné úrovni zdroje dat a součásti vyberte **aktualizovat** na obrazovce uživatelské rozhraní skupiny ochrany. |
| Konfigurace skupin ochrany | Ochranu nejde nakonfigurovat. | Pokud je chráněný server SQL Server, ověřte, jestli je na chráněném počítači k dispozici oprávnění role sysadmin k systémovému účtu (Ntauthority\system.), jak je popsáno v [tomto článku](/system-center/dpm/back-up-sql-server).
| Konfigurace skupin ochrany | Ve fondu úložiště není pro tuto skupinu ochrany dostatek volného místa. | Disky, které se přidají do fondu úložiště, [by neměly obsahovat oddíl](/system-center/dpm/create-dpm-protection-groups). Odstraňte všechny existující svazky na discích. Pak je přidejte do fondu úložiště.|
| Změna zásad |Zásady zálohování nešlo změnit. Chyba: aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Po uplynutí určité doby zkuste operaci zopakovat. Pokud potíže trvají, obraťte se na podporu Microsoftu. | **Příčina:**<br/>K této chybě dochází za tři podmínky: Pokud je povolené nastavení zabezpečení, pokusíte se zmenšit rozsah uchování pod minimálními výše uvedenými minimálními hodnotami a při práci s nepodporovanou verzí. (Nepodporované verze jsou nižší než Microsoft Azure Backup Server verze 2.0.9052 a Azure Backup Server Update 1.) <br/>**Doporučená akce:**<br/> Pokud chcete pokračovat s aktualizacemi souvisejícími s zásadami, nastavte dobu uchování nad určenou minimální dobu uchování. (Minimální doba uchovávání je sedm dní denně, čtyři týdny pro každý týden, tři týdny pro každý měsíc nebo jeden rok pro roční.) <br><br>Dalším upřednostňovaným přístupem je volitelně aktualizace agenta Zálohování a Azure Backup Server pro využití všech aktualizací zabezpečení. |

## <a name="backup"></a>Backup

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Backup | Během běhu úlohy došlo k neočekávané chybě. Zařízení není připravené. | **Pokud Doporučená akce, která je zobrazená v produktu, nefunguje, proveďte následující kroky:** <br> <ul><li>Nastavte prostor úložiště stínové kopie na neomezený pro položky ve skupině ochrany a poté spusťte kontrolu konzistence.<br></li> ANI <li>Zkuste odstranit existující skupinu ochrany a vytvořit několik nových skupin. Každá nová skupina ochrany by měla obsahovat jednotlivou položku.</li></ul> |
| Backup | Pokud zálohujete pouze stav systému, ověřte, zda je v chráněném počítači dostatek volného místa pro uložení zálohy stavu systému. | <ol><li>Ověřte, že je na chráněném počítači nainstalovaná Zálohování Windows Serveru.</li><li>Ověřte, zda je v chráněném počítači dostatek místa pro stav systému. Nejjednodušší způsob, jak to ověřit, je přejít na chráněný počítač, otevřít Zálohování Windows Serveru, kliknout na výběr a pak vybrat BMR. Uživatelské rozhraní pak oznamuje, kolik místa je potřeba. Otevřete   >    >  možnost **plán zálohování** místních záloh WSB  >  **Vybrat nastavení zálohování**  >  **úplný Server** (velikost se zobrazí). Tuto velikost použijte k ověření.</li></ol>
| Backup | Selhání zálohování pro BMR | Pokud je velikost BMR velká, přesuňte některé soubory aplikace na jednotku operačního systému a zkuste to znovu. |
| Backup | Možnost znovu zapnout ochranu virtuálního počítače VMware na novém serveru Microsoft Azure Backup nezobrazuje, jak je možné přidat. | Vlastnosti VMware jsou odkazovaly na starou a vyřazenou instanci serveru Microsoft Azure Backup. Řešení tohoto problému:<br><ol><li>V VCenter (ekvivalent SC-VMM), přejít na kartu **Souhrn** a pak na **vlastní atributy**.</li>  <li>Odstraňte starý název Microsoft Azure Backup serveru z hodnoty **DPMServer** .</li>  <li>Vraťte se na nový server Microsoft Azure Backup a upravte PG.  Po výběru tlačítka **aktualizovat** se virtuální počítač zobrazí se zaškrtávacím políčkem, jako je k dispozici pro přidání do ochrany.</li></ol> |
| Backup | Při přístupu k souborům nebo sdíleným složkám došlo k chybě. | Zkuste upravit nastavení antivirového programu podle doporučení v tomto článku [Spusťte antivirový software na serveru DPM](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Změnit heslo

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Změnit heslo |Zadaný kód PIN zabezpečení je nesprávný. Pro dokončení této operace zadejte správný bezpečnostní kód PIN. |**Příčina:**<br/> K této chybě dojde, když zadáte neplatný nebo vypršelý PIN kód pro zabezpečení při provádění kritické operace (například změna hesla). <br/>**Doporučená akce:**<br/> K dokončení této operace je nutné zadat platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste se k Azure Portal a přejít do trezoru Recovery Services. Pak přejdete na  >  **vlastnosti** nastavení  >  **generovat bezpečnostní kód PIN**. Pro změnu hesla použijte tento PIN kód. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>K této chybě dochází, pokud je povoleno nastavení zabezpečení nebo pokud se pokusíte změnit heslo při použití nepodporované verze.<br/>**Doporučená akce:**<br/> Pokud chcete změnit heslo, musíte nejdřív aktualizovat agenta Zálohování na minimální verzi, která je 2.0.9052. Musíte taky aktualizovat Azure Backup Server na minimum aktualizace 1 a pak zadat platný bezpečnostní PIN kód. Pokud chcete získat kód PIN, přihlaste se k Azure Portal a přejít do trezoru Recovery Services. Pak přejdete na  >  **vlastnosti** nastavení  >  **generovat bezpečnostní kód PIN**. Pro změnu hesla použijte tento PIN kód. |

## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Nastavení e-mailových oznámení pomocí pracovního nebo školního účtu |ID chyby: 2013| **Příčina:**<br> Probíhá pokus o použití pracovního nebo školního účtu. <br>**Doporučená akce:**<ol><li> První věc, kterou je potřeba zajistit, je, že u serveru DPM je nastavená možnost "povolení anonymního přenosu na konektoru pro příjem" pro server DPM. Další informace o tom, jak to nakonfigurovat, najdete v tématu [Povolení anonymního přenosu v konektoru pro příjem](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Pokud nemůžete použít interní předávání SMTP a potřebujete ho nastavit pomocí serveru Office 365, můžete nastavit, aby služba IIS byla přenosová. Nakonfigurujte server DPM tak, aby k [přenosu SMTP do sady Office 365 používal službu IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Nezapomeňte použít \@ formát Domain.com uživatele, *ne* doména \ uživatel.<br><br><li>Najeďte na to, aby aplikace DPM používala název místního serveru jako server SMTP, port 587. Pak ji nasměrujte na e-mail uživatele, ze kterého by měly přijít e-maily.<li> Uživatelské jméno a heslo na stránce nastavení SMTP DPM by měly být pro doménový účet v doméně, ve které je aplikace DPM zapnutá. </li><br> Když měníte adresu serveru SMTP, proveďte změnu na nové nastavení, zavřete okno nastavení a pak ho znovu otevřete, abyste se ujistili, že odráží novou hodnotu.  Pouhou změnou a testováním nemusí vždy dojít k tomu, že se nové nastavení projeví, takže testování tímto způsobem je osvědčeným postupem.<br><br>V každém okamžiku tohoto procesu můžete tato nastavení vymazat zavřením konzole DPM a úpravou následujících klíčů registru: **HKLM\SOFTWARE\Microsoft\Microsoft data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName Keys**. Můžete je přidat zpátky do uživatelského rozhraní, když ho znovu spustíte.

## <a name="common-issues"></a>Běžné problémy

Tato část se zabývá běžnými chybami, ke kterým může dojít při použití Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Chybová zpráva | Doporučená akce |
-- | --
Zálohování selhalo kvůli neplatné nebo chybějící replice zálohy disku. | Chcete-li tento problém vyřešit, ověřte níže uvedené kroky a opakujte operaci: <br/> 1. vytvoření bodu obnovení disku<br/> 2. spustit kontrolu konzistence u zdroje dat <br/> 3. Zastavte ochranu zdroje dat a potom překonfigurujte ochranu pro tento zdroj dat.

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Chybová zpráva | Doporučená akce |
-- | --
Snímek zdrojového svazku se nezdařil, protože metadata v replice nejsou platná. | Vytvořte bod obnovení disku tohoto zdroje dat a zkuste znovu spustit online zálohování.

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>Chyba CBPSourceSnapshotFailedReplicaInconsistent

Chybová zpráva | Doporučená akce |
-- | --
Snímek zdrojového svazku selhal kvůli nekonzistentní replice zdroje dat. | Spusťte kontrolu konzistence tohoto zdroje dat a zkuste to znovu.

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Chybová zpráva | Doporučená akce |
-- | --
Zálohování selhalo, protože se nepodařilo naklonovat repliku zálohy disku.| Zajistěte, aby všechny předchozí soubory replik zálohování disku (. vhdx) byly odpojené a během online zálohování neprobíhal žádný disk k zálohování na disk.
