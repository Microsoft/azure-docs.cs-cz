---
title: Vývoj zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které byste měli zvážit během fáze implementace a ověření vašeho projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f68f6c366571b8f0f04f8507606c1a4008ab0405
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443244"
---
# <a name="develop-secure-applications-on-azure"></a>Vývoj zabezpečených aplikací v Azure
V tomto článku Představujeme aktivity související se zabezpečením a ovládací prvky, které je třeba zvážit při vývoji aplikací pro cloud. Bezpečnostní otázky a koncepty, které je vhodné zvážit během fáze implementace a ověření Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) se vztahuje. Cílem je může pomoci určit činností a služeb Azure, které můžete použít k vývoji bezpečnější aplikace.

Následující fáze SDL jsou popsané v tomto článku:

- Implementace
- Ověření

## <a name="implementation"></a>Implementace
Cílem této fáze je zavést osvědčené postupy pro počáteční ochrany před únikem informací a detekovat a odstraňovat problémy se zabezpečením z kódu.
Předpokládejme, že vaše aplikace se použije způsobem, že jste nechtěli jeho používání. To umožňuje ochranu proti zneužití náhodného nebo záměrného vaší aplikace.

### <a name="perform-code-reviews"></a>Provedení revize kódu

Před vrácením se změnami kódu, proveďte [revizemi kódu](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) ke zvýšení celkové kvality kódu a snížení riziko vytváření chyb. Můžete použít [sady Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) spravovat proces revize kódu.

### <a name="perform-static-code-analysis"></a>Analýza statického kódu

