---
title: Návrh zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je potřeba zvážit během fází požadavků a návrhů projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487673"
---
# <a name="design-secure-applications-on-azure"></a>Návrh zabezpečených aplikací v Azure
V tomto článku jsou uvedeny bezpečnostní aktivity a ovládací prvky, které je potřeba vzít v úvahu při návrhu aplikací pro Cloud. Pojednává o školicích materiálech spolu s bezpečnostními otázkami a koncepty, které je potřeba vzít v úvahu během fází vývoje požadavků a návrhů na [životní cyklus Microsoft Security Development (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) . Cílem je pomáhat vám definovat aktivity a služby Azure, které můžete použít k návrhu bezpečnější aplikace.

V tomto článku jsou uvedené tyto fáze SDL:

- Školení
- Požadavky
- Návrh

## <a name="training"></a>Školení
Než začnete vyvíjet cloudovou aplikaci, pochopíte si dobu zabezpečení a ochrany osobních údajů v Azure. Provedením tohoto kroku můžete snížit počet a závažnost zneužití ohrožení zabezpečení ve vaší aplikaci. Připravili jste příslušně na to, aby reagovala vhodně na na ni neustále se měnícím ohrožením.

Během školení použijte následující zdroje, abyste se seznámili se službami Azure, které jsou dostupné vývojářům a s osvědčenými postupy zabezpečení v Azure:

  - [Příručka pro vývojáře k Azure](https://azure.microsoft.com/campaigns/developer-guide/) vám ukáže, jak začít s Azure. V této příručce se dozvíte, které služby můžete používat ke spouštění aplikací, ukládání dat, začleňování inteligentních aplikací, sestavování aplikací IoT a k efektivnějšímu a bezpečnému nasazení řešení.

  - [Příručka Začínáme pro vývojáře v Azure](../../guides/developer/azure-developer-guide.md) poskytuje základní informace pro vývojáře, kteří chtějí začít používat platformu Azure pro potřeby vývoje.

  - Sady [SDK a nástroje](../../index.yml?pivot=sdkstools) popisují nástroje, které jsou k dispozici v Azure.

  - [Azure DevOps Services](/azure/devops/) poskytuje vývojové nástroje pro spolupráci. Mezi tyto nástroje patří kanály s vysokým výkonem, bezplatné úložiště Git, konfigurovatelné kanbanové desky a rozsáhlé automatizované testování zatížení založené na cloudu.
    [Centrum prostředků DevOps](/azure/devops/learn/) kombinuje naše prostředky pro vzdělávací postupy DevOps, správu verzí Git, agilní metody, způsob, jakým spolupracujeme s DevOps v Microsoftu, a jak si můžete vyhodnotit vlastní pokrok v DevOps.

  - [Hlavní 5 položek zabezpečení, které je třeba zvážit před odesláním do produkčního](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) prostředí, ukazuje, jak vám pomůžou zabezpečit webové aplikace v Azure a chránit vaše aplikace před nejběžnějšími a nebezpečnými útoky na webové aplikace.

  - [Sada Secure DevOps Kit pro Azure](https://azsk.azurewebsites.net/index.html) je kolekce skriptů, nástrojů, rozšíření a automatizace, které se v rámci služby DevOps pro týmy, které používají rozsáhlou automatizaci, využívají k komplexnímu předplatnému Azure a potřebám zabezpečení prostředků. Sada Secure DevOps Kit pro Azure vám může ukázat, jak plynule integrovat zabezpečení do vašich nativních pracovních postupů DevOps. Sada SDK adresuje nástroje jako testy zabezpečení (SVTs), které vývojářům pomůžou psát zabezpečený kód a testovat zabezpečenou konfiguraci svých cloudových aplikací ve fázích kódování a předčasného vývoje.

  - [Osvědčené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) poskytují sadu osvědčených postupů zabezpečení, které můžete použít při návrhu, nasazení a správě cloudových řešení pomocí Azure.

## <a name="requirements"></a>Požadavky
Fáze definice požadavků je zásadním krokem při definování toho, co je vaše aplikace a co se při vydání uvolní. Fáze požadavků je také čas, který se zamyslete nad ovládacími prvky zabezpečení, které budete sestavovat do vaší aplikace. V průběhu této fáze taky zahájíte kroky, které provedete v celém SDL, abyste měli jistotu, že vydáváte a nasazujete zabezpečenou aplikaci.

### <a name="consider-security-and-privacy-issues"></a>Zvažte problémy se zabezpečením a ochranou osobních údajů.
Tato fáze je nejlepší čas pro zvážení základních problémů zabezpečení a ochrany osobních údajů. Definování přijatelných úrovní zabezpečení a ochrany osobních údajů na začátku projektu pomáhá týmu:

- Pochopení rizik spojených s problémy se zabezpečením.
- Identifikujte a opravte chyby zabezpečení během vývoje.
- V celém projektu použijte zavedené úrovně zabezpečení a ochrany osobních údajů.

Při psaní požadavků pro aplikaci nezapomeňte vzít v úvahu ovládací prvky zabezpečení, které vám pomůžou zajistit bezpečnost aplikace a dat.

### <a name="ask-security-questions"></a>Zeptat se na bezpečnostní otázky
Zeptejte se na bezpečnostní otázky:

  - Obsahuje moje aplikace citlivá data?

  - Shromažďuje moje aplikace nebo ukládá data, která vyžadují, aby vyhovovala oborovým standardům a programům dodržování předpisů, jako je [Rada pro posuzování federálních finančních institucí (FFIEC)](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) nebo [standardy zabezpečení dat v oboru platební karty (PCI DSS)](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)?

  - Má moje aplikace shromažďovat nebo obsahovat citlivá osobní nebo zákaznická data, která je možné použít, a to buď samostatně, nebo s dalšími informacemi, identifikovat, kontaktovat nebo najít jednu osobu?

  - Shromažďuje moje aplikace nebo obsahuje data, která lze použít pro přístup k údajům o lékařské, vzdělávací, finanční nebo pracovní síti jednotlivce? Identifikace citlivosti vašich dat během fáze požadavků vám pomůže klasifikovat vaše data a identifikovat způsob ochrany dat, který budete používat pro svou aplikaci.

  - Kde a jak se ukládají moje data? Vezměte v úvahu, jak budete monitorovat služby úložiště, které vaše aplikace používá pro jakékoli neočekávané změny (například pomalejší doby odezvy). Budete moci ovlivnit protokolování a shromažďovat podrobnější data a analyzovat problém podrobněji?

  - Bude moje aplikace k dispozici veřejnosti (na internetu) nebo jenom interně? Pokud je vaše aplikace k dispozici pro veřejnost, jak chránit data, která se můžou shromažďovat nesprávným způsobem? Pokud je vaše aplikace dostupná jenom interně, zvažte, kdo ve vaší organizaci by měl mít přístup k aplikaci a jak dlouho mají mít přístup.

  - Chápete svůj model identity ještě před tím, než začnete navrhovat aplikaci? Jak zjistíte, že uživatelé říkají, kdo jsou a k čemu má uživatel autorizaci?

  - Provádí moje aplikace citlivé nebo důležité úkoly (například přenos peněz, odemknutí dveří nebo doručování lékařství)?
    Vezměte v úvahu, jak ověříte, že uživatel, který provádí citlivou úlohu, má autorizaci k provedení této úlohy a jak ověřit, že se jedná o osobu, kterou říkají. Autorizace (AuthZ) je zákonem, který umožňuje udělení oprávnění objektu zabezpečení ověřený. Ověřování (AuthN) je to, že je smluvní strana pro legitimní přihlašovací údaje náročná.

  - Provádí moje aplikace jakékoli rizikové softwarové aktivity, jako je například umožnění uživatelům odesílat nebo stahovat soubory nebo jiná data? Pokud vaše aplikace provádí rizikové aktivity, vezměte v úvahu, jak bude aplikace chránit uživatele před manipulací se škodlivými soubory nebo daty.

### <a name="review-owasp-top-10"></a>Zkontrolovat prvních 10 OWASP
Zvažte možnost projít si [<span class="underline">OWASP hlavních 10 rizik zabezpečení aplikací</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 řeší kritická bezpečnostní rizika pro webové aplikace.
Povědomí o těchto bezpečnostních rizicích vám může usnadnit rozhodování a navrhovat rozhodnutí, která minimalizují tato rizika ve vaší aplikaci.

Zamyslete se nad ovládacími prvky zabezpečení, abyste zabránili narušení.
Nicméně ale chcete předpokládat, že dojde k [porušení](/azure/devops/learn/devops-at-microsoft/security-in-devops) . Za předpokladu, že porušení vám pomůže předem zodpovědět některé důležité otázky týkající se zabezpečení, takže se nemusí v naléhavosti zodpovědět:

  - Jak zjistím útok?

  - Co mám dělat, když dojde k útoku nebo porušení?

  - Jak se dá zotavit z útoku, jako je únik dat nebo manipulace?

## <a name="design"></a>Návrh

Fáze návrhu je zásadní pro vytváření osvědčených postupů pro návrh a funkční specifikace. Je také zásadní pro provádění analýzy rizik, která pomáhá zmírnit problémy zabezpečení a ochrany osobních údajů v celém projektu.

Pokud máte zavedené požadavky na zabezpečení a používáte koncepce zabezpečeného návrhu, můžete se vyhnout nebo minimalizovat příležitosti pro chybu zabezpečení. Chyba zabezpečení je dohledem v návrhu aplikace, která může uživateli dovolit, aby při vydání vaší aplikace prováděla škodlivé nebo neočekávané akce.

Ve fázi návrhu si také myslíte, jak můžete použít zabezpečení v vrstvách. Jedna úroveň obrany není nutně dostatečná. Co se stane, když útočník získá za bránou firewall webových aplikací (WAF)? Chcete chránit před tímto útokem další kontrolu zabezpečení.

V takovém případě probereme následující koncepce zabezpečeného návrhu a bezpečnostní prvky, které byste měli řešit při návrhu zabezpečených aplikací:

- Použijte zabezpečenou knihovnu kódování a softwarové rozhraní.
- Vyhledá ohrožené součásti.
- Použití modelování hrozeb při návrhu aplikace
- Snižte plochu pro útok.
- Připraví zásadu identity jako primární hraniční zabezpečení.
- Vyžaduje opakované ověření pro důležité transakce.
- Použijte řešení správy klíčů k zabezpečení klíčů, přihlašovacích údajů a dalších tajných kódů.
- Chraňte citlivá data.
- Implementujte míry bezpečné pro selhání.
- Využijte možnosti zpracování chyb a výjimek.
- Použijte protokolování a výstrahy.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Použití zabezpečené knihovny kódování a softwarového rozhraní

Pro vývoj použijte knihovnu zabezpečeného kódování a softwarové rozhraní, které má integrované zabezpečení. Vývojáři mohou použít stávající, osvědčené funkce (šifrování, vstupní kanalizace, výstupní kódování, klíče nebo připojovací řetězce a cokoli jiného, co by bylo považováno za řízení zabezpečení) místo vývoje bezpečnostních mechanismů od začátku. To pomáhá chránit před chybami návrhu a implementace souvisejících se zabezpečením.

Ujistěte se, že používáte nejnovější verzi vašeho rozhraní a všechny funkce zabezpečení, které jsou k dispozici v rozhraní. Microsoft nabízí komplexní [sadu vývojářských nástrojů](https://azure.microsoft.com/product-categories/developer-tools/) pro všechny vývojáře a pracuje na libovolné platformě a v jakémkoli jazyce, abyste mohli doručovat cloudové aplikace. Můžete si kód s vámi zvoleným jazykem vybrat z různých [sad SDK](https://azure.microsoft.com/downloads/).
Můžete využívat plnohodnotná integrovaná vývojová prostředí (IDEs) a editory s pokročilými možnostmi ladění a integrovanou podporou Azure.

Microsoft nabízí celou řadu [jazyků, platforem a nástrojů](../../index.yml?panel=sdkstools-all&pivot=sdkstools) , které můžete použít k vývoji aplikací v Azure. Příkladem je [Azure pro vývojáře na platformě .NET a .NET Core](/dotnet/azure/). Pro každý jazyk a rozhraní, které nabízíme, najdete rychlé starty, kurzy a Reference k rozhraní API, které vám pomůžou rychle začít.

Azure nabízí celou řadu služeb, které můžete použít k hostování webů a webových aplikací. Tyto služby umožňují vyvíjet ve svém oblíbeném jazyce, ať už se jedná o .NET, .NET Core, Java, Ruby, Node.js, PHP nebo Python.
Jednou z těchto služeb je [Azure App Service Web Apps](../../app-service/overview.md) (Web Apps).

Web Apps zvyšuje výkon aplikace Microsoft Azure. Zahrnuje zabezpečení, Vyrovnávání zatížení, automatické škálování a automatizovanou správu. Můžete také využít možnosti DevOps v Web Apps, jako je Správa balíčků, pracovní prostředí, vlastní domény, certifikáty SSL/TLS a průběžné nasazování z Azure DevOps, GitHubu, Docker Hub a dalších zdrojů.

Azure nabízí další služby, které můžete použít k hostování webů a webových aplikací. Pro většinu scénářů je nejlepší volbou služba Web Apps. V případě architektury Micro Service zvažte [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých se kód spouští, zvažte službu [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Další informace o tom, jak si vybrat mezi těmito službami Azure, najdete v tématu [porovnání Azure App Service, Virtual Machines, Service Fabric a Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Použít aktualizace pro součásti

Aby se zabránilo ohrožení zabezpečení, měli byste průběžně docházet na klientské komponenty i na straně serveru (například architektury a knihovny) a jejich závislosti na aktualizacích. Nové chyby zabezpečení a aktualizované verze softwaru jsou vydávány průběžně. Ujistěte se, že máte průběžný plán monitorování, třídění a uplatnění aktualizací nebo změn konfigurace u knihoven a komponent, které používáte.

Na stránce [Otevřít projekt zabezpečení webové aplikace (OWASP)](https://www.owasp.org/index.php/Main_Page) najdete informace o [Používání komponent se známými chybami zabezpečení](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) pro návrhy nástrojů. Můžete se také přihlásit k odběru e-mailových upozornění pro slabá místa zabezpečení související s komponentami, které používáte.

### <a name="use-threat-modeling-during-application-design"></a>Použití modelování hrozeb během návrhu aplikace

Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a následnému zajištění, že jsou zavedena správná omezení rizik. SDL určuje, že týmy by se měly během fáze návrhu zapojit do modelování hrozeb, zatímco řešení potenciálních problémů je poměrně jednoduché a nákladově efektivní. Použití modelování hrozeb ve fázi návrhu může výrazně snížit celkové náklady na vývoj.

Abychom vám usnadnili proces modelování hrozeb, navrhli jsme [Threat Modeling Tool SDL](threat-modeling-tool.md) s ohledem na nebezpečnostní odborníky. Tento nástroj usnadňuje modelování hrozeb všem vývojářům tím, že poskytuje jasné pokyny, jak vytvářet a analyzovat modely hrozeb.

Modelování návrhu aplikace a vytváření [výčtu hrozeb a](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) jejich výčet – falšování identity, manipulace, odmítnutí, zpřístupnění informací, odepření služby a zvýšení úrovně oprávnění – napříč všemi hranicemi vztahů důvěryhodnosti byl prověřen účinný způsob, jak zachytit chyby návrhu včas. V následující tabulce jsou uvedeny hrozby pro rozteč a poskytuje několik ukázkových rizik, která využívají funkce poskytované Azure. Tato zmírnění rizika nebudou v každé situaci fungovat.

| Hrozba | Vlastnost zabezpečení | Potenciální omezení pro platformu Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falšování identity               | Ověřování        | [Vyžadovat připojení HTTPS](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1). |
| Manipulace              | Integrita             | Ověřte certifikáty protokolu SSL/TLS. Aplikace, které používají protokol SSL/TLS, musí plně ověřit certifikáty X. 509 entit, ke kterým se připojují. Pomocí Azure Key Vault certifikátů můžete [spravovat certifikáty x509](../../key-vault/general/about-keys-secrets-certificates.md). |
| Popírání odpovědnosti            | Neodvolatelnost       | Povolte [monitorování a diagnostiku](/azure/architecture/best-practices/monitoring)Azure.|
| Zveřejnění informací | Důvěrnost       | Šifrování citlivých dat v [klidovém umístění](../fundamentals/encryption-atrest.md) a [přenosu](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Odepření služby      | Dostupnost          | Monitoruje metriky výkonu pro potenciální odepření podmínek poskytování služeb. Implementujte filtry připojení. [Ochrana Azure DDoS](../../ddos-protection/ddos-protection-overview.md#next-steps)v kombinaci s osvědčenými postupy pro návrh aplikací poskytuje ochranu před útoky DDoS.|
| Zvýšení oprávnění | Autorizace         | Použijte Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Omezení prostoru pro útok

Plocha pro útok je celkový součet, kde může dojít k možným ohrožením zabezpečení. V tomto dokumentu se zaměřujeme na plochu útoku aplikace.
Zaměřuje se na ochranu aplikace před útoky. Jednoduchým a rychlým způsobem minimalizace prostoru pro útok je odebrání nepoužívaných prostředků a kódu z vaší aplikace. Menší vaše aplikace, menší plocha pro útok. Například odeberte:

- Kód pro funkce, které jste ještě neuvolnili.
- Ladění kódu podpory.
- Síťová rozhraní a protokoly, které se nepoužívají nebo které jsou zastaralé.
- Virtuální počítače a další prostředky, které nepoužíváte.

Pravidelným vyčištěním vašich prostředků a zajištěním, že odeberete nepoužitý kód, jsou skvělé způsoby, jak zajistit, aby se škodlivým aktérům při útoku staly méně.

Podrobnější a podrobný způsob, jak omezit prostor pro útoky, je provést analýzu prostoru pro útoky. Analýza prostoru pro útoky vám pomůže namapovat části systému, které je potřeba zkontrolovat a otestovat pro slabá místa zabezpečení.

Účelem analýzy roviny útoku je pochopit oblasti rizik v aplikaci, aby se vývojáři a odborníci na zabezpečení dozvěděli, jaké části aplikace jsou otevřeny k útokům. Pak můžete najít možnosti pro minimalizaci tohoto potenciálu, sledování, kdy a jak se změní prostor pro útoky a co to znamená z hlediska rizika.

Analýza plochy pro útoky vám pomůže identifikovat:

- Funkce a části systému, které potřebujete ke kontrole a testování bezpečnostních chyb zabezpečení.
- Vysoce rizikové oblasti kódu, které vyžadují ochranu důkladnou ochranou (části systému, které je třeba chránit).
- Když změníte plochu pro útok a potřebujete aktualizovat posouzení hrozeb.

Snížení příležitostí útočníků pro zneužití potenciálně slabého místa nebo ohrožení zabezpečení vyžaduje důkladnou analýzu celkového prostoru pro útoky vaší aplikace. Zahrnuje taky zakázání nebo omezení přístupu k systémovým službám, použití principu nejnižší úrovně oprávnění a využívání obrany s vrstvami bez ohledu na to, kde je to možné.

Probereme během ověřovací fáze SDL [kontrolu prostoru pro útoky](secure-develop.md#conduct-attack-surface-review) .

> [!NOTE]
> **Jaký je rozdíl mezi modelováním hrozeb a analýzou možností útoku?**
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši aplikaci a zajištění, že jsou zavedena správná zmírnění hrozeb proti hrozbám. Analýza povrchu útoku identifikuje vysoce rizikové oblasti kódu, které jsou otevřeny pro útok. Zahrnuje hledání způsobů, jak chránit vysoce rizikové oblasti vaší aplikace a kontrola a testování těchto oblastí kódu před nasazením aplikace.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásadu identity jako primární hraniční zabezpečení

Když navrhujete cloudové aplikace, je důležité rozšířit fokus hraničního zabezpečení ze sítě zaměřené na přístup k identitě orientovaný na identitu. Historicky primární místní bezpečnostní zóna byla síť organizace. Většina místních návrhů zabezpečení používá síť jako primární Pivot zabezpečení. U cloudových aplikací je lepší pracovat s tím, že jako primární hraniční zabezpečení zvažujete identitu.

Věci, které můžete využít k vývoji přístupu zaměřeného na identitu pro vývoj webových aplikací:

- Vynutili službu Multi-Factor Authentication pro uživatele.
- Používejte silné ověřování a platformy pro autorizaci.
- Použijte princip nejnižších oprávnění.
- Implementujte přístup za běhu.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Vynutilit službu Multi-Factor Authentication pro uživatele

Používejte dvojúrovňové ověřování. Dvojúrovňové ověřování je aktuální standard pro ověřování a autorizaci, protože se vyhne slabým a bezpečnostním omezením vyplývajícím z uživatelského jména a hesla ověřování. Přístup k rozhraním pro správu Azure (Azure Portal/vzdáleným PowerShellu) a službám orientovaným na zákazníky by měly být navržené a nakonfigurované tak, aby používaly [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Použití silných ověřovacích a autorizačních platforem

Místo vlastního kódu použijte mechanismy ověřování a autorizace dodané platformou. Důvodem je, že vývoj vlastního ověřovacího kódu může být náchylný k chybě. Komerční kód (například od společnosti Microsoft) je často výrazně přezkoumán z hlediska zabezpečení. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) je řešení Azure pro správu identit a přístupu. Tyto nástroje a služby Azure AD vám pomůžou s zabezpečeným vývojem:

- [Microsoft Identity Platform](../../active-directory/develop/index.yml) je sada komponent, které vývojáři používají k sestavování aplikací, které bezpečně přihlásí uživatele. Platforma pomáhá vývojářům, kteří sestavují aplikace pro jednoho tenanta, obchodní aplikace (LOB) a vývojáře, kteří chtějí vyvíjet aplikace s více klienty. Kromě základního přihlašování můžou aplikace sestavené pomocí platformy Microsoft Identity volat rozhraní API Microsoftu a vlastní rozhraní API. Platforma Microsoft identity podporuje standardní protokoly jako OAuth 2,0 a OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) je služba pro správu identit, kterou můžete použít k přizpůsobení a řízení způsobu registrace, přihlašování a správy profilů při používání vašich aplikací. To zahrnuje aplikace vyvinuté pro iOS, Android a .NET mimo jiné. Azure AD B2C tyto akce povoluje při ochraně zákaznických identit.

#### <a name="apply-the-principle-of-least-privilege"></a>Použít princip nejnižších oprávnění

Koncept [nejnižších oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) znamená, že uživatelé budou mít přesnou úroveň přístupu a ovládají jejich úlohy a ještě nic dalšího.

Potřebuje vývojář softwaru práva správce domény? Má asistent pro správu přístup k administrativním ovládacím prvkům na svém osobním počítači? Vyhodnocení přístupu k softwaru se neliší. Pokud pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) udělíte uživatelům různé možnosti a autority v aplikaci, nebudete mít přístup ke všemu všem. Omezením přístupu na to, co je potřeba pro jednotlivé role, můžete omezit riziko výskytu potíží se zabezpečením.

Ujistěte se, že vaše aplikace v celém vzoru přístupu vynutila [nejnižší oprávnění](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) .

> [!NOTE]
> Pravidla nejnižší úrovně oprávnění musí platit pro software a osoby vytvářející software. Vývojáři softwaru můžou být velkým rizikem zabezpečení IT, pokud mají k disvelkému množství přístup. Důsledky můžou být závažné, pokud má vývojář škodlivý záměr nebo máte příliš velký přístup. Doporučujeme, abyste pro vývojáře v průběhu životního cyklu vývoje použili pravidla s minimálním oprávněním.

#### <a name="implement-just-in-time-access"></a>Implementace přístupu za běhu

Implementujte přístup za běhu (Just *-in-time* ) k dalšímu snížení doby expozice oprávnění. Použít [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) k:

- Poskytněte uživatelům oprávnění, která potřebují pouze JIT.
- Přiřaďte role pro zkrácenou dobu trvání a jistotu, že se oprávnění odvolají automaticky.

### <a name="require-re-authentication-for-important-transactions"></a>Vyžadovat opakované ověření pro důležité transakce

[Padělání požadavků napříč weby](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (označované také jako *XSRF* nebo *CSRF*) představuje útok proti aplikacím hostovaným na webu, ve kterých škodlivá webová aplikace ovlivňuje interakci mezi klientským prohlížečem a webovou aplikací, která tento prohlížeč důvěřuje. Útoky na padělání žádostí mezi weby jsou možné, protože webové prohlížeče odesílají některé typy ověřovacích tokenů automaticky pomocí všech požadavků na web.
Tato forma využití se označuje také jako útok s *jedním kliknutím* nebo při *jízdě relace* , protože útok využívá dřív ověřenou relaci uživatele.

Nejlepším způsobem, jak chránit před tímto druhem útoku, je požádat uživatele o něco, co může jenom uživatel poskytnout před každou důležitou transakci, jako je nákup, deaktivace účtu nebo změna hesla. Můžete požádat uživatele, aby znovu zadal heslo, dokončili CAPTCHA nebo odeslali tajný token, který by měl mít jenom uživatel. Nejběžnějším přístupem je tajný token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Použití řešení pro správu klíčů k zabezpečení klíčů, přihlašovacích údajů a dalších tajných kódů

Ztráta klíčů a přihlašovacích údajů je běžný problém. Jediná věc, kterou neztratí vaše klíče a přihlašovací údaje, má přístup k nim neoprávněná osoba. Útočníci můžou využít automatizovaných a ručních technik k nalezení klíčů a tajných kódů, které jsou uložené v úložištích kódu jako GitHub. Neumísťujte klíče a tajné klíče do těchto veřejných úložišť kódu ani na žádný jiný server.

Klíče, certifikáty, tajné kódy a připojovací řetězce vždy umístěte do řešení správy klíčů. Můžete použít centralizované řešení, ve kterém klíče a tajné klíče jsou uložené v modulech hardwarového zabezpečení (HSM). Azure poskytuje modul HARDWAROVÉho zabezpečení v cloudu s [Azure Key Vault](../../key-vault/general/overview.md).

Key Vault je *tajné úložiště*: Jedná se o centralizovanou cloudovou službu pro ukládání tajných klíčů aplikací. Key Vault udržuje bezpečí důvěrných dat tím, že zachovává tajné klíče aplikace v jediném, centrálním umístění a poskytuje zabezpečený přístup, řízení oprávnění a přístup k protokolování.

Tajné kódy jsou uložené v jednotlivých *trezorech*. Každý trezor má vlastní konfiguraci a zásady zabezpečení pro řízení přístupu. K datům získáte přístup pomocí REST API nebo prostřednictvím klientské sady SDK, která je dostupná pro většinu programovacích jazyků.

> [!IMPORTANT]
> Azure Key Vault je navržená tak, aby ukládala konfigurační tajné klíče pro serverové aplikace. Není určený pro ukládání dat, která patří uživatelům aplikace. To se odráží v jeho výkonových charakteristikách, rozhraní API a modelu nákladů.
>
> Uživatelská data by se měla ukládat jinde, například v instanci Azure SQL Database, která má transparentní šifrování dat (TDE) nebo v účtu úložiště, který používá Azure Storage šifrování služby. Tajné kódy, které vaše aplikace používá pro přístup k těmto úložištím dat, se můžou uchovávat v Azure Key Vault.

### <a name="protect-sensitive-data"></a>Chránit citlivá data

Ochrana dat je zásadní součástí vaší strategie zabezpečení.
Klasifikace dat a určení vašich potřeb ochrany dat vám pomůžou při návrhu aplikace s ohledem na zabezpečení dat. Klasifikace (kategorizace) uložených dat podle citlivosti a dopadu firmy pomáhá vývojářům určit rizika spojená s daty.

Při návrhu formátů dat označte všechna relevantní data jako citlivá. Ujistěte se, že aplikace považuje příslušná data za citlivá. Tyto postupy vám pomůžou chránit citlivá data:

- Použijte šifrování.
- Vyhněte se kódování tajných kódů, jako jsou klíče a hesla.
- Ujistěte se, že jsou na místě řízení přístupu a auditování.

#### <a name="use-encryption"></a>Použít šifrování

Ochrana dat by měla být důležitou součástí vaší strategie zabezpečení.
Pokud jsou vaše data uložená v databázi nebo se mezi místy pohybují, používejte šifrování neaktivních uložených [dat](../fundamentals/encryption-atrest.md) (v databázi) a šifrování přenášených [dat](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (na cestě a od uživatele, databáze, rozhraní API nebo koncového bodu služby). Pro výměnu dat doporučujeme vždy používat protokoly SSL/TLS. Ujistěte se, že používáte nejnovější verzi TLS pro šifrování (aktuálně se jedná o verzi 1,2).

#### <a name="avoid-hard-coding"></a>Vyhnout se hardwarovému kódování

Některé věci by nikdy neměly být v softwaru pevně zakódované. Některé příklady jsou názvy hostitelů nebo IP adresy, adresy URL, e-mailové adresy, uživatelská jména, hesla, klíče účtu úložiště a další kryptografické klíče. Zvažte implementaci požadavků kolem toho, co může nebo nemůže být v kódu pevně zakódováno, včetně v oddílech komentářů vašeho kódu.

Při vložení komentářů do kódu se ujistěte, že neuložíte žádné citlivé informace. Patří sem vaše e-mailová adresa, hesla, připojovací řetězce, informace o vaší aplikaci, které by znali jenom někdo ve vaší organizaci, a cokoli jiného, co by útočník mohl využít k útoku na vaši aplikaci nebo organizaci.

V podstatě předpokládáme, že vše ve vývojovém projektu bude při nasazení veřejné. Vyhněte se zahrnutí citlivých dat jakéhokoli druhu v projektu.

Dříve jsme probrali [Azure Key Vault](../../key-vault/general/overview.md). Key Vault můžete použít k ukládání tajných kódů, jako jsou klíče a hesla, místo jejich hardwarového kódování. Když použijete Key Vault v kombinaci se spravovanými identitami pro prostředky Azure, vaše webová aplikace Azure může snadno a bezpečně přistupovat k hodnotám konfigurace tajných klíčů bez uložení tajných kódů v konfiguraci nebo správě zdrojového kódu. Další informace najdete v tématu [Správa tajných klíčů ve vašich serverových aplikacích pomocí Azure Key Vault](/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementace opatření v bezpečí selhání

Vaše aplikace musí být schopna zpracovat [chyby](/dotnet/standard/exceptions/) , ke kterým došlo během provádění konzistentním způsobem. Aplikace by měla zachytit všechny chyby a nemusí být bezpečná nebo uzavřená.

Zajistěte také, aby byly chyby zaznamenávány do protokolu s dostatečným uživatelským kontextem k identifikaci podezřelých nebo škodlivých aktivit. Protokoly by měly být uchovány dostatečně dlouho, aby bylo možné provést opožděnou forenzní analýzu. Protokoly by měly být ve formátu, který je možné snadno spotřebovat pomocí řešení správy protokolů. Zajistěte, aby se aktivovaly výstrahy týkající se chyb souvisejících se zabezpečením. Nedostatečné protokolování a monitorování umožňuje útočníkům lépe napadnout systémy a zachovat trvalost.

### <a name="take-advantage-of-error-and-exception-handling"></a>Využijte zpracování chyb a výjimek

Implementace správného zpracování chyb a [výjimek](/dotnet/standard/exceptions/best-practices-for-exceptions) je důležitou součástí kódování obrannou linií. Zpracování chyb a výjimek je důležité pro zajištění spolehlivého a zabezpečeného systému. Chyby při zpracování chyb můžou vést k různým druhům ohrožení zabezpečení, jako jsou nevracení informací útočníkům a pomoc útočníkům, které pochopí více o vaší platformě a návrhu.

Zajistěte, aby:

- Výjimky se zpracovávají centralizovaným způsobem, aby se předešlo duplicitním [blokům try/catch](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) v kódu.

- V rámci aplikace jsou zpracovávána všechna neočekávaná chování.

- Zprávy, které se zobrazují uživatelům, nevrátí kritická data, ale poskytnou dostatek informací pro vysvětlení problému.

- Výjimky jsou protokolovány a poskytují dostatek informací pro prošetření forenzní nebo týmů reakce na incidenty.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) poskytuje prvotřídní prostředí pro [zpracování chyb a výjimek](../../logic-apps/logic-apps-exception-handling.md) , které jsou způsobeny závislými systémy. Pomocí Logic Apps můžete vytvářet pracovní postupy pro automatizaci úloh a procesů, které integrují aplikace, data, systémy a služby napříč podniky a organizacemi.

### <a name="use-logging-and-alerting"></a>Použití protokolování a upozorňování

[Protokolujte](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) své problémy se zabezpečením pro vyšetřování zabezpečení a aktivujte výstrahy týkající se problémů, abyste zajistili, že lidé budou včas informovat o problémech. Povolte auditování a protokolování pro všechny součásti. Protokoly auditu by měly zachytit kontext uživatele a identifikovat všechny důležité události.

Ověřte, že nechcete protokolovat žádná citlivá data, která uživatel odešle do vaší lokality. Mezi příklady citlivých dat patří:

- Přihlašovací údaje uživatele
- Čísla sociálního pojištění nebo jiné identifikační údaje
- Čísla platebních karet nebo jiné finanční informace
- Informace o stavu
- Soukromé klíče nebo jiná data, která lze použít k dešifrování šifrovaných informací
- Informace o systému nebo aplikaci, které lze použít k efektivnějšímu útoku aplikace

Ujistěte se, že aplikace monitoruje události správy uživatelů, jako jsou úspěšná a Nezdařená přihlášení uživatelů, resetování hesla, změny hesla, uzamčení účtu a registrace uživatele. Protokolování těchto událostí pomáhá detekovat a reagovat na potenciálně podezřelé chování. Umožňuje taky shromažďovat data o operacích, jako je přístup k aplikaci.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme bezpečnostní mechanismy a aktivity, které vám pomůžou s vývojem a nasazením zabezpečených aplikací.

- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)