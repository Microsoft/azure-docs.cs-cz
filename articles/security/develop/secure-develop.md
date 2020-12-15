---
title: Vývoj zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je třeba zvážit během fáze implementace a ověření projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 421fb7b0c91171756f55ad25c918955870054e3e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511276"
---
# <a name="develop-secure-applications-on-azure"></a>Vývoj zabezpečených aplikací v Azure
V tomto článku jsou uvedeny bezpečnostní aktivity a ovládací prvky, které je potřeba vzít v úvahu při vývoji aplikací pro Cloud. Pojednává o bezpečnostních otázkách a konceptech, které je potřeba vzít v úvahu během fáze implementace a ověření v rámci služby [SDL (Microsoft Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) . Cílem je pomáhat vám definovat aktivity a služby Azure, které můžete použít k vývoji bezpečnější aplikace.

V tomto článku jsou uvedené tyto fáze SDL:

- Implementace
- Ověření

## <a name="implementation"></a>Implementace
Cílem fáze implementace je vytvořit osvědčené postupy pro včasnou prevenci a zjistit a odebrat problémy zabezpečení z kódu.
Předpokládejme, že se vaše aplikace bude používat způsobem, který jste nechtěli použít. To vám pomůže chránit proti náhodnému nebo úmyslnému zneužití vaší aplikace.

### <a name="perform-code-reviews"></a>Provést revize kódu

Před vrácením kódu se změnami proveďte [Revize kódu](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) a zvyšte tak celkovou kvalitu kódu a snižte riziko vytváření chyb. Můžete použít [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) ke správě procesu revize kódu.

### <a name="perform-static-code-analysis"></a>Provedení analýzy statického kódu

[Statická analýza kódu](https://owasp.org/www-community/controls/Static_Code_Analysis) (označovaná také jako *analýza zdrojového kódu*) se obvykle provádí jako součást revize kódu. Analýza statického kódu obvykle odkazuje na spouštění nástrojů pro analýzu statického kódu pro nalezení potenciálních chyb zabezpečení v nespuštěném kódu pomocí technik, jako je [Kontrola chuti](https://en.wikipedia.org/wiki/Taint_checking) a [Analýza toku dat](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace nabízí [vývojářské nástroje](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) , které provádějí analýzu statického kódu a pomáhají s revizemi kódu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Ověření a upravení všech vstupů aplikace

Zacházet se všemi vstupy jako nedůvěryhodnými k ochraně aplikace před nejběžnějšími ohroženími zabezpečení webových aplikací. Nedůvěryhodná data jsou vozidlo pro útoky prostřednictvím injektáže. Vstup pro vaši aplikaci zahrnuje parametry v adrese URL, vstup od uživatele, data z databáze nebo z rozhraní API a cokoli, co se předává v tom, že uživatel může potenciálně manipulovat. Aplikace by měla [ověřit](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) , že data jsou syntakticky a sémanticky platná předtím, než aplikace použije data jakýmkoli způsobem (včetně jejich zobrazení zpět uživateli).

Ověřte vstup na začátku v toku dat, abyste zajistili, že pracovní postup bude do pracovního postupu zajišťovat pouze správně vytvořená data. Nechcete, aby ve vaší databázi trvaly poškozená data, nebo aby se v součásti pro příjem dat aktivovala selhání.

Blocklisting a allowlisting jsou dva obecné přístupy k provádění ověřování zadáním syntaxe:

  - Blocklisting se pokusí ověřit, že zadaný uživatelský vstup neobsahuje "známý jako škodlivý" obsah.

  - Allowlisting se pokusí ověřit, jestli daný vstup uživatele odpovídá sadě "známých dobrých" vstupů. Znaková allowlisting je forma allowlisting, kde aplikace kontroluje, zda vstup uživatele obsahuje pouze "známé" znaky, nebo že vstup odpovídá známému formátu.
    To může zahrnovat například kontrolu, že uživatelské jméno obsahuje pouze alfanumerické znaky nebo že obsahuje přesně dvě čísla.

Allowlisting je upřednostňovaným přístupem k sestavování zabezpečeného softwaru.
Blocklisting je náchylná k chybě, protože není možné považovat úplný seznam potenciálně špatného vstupu.

Proveďte tuto činnost na serveru, nikoli na straně klienta (nebo na straně serveru a na straně klienta).

### <a name="verify-your-applications-outputs"></a>Ověření výstupů aplikace

Jakýkoli výstup, který prezentujete vizuálně nebo v dokumentu, by měl být vždy kódovaný a řídicí. [Uvozovací znaky](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), označované také jako *výstupní kódování*, slouží k zajištění, že nedůvěryhodná data nejsou vozidlo pro útok na injektáže. Uvozovací znaky kombinované s ověřováním dat poskytují vrstvenou obranu za účelem zvýšení zabezpečení systému jako celku.

Uvozovací znaky zajistí, že se všechny zobrazují jako *výstup.* Uvozovací znaky také umožňuje Překladači zjistit, že data nejsou určena ke spuštění, a tím zabráníte útokům v práci. Toto je další běžná technika útoku označovaná jako *skriptování mezi weby* (XSS).

Pokud používáte webovou architekturu od třetí strany, můžete ověřit možnosti pro výstupní kódování na webech pomocí [listu tahák pro ochranu OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Použití parametrizovaných dotazů při kontaktování databáze

Nikdy nevytvářejte vložený databázový dotaz "průběžně" v kódu a odešlete ho přímo do databáze. Škodlivý kód vložený do vaší aplikace může potenciálně způsobit odcizení, vymazání nebo úpravu databáze. Vaši aplikaci je také možné použít ke spouštění škodlivých příkazů operačního systému v operačním systému, který hostuje vaši databázi.

Místo toho použijte parametrizované dotazy nebo uložené procedury. Použijete-li parametrizované dotazy, můžete vyvolat proceduru z kódu bezpečně a předat ji řetězci bez obav, že bude zpracována jako součást příkazu dotazu.

### <a name="remove-standard-server-headers"></a>Odebrat standardní hlavičky serveru

Hlavičky, jako jsou server, X se systémem a X-AspNet – verze, odhalují informace o serveru a základních technologiích. Doporučujeme potlačit tyto hlavičky, abyste se vyhnuli otiskům prstů aplikace.
Podívejte se [na téma odebrání standardních hlaviček serveru na webech Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Oddělení provozních dat

Vaše produkční data nebo "skutečná" data by se neměla používat pro vývoj, testování nebo jiné účely, než jaké jsou zamýšlené v podniku. Pro všechny účely vývoje a testování by se měla použít maskovaná datová sada ([anonymitá](https://en.wikipedia.org/wiki/Data_anonymization)).

To znamená, že přístup k vašim skutečným datům má méně lidí, což snižuje plochu útoku. Znamená to také méně zaměstnanců, kteří uvidí osobní údaje, což eliminuje případné porušení důvěrnosti.

### <a name="implement-a-strong-password-policy"></a>Implementace zásad silného hesla

Aby bylo možné chránit před hrubou silou a odhadem na základě slovníku, je nutné implementovat zásady silného hesla, aby uživatelé mohli vytvářet složitá hesla (například 12 znaků minimální délky a vyžadovat alfanumerické a speciální znaky).

Rozhraní identity můžete použít k vytvoření a prosazování zásad hesel. Azure AD B2C vám pomůže se správou hesel tím, že poskytuje [předdefinované zásady](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [Samoobslužné resetování hesla](../../active-directory-b2c/user-flow-self-service-password-reset.md)a další služby.

Aby se zabránilo útokům na výchozí účty, ověřte, že jsou všechny klíče a hesla nahraditelný a že jsou vygenerované nebo nahrazené po instalaci prostředků.

Pokud aplikace musí automaticky generovat hesla, ujistěte se, že vygenerovaná hesla jsou náhodná a že mají vysokou entropii.

### <a name="validate-file-uploads"></a>Ověřit nahrávání souborů

Pokud vaše aplikace umožňuje [nahrávání souborů](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), zvažte opatření, která můžete pro tuto rizikové aktivity provést. Prvním krokem v mnoha útokech je získání škodlivého kódu do systému, který je napadený. K tomu může útočník využít nahrávání souboru. OWASP nabízí řešení pro ověřování souboru, aby se zajistilo, že soubor, který odesíláte, je bezpečný.

Ochrana proti malwaru pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Můžete nainstalovat [Microsoft Antimalware](../fundamentals/antimalware.md) nebo řešení ochrany koncového bodu Microsoft Partner Microsoftu ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)a [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](../fundamentals/antimalware.md) obsahuje funkce, jako je ochrana v reálném čase, plánované prohledávání, náprava malwaru, aktualizace signatur, aktualizace modulu, vytváření sestav ukázek a shromažďování událostí vyloučení. Pomocí [Azure Security Center](../../security-center/security-center-partner-integration.md) můžete integrovat řešení Microsoftu proti malwaru a partnerům, aby se usnadnilo nasazení a vestavěné detekce (výstrahy a incidenty).

### <a name="dont-cache-sensitive-content"></a>Neukládat citlivý obsah do mezipaměti

Nepoužívejte v prohlížeči ukládání citlivého obsahu do mezipaměti. Prohlížeče můžou ukládat informace pro ukládání do mezipaměti a historii. Soubory uložené v mezipaměti se ukládají do složky, jako je například složka dočasných souborů Internetu, v případě aplikace Internet Explorer. Když se tyto stránky označují znovu, prohlížeč zobrazí stránky ze své mezipaměti. Pokud se uživateli zobrazí citlivé informace (adresa, podrobnosti kreditní karty, číslo sociálního pojištění, uživatelské jméno), mohou být informace uloženy v mezipaměti prohlížeče a lze je získat prozkoumáním mezipaměti prohlížeče nebo pouhým stisknutím tlačítka **zpět** v prohlížeči.

## <a name="verification"></a>Ověření
Fáze ověření zahrnuje komplexní úsilí, které zajistí, že kód bude vyhovovat zabezpečení a ochraně osobních údajů principy, které byly vytvořeny v předchozích fázích.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Vyhledání a oprava chyb zabezpečení v závislostech aplikace

Provedete kontrolu aplikace a jejích závislých knihoven, abyste identifikovali všechny známé ohrožené součásti. K dispozici jsou produkty, které jsou k dispozici k provedení tohoto hledání, včetně [OWASP závislostí](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)a [černých kachen](https://www.blackducksoftware.com/).

Pro Azure App Service Web Apps je k dispozici kontrola ohrožení zabezpečení založená na [zabezpečení TINFOIL](https://www.tinfoilsecurity.com/) . [TINFOIL prověřování zabezpečení prostřednictvím App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) nabízí vývojářům a správcům rychlý, integrovaný a ekonomický způsob zjišťování a řešení ohrožení zabezpečení předtím, než ho škodlivý objekt actor může využít.

> [!NOTE]
> [Zabezpečení TINFOIL můžete také integrovat s Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Integrace zabezpečení TINFOIL s Azure AD poskytuje následující výhody:
>  - V Azure AD můžete řídit, kdo má přístup k TINFOIL zabezpečení.
>  - Uživatelé můžou být automaticky přihlášení k zabezpečení TINFOIL (jednotné přihlašování) pomocí svých účtů Azure AD.
>  - Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

### <a name="test-your-application-in-an-operating-state"></a>Testování aplikace v provozním stavu

Testování dynamického zabezpečení aplikací (DAST) je proces testování aplikace v operačním stavu za účelem nalezení slabých chyb zabezpečení. Nástroje DAST analyzují programy, když se spouštějí, aby našli chyby zabezpečení, například poškození paměti, nezabezpečenou konfiguraci serveru, skriptování mezi weby, problémy s uživatelskými oprávněními, injektáže SQL a další důležité aspekty zabezpečení.

DAST se liší od statického testování zabezpečení aplikací (SAST). Nástroje SAST analyzují zdrojový kód nebo zkompilované verze kódu, když kód není spuštěn, aby bylo možné najít chyby zabezpečení.

Proveďte DAST, nejlépe s asistencí odborníka na zabezpečení ( [Tester pro průnik](../fundamentals/pen-testing.md) nebo posuzovatel ohrožení zabezpečení). Pokud není k dispozici specialista zabezpečení, můžete DAST sami provádět pomocí webového proxy serveru a některých školení. Připojte se k DAST skeneru na začátku, abyste se ujistili, že do kódu nepřinášíte zjevné problémy zabezpečení. Seznam skenerů ohrožení zabezpečení webových aplikací najdete na webu [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) .

### <a name="perform-fuzz-testing"></a>Provést testování fuzzy

V případě neočekávaného [testování](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)vystavíte selhání programu záměrně, když do aplikace zavedete poškozená nebo náhodná data. Selhání programu pomáhá odhalit potenciální problémy zabezpečení před vydáním aplikace.

[Detekce rizik se zabezpečením](https://www.microsoft.com/en-us/security-risk-detection/) je jedinečná služba pro nenáročné testování Microsoftu pro nalezení chyb kritických pro zabezpečení v softwaru.

### <a name="conduct-attack-surface-review"></a>Provést kontrolu na Surface útoků

Kontrola prostoru pro útoky po dokončení kódu pomáhá zajistit, že byly zváženy všechny změny návrhu nebo implementace v aplikaci nebo systému. Pomáhá zajistit, že všechny nové vektory útoku, které byly vytvořeny v důsledku změn, včetně modelů hrozeb, byly zkontrolovány a omezeny.

Pomocí prohledávání aplikace můžete vytvořit obrázek prostoru pro útoky. Microsoft nabízí analytický nástroj pro útoky, který se nazývá [analyzátor Surface útoků](https://www.microsoft.com/download/details.aspx?id=24487). Můžete si vybrat z mnoha komerčních nástrojů pro kontrolu a testování ohrožení zabezpečení, včetně [projektu proxy útoku OWASP zovaný](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)a [w3af](http://w3af.sourceforge.net/). Tyto skenovací nástroje procházejí vaši aplikaci a mapují části aplikace, které jsou přístupné přes web. Můžete také Hledat v Azure Marketplace podobných [vývojářských nástrojů](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Provádění testování průniku zabezpečení

Zajištění zabezpečení vaší aplikace je důležité jako testování jakékoli jiné funkce. Proveďte [testování průniku](../fundamentals/pen-testing.md) standardní části procesu sestavení a nasazení. Naplánujte pravidelné testy zabezpečení a kontrolu ohrožení zabezpečení u nasazených aplikací a sledujte otevřené porty, koncové body a útoky.

### <a name="run-security-verification-tests"></a>Spustit ověřovací testy zabezpečení

[Sada Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) obsahuje SVTs pro několik služeb platformy Azure. Tyto SVTs pravidelně spouštíte, abyste měli jistotu, že vaše předplatné Azure a různé prostředky, které tvoří vaši aplikaci, jsou v zabezpečeném stavu. Tyto testy můžete automatizovat také pomocí funkce rozšíření průběžná integrace/průběžné nasazování (CI/CD) v AzSK, která zpřístupňuje SVTs jako rozšíření sady Visual Studio.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme kontrolu zabezpečení a aktivity, které vám pomůžou navrhovat a nasazovat zabezpečené aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)