---
title: Odstraňování potíží Azure Backup Serveru
description: Řešení potíží s instalací, registraci Azure Backup Server a zálohování a obnovení úlohy aplikací.
services: backup
author: pvrk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/24/2017
ms.author: pullabhk
ms.openlocfilehash: 2b4c2f8b4c8311580692619cfb6a72fe8bcad6de
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605062"
---
# <a name="troubleshoot-azure-backup-server"></a>Odstraňování potíží Azure Backup Serveru

Použijte informace v následujících tabulkách k řešení chyb, které zaznamenáte při používání serveru Azure Backup.

## <a name="invalid-vault-credentials-provided"></a>Neplatné přihlašovací údaje úložiště 

Chcete-li vyřešit tento problém, postupujte podle [následující kroky pro řešení](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operace agenta se nezdařila kvůli chybě komunikace s služby DPM agent Coordinator na serveru 

Chcete-li vyřešit tento problém, postupujte podle [následující kroky pro řešení](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues). 

## <a name="setup-could-not-update-registry-metadata"></a>Instalační program nemohl aktualizovat metadata registru

Chcete-li vyřešit tento problém, postupujte podle [následující kroky pro řešení](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).




## <a name="installation-issues"></a>Problémy instalace

| Operace | Detaily chyby | Alternativní řešení |
|-----------|---------------|------------|
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohla vést k overusage spotřebu úložiště. Abyste tomu předešli, aktualizujte položky registru ořezávání odolný systém souborů. | Upravit klíč registru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Nastavte hodnotu Dword na 1. |
|Instalace | Instalační program nemohl aktualizovat metadata registru. Toto selhání aktualizace by mohla vést k overusage spotřebu úložiště. Abyste tomu předešli, aktualizujte položky registru SnapOptimization svazku. | Vytvořte klíč registru **Manager\Configuration\VolSnapOptimization\WriteIds ochrany dat SOFTWARE\Microsoft** s hodnotou prázdný řetězec. |
## <a name="registration-and-agent-related-issues"></a>Registrace a problémy související s agenta
| Operace | Detaily chyby | Alternativní řešení |
| --- | --- | --- |
| Registrace do trezoru | Neplatné přihlašovací údaje úložiště. Soubor je poškozený nebo nemá mít poslední přihlašovací údaje související se službou obnovení. | Chcete-li vyřešit tuto chybu: <ul><li> Stáhnout nejnovější soubor s přihlašovacími údaji trezoru a zkuste to znovu. <br>(NEBO)</li> <li> Pokud předchozí akce nefunguje, zkuste stáhnout přihlašovací údaje jiného místního adresáře nebo vytvořit nový trezor. <br>(NEBO)</li> <li> Zkuste aktualizovat datum a čas nastavení, jak je popsáno v [tomto blogu](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(NEBO)</li> <li> Zkontrolujte, zda c:\windows\temp má více než 65000 soubory. Přesunutí zastaralých souborů do jiného umístění nebo odstranit položky ve složce Temp. <br>(NEBO)</li> <li> Zkontrolujte stav certifikátů. <br> a. Otevřete **spravovat certifikáty počítače** (v Ovládacích panelech). <br> b. Rozbalte **osobní** uzel a jeho podřízený uzel **certifikáty**.<br> c.  Odebrat certifikát **nástroje systému Windows Azure**. <br> d. Opakujte registraci v klientovi Azure Backup. <br> (NEBO) </li> <li> Zkontrolujte, zda všechna zásad skupiny, je na místě. </li></ul> |
| Nabízení agentů do chráněných serverů | Operace agenta se nezdařila kvůli chybě komunikace se službou koordinátora agenta DPM na \<ServerName >. | **Pokud nebude fungovat doporučené akce, které se zobrazí v produktu, proveďte následující kroky**: <ul><li> Pokud se připojujete počítači z nedůvěryhodné domény, postupujte podle [tyto kroky](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (NEBO) </li><li> Pokud se připojujete počítače z důvěryhodné domény, řešení problémů pomocí kroků uvedených v [tomto blogu](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(NEBO)</li><li> Zkuste zakázat antivirový v rámci řešení potíží. Pokud tento problém řeší, upravte nastavení antivirový dle pokynů [v tomto článku](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |
| Nabízení agentů do chráněných serverů | Přihlašovací údaje, které jsou určené pro server jsou neplatné. | **Pokud nebude fungovat doporučené akce, které se zobrazí v produktu, proveďte následující kroky**: <br> Pokuste se ručně nainstalovat agenta ochrany na provozním serveru zadané v [v tomto článku](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup Agent se nemohl připojit ke službě Azure Backup (ID: 100050) | Azure Backup Agent se nemohl připojit ke službě Azure Backup. | **Pokud nebude fungovat doporučené akce, které se zobrazí v produktu, proveďte následující kroky**: <br>1. Řádku se zvýšenými oprávněními spusťte následující příkaz: **nástroje psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Otevře se okno aplikace Internet Explorer. <br/> 2. Přejděte na **nástroje** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**. <br/> 3. Ověřte nastavení proxy pro účet system. Nastavení proxy serveru IP adresy a portu. <br/> 4. Zavřete Internet Explorer.|
| Instalace služby Azure Backup Agent se nezdařila | Instalace služeb zotavení Microsoft Azure se nezdařila. Všechny změny provedené v systému při instalaci služeb zotavení Microsoft Azure byly vráceny zpět. (ID: 4024) | Ručně nainstalujte agenta Azure.


## <a name="configuring-protection-group"></a>Konfigurace skupiny ochrany
| Operace | Detaily chyby | Alternativní řešení |
| --- | --- | --- |
| Konfigurace skupin ochrany | Aplikace DPM se nepodařil výčet součásti aplikace na chráněného počítače (název chráněného počítače). | Vyberte **aktualizovat** na obrazovce konfigurace skupiny ochrany uživatelského rozhraní na příslušné úrovni zdroj dat nebo součásti. |
| Konfigurace skupin ochrany | Ochranu nelze nakonfigurovat | Pokud na chráněném serveru SQL server, ověřte, že oprávnění role správce systému pro systémový účet (NTAuthority\System) v chráněném počítači byly zadány jak je popsáno v [v tomto článku](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurace skupin ochrany | Není dostatek volného místa ve fondu úložiště pro tuto skupinu ochrany. | Disky, které jsou přidány do fondu úložiště [nesmí obsahovat oddíl](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Odstraňte všechny existující svazky na discích. Pak je přidejte do fondu úložiště.|
| Změny zásad |Zásady zálohování se nepodařilo upravit. Chyba: Aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Opakujte operaci po uplynutí nějaké doby. Pokud problém přetrvá, obraťte se na podporu společnosti Microsoft. |**Příčina:**<br/>K této chybě dojde v části tři podmínky: Pokud jsou povolené nastavení zabezpečení, při pokusu zmenšit rozsah uchování nižší než minimální hodnoty zadali dřív, a pokud jste v nepodporované verzi. (Nepodporovaná verze jsou takové níže verzi serveru Microsoft Azure Backup 2.0.9052 a Azure Backup Server update 1.) <br/>**Doporučená akce:**<br/> Chcete-li pokračovat s aktualizací související se zásadami, nastavte dobu uchování výše zadaná minimální doba uchovávání období. (Dobu uchování minimální je sedm dní pro denní, čtyři týdny po týdně, tři týdny pro každý měsíc nebo jeden rok pro roční.) <br><br>Volitelně jiné upřednostňovaný způsob je aktualizovat agenta zálohování a Azure Backup Server, můžete využít všech aktualizací zabezpečení. |

## <a name="backup"></a>Backup
| Operace | Detaily chyby | Alternativní řešení |
| --- | --- | --- |
| Backup | Replika je nekonzistentní | Ověřte, jestli je zapnutá možnost kontroly konzistence automatické v Průvodci skupinou ochrany. Další informace o příčinách nekonzistence repliky a relevantní návrzích, najdete v článku Microsoft TechNet [replika je nekonzistentní](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> V případě zálohování stavu systému nebo BMR ověřte, zda zálohování serveru je nainstalován na chráněném serveru.</li><li> Zkontrolujte problémy související s místa ve fondu úložiště aplikace DPM na serveru aplikace DPM nebo Microsoft Azure Backup a přidělit úložiště podle potřeby.</li><li> Zkontrolujte stav služby Stínová kopie svazku na chráněném serveru. Pokud je v zakázaném stavu, nastavte ji na ruční spouštění. Spusťte službu na serveru. Poté přejděte zpět ke konzole serveru aplikace DPM nebo Microsoft Azure Backup a spustí synchronizaci s kontrolou konzistence.</li></ol>|
| Backup | Při provádění úlohy došlo k neočekávané chybě. Zařízení není připraveno. | **Pokud nebude fungovat doporučené akce, které se zobrazí v produktu, proveďte následující kroky:** <br> <ul><li>Nastavit prostor úložiště stínové kopie na neomezený na položky ve skupině ochrany a poté spusťte kontrolu konzistence.<br></li> (NEBO) <li>Odstraňte existující ochranu skupiny a vytváření více nových skupin. Každé nové skupiny ochrany musí mít jednotlivé položky v něm.</li></ul> |
| Backup | Pokud zálohujete pouze stav systému, ověřte, zda je dostatek volného místa v chráněném počítači k uložení zálohy stavu systému. | <ol><li>Ověřte, zda je v chráněném počítači nainstalován zálohování serveru.</li><li>Ověřte, zda je dostatek místa v chráněném počítači pro stav systému. Nejjednodušší způsob, jak ověřit, zda přejít na chráněném počítači otevřete zálohování Windows serveru, klikněte na tlačítko prostřednictvím výběr a vyberte úplné obnovení systému. Uživatelské rozhraní pak poznáte, kolik místa je požadovaná. Otevřete **WSB** > **místní záloha** > **plán zálohování** > **Vybrat konfiguraci zálohování**  >  **Celého serveru** (velikost je zobrazený). Použijte pro ověření této velikosti.</li></ol>
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud je zobrazeno chybová zpráva "Windows Azure Backup Agent nemohl vytvořit snímek vybraného svazku" zvětšete místo na svazku bodu obnovení a repliky.
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud chybová zpráva říká "Windows Azure Backup Agent se nemůže připojit ke službě obengine", ověřte, zda obengine uvedena v seznamu služeb spuštěných v počítači. Pokud není spuštěna služba obengine uvedena, použijte příkaz "net start obengine uvedena" zahájíte službě obengine.
| Backup | Vytvoření bodu obnovení online se nezdařilo | Pokud chybová zpráva říká "šifrovací přístupové heslo pro tento server není nastaven. Konfigurujte šifrovací přístupové heslo,"zkuste konfigurace šifrovací přístupové heslo. Pokud se nezdaří, proveďte následující kroky: <br> <ol><li>Ověřte, zda pomocné umístění existuje. Toto je umístění, které je uvedený v registru **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, s názvem **ScratchLocation** by měla existovat.</li><li> Pokud pomocné umístění existuje, zkuste se znovu zaregistrovat pomocí staré heslo. *Vždy, když konfigurujete šifrovací přístupové heslo, uložte na bezpečném místě.*</li><ol>
| Backup | Selhání zálohování pro úplné obnovení systému | Pokud je velikost BMR velká, přesunout některé soubory aplikace na jednotku operačního systému a opakujte. |
| Backup | Možnost znovu nastavit ochranu virtuálního počítače VMware na nový Server Microsoft Azure Backup nezobrazuje jako dostupné pro přidání. | Na instanci serveru Microsoft Azure Backup starý, vyřazeno jsou odkazoval vlastnosti VMware. K vyřešení tohoto problému:<br><ol><li>V systému VCenter (SC-VMM ekvivalentní), přejděte na **Souhrn** kartě a pak na **vlastní atributy**.</li>  <li>Odstraňte starý název serveru služby Microsoft Azure Backup z **DPMServer** hodnotu.</li>  <li>Přejděte zpět na nový server Microsoft Azure Backup a upravit stránky  Po výběru **aktualizovat** tlačítko se zobrazí se zaškrtávacím políčkem jako dostupný pro přidání do ochrany virtuálního počítače.</li></ol> |
| Backup | Chyba při přístupu k soubory nebo sdílené složky | Zkuste upravit nastavení antivirový dle pokynů v článku na webu TechNet [spuštění antivirového softwaru na serveru aplikace DPM](https://technet.microsoft.com/library/hh757911.aspx).|
| Backup | Selhání úlohy vytvoření bodu obnovení online pro virtuální počítač VMware. Aplikace DPM došlo k chybě z VMware při pokusu o získání informací o vlastnost ChangeTracking. ErrorCode – FileFaultFault (ID 33621) |  <ol><li> Resetovat CTK VMware pro ovlivněné virtuální počítače.</li> <li>Zkontrolujte, zda že tento nezávislé disk není v místě VMware.</li> <li>Ukončete ochranu pro ovlivněné virtuální počítače a znovu ochranu s **aktualizovat** tlačítko. </li><li>Spusťte kopie pro ovlivněné virtuální počítače.</li></ol>|


## <a name="change-passphrase"></a>Změnit heslo
| Operace | Detaily chyby | Alternativní řešení |
| --- | --- | --- |
| Změnit heslo |Zabezpečení kódu PIN, který byl zadán je nesprávný. Zadejte správné zabezpečení PIN kód pro dokončení této operace. |**Příčina:**<br/> Tato chyba nastane, když zadáte neplatnou nebo vypršelo zabezpečení PIN kódu, když provádíte kritické operace (jako je například změna přístupové heslo). <br/>**Doporučená akce:**<br/> K dokončení operace, je nutné zadat platný zabezpečení PIN kódu. Chcete-li získat kód PIN, přihlaste se k portálu Azure a přejděte k trezor služeb zotavení. Pak přejděte na **nastavení** > **vlastnosti** > **generování kódu PIN zabezpečení**. Chcete-li změnit heslo, použijte tento PIN kód. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>K této chybě dojde, když je nastavení zabezpečení povoleno, nebo když se pokusíte změnit heslo, když používáte nepodporovanou verzi.<br/>**Doporučená akce:**<br/> Chcete-li změnit heslo, je nejprve nutné aktualizovat agenta zálohování na minimální verzi, která je 2.0.9052. Také musíte aktualizovat serveru Azure Backup na minimum update 1 a pak zadejte platný zabezpečení PIN kódu. Chcete-li získat kód PIN, přihlaste se k portálu Azure a přejděte k trezor služeb zotavení. Pak přejděte na **nastavení** > **vlastnosti** > **generování kódu PIN zabezpečení**. Chcete-li změnit heslo, použijte tento PIN kód. |


## <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení
| Operace | Detaily chyby | Alternativní řešení |
| --- | --- | --- |
| Nastavení e-mailová oznámení pomocí účtu Office 365 |ID chyby: 2013| **Příčina:**<br> Při pokusu o použití účtu Office 365 <br>**Doporučená akce:**<ol><li> První věc zajistit je, že "Povolit anonymní předávání na přijímat konektor" pro váš server aplikace DPM je nastavený na serveru Exchange. Další informace o tom, jak nakonfigurovat tuto najdete v tématu [povolit anonymní předávání konektoru přijímat](http://technet.microsoft.com/library/bb232021.aspx) na webu TechNet.</li> <li> Pokud nemůžete použít interní předávací doména SMTP a muset nastavit pomocí serveru Office 365, můžete nastavit IIS jako předávací hostitel. Nakonfigurujte server DPM za účelem [předávání pomocí služby IIS SMTP k O365](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **Důležité:** nezapomeňte použít user@domain.com formátu a *není* doména\uživatel.<br><br><li>Aplikaci DPM bodu použít název místního serveru jako SMTP server, portu 587. Potom se k e-mailu uživatel, který e-maily musí pocházet z.<li> Uživatelské jméno a heslo na stránce instalačního programu aplikace DPM SMTP musí být pro účet domény v doméně, která je aplikace DPM na. </li><br> **Poznámka:**: Pokud měníte adresu serveru SMTP, proveďte požadovanou změnu nové nastavení, nastavení okno zavřít a znovu ji ujistit se odráží novou hodnotu.  Nová nastavení vstoupila v platnost, takže jeho otestováním tímto způsobem je osvědčeným postupem nemusí vždy způsobit jednoduše změna a testování.<br><br>V průběhu tohoto procesu, můžete toto nastavení vymazat zavření konzoly DPM a úpravou těchto klíčů registru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> odstranit SMTPPassword a Klíče SMTPUserName**. Přidáním zpět do uživatelského rozhraní při znovu spusťte.
