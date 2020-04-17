---
title: Návrh zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je třeba zvážit během fáze požadavku a návrhu projektu webové aplikace.
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
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461714"
---
# <a name="design-secure-applications-on-azure"></a>Návrh zabezpečených aplikací v Azure
V tomto článku uvádíme aktivity zabezpečení a ovládací prvky, které je třeba zvážit při navrhování aplikací pro cloud. Školicí prostředky spolu s bezpečnostníotázky a koncepty, které je třeba zvážit během požadavků a fází návrhu životního cyklu vývoje zabezpečení společnosti Microsoft [(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) jsou zahrnuty. Cílem je pomoci vám definovat aktivity a služby Azure, které můžete použít k návrhu bezpečnější aplikace.

Následující fáze SDL jsou uvedeny v tomto článku:

- Školení
- Požadavky
- Návrh

## <a name="training"></a>Školení
Než začnete vyvíjet cloudovou aplikaci, udělejte si čas na pochopení zabezpečení a ochrany osobních údajů v Azure. Provedením tohoto kroku můžete snížit počet a závažnost zneužitelných chyb zabezpečení ve vaší aplikaci. Budete více připraveni reagovat vhodně na neustále se měnící prostředí hrozeb.

Pomocí následujících prostředků během fáze školení se můžete seznámit se službami Azure, které jsou dostupné vývojářům, a s osvědčenými postupy zabezpečení v Azure:

  - [Průvodce pro vývojáře azure](https://azure.microsoft.com/campaigns/developer-guide/) vám ukáže, jak začít s Azure. Průvodce vám ukáže, které služby můžete použít ke spouštění aplikací, ukládání dat, začlenění inteligence, vytváření aplikací IoT a efektivnějším a bezpečnějším nasazením řešení.

  - [Příručka Začínáme pro vývojáře Azure](../../guides/developer/azure-developer-guide.md) poskytuje základní informace pro vývojáře, kteří chtějí začít používat platformu Azure pro své potřeby vývoje.

  - [Sady SDK a nástroje](https://docs.microsoft.com/azure/index?pivot=sdkstools) popisují nástroje, které jsou k dispozici v Azure.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) poskytuje nástroje pro spolupráci na vývoji. Nástroje zahrnují vysoce výkonné kanály, bezplatná úložiště Git, konfigurovatelné kanbanové desky a rozsáhlé automatizované a cloudové zátěžové testování.
    [Centrum zdrojů DevOps](https://docs.microsoft.com/azure/devops/learn/) kombinuje naše zdroje pro učení devOps postupy, Git správu verzí, agilní metody, jak pracujeme s DevOps v Microsoftu a jak můžete posoudit vlastní DevOps progrese.

  - [Prvních 5 položek zabezpečení, které je třeba zvážit před odesláním do produkčního prostředí,](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) ukazuje, jak zabezpečit webové aplikace v Azure a chránit vaše aplikace před nejběžnějšími a nebezpečnými útoky webových aplikací.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) je kolekce skriptů, nástrojů, rozšíření a automatizací, která uspokojuje komplexní předplatným Azure a potřebami zabezpečení prostředků týmů DevOps, které využívají rozsáhlou automatizaci. Secure DevOps Kit for Azure vám může ukázat, jak plynule integrovat zabezpečení do nativních pracovních postupů DevOps. Sada řeší nástroje, jako jsou testy ověření zabezpečení (SVT), které mohou vývojářům pomoci psát zabezpečený kód a testovat zabezpečenou konfiguraci svých cloudových aplikací v kódování a raných fázích vývoje.

  - [Doporučené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) poskytují kolekci doporučených postupů zabezpečení, které můžete používat při navrhování, nasazování a správě cloudových řešení pomocí Azure.

## <a name="requirements"></a>Požadavky
Fáze definice požadavků je klíčovým krokem při definování toho, co je vaše aplikace a co bude dělat, když je vydána. Fáze požadavků je také čas přemýšlet o ovládací prvky zabezpečení, které budete vytvářet do aplikace. Během této fáze také zahájíte kroky, které budete mít v celém SDL k zajištění uvolnění a nasazení zabezpečené aplikace.

### <a name="consider-security-and-privacy-issues"></a>Zvažte problémy se zabezpečením a ochranou osobních údajů
Tato fáze je nejlepší čas, aby zvážila základní otázky zabezpečení a ochrany osobních údajů. Definování přijatelné úrovně zabezpečení a ochrany osobních údajů na začátku projektu pomáhá týmu:

- Seznamte se s riziky spojenými s problémy se zabezpečením.
- Identifikovat a opravit chyby zabezpečení během vývoje.
- Uplatňovat zavedené úrovně zabezpečení a soukromí v celém projektu.

Při psaní požadavků pro vaši aplikaci, nezapomeňte zvážit ovládací prvky zabezpečení, které mohou pomoci udržet vaše aplikace a data v bezpečí.

### <a name="ask-security-questions"></a>Pokládejte bezpečnostní otázky
Pokládejte bezpečnostní otázky, jako jsou:

  - Obsahuje moje aplikace citlivá data?

  - Shromažďuje nebo ukládá moje aplikace data, která vyžadují, abych dodržoval oborové standardy a programy dodržování předpisů, jako je [Federální rada pro zkoušení finančních institucí (FFIEC)](../blueprints/ffiec-analytics-overview.md) nebo [standardy zabezpečení dat v odvětví platebních karet (PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Shromažďuje nebo obsahuje moje aplikace citlivé osobní nebo zákaznické údaje, které lze použít samostatně nebo s jinými informacemi k identifikaci, kontaktování nebo vyhledání jedné osoby?

  - Shromažďuje nebo obsahuje moje přihláška údaje, které lze použít k přístupu k lékařským, vzdělávacím, finančním nebo pracovním informacím jednotlivce? Identifikace citlivosti vašich dat během fáze požadavků vám pomůže klasifikovat data a identifikovat metodu ochrany dat, kterou budete používat pro vaši aplikaci.

  - Kde a jak jsou moje data uložena? Zvažte, jak budete sledovat služby úložiště, které vaše aplikace používá pro všechny neočekávané změny (například pomalejší doby odezvy). Budete moci ovlivnit protokolování, abyste shromáždili podrobnější data a podrobně analyzovali problém?

  - Bude moje přihláška dostupná veřejnosti (na internetu) nebo pouze interně? Pokud je vaše aplikace dostupná veřejnosti, jak chráníte data, která mohou být shromažďována, před nesprávným použitím? Pokud je vaše aplikace k dispozici pouze interně, zvažte, kdo ve vaší organizaci by měl mít přístup k aplikaci a jak dlouho by měl mít přístup.

  - Rozumíte modelu identity před zahájením navrhování aplikace? Jak zjistíte, že uživatelé jsou tím, za koho se podle vás jedná, a k čemu je uživatel oprávněn?

  - Plní aplikace citlivé nebo důležité úkoly (například převod peněz, odemykání dveří nebo dodávání léků)?
    Zvažte, jak ověříte, zda je uživatel provádějící citlivou úlohu oprávněn k provedení úkolu a jak ověříte, že osoba je tím, za koho se jedná. Autorizace (AuthZ) je udělení oprávnění ověřeného objektu zabezpečení k něčemu. Ověřování (AuthN) je akt náročné strany pro legitimní pověření.

  - Provádí moje aplikace nějaké rizikové softwarové aktivity, jako je umožnění uživatelům nahrávat nebo stahovat soubory nebo jiná data? Pokud vaše aplikace provádí rizikové aktivity, zvažte, jak bude vaše aplikace chránit uživatele před zpracováním škodlivých souborů nebo dat.

### <a name="review-owasp-top-10"></a>Recenze OWASP top 10
Zvažte revizi [<span class="underline">OWASP Top 10 rizika zabezpečení aplikací</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 řeší kritická bezpečnostní rizika pro webové aplikace.
Povědomí o těchto bezpečnostních rizik vám může pomoci provádět požadavky a navrhovat rozhodnutí, která minimalizují tato rizika ve vaší aplikaci.

Je důležité přemýšlet o bezpečnostních kontrolách, které zabrání narušení.
Chcete však také předpokládat, že dojde k [porušení.](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) Za předpokladu porušení pomáhá odpovědět na některé důležité otázky týkající se bezpečnosti předem, takže nemusí být zodpovězeny v případě nouze:

  - Jak zjistím útok?

  - Co mám dělat, pokud dojde k útoku nebo porušení?

  - Jak se mám zotavit z útoku, jako je únik dat nebo manipulace?

## <a name="design"></a>Návrh

Fáze návrhu je rozhodující pro stanovení osvědčených postupů pro návrh a funkční specifikace. Je také důležité pro provádění analýzy rizik, která pomáhá zmírnit problémy se zabezpečením a ochranou osobních údajů v celém projektu.

Pokud máte požadavky na zabezpečení a používáte koncepty zabezpečeného návrhu, můžete se vyhnout nebo minimalizovat příležitosti pro chybu zabezpečení. Chyba zabezpečení je dohled v návrhu aplikace, které mohou umožnit uživateli provádět škodlivé nebo neočekávané akce po vydání aplikace.

Během fáze návrhu také přemýšlejte o tom, jak můžete použít zabezpečení ve vrstvách; jedna úroveň obrany není nutně dost. Co se stane, když se útočník dostane přes bránu firewall webové aplikace (WAF)? Chcete další bezpečnostní kontrolu na místě, aby se bránili proti tomuto útoku.

S ohledem na tuto skutečnost diskutujeme o následujících konceptech zabezpečeného návrhu a ovládacích prvcích zabezpečení, které byste měli řešit při navrhování zabezpečených aplikací:

- Použijte zabezpečenou knihovnu kódování a softwarový rámec.
- Hledat ohrožené součásti.
- Při návrhu aplikace používejte modelování hrozeb.
- Zmenšete svůj útočný povrch.
- Přijmout zásadu identity jako primární zabezpečení obvodu.
- Vyžadovat opětovné ověření pro důležité transakce.
- Pomocí řešení správy klíčů můžete zabezpečit klíče, přihlašovací údaje a další tajné klíče.
- Chraňte citlivá data.
- Zaváděte bezpečnostní opatření.
- Využijte využití zpracování chyb a výjimek.
- Použijte protokolování a upozorňování.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Použití zabezpečené kódovací knihovny a softwarového frameworku

Pro vývoj použijte zabezpečenou knihovnu kódování a softwarový rámec, který má integrované zabezpečení. Vývojáři mohou používat stávající, osvědčené funkce (šifrování, vstupní hygiena, kódování výstupu, klíče nebo připojovací řetězce a cokoli jiného, co by bylo považováno za bezpečnostní řízení) namísto vývoje bezpečnostních ovládacích prvků od nuly. To pomáhá chránit před chybami návrhu a implementace souvisejícíse zabezpečením.

Ujistěte se, že používáte nejnovější verzi rozhraní frameworku a všechny funkce zabezpečení, které jsou v rámci k dispozici. Společnost Microsoft nabízí komplexní [sadu vývojových nástrojů](https://azure.microsoft.com/product-categories/developer-tools/) pro všechny vývojáře, kteří pracují na libovolné platformě nebo jazyce a poskytují cloudové aplikace. Můžete kód ovat s jazykem dle vašeho výběru výběrem z různých [sad SDK](https://azure.microsoft.com/downloads/).
Můžete využít plně vybavený integrovaný vývojových prostředí (IDE) a editory, které mají pokročilé možnosti ladění a integrovanou podporu Azure.

Microsoft nabízí celou řadu [jazyků, architektur a nástrojů,](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) které můžete použít k vývoji aplikací v Azure. Příkladem je [Azure pro vývojáře .NET a .NET Core](https://docs.microsoft.com/dotnet/azure/). Pro každý jazyk a architekturu, které nabízíme, najdete rychlé starty, kurzy a odkazy na rozhraní API, které vám pomohou rychle začít.

Azure nabízí celou řadu služeb, které můžete použít k hostování webů a webových aplikací. Tyto služby vám umožní vyvíjet ve vašem oblíbeném jazyce, ať už je to .NET, .NET Core, Java, Ruby, Node.js, PHP nebo Python.
[Jednou](../../app-service/overview.md) z těchto služeb je Azure App Service Web Apps (Web Apps).

Web Apps přidává do vaší aplikace sílu Microsoft Azure. Zahrnuje zabezpečení, vyrovnávání zatížení, automatické škálování a automatizovanou správu. Můžete také využít funkce DevOps ve webových aplikacích, jako je správa balíčků, pracovní prostředí, vlastní domény, certifikáty SSL/TLS a průběžné nasazení z Azure DevOps, GitHub, Docker Hub a dalších zdrojů.

Azure nabízí další služby, které můžete použít k hostování webů a webových aplikací. Pro většinu scénářů je nejlepší volbou služba Web Apps. Pro architekturu mikroslužeb zvažte [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých se kód spouští, zvažte službu [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Další informace o tom, jak si vybrat mezi těmito službami Azure, najdete v [porovnání služby Azure App Service, virtuální počítače, Service Fabric a cloudové služby](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Použití aktualizací na součásti

Chcete-li zabránit chybám zabezpečení, měli byste průběžně inventarizovat součásti na straně klienta i na serveru (například architektury a knihovny) a jejich závislosti pro aktualizace. Kontinuálně se uvolňují nové chyby zabezpečení a aktualizované verze softwaru. Ujistěte se, že máte průběžný plán pro monitorování, třídění a použití aktualizací nebo změn konfigurace v knihovnách a součástech, které používáte.

Návrhy nástrojů naleznete na stránce [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) o používání součástí se [známými chybami zabezpečení.](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) Můžete se také přihlásit k odběru e-mailových upozornění na chyby zabezpečení, které souvisejí s komponentami, které používáte.

### <a name="use-threat-modeling-during-application-design"></a>Použití modelování hrozeb při návrhu aplikace

Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a následné zajištění, že jsou zavedena správná opatření ke zmírnění rizik. SDL určuje, že týmy by se měly zapojit do modelování hrozeb během fáze návrhu, při řešení potenciálních problémů je relativně snadné a nákladově efektivní. Pomocí modelování hrozeb ve fázi návrhu může výrazně snížit celkové náklady na vývoj.

Abychom usnadnili proces modelování hrozeb, navrhli jsme [nástroj pro modelování hrozeb SDL](threat-modeling-tool.md) s ohledem na odborníky, kteří nejsou bezpečnostními odborníky. Tento nástroj usnadňuje modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny o tom, jak vytvářet a analyzovat modely hrozeb.

Modelování návrhu aplikace a výčet hrozeb [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) – Falšování, Falšování, Odmítnutí, Zpřístupnění informací, Odmítnutí služby a Zvýšení oprávnění – přes všechny hranice důvěryhodnosti se osvědčilo jako účinný způsob, jak včas zachytit chyby návrhu. V následující tabulce jsou uvedeny hrozby STRIDE a některé příklady skutečnosti snižující závažnost rizika, které používají funkce poskytované Azure. Tato skutečnosti snižující závažnost rizika nebudou fungovat v každé situaci.

| Hrozba | Vlastnost zabezpečení | Potenciální zmírnění platformy Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falšování identity               | Authentication        | [Vyžadovat připojení HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Falšování              | Integrita             | Ověřte certifikáty SSL/TLS. Aplikace, které používají SSL/TLS, musí plně ověřit certifikáty X.509 entit, ke kterým se připojují. Ke [správě certifikátů x509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)použijte certifikáty Azure Key Vault . |
| Odmítnutí            | Neodvolatelnost       | Povolte [monitorování a diagnostiku](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)Azure .|
| Zpřístupnění informací | Důvěrnost       | Šifrujte citlivá data [v klidovém](../fundamentals/encryption-atrest.md) stavu a [při přenosu](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Útok DoS      | Dostupnost          | Sledujte metriky výkonu pro potenciální podmínky odmítnutí služby. Implementujte filtry připojení. [Azure DDoS protection](../../virtual-network/ddos-protection-overview.md#next-steps), v kombinaci s osvědčenými postupy návrhu aplikace, poskytuje ochranu proti útokům DDoS.|
| Zvýšení oprávnění | Autorizace         | Použijte Azure <span class="underline"> </span> Active Directory [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Snižte svůj útočný povrch

Povrch útoku je celkový součet možných chyb zabezpečení. V tomto článku se zaměříme na útočnou plochu aplikace.
Důraz je kladen na ochranu aplikace před útokem. Jednoduchý a rychlý způsob, jak minimalizovat prostor pro útok, je odebrat nepoužívané prostředky a kód z vaší aplikace. Čím menší aplikace, tím menší je povrch útoku. Odstraňte například:

- Kód pro funkce, které jste ještě nevydali.
- Ladění kódu podpory.
- Síťová rozhraní a protokoly, které nejsou používány nebo které byly zastaralé.
- Virtuální počítače a další prostředky, které nepoužíváte.

Provádění pravidelné vyčištění prostředků a zajištění odebrání nepoužívaného kódu jsou skvělé způsoby, jak zajistit, že existuje méně příležitostí pro škodlivé subjekty k útoku.

Podrobnějším a podrobnějším způsobem, jak snížit povrch útoku, je dokončit analýzu povrchu útoku. Analýza povrchu útoku vám pomůže zmapovat části systému, které je třeba zkontrolovat a otestovat na ohrožení zabezpečení.

Účelem analýzy povrchu útoku je pochopit rizikové oblasti v aplikaci, aby vývojáři a specialisté na zabezpečení věděli, na jaké části aplikace jsou otevřeny k útoku. Potom můžete najít způsoby, jak minimalizovat tento potenciál, sledovat, kdy a jak se mění povrch útoku a co to znamená z hlediska rizika.

Analýza povrchu útoku vám pomůže identifikovat:

- Funkce a části systému, které potřebujete ke kontrole a testování slabých míst zabezpečení.
- Vysoce rizikové oblasti kódu, které vyžadují ochranu do hloubky ochrany (části systému, které je třeba chránit).
- Když změníte prostor pro útok a potřebujete aktualizovat hodnocení hrozeb.

Omezení příležitostí pro útočníky ke zneuzení potenciálního slabého místa nebo chyby zabezpečení vyžaduje důkladnou analýzu celkového povrchu útoku vaší aplikace. Zahrnuje také zakázání nebo omezení přístupu k systémovým službám, uplatňování zásady nejnižšího oprávnění a použití vrstvené obrany, kdykoli je to možné.

Diskutujeme o [provedení kontroly povrchu útoku](secure-develop.md#conduct-attack-surface-review) během ověřovací fáze SDL.

> [!NOTE]
> **Jaký je rozdíl mezi modelováním hrozeb a analýzou povrchu útoku?**
Modelování hrozeb je proces identifikace potenciálních ohrožení zabezpečení pro vaši aplikaci a zajištění, že jsou na místě správné skutečnosti snižující závažnost i proti hrozbám. Analýza povrchu útoku identifikuje vysoce rizikové oblasti kódu, které jsou otevřené k útoku. Zahrnuje hledání způsobů, jak bránit vysoce rizikové oblasti vaší aplikace a kontrolu a testování těchto oblastí kódu před nasazením aplikace.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásadu identity jako primárního bezpečnostního obvodu

Při navrhování cloudových aplikací je důležité rozšířit zaměření na obvod zabezpečení z přístupu zaměřeného na síť až po přístup zaměřený na identitu. Historicky primární min. místní zabezpečení byl síť organizace. Většina místních návrhů zabezpečení používá síť jako primární pivot zabezpečení. Pro cloudové aplikace, jsou lépe obsluhovány tím, že považujete identitu za primární obvod zabezpečení.

Co můžete udělat pro vývoj přístupu zaměřeného na identitu k vývoji webových aplikací:

- Vynucujte vícefaktorové ověřování pro uživatele.
- Používejte silné platformy pro ověřování a autorizaci.
- Uplatňovat zásadu nejnižších privilegií.
- Implementujte přístup just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Vynucení vícefaktorového ověřování pro uživatele

Použijte dvoufaktorové ověřování. Dvoufaktorové ověřování je aktuální standard pro ověřování a autorizaci, protože se vyhýbá slabým místům zabezpečení, které jsou vlastní typům uživatelského jména a hesla ověřování. Přístup k rozhraním pro správu Azure (Azure portal/remote PowerShell) a ke službám orientovaným na zákazníky by měl být navržený a nakonfigurovaný tak, aby používal [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Používejte silné platformy pro ověřování a autorizaci

Místo vlastního kódu používejte mechanismy ověřování a autorizace dodané platformou. Důvodem je, že vývoj vlastního ověřovacího kódu může být náchylný k chybě. Komerční kód (například od společnosti Microsoft) je často rozsáhle kontrolován z důvodu zabezpečení. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) je řešení Azure pro správu identit a přístupu. Tyto nástroje a služby Azure AD pomáhají se zabezpečeným vývojem:

- [Platforma identit Azure AD (Azure AD pro vývojáře)](../../active-directory/develop/about-microsoft-identity-platform.md) je cloudová služba identit, kterou vývojáři používají k vytváření aplikací, které bezpečně přihlašují uživatele. Azure AD pomáhá vývojářům, kteří budují aplikace pro jednoho tenanta, obchodní (LOB) a vývojářům, kteří chtějí vyvíjet aplikace pro více klientů. Kromě základnípřihlášení, aplikace, které jsou vytvořené pomocí Azure AD můžete volat Microsoft API a vlastní rozhraní API, které jsou postavené na platformě Azure AD. Platforma identit Azure AD podporuje standardní protokoly, jako je OAuth 2.0 a OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) je služba pro správu identit, kterou můžete použít k přizpůsobení a řízení toho, jak se zákazníci přihlašují, přihlašují se a spravují své profily při používání vašich aplikací. To zahrnuje aplikace, které jsou vyvinuty pro iOS, Android a .NET, mimo jiné. Azure AD B2C umožňuje tyto akce při ochraně identit y zákazníků.

#### <a name="apply-the-principle-of-least-privilege"></a>Uplatňovat zásadu nejnižšího oprávnění

Koncept [nejnižšího oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) znamená poskytnout uživatelům přesnou úroveň přístupu a kontroly, kterou potřebují ke své práci, a nic víc.

Potřeboval by vývojář softwaru práva správce domény? Potřeboval by administrativní asistent přístup k administrativním kontrolám na svém osobním počítači? Vyhodnocení přístupu k softwaru se nijak neliší. Pokud používáte [řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) poskytnout uživatelům různé schopnosti a oprávnění ve vaší aplikaci, neměli byste udělit všem přístup ke všemu. Omezením přístupu k tomu, co je požadováno pro každou roli, omezíte riziko výskytu problému se zabezpečením.

Ujistěte se, že vaše aplikace vynucuje [nejnižší oprávnění](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) v rámci svých vzorů přístupu.

> [!NOTE]
>  Pravidla nejmenších oprávnění je třeba použít na software a na lidi, kteří software vytvářejí. Vývojáři softwaru mohou být obrovským rizikem pro zabezpečení IT, pokud mají příliš velký přístup. Důsledky mohou být závažné, pokud má vývojář zlý úmysl nebo má příliš velký přístup. Doporučujeme, aby pravidla nejnižší oprávnění použít pro vývojáře v průběhu životního cyklu vývoje.

#### <a name="implement-just-in-time-access"></a>Implementace přístupu just-in-time

Implementujte přístup *za čas* (JIT) k dalšímu snížení doby expozice oprávnění. Pomocí [správy privilegovaných identit Azure AD můžete:](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity)

- Podejte uživatelům oprávnění, která potřebují pouze JIT.
- Přiřaďte role pro zkrácenou dobu trvání s jistotou, že oprávnění jsou automaticky odvolána.

### <a name="require-re-authentication-for-important-transactions"></a>Vyžadovat opětovné ověření pro důležité transakce

[Padělání požadavků mezi weby](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (označované také jako *XSRF* nebo *CSRF*) je útok proti webovým aplikacím, ve kterých škodlivá webová aplikace ovlivňuje interakci mezi klientským prohlížečem a webovou aplikací, která tomuto prohlížeči důvěřuje. Útoky padělání požadavků mezi weby jsou možné, protože webové prohlížeče automaticky odesílají některé typy ověřovacích tokenů s každým požadavkem na web.
Tato forma zneužití se také označuje jako *útok jedním kliknutím* nebo jízda na *relaci,* protože útok využívá dříve ověřené relace uživatele.

Nejlepší způsob, jak se bránit proti tomuto druhu útoku, je požádat uživatele o něco, co může poskytnout pouze uživatel před každou důležitou transakcí, jako je nákup, deaktivace účtu nebo změna hesla. Můžete požádat uživatele o opětovné zadání hesla, dokončení captcha nebo odeslání tajného tokenu, který by měl pouze uživatel. Nejběžnější přístup je tajný token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Použití řešení správy klíčů k zabezpečení klíčů, přihlašovacích údajů a dalších tajných kódů

Ztráta klíčů a přihlašovacích údajů je běžný problém. Jediná věc horší než ztráta klíčů a pověření je, že k nim získá přístup neoprávněná strana. Útočníci mohou využít automatizované a ruční techniky k vyhledání klíčů a tajných kódů, které jsou uloženy v úložištích kódu, jako je GitHub. Nevložte klíče a tajné klíče do těchto úložišť veřejného kódu nebo na jiný server.

Klíče, certifikáty, tajné klíče a připojovací řetězce vždy vložte do řešení správy klíčů. Můžete použít centralizované řešení, ve kterém jsou klíče a tajné klíče uloženy v modulech hardwarového zabezpečení (HSM). Azure poskytuje hsm v cloudu s [Azure Key Vault](../../key-vault/general/overview.md).

Trezor klíčů je *tajné úložiště:* je to centralizovaná cloudová služba pro ukládání tajných kódů aplikací. Trezor klíčů udržuje vaše důvěrná data v bezpečí tím, že uchovává tajné kódy aplikací v jednom, centrálním umístění a poskytuje zabezpečený přístup, řízení oprávnění a protokolování přístupu.

Tajemství jsou uložena v jednotlivých *trezorech*. Každý trezor má vlastní zásady konfigurace a zabezpečení pro řízení přístupu. K datům se dostanete prostřednictvím rozhraní REST API nebo prostřednictvím sady SDK klienta, která je k dispozici pro většinu programovacích jazyků.

> [!IMPORTANT]
> Azure Key Vault je určen k ukládání tajných klíčů konfigurace pro serverové aplikace. Není určen pro ukládání dat, která patří uživatelům aplikace. To se odráží v jeho výkonových charakteristikách, rozhraní API a modelu nákladů.
>
> Uživatelská data by měla být uložena jinde, například v instanci Azure SQL Database, která má transparentní šifrování dat (TDE) nebo v účtu úložiště, který používá Šifrování služby Azure Storage Service. Tajné klíče, které vaše aplikace používá pro přístup k těmto úložištím dat, mohou být uloženy v úložišti klíčů Azure.

### <a name="protect-sensitive-data"></a>Ochrana citlivých dat

Ochrana dat je nezbytnou součástí vaší strategie zabezpečení.
Klasifikace dat a identifikace potřeb ochrany dat vám pomůže navrhnout aplikaci s ohledem na zabezpečení dat. Klasifikace (kategorizace) uložených dat podle citlivosti a dopadu na podnikání pomáhá vývojářům určit rizika spojená s daty.

Při návrhu datových formátů označte všechna použitelná data jako citlivá. Ujistěte se, že aplikace zachází s příslušnými daty jako s citlivými. Tyto postupy vám mohou pomoci chránit citlivá data:

- Použijte šifrování.
- Vyhněte se pevným kódovacím tajným kódům, jako jsou klíče a hesla.
- Ujistěte se, že jsou zavedeny kontroly přístupu a auditování.

#### <a name="use-encryption"></a>Použití šifrování

Ochrana dat by měla být nezbytnou součástí vaší strategie zabezpečení.
Pokud jsou data uložena v databázi nebo pokud se přesouvají mezi umístěními, použijte šifrování [dat v klidovém stavu](../fundamentals/encryption-atrest.md) (v databázi) a šifrování dat při [přenosu](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (na cestě k uživateli, databázi, rozhraní API nebo koncovému bodu služby). K výměně dat doporučujeme vždy používat protokoly SSL/TLS. Ujistěte se, že používáte nejnovější verzi TLS pro šifrování (v současné době se jedná o verzi 1.2).

#### <a name="avoid-hard-coding"></a>Vyhněte se tvrdému kódování

Některé věci by nikdy neměly být pevně zakódovány ve vašem softwaru. Některé příklady jsou názvy hostitelů nebo IP adresy, adresy URL, e-mailové adresy, uživatelská jména, hesla, klíče účtu úložiště a další kryptografické klíče. Zvažte implementaci požadavků na to, co může nebo nemůže být pevně zakódováno ve vašem kódu, včetně částí s komentáři v kódu.

Když do kódu vložíte komentáře, ujistěte se, že neukládáte žádné citlivé informace. To zahrnuje vaši e-mailovou adresu, hesla, připojovací řetězce, informace o vaší aplikaci, které by znal pouze někdo ve vaší organizaci, a cokoli jiného, co by mohlo útočníkovi poskytnout výhodu při útoku na vaši aplikaci nebo organizaci.

V podstatě předpokládejme, že vše, co ve vašem vývojovém projektu bude veřejné znalosti, když je nasazen. Vyhněte se zahrnutí citlivých dat jakéhokoli druhu do projektu.

Dříve jsme diskutovali [O Azure Key Vault](../../key-vault/general/overview.md). Trezor klíčů můžete použít k ukládání tajných kódů, jako jsou klíče a hesla, namísto jejich pevného kódování. Když používáte Trezor klíčů v kombinaci se spravovanými identitami pro prostředky Azure, vaše webová aplikace Azure může snadno a bezpečně přistupovat k tajným konfiguračním hodnotám bez uložení tajných kódů ve vaší spravovací směřování nebo konfiguraci. Další informace najdete [v tématu Správa tajných klíčů v serverových aplikacích pomocí služby Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Zavést bezpečnostní opatření

Aplikace musí být schopen zpracovat [chyby,](https://docs.microsoft.com/dotnet/standard/exceptions/) ke kterým dochází během provádění konzistentním způsobem. Aplikace by měla zachytit všechny chyby a buď selhání bezpečné nebo uzavřené.

Měli byste také zajistit, že chyby jsou protokolovány s dostatečným uživatelským kontextem k identifikaci podezřelé nebo škodlivé aktivity. Protokoly by měly být uchovávány po dostatečně dlouhou dobu, aby bylo možné opožděnou forenzní analýzu. Protokoly by měly být ve formátu, který lze snadno spotřebovat řešením pro správu protokolu. Ujistěte se, že jsou aktivovány výstrahy pro chyby související se zabezpečením. Nedostatečné protokolování a monitorování umožňuje útočníkům dále útočit na systémy a udržovat trvalost.

### <a name="take-advantage-of-error-and-exception-handling"></a>Využijte využití chyb a zpracování výjimek

Implementace správné chyby a [zpracování výjimek](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) je důležitou součástí obranného kódování. Zpracování chyb a výjimek je důležité pro zajištění spolehlivého a bezpečného systému. Chyby při zpracování chyb mohou vést k různým druhům chyb zabezpečení, jako je například únik informací útočníkům a pomoc útočníkům lépe porozumět vaší platformě a návrhu.

Zajistit:

- Výjimky zpracováváte centralizovaným způsobem, abyste se vyhnuli duplicitním [blokům try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) v kódu.

- Všechny neočekávané chování jsou zpracovány uvnitř aplikace.

- Zprávy, které jsou zobrazeny uživatelům, neuniknou kritická data, ale poskytují dostatek informací k vysvětlení problému.

- Výjimky jsou protokolovány a poskytují dostatek informací pro forenzní nebo incidentové týmy k prošetření.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) poskytuje prvotřídní prostředí pro [zpracování chyb a výjimek,](../../logic-apps/logic-apps-exception-handling.md) které jsou způsobeny závislými systémy. Logic Apps můžete použít k vytváření pracovních postupů k automatizaci úkolů a procesů, které integrují aplikace, data, systémy a služby napříč podniky a organizacemi.

### <a name="use-logging-and-alerting"></a>Použití protokolování a upozorňování

[Protokolujte](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) své problémy se zabezpečením pro šetření zabezpečení a aktivujte výstrahy o problémech, abyste zajistili, že lidé o problémech budou včas vědět. Povolte auditování a protokolování všech součástí. Protokoly auditu by měly zachytit kontext uživatele a identifikovat všechny důležité události.

Zkontrolujte, zda nezaznamenáváte žádná citlivá data, která uživatel odešle na váš web. Příklady citlivých údajů zahrnují:

- Pověření uživatele
- Čísla sociálního pojištění nebo jiné identifikační údaje
- Čísla kreditních karet nebo jiné finanční informace
- Informace o zdravotním stavu
- Soukromé klíče nebo jiná data, která lze použít k dešifrování šifrovaných informací
- Informace o systému nebo aplikaci, které lze použít k efektivnějšímu útoku na aplikaci

Ujistěte se, že aplikace monitoruje události správy uživatelů, jako jsou úspěšná a neúspěšná přihlášení uživatelů, resetování hesla, změny hesla, uzamčení účtu a registrace uživatelů. Protokolování pro tyto události pomáhá zjistit a reagovat na potenciálně podezřelé chování. Umožňuje také shromažďovat provozní data, například kdo přistupuje k aplikaci.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme ovládací prvky zabezpečení a aktivity, které vám pomohou vyvíjet a nasazovat zabezpečené aplikace.

- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)
