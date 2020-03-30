---
title: Vývoj zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je třeba zvážit během fáze implementace a ověřování projektu webové aplikace.
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
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255648"
---
# <a name="develop-secure-applications-on-azure"></a>Vývoj zabezpečených aplikací v Azure
V tomto článku uvádíme aktivity zabezpečení a ovládací prvky, které je třeba zvážit při vývoji aplikací pro cloud. Bezpečnostní otázky a koncepty, které je třeba zvážit během fází implementace a ověřování životního cyklu vývoje zabezpečení společnosti Microsoft [(SDL),](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) jsou zahrnuty. Cílem je pomoci vám definovat aktivity a služby Azure, které můžete použít k vývoji bezpečnější aplikace.

Následující fáze SDL jsou uvedeny v tomto článku:

- Implementace
- Ověření

## <a name="implementation"></a>Implementace
Fáze implementace se zaměřuje na zavedení osvědčených postupů pro včasnou prevenci a na detekci a odstranění bezpečnostních problémů z kódu.
Předpokládejme, že vaše aplikace bude použita způsoby, které jste neměli v úmyslu použít. To vám pomůže chránit před náhodným nebo úmyslným zneužitím vaší aplikace.

### <a name="perform-code-reviews"></a>Provádění kontrol kódu

Než začnete kontrolovat kód, proveďte [revize kódu,](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) abyste zvýšili celkovou kvalitu kódu a snížili riziko vytváření chyb. [Pomocí sady Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) můžete spravovat proces kontroly kódu.

### <a name="perform-static-code-analysis"></a>Provedení analýzy statického kódu