[Analýza statického kódu](https://www.owasp.org/index.php/Static_Code_Analysis) (označované také jako *zdroje analýzy kódu*) se obvykle provádí jako součást přezkoumání kódu. Analýza statického kódu obvykle odkazuje na spuštění najít potenciální nedostatky zabezpečení bez spuštění kódu pomocí postupů, jako jsou nástroji pro analýzu statického kódu [taint kontrola](https://en.wikipedia.org/wiki/Taint_checking) a [analýzy toku dat](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace nabízí [vývojářské nástroje](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) , provádět analýzu statického kódu a pomoci v oblastech revize kódu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Ověřit a sanitizovat každý vstup pro vaši aplikaci

Považovat za všechny vstupy nedůvěryhodné k ochraně vaší aplikace z nejběžnějších ohrožení zabezpečení webových aplikací. Nedůvěryhodná data je prostředkem pro útoky prostřednictvím injektáže. Vstup pro vaše aplikace obsahuje parametry v adrese URL vstupy od uživatele, data z databáze nebo z rozhraní API a cokoli, co je předáno, která uživatel může potenciálně manipulaci. Aplikace by měla [ověření](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) , že data jsou syntakticky a sémanticky platný předtím, než se aplikace používá data žádným způsobem (včetně zobrazení zpět na uživatele).

Ověření vstupu již v rané fázi v toku dat k zajištění, že pouze správně vytvořená data přejde do pracovního postupu. Nechcete poškozených datových uchování v databázi nebo aktivuje selhání v podřízené součásti.

Blacklisting a přidávání na seznam povolených jsou dvě obecné metody k provedení ověření vstupu syntaxi:

  - Adaptivně se pokusí zkontrolovat, že daný uživatelský vstup neobsahuje "známé škodlivé" obsah.

  - Přidávání na seznam povolených pokusí zkontrolovat, že daný uživatelský vstup odpovídá sadu vstupů "známé dobré". Pracující na seznam povolených je forma přidávání na seznam povolených, kde aplikace zkontroluje, zda uživatelský vstup obsahuje pouze "známé dobré" znaky nebo, že vstup odpovídá známý formát.
    Například to může zahrnovat, kontrola, že uživatelské jméno obsahuje pouze alfanumerické znaky nebo obsahuje přesně dvě číslice.

Přidávání na seznam povolených je upřednostňovaný způsob pro vytváření zabezpečených softwaru.
Seznamů zakázaných je náchylné k chybám, protože není možné si můžete představit úplný seznam potenciálně chybná vstup.

Provést tuto práci na serveru, ne na straně klienta (nebo na serveru a na straně klienta).

### <a name="verify-your-applications-outputs"></a>Ověřte výstupy vaší aplikace

Žádný výstup, který je k dispozici vizuálně nebo v rámci dokumentu by měla vždy kódování a uvozeny řídicími znaky. [Uvozovací znaky](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), označované také jako *kódování výstupu*, se používají k zajištění, že nedůvěryhodná data není prostředkem pro útok prostřednictvím injektáže. Uvozovací znaky, v kombinaci s ověření dat, poskytuje vrstvami ochrany pro zvýšení zabezpečení systému jako celek.

Uvozovacích znaků je, že tento všechno, co se zobrazuje jako *výstup.* Uvozovací znaky také umožňuje překladač vědět, že data není určena pro provést, a tím je zabráněno útokům z pracovní. Toto je další běžné techniky útoku označované jako *skriptování napříč weby* (XSS).

Pokud používáte webovou architekturu od třetí strany, můžete ověřit vaše možnosti pro výstup kódování na websites pomocí [OWASP XSS ochrany před únikem informací tahák](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Použití parametrizovaných dotazů při kontaktování databáze

Nikdy nevytvářejte dotazu vložené databáze "v reálném čase" ve vašem kódu a poslat ho přímo do databáze. Škodlivý kód vložení do vaší aplikace by mohla způsobit databáze do vám ho někdo ukradne, vymazanými nebo upravit. Aplikace může také použít ke spuštění příkazů škodlivý operačního systému v operačním systému, který je hostitelem databáze.

Místo toho použijte parametrizovaných dotazů nebo uložených procedur. Při použití parametrizovaných dotazů můžete bezpečně použít postup pro z vašeho kódu a předat řetězec bez obav, že bude zpracována jako součást příkazu dotazu.

### <a name="remove-standard-server-headers"></a>Odebrání hlaviček standardní server

Hlavičky, jako je Server, X-využívá –, a verze X AspNet zobrazit informace o serveru a základním technologiím. Doporučujeme, abyste potlačení tyto hlavičky, aby se zabránilo otisků aplikace.
Zobrazit [odebrání hlaviček standardní server na Azure websites na úrovni](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Oddělit provozních dat

Produkčních dat nebo "text real" data by neměl používat pro vývoj, testování nebo jakékoli jiné účely, než co obchodní určené. Maskované ([anonymizované](https://en.wikipedia.org/wiki/Data_anonymization)) datová sada by měla být použity pro všechny vývoje a testování.

To znamená, že máte přístup k reálným datům, což snižuje útoky menším počtem osob. Také znamená, že méně zaměstnancům, najdete v článku osobní údaje, které eliminuje na potenciální porušení dokáže upozornit v důvěrnosti.

### <a name="implement-a-strong-password-policy"></a>Implementovat zásady silné heslo

Se chránit před útoky hrubou silou a na základě slovníku opakovaně uhodnout, je nutné implementovat zásady silných hesel k zajištění, že uživatelé vytvářet složité heslo (například minimální délku 12 znaků a vyžadování alfanumerické a speciální znaky).

Můžete vytvářet a vynucovat zásady pro hesla rozšiřovatelnou platformu pro identitu. Azure AD B2C vám pomůže se správou hesel poskytnutím [integrované zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy), [samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)a provádění dalších akcí.

Se chránit před útoky zaměřenými na výchozích účtů, ověřte, že jsou všechny klíče a hesla nahraditelné a, že se jedná o vygenerované nebo nahradit po instalaci prostředky.

Pokud aplikace musí automatické generování hesla, ujistěte se, že generované hesla jsou náhodné a, ke kterým mají vysokou entropie.

### <a name="validate-file-uploads"></a>Ověření nahrávání souborů

Pokud vaše aplikace umožňuje [nahrávání souboru](https://www.owasp.org/index.php/Unrestricted_File_Upload), vezměte v úvahu bezpečnostní opatření, které můžete využít pro takovou rizikovou aktivitu. Prvním krokem v mnoha útokům je získat některé škodlivý kód do systému, který je terčem útoku. Pomocí možnosti nahrávání souborů pomáhá útočník dosáhnout. OWASP nabízí řešení pro ověření souboru tak, aby byl soubor, který jste nahrávání bezpečné.

Ochrany proti malwaru pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Můžete nainstalovat [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) nebo partnera společnosti Microsoft řešení ochrany koncových bodů ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10), a [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) zahrnuje funkce, jako je ochrana v reálném čase, naplánovanou kontrolu, malwarové nápravy, aktualizace signatur, aktualizace vyhledávacího stroje, ukázky reporting a vyloučení shromažďování událostí. Můžete integrovat Antimalware od Microsoftu a partnerských řešení pomocí [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) pro snadné nasazení a integrované detekce (výstrah a incidentů).

### <a name="dont-cache-sensitive-content"></a>Není citlivý obsah do mezipaměti

Nemusíte ukládat do mezipaměti citlivého obsahu v prohlížeči. Prohlížeči můžete ukládání informací o ukládání do mezipaměti a historie. Soubory uložené v mezipaměti jsou uloženy ve složce, jako je složka dočasných souborů Internetu, v případě aplikace Internet Explorer. Když se tyto stránky označují znovu v prohlížeči zobrazí na stránkách uloženou v mezipaměti. Pokud citlivých informací (adresu, údajů z platební karty, číslo sociálního pojištění, uživatelské jméno) se zobrazí uživateli, informace můžou být uložená v mezipaměti prohlížeče a bude nenávratně prozkoumáním mezipaměť prohlížeče nebo jednoduše stisknutím prohlížeče  **Zpět** tlačítko.

## <a name="verification"></a>Ověření
Fáze ověření zahrnuje komplexní úsilí na zajištění, že kód splňuje zásady zabezpečení a ochrana osobních údajů, vytvořené v předchozích fází.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Najít a opravit chyby zabezpečení v závislosti vašich aplikací

Můžete kontrolovat vaše aplikace a jeho závislé knihovny identifikovat všechny známé zranitelné komponenty. Produkty, které jsou k dispozici k provedení této kontroly patří [kontrolu závislostí OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), a [Black Duck](https://www.blackducksoftware.com/).

Zjišťování ohrožení zabezpečení využívající [Tinfoil Security](https://www.tinfoilsecurity.com/) je k dispozici pro Azure App Service Web Apps. [Tinfoil Security scanning. pomocí služby App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) nabízí vývojářům a správcům rychlé, integrované a nákladově výhodný způsob zjišťování a adresování ohrožení zabezpečení před škodlivý objekt actor mohou využít.

> [!NOTE]
> Můžete také [Tinfoil Security integrovat Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Integrace Tinfoil Security s Azure AD poskytuje následující výhody:
>  - Ve službě Azure AD můžete řídit, kdo má přístup k Tinfoil Security.
>  - Uživatelé mohou být automaticky přihlášeni ke Tinfoil Security (jednotné přihlašování) pomocí svých účtů služby Azure AD.
>  - Můžete spravovat své účty jednom centrálním umístění, na webu Azure portal.

### <a name="test-your-application-in-an-operating-state"></a>Otestujte aplikaci v provozní stav

Dynamická aplikace testování zabezpečení (DAST) je proces testování aplikace v provozní stav najít ohrožení zabezpečení. DAST nástroje Analýza programy, zatímco jsou spuštěny najít ohrožení zabezpečení, jako je poškození paměti, konfigurace nezabezpečené serveru, skriptování napříč weby, potíže s oprávnění uživatele, útok prostřednictvím injektáže SQL a další kritické bezpečnostní otázky.

DAST se liší od zabezpečení statické aplikace testování (SAST). Nástroje SAST analyzovat zdrojový kód nebo zkompilovány verzí kódu, když kód není executing. aby bylo možné najít nedostatky v zabezpečení.

Pokud možno provést DAST, pomoc odborníky na zabezpečení ( [testování průniku](https://docs.microsoft.com/azure/security/azure-security-pen-testing) nebo ohrožení zabezpečení assessor). Pokud není k dispozici odborníky na zabezpečení, můžete provést DAST sami pomocí skeneru webového proxy serveru a některé školení. Zařaďte DAST skener raném stádiu zajistit, že nezpůsobíte ze zřejmých bezpečnostních problémů do kódu. Zobrazit [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) lokality seznam kontroly ohrožení zabezpečení webové aplikace.

### <a name="perform-fuzz-testing"></a>Proveďte testování argumentu neurčité

V [argumentu neurčité testování](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), způsobit selhání programu záměrně zavedením poškozený nebo náhodných dat do aplikace. Nevyvolá selhání program pomáhá odhalit možné problémy zabezpečení před vydáním aplikace.

[Bezpečnostní riziko detekce](https://docs.microsoft.com/security-risk-detection/) je jedinečný argumentu neurčité Microsoft testování služby pro vyhledání kritické chyby v softwaru.

### <a name="conduct-attack-surface-review"></a>Provedení revize možností útoku

Kontrola útoky po doplňování kódu pomáhá zajistit, že všechny designu nebo implementace změny aplikace nebo považovány za systému. Pomáhá zajistit, že nové vektory útoku, které byly vytvořeny v důsledku změny, včetně modely hrozeb, byly zkontrolován a zmírnit.

Tím, že kontroluje aplikace můžete vytvářet představu o útoku. Společnost Microsoft nabízí nástroj pro analýzu útoku volá [útoku Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Můžete si vybrat z mnoha komerčních dynamické testování a nástrojů a služeb, včetně zjišťování ohrožení zabezpečení [projektu OWASP Zovaný útoku Proxy](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/), a [w3af](http://w3af.sourceforge.net/). Těchto nástrojů pro vyhledávání procházení vaší aplikace a mapování části aplikace, které jsou dostupné prostřednictvím webu. Můžete také vyhledat na webu Azure Marketplace pro podobné [vývojářské nástroje](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Provádět testování průniku zabezpečení

Zajištění, že je vaše aplikace zabezpečené je důležité jako další funkce testování. Ujistěte se, [testování průniku](https://docs.microsoft.com/azure/security/azure-security-pen-testing) standardní součástí procesu sestavení a nasazení. Plánování testů pravidelných bezpečnostních a zjišťování u nasazených aplikací, jejichž ohrožení zabezpečení a monitorování pro otevření portů, koncových bodů a útoky.

### <a name="run-security-verification-tests"></a>Spustit testy ověření zabezpečení

[Secure DevOps Kit pro Azure](https://azsk.azurewebsites.net/index.html) (AzSK) obsahuje SVTs pro víc služeb platformy Azure. Spuštění těchto SVTs pravidelně, aby se ujistit, že vaše předplatné Azure a různé prostředky, které tvoří vaši aplikaci stavu zabezpečení. Tyto testy můžete automatizovat pomocí průběžné integrace a nasazování (CI/CD) rozšíření funkce AzSK, který zpřístupňuje SVTs jako rozšíření sady Visual Studio.

## <a name="next-steps"></a>Další postup
V následujících článcích doporučujeme kontrolních mechanismů pro zabezpečení a aktivity, které vám pomůžou navrhnout a nasadit zabezpečené aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Nasazení zabezpečené aplikace](secure-deploy.md)
