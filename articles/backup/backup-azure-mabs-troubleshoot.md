---
title: Odstraňování potíží Azure Backup Serveru
description: Řešení potíží s instalací, registrace Azure Backup Server a zálohování a obnovení úlohy aplikací.
services: backup
author: pvrk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/24/2017
ms.author: pullabhk
ms.openlocfilehash: 657fa71a47f3a7899596e3be35482abb56c6f37c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260512"
---
# <a name="troubleshoot-azure-backup-server"></a>Odstraňování potíží Azure Backup Serveru

Řešení potíží s chybami, které zaznamenáte při používání Azure Backup serveru pomocí informací v následujících tabulkách.

## <a name="invalid-vault-credentials-provided"></a>Neplatné přihlašovací údaje úložiště k dispozici 

Chcete-li tento problém vyřešit, postupujte podle [tyto kroky pro řešení potíží](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operace agenta se nezdařila kvůli chybě komunikace s službu DPM agent coordinator na serveru 

Chcete-li tento problém vyřešit, postupujte podle [tyto kroky pro řešení potíží](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues). 

## <a name="setup-could-not-update-registry-metadata"></a>Instalační program nemohl aktualizovat metadata registru

Chcete-li tento problém vyřešit, postupujte podle [tyto kroky pro řešení potíží](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).




## <a name="installation-issues"></a>Problémy s instalací

| Operace | Podrobnosti o chybě | Alternativní řešení |
|-----------|---------------|------------|
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohlo vést k overusage spotřebu úložiště. Abyste tomu předešli, aktualizujte položku registru ReFS. | Upravit klíč registru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Nastavte hodnotu Dword na 1. |
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohlo vést k overusage spotřebu úložiště. Abyste tomu předešli, aktualizujte položku registru Volume SnapOptimization. | Vytvořte klíč registru **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** s prázdnou řetězcovou hodnotu. |
## <a name="registration-and-agent-related-issues"></a>Registrace a problémů souvisejících s agenty
| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Registrace do trezoru | Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje nesouvisí s využitím služby recovery. | Chcete-li vyřešit tuto chybu: <ul><li> Stáhněte si nejnovější soubor s přihlašovacími údaji z trezoru a zkuste to znovu. <br>(NEBO)</li> <li> Pokud předchozí akci nefungovalo, zkusit stáhnout přihlašovací údaje na jiný místní adresář nebo vytvořit nový trezor. <br>(NEBO)</li> <li> Zkuste aktualizovat datum a čas nastavení, jak je popsáno v [tento blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(NEBO)</li> <li> Zkontrolujte, jestli c:\windows\temp má více než 65000 soubory. Přesunutí zastaralých souborů do jiného umístění nebo odstranění položek ve složce Temp. <br>(NEBO)</li> <li> Zkontrolujte stav certifikáty. <br> a. Otevřít **spravovat certifikáty počítače** (v Ovládacích panelech). <br> b. Rozbalte **osobní** uzel a jeho podřízeným uzlem **certifikáty**.<br> c.  Odeberte certifikát **Windows Azure Tools**. <br> d. Opakujte registraci ve službě Azure Backup klienta. <br> (NEBO) </li> <li> Zaškrtněte, pokud chcete zjistit, jestli všechny zásady skupiny na místě. </li></ul> |
| Odesílání agenty na chráněných serverech | Operace agenta se nezdařila kvůli chybě komunikace se službou DPM Agent Coordinator na \<ServerName >. | **Pokud nebude fungovat doporučenou akci, která se zobrazí v rámci produktu, proveďte následující kroky**: <ul><li> Pokud připojujete počítače z nedůvěryhodné domény, postupujte podle [tyto kroky](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (NEBO) </li><li> Pokud připojujete počítač v důvěryhodné doméně, řešení potíží pomocí kroků uvedených v [tento blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(NEBO)</li><li> Zkuste zakázat antivirový v rámci řešení potíží. Pokud se problém vyřeší, upravit nastavení pro antivirovou ochranu jak je navrženo v [v tomto článku](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |
| Odesílání agenty na chráněných serverech | Přihlašovací údaje, které jsou určené pro server jsou neplatné. | **Pokud nebude fungovat doporučenou akci, která se zobrazí v rámci produktu, proveďte následující kroky**: <br> Pokuste se ručně nainstalovat agenta ochrany na provozním serveru, jak je uvedeno v [v tomto článku](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup Agent se nemohl připojit ke službě Azure Backup (ID: 100050) | Azure Backup Agent nemohl připojit ke službě Azure Backup. | **Pokud nebude fungovat doporučenou akci, která se zobrazí v rámci produktu, proveďte následující kroky**: <br>1. Spusťte na příkazovém řádku se zvýšenými oprávněními: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Otevře se okno aplikace Internet Explorer. <br/> 2. Přejděte na **nástroje** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**. <br/> 3. Ověřte nastavení proxy serveru pro systémový účet. Nastavit Proxy IP adresy a portu. <br/> 4. Zavřete aplikaci Internet Explorer.|
| Instalace služby Azure Backup Agent se nezdařilo | Instalace Microsoft Azure Recovery Services se nezdařila. Všechny změny provedené v systému instalací Microsoft Azure Recovery Services byly vráceny zpět. (ID: 4024) | Ruční instalace agenta služby Azure.


## <a name="configuring-protection-group"></a>Konfigurace skupiny ochrany
| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Konfigurace skupin ochrany | Aplikace DPM se nepodařil výčet součásti aplikace v chráněném počítači (název chráněného počítače). | Vyberte **aktualizovat** na obrazovce konfigurace skupiny ochrany uživatelského rozhraní na příslušné úrovni datasource/součásti. |
| Konfigurace skupin ochrany | Ochranu nelze nakonfigurovat | Pokud je chráněný server SQL server, ověřte, že oprávnění role správce byla poskytnutá těmto osobám systémový účet (NTAuthority\System) v chráněném počítači jak je popsáno v [v tomto článku](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurace skupin ochrany | Není dostatek volného místa ve fondu úložiště pro tuto skupinu ochrany. | Disky, které jsou přidané do fondu úložiště [oddílu by neměla obsahovat](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Odstraňte všechny existující svazky na discích. Poté je přidejte do fondu úložiště.|
| Změna zásad |Zásady zálohování se nepodařilo upravit. Chyba: Aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Zkuste prosím tuto operaci po uplynutí nějakou dobu. Pokud se problém nevyřeší, obraťte se na podporu Microsoftu. |**Příčina:**<br/>K této chybě dochází v části tří podmínek: Pokud jsou povolené nastavení zabezpečení, při pokusu zmenšit rozsah uchování nižší než minimální hodnoty dříve, a když jste v nepodporované verzi. (Nepodporovaná verze jsou u funkcí pod verzi Microsoft Azure Backup serveru 2.0.9052 a Azure Backup serveru update 1.) <br/>**Doporučená akce:**<br/> Chcete-li pokračovat s aktualizací související se zásadami, nastavte dobu uchování nad minimální období zadaná doba uchovávání. (Minimální doba uchování je sedm dní pro denní, čtyři týdny pro týdenní, tři týdny, než každý měsíc nebo jeden rok pro roční.) <br><br>Volitelně jiného upřednostňováno, že přístup je k aktualizaci agenta zálohování a Azure Backup Server využít všech aktualizací zabezpečení. |

## <a name="backup"></a>Backup
| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Backup | Replika je nekonzistentní | Ověřte, že je zapnutá možnost kontroly automatické kontroly konzistence v Průvodci skupiny ochrany. Další informace o příčinách nekonzistence repliky a relevantní doporučení, najdete v článku Microsoft TechNet [replika je nekonzistentní](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> V případě zálohování stavu systému/úplné obnovení systému ověřte, že zálohování Windows serveru je nainstalovaná na chráněném serveru.</li><li> Zkontrolujte problémy související s místa ve fondu úložiště DPM na serveru aplikace DPM nebo Microsoft Azure Backup a přidělení úložiště podle potřeby.</li><li> Kontrola stavu služby Stínová kopie svazku na chráněném serveru. Pokud je v zakázaném stavu, nastavte ji spustit ručně. Spusťte službu na serveru. Pak přejděte zpět do konzoly aplikace DPM nebo Microsoft Azure Backup serveru a spusťte synchronizaci s úlohou kontroly konzistence.</li></ol>|
| Backup | Při běhu úlohy došlo k neočekávané chybě. Zařízení není připraveno. | **Pokud nebude fungovat doporučenou akci, která se zobrazí v rámci produktu, proveďte následující kroky:** <br> <ul><li>Nastavte úložiště stínové kopie místo na neomezený počet položek ve skupině ochrany a potom spustíte kontrolu konzistence.<br></li> (NEBO) <li>Zkuste odstranit stávající ochranu skupiny a vytváření nových skupin více. Všechny nové skupiny ochrany by měl mít jednotlivé položky v něm.</li></ul> |
| Backup | Pokud zálohujete jenom stav systému, ověřte, zda je dostatek volného místa na chráněném počítači pro uložení zálohy stavu systému. | <ol><li>Ověřte, že zálohování Windows serveru je nainstalovaný v chráněném počítači.</li><li>Ověřte, zda je dostatek místa na chráněném počítači stavu systému. Nejjednodušší způsob, jak ověřit, že to je přejít na chráněném počítači otevřete zálohování Windows serveru, klikněte na tlačítko prostřednictvím výběry a vyberte BMR. Uživatelské rozhraní pak zjistíte, kolik místa je povinný. Otevřít **WSB** > **místní záloha** > **plán zálohování** > **Vybrat konfiguraci zálohování**  >  **Plnou instalaci systému server** (zobrazí se velikost). Tato velikost slouží k ověření.</li></ol>
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud je zobrazeno chybová zpráva "Windows Azure Backup Agent nemohl vytvořit snímek vybraného svazku" zkuste zvětšit místo na svazku bodu obnovení a repliky.
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud chybová zpráva "Windows, Azure Backup Agent se nemůže připojit ke službě obengine", ověřte, zda OBEngine existuje v seznamu služeb spuštěných v počítači. Pokud není spuštěná služba OBEngine, použijte příkaz "net start OBEngine" spustit službu OBEngine.
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud chybová zpráva "šifrovací heslo pro tento server není nastaven. Nakonfigurujte prosím šifrovací heslo,"Zkuste nakonfigurovat šifrovací heslo. Pokud selže, proveďte následující kroky: <br> <ol><li>Ověřte, zda pomocné umístění existuje. Toto je umístění, které je uvedený v registru **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, s názvem **ScratchLocation** by měla existovat.</li><li> Pokud pomocné umístění existuje, zkuste se znovu zaregistrovat pomocí staré heslo. *Pokaždé, když konfigurujete šifrovací heslo, uložte ho na bezpečném místě.*</li><ol>
| Backup | Selhání zálohování pro úplné obnovení systému | Pokud je velká velikost BMR, přesunout některé soubory aplikace do jednotky operačního systému a zkuste to znovu. |
| Backup | Možné znovunastavení ochrany virtuálních počítačů VMware na nový Server Microsoft Azure Backup není uveden jako dostupné pro přidání. | Vlastnosti VMware jsou nasměrovaného na staré, vyřazené instance Microsoft Azure Backup serveru. K vyřešení tohoto problému:<br><ol><li>V systému VCenter (SC-VMM ekvivalent), přejděte na **Souhrn** kartu a potom do **vlastní atributy**.</li>  <li>Odstraňte název staré Microsoft Azure Backup serveru z **DPMServer** hodnotu.</li>  <li>Vraťte se do Microsoft Azure Backup serveru novou verzi a upravovat stránky  Po výběru **aktualizovat** tlačítko se zobrazí zaškrtávací políčko podle potřeby, přidejte do ochrany virtuálního počítače.</li></ol> |
| Backup | Chyba při přístupu k soubory nebo sdílené složky | Zkuste upravit nastavení pro antivirovou ochranu jak je navrženo v článku na webu TechNet [spuštění antivirového softwaru na serveru DPM](https://technet.microsoft.com/library/hh757911.aspx).|
| Backup | Selhání úlohy vytvoření bodu obnovení online pro virtuální počítač VMware. Aplikace DPM narazila na chybu ve VMware při pokusu získat informace o sledování změn ve. Kód chyby - FileFaultFault (ID 33621) |  <ol><li> Resetování CTK ve VMware příslušných virtuálních počítačích.</li> <li>Zkontrolujte, že nezávislé disku není místo ve VMware.</li> <li>Zastavte ochranu pro ovlivněné virtuální počítače a znovu nastavit ochranu s **aktualizovat** tlačítko. </li><li>Spuštění kopie pro ovlivněné virtuální počítače.</li></ol>|


## <a name="change-passphrase"></a>Změnit heslo
| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Změnit heslo |Tento bezpečnostní kód PIN, který jste zadali, je nesprávná. Zadejte správný bezpečnostní kód PIN k dokončení této operace. |**Příčina:**<br/> Tato chyba nastane, pokud zadáte neplatný nebo vypršelo bezpečnostní kód PIN, když provádíte důležité operace (jako je například změna přístupové heslo). <br/>**Doporučená akce:**<br/> K dokončení operace, je nutné zadat platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste se k webu Azure portal a přejděte do trezoru služby Recovery Services. Pak přejděte na **nastavení** > **vlastnosti** > **vygenerovat bezpečnostní kód PIN**. Chcete-li změnit heslo, použijte tento PIN kód. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>K této chybě dochází, pokud jsou povolené nastavení zabezpečení, nebo když se pokusíte změnit heslo, pokud používáte nepodporované verze.<br/>**Doporučená akce:**<br/> Chcete-li změnit heslo, musíte nejprve aktualizovat agenta zálohování na minimální verzi, která je 2.0.9052. Také musíte aktualizovat Azure Backup serveru na minimum update 1 a pak zadejte platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste na webu Azure portal a přejděte do trezoru služby Recovery Services. Pak přejděte na **nastavení** > **vlastnosti** > **vygenerovat bezpečnostní kód PIN**. Chcete-li změnit heslo, použijte tento PIN kód. |


## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení
| Operace | Podrobnosti o chybě | Alternativní řešení |
| --- | --- | --- |
| Nastavení e-mailová oznámení pomocí účtu Office 365 |ID chyby: 2013| **Příčina:**<br> Chcete-li použít účet Office 365 <br>**Doporučená akce:**<ol><li> Nejprve zkontrolujte je, že "umožnit anonymní Relay na přijímat konektor" pro server aplikace DPM je nastavený na serveru Exchange. Další informace o tom, jak nastavit tuto konfiguraci najdete v tématu [povolit anonymní Relay konektoru přijímat](https://technet.microsoft.com/library/bb232021.aspx) na webu TechNet.</li> <li> Pokud nemůžete použít interní předávání SMTP a muset nastavit pomocí serveru služeb Office 365, můžete nastavit IIS bude přenos. Nakonfigurujte server DPM za účelem [předávání SMTP k O365, pomocí služby IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **Důležité:** nezapomeňte použít user@domain.com formátu a *není* doména\uživatel.<br><br><li>Bod DPM použít název místního serveru jako serveru SMTP port 587. Pak nasměrovat ho na e-mailu uživatele, který do e-mailů by měl pocházet z.<li> Uživatelské jméno a heslo na stránce instalace aplikace DPM SMTP by měl být pro doménový účet v doméně, tj. aplikace DPM v. </li><br> **Poznámka:**: když změníte adresu serveru SMTP, proveďte změnu nová nastavení, zavřete dialogové okno nastavení a znovu ji ujistit se odráží novou hodnotu.  Nové nastavení se projeví, takže jeho otestování. Díky tomu je osvědčeným postupem nemusí vždy způsobit jednoduše mění a testování.<br><br>Kdykoli během tohoto procesu můžete tato nastavení vymazat tím, že zavření konzoly DPM a úpravy následující klíče registru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> odstranit SMTPPassword a Klíče SMTPUserName**. Přidáním zpět do uživatelského rozhraní při znovu spusťte.