[Analýza statického kódu](https://www.owasp.org/index.php/Static_Code_Analysis) (označovaná také jako *analýza zdrojového kódu)* se obvykle provádí jako součást revize kódu. Analýza statického kódu běžně odkazuje na spuštění nástrojů pro analýzu statického kódu k nalezení potenciálních chyb zabezpečení v neběžícím kódu pomocí technik, jako [je kontrola počitadla](https://en.wikipedia.org/wiki/Taint_checking) a [analýza toku dat](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace nabízí [vývojářské nástroje,](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) které provádějí analýzu statického kódu a pomáhají s revizemi kódu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Ověření a dezinfekce každého vstupu pro vaši aplikaci

Považovat všechny vstupy za nedůvěryhodné, aby byla vaše aplikace chráněna před nejčastějšími chybami zabezpečení webových aplikací. Nedůvěryhodná data jsou prostředkem pro vstřikovací útoky. Vstup pro vaši aplikaci obsahuje parametry v adrese URL, vstup od uživatele, data z databáze nebo z rozhraní API a vše, co je předánv tom, že uživatel může potenciálně manipulovat. Aplikace by měla [ověřit,](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) že data jsou syntakticky a sémanticky platná, než aplikace data jakýmkoli způsobem použije (včetně zobrazení zpět uživateli).

Ověřte vstup v rané fázi toku dat, abyste zajistili, že do pracovního postupu vstoupí pouze správně vytvořená data. Nechcete, aby poškozená data přetrvávala v databázi nebo spouštěla selhání v podřízené součásti.

Blacklisting a whitelisting jsou dva obecné přístupy k provádění ověření vstupní syntaxe:

  - Blacklisting se pokusí zkontrolovat, zda daný uživatelský vstup neobsahuje obsah "známý jako škodlivý".

  - Whitelisting se pokusí zkontrolovat, zda daný vstup uživatele odpovídá sadě vstupů "známého zboží". Seznam povolených znaků je forma whitelistingu, kde aplikace kontroluje, že vstup uživatele obsahuje pouze znaky "známé dobré" nebo že vstup odpovídá známému formátu.
    To může například zahrnovat kontrolu, že uživatelské jméno obsahuje pouze alfanumerické znaky nebo že obsahuje přesně dvě čísla.

Whitelisting je preferovaný přístup pro vytváření zabezpečeného softwaru.
Blacklisting je náchylný k chybám, protože je nemožné myslet na úplný seznam potenciálně špatný vstup.

Proveďte tuto práci na serveru, nikoli na straně klienta (nebo na serveru a na straně klienta).

### <a name="verify-your-applications-outputs"></a>Ověření výstupů aplikace

Všechny výstupy, které prezentujete vizuálně nebo v rámci dokumentu, by měly být vždy zakódovány a uvozeny. [Escapování](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), označované také jako *kódování výstupu*, se používá k zajištění, že nedůvěryhodná data nejsou nástrojem pro vstřikovací útok. Escapování, v kombinaci s ověřením dat, poskytuje vrstvené obrany pro zvýšení zabezpečení systému jako celku.

Únik zajišťuje, že vše je zobrazeno jako *výstup.* Escaping také umožňuje interpretu vědět, že data nejsou určena k provedení, a to zabraňuje útokům z práce. Jedná se o další běžnou techniku útoku, která se nazývá *skriptování mezi sítěmi* (XSS).

Pokud používáte webový rámec od třetí strany, můžete ověřit možnosti kódování výstupu na webových stránkách pomocí [cheat sheet prevence OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Použití parametrizovaných dotazů při kontaktování databáze

Nikdy nevytvářejte vsazený databázový dotaz "průběžně" v kódu a odesílejte jej přímo do databáze. Škodlivý kód vložený do aplikace může potenciálně způsobit odcizení, vymazání nebo úpravu databáze. Aplikace může být také použita ke spuštění škodlivých příkazů operačního systému v operačním systému, který je hostitelem databáze.

Místo toho použijte parametrizované dotazy nebo uložené procedury. Při použití parametrizované dotazy, můžete vyvolat postup z kódu bezpečně a předat řetězec bez obav, že bude považována za součást příkazu dotazu.

### <a name="remove-standard-server-headers"></a>Odebrání standardních záhlaví serveru

Záhlaví jako Server, X-Powered-By a X-AspNet-Version odhalují informace o serveru a základních technologiích. Doporučujeme potlačit tyto hlavičky, aby se zabránilo snímání otisků prstů aplikace.
Viz [odebrání standardních záhlaví serverů na webech Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Oddělení výrobních dat

Vaše produkční data, nebo "skutečné" data, by neměly být používány pro vývoj, testování, nebo jakýkoli jiný účel, než to, co firma zamýšlela. Pro veškerý vývoj a testování by měla být použita maskovaná ([anonymizovaná)](https://en.wikipedia.org/wiki/Data_anonymization)datová sada.

To znamená, že k vašim skutečným datům má přístup méně lidí, což snižuje vaši prostor pro útok. Znamená to také, že osobní údaje vidí méně zaměstnanců, což eliminuje možné porušení důvěrnosti.

### <a name="implement-a-strong-password-policy"></a>Implementace zásad silného hesla

Chcete-li se bránit proti hrubou silou a hádání založenéna slovníku, musíte implementovat zásady silné heslo zajistit, aby uživatelé vytvořit složité heslo (například 12 znaků minimální délka a vyžadující alfanumerické a speciální znaky).

K vytvoření a vynucení zásad hesel můžete použít rozhraní identity. Azure AD B2C vám pomůže se správou hesel tím, že poskytuje [integrované zásady](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [samoobslužné resetování hesla](../../active-directory-b2c/user-flow-self-service-password-reset.md)a další.

Chcete-li se bránit útokům na výchozí účty, ověřte, zda jsou všechny klíče a hesla nahraditelné a zda jsou po instalaci prostředků generována nebo nahrazena.

Pokud aplikace musí automaticky generovat hesla, ujistěte se, že vygenerovaná hesla jsou náhodná a že mají vysokou entropii.

### <a name="validate-file-uploads"></a>Ověřit odeslání souborů

Pokud vaše aplikace umožňuje [nahrávání souborů](https://www.owasp.org/index.php/Unrestricted_File_Upload), zvažte opatření, která můžete přijmout pro tuto rizikovou aktivitu. Prvním krokem v mnoha útocích je dostat nějaký škodlivý kód do systému, který je pod útokem. Použití nahrávání souborů pomáhá útočníkovi toho dosáhnout. OWASP nabízí řešení pro ověření souboru, aby bylo zajištěno, že soubor, který nahráváte, je bezpečný.

Antimalwarová ochrana pomáhá identifikovat a odstranit viry, spyware a další škodlivý software. Můžete nainstalovat [program Microsoft Antimalware](../fundamentals/antimalware.md) nebo řešení ochrany koncového bodu partnera společnosti Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)a [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](../fundamentals/antimalware.md) obsahuje funkce, jako je ochrana v reálném čase, plánované skenování, náprava malwaru, aktualizace podpisů, aktualizace motorů, ukázky hlášení a shromažďování událostí vyloučení. Antimalwarová a partnerská řešení Microsoftu můžete integrovat pomocí [Azure Security Center](../../security-center/security-center-partner-integration.md) pro snadné nasazení a integrované detekce (výstrahy a incidenty).

### <a name="dont-cache-sensitive-content"></a>Neukládat do mezipaměti citlivý obsah

Neukládat citlivý obsah do mezipaměti v prohlížeči. Prohlížeče mohou ukládat informace pro ukládání do mezipaměti a historii. Soubory uložené v mezipaměti jsou uloženy ve složce, jako je složka Dočasné soubory Internetu, v případě aplikace Internet Explorer. Když jsou tyto stránky znovu odkazovány, prohlížeč zobrazí stránky ze své mezipaměti. Pokud se uživateli zobrazí citlivé informace (adresa, údaje o kreditní kartě, číslo sociálního pojištění, uživatelské jméno), mohou být tyto informace uloženy v mezipaměti prohlížeče a mohou být možné je získat kontrolou mezipaměti prohlížeče nebo pouhým stisknutím tlačítka **Zpět** prohlížeče.

## <a name="verification"></a>Ověření
Ověřovací fáze zahrnuje komplexní úsilí o zajištění toho, aby kodex splňoval zásady zabezpečení a ochrany osobních údajů, které byly stanoveny v předchozích fázích.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Vyhledání a oprava slabých míst v závislostech aplikací

Prohledáte aplikaci a její závislé knihovny, abyste identifikovali všechny známé ohrožené součásti. Mezi produkty, které jsou k dispozici pro provedení této [kontroly, patří Kontrola závislostí OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)a [Černá kachna](https://www.blackducksoftware.com/).

Pro webové aplikace Azure App Service Web Apps je k dispozici prohledávání zranitelnosti využívající zabezpečení [tinfoil.](https://www.tinfoilsecurity.com/) [Tinfoil Security scanning prostřednictvím služby App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) nabízí vývojářům a správcům rychlý, integrovaný a ekonomický způsob zjišťování a řešení chyb zabezpečení dříve, než je škodlivý objekt může využít.

> [!NOTE]
> Zabezpečení [tinfoilů](../../active-directory/saas-apps/tinfoil-security-tutorial.md)můžete také integrovat s Azure AD . Integrace zabezpečení alobalu s Azure AD vám poskytuje následující výhody:
>  - Ve službě Azure AD můžete řídit, kdo má přístup k zabezpečení tinfoil.
>  - Vaši uživatelé mohou být automaticky přihlášeni k zabezpečení tinfoil (jednotné přihlašování) pomocí svých účtů Azure AD.
>  - Své účty můžete spravovat v jediném centrálním umístění, na webu Azure Portal.

### <a name="test-your-application-in-an-operating-state"></a>Testování aplikace v provozním stavu

Dynamické testování zabezpečení aplikací (DAST) je proces testování aplikace v provozním stavu za účelem nalezení slabých míst zabezpečení. Nástroje DAST analyzují programy při jejich provádění, aby nalezly chyby zabezpečení, jako je poškození paměti, nezabezpečená konfigurace serveru, skriptování mezi sítěmi, problémy s oprávněními uživatelů, vkládání SQL a další důležité problémy se zabezpečením.

DAST se liší od testování zabezpečení statické aplikace (SAST). Nástroje SAST analyzují zdrojový kód nebo zkompilované verze kódu, když se kód neprovádí, aby našel chyby zabezpečení.

Proveďte DAST, nejlépe s pomocí bezpečnostního odborníka [(tester penetračního testu](../fundamentals/pen-testing.md) nebo posuzovatel zranitelnosti). Pokud odborník na zabezpečení není k dispozici, můžete dast provést sami pomocí webového proxy skeneru a některých školení. Připojte skener DAST včas, abyste zajistili, že do kódu nezavedete zjevné problémy se zabezpečením. Seznam skenerů ohrožení zabezpečení webových aplikací naleznete na webu [OWASP.](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)

### <a name="perform-fuzz-testing"></a>Proveďte testování fuzz

Při [testování fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), můžete vyvolat selhání programu záměrným zavedením poškozených nebo náhodných dat do aplikace. Vyvolání selhání programu pomáhá odhalit potenciální problémy se zabezpečením před vydáním aplikace.

[Detekce bezpečnostních rizik](https://docs.microsoft.com/security-risk-detection/) je jedinečná testovací služba společnosti Microsoft pro hledání chyb v softwaru kritických pro zabezpečení.

### <a name="conduct-attack-surface-review"></a>Proveďte kontrolu povrchu útoku

Kontrola povrchu útoku po dokončení kódu pomáhá zajistit, že byly zváženy všechny změny návrhu nebo implementace aplikace nebo systému. Pomáhá zajistit, že všechny nové způsoby útoku, které byly vytvořeny v důsledku změn, včetně modelů hrozeb, byly přezkoumány a zmírněny.

Můžete vytvořit obrázek o povrchu útoku skenováním aplikace. Společnost Microsoft nabízí nástroj pro analýzu povrchu útoku s názvem [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Můžete si vybrat z mnoha komerčních dynamických testovacích a zranitelných nástrojů nebo služeb, včetně [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)a [w3af](http://w3af.sourceforge.net/). Tyto nástroje pro skenování procházet aplikaci a mapovat části aplikace, které jsou přístupné přes web. Na Azure Marketplace můžete taky hledat podobné [vývojářské nástroje](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Provedení testování průniku zabezpečení

Zajištění zabezpečení aplikace je stejně důležité jako testování jiných funkcí. Udělejte [penetrační testování](../fundamentals/pen-testing.md) standardní součástí procesu sestavení a nasazení. Naplánujte pravidelné testy zabezpečení a prohledávání chyb zabezpečení v nasazených aplikacích a sledujte otevřené porty, koncové body a útoky.

### <a name="run-security-verification-tests"></a>Spuštění ověřovacích testů zabezpečení

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) obsahuje SVT pro více služeb platformy Azure. Tyto spouštění SVTs pravidelně zajistit, že vaše předplatné Azure a různé prostředky, které tvoří vaše aplikace jsou v zabezpečeném stavu. Můžete také automatizovat tyto testy pomocí průběžné integrace/průběžné nasazení (CI/CD) rozšíření funkce AzSK, který zpřístupňuje SVTs jako rozšíření sady Visual Studio.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme ovládací prvky zabezpečení a aktivity, které vám pomohou navrhovat a nasazovat zabezpečené aplikace.

- [Navrhujte zabezpečené aplikace](secure-design.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)
