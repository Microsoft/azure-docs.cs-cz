---
title: Návrh zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které byste měli zvážit během fáze požadavků a návrhu projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653286"
---
# <a name="design-secure-applications-on-azure"></a>Návrh zabezpečených aplikací v Azure
V tomto článku Představujeme aktivity související se zabezpečením a ovládací prvky, které je třeba zvážit při návrhu aplikací pro cloud. Školení prostředků spolu s bezpečnostní otázky a koncepty, které je vhodné zvážit během požadavky a navrhnout fáze Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) se vztahuje. Cílem je může pomoci určit činností a služeb Azure, které slouží k návrhu bezpečnější aplikace.

Následující fáze SDL jsou popsané v tomto článku:

- Školení
- Požadavky
- Návrh

## <a name="training"></a>Školení
Než začnete vyvíjet cloudové aplikace, trvat dobu vysvětlení zabezpečení a ochrany osobních údajů v Azure. Provedením tohoto kroku můžete snížit množství a závažnosti chyb zabezpečení zneužitelné ve vaší aplikaci. Budete mít více připravení na odpovídajícím způsobem reagovat na neustále se měnící světě hrozeb.

Použijte následující zdroje během fáze školení, seznamte se se službami Azure, které jsou k dispozici pro vývojáře a s osvědčenými postupy pro zabezpečení v Azure:

  - [Příručka pro vývojáře k Azure](https://azure.microsoft.com/campaigns/developer-guide/) ukazuje, jak začít s Azure. V Průvodci se dozvíte, služby, které můžete použít pro provozování vašich aplikací, ukládání dat, začlenit intelligence, vývoj IoT aplikací a nasazení řešení tak efektivní a zabezpečený.

  - [Příručka Začínáme pro vývojáře v Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) poskytuje základní informace pro vývojáře, kteří chtějí Začínáme s využitím platformy Azure pro potřeby jejich vývoje.

  - [Sady SDK a nástroje](https://docs.microsoft.com/azure/index#pivot=sdkstools) popisuje nástroje, které jsou k dispozici v Azure.

  - [Služby Azure DevOps](https://docs.microsoft.com/azure/devops/) poskytuje vývojové nástroje pro spolupráci. Tyto nástroje zahrnují vysoce výkonných kanálů, bezplatná úložiště Git, konfigurovatelné karty kanban a rozsáhlé automatizované a cloudové zátěžové testování.
    [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) spojuje naše materiály pro seznámení s DevOps postupy Git správy verzí, agilní metodologie, jak Pracujeme s DevOps v Microsoftu a jak můžete vyhodnotit vlastní postup DevOps.

  - [Prvních 5 pět položek zabezpečení, které je třeba zvážit před doručením (push) do produkčního prostředí](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) ukazuje, jak pomoci zabezpečit vaše webové aplikace v Azure a Chraňte své aplikace před webovými útoky aplikace nejběžnější a nebezpečné.

  - [Secure DevOps Kit pro Azure](https://azsk.azurewebsites.net/index.html) je kolekce skripty, nástroje, rozšíření a automatizace, které určeného pro komplexní Azure předplatném a prostředcích požadavkům na zabezpečení týmy DevOps, které používají rozsáhlé služby automation. Sady Secure DevOps Kit pro Azure můžete zobrazit jak hladce integrovat zabezpečení do nativních pracovních postupů DevOps. Sadě řeší nástroje jako testy ověření zabezpečení (SVTs), které může pomoci vývojářům psát bezpečný kód a otestovat konfiguraci zabezpečení svých cloudových aplikací v rané fázi a kódování fází vývoje.

  - [Osvědčené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) poskytuje kolekci zabezpečení osvědčené postupy při práci podle návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

## <a name="requirements"></a>Požadavky
Fáze definice požadavků je zásadní krok při definování co vaše aplikace je a co bude po vydání. Požadavky na fázi je také doba vnímat kontrolní mechanismy zabezpečení, že vytvoříte do vaší aplikace. V této fázi můžete také začít s kroky, které vás přenesou v průběhu proces SDL vydání a nasazení zabezpečené aplikace.

### <a name="consider-security-and-privacy-issues"></a>Vezměte v úvahu problémy zabezpečení a ochrana osobních údajů
Tato fáze je nejlepší vzít v úvahu základní zabezpečení a ochrany osobních údajů. Definování přijatelnou úroveň zabezpečení a ochrany osobních údajů na začátku projektu pomáhá týmu:

- Vysvětlení rizika spojená s problémy se zabezpečením.
- Zjistit a opravit chyby zabezpečení během vývoje.
- Použití zavedené úrovní zabezpečení a ochrany osobních údajů v rámci celého projektu.

Při zápisu požadavků pro vaši aplikaci, je nutné zvážit kontrolní mechanismy zabezpečení, které pomáhají zabezpečit vaše aplikace a data.

### <a name="ask-security-questions"></a>Klást otázky zabezpečení
Klást otázky zabezpečení, jako jsou:

  - Obsahuje aplikaci citlivá data?

  - Nemá aplikaci shromažďovat nebo ukládání dat, která je třeba dodržovat standardy a programů dodržování předpisů, jako je [federální finanční instituce zkoumání Rady (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) nebo [odvětví platebních karet Data Security Standards (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - Moje aplikace shromažďovat a obsahovat citlivé osobní nebo zákaznický data, která je možné, samostatně nebo s jinými obraťte se na informace, pokud chcete zjistit, nebo vyhledejte jedné osobě?

  - Moje aplikace shromažďování nebo obsahovat data, která slouží pro přístup k medical jednotlivce, vzdělávání, finanční, nebo informace o zaměstnání? Identifikace míře citlivosti dat v průběhu fáze požadavky pomáhá klasifikovat data a určit způsob ochrany dat, které budete používat pro vaši aplikaci.

  - Kde a jak se ukládají Moje data? Zvažte, jak budete monitorovat služby úložiště, které vaše aplikace používá pro všechny neočekávané změny (například pomalejší doby odezvy). Budete moci ovlivnit protokolování, aby se shromažďovat detailní data a analyzovat problém do hloubky?

  - Bude Moje aplikace dostupné pro veřejnost (na Internetu) nebo interně jedinou? Pokud je aplikace dostupná, jak můžete zabezpečit data, která mohou být shromažďovány z používání v ukázka nesprávného způsobu? Pokud vaše aplikace je k dispozici interně pouze, zvažte, kdo ve vaší organizaci měli přístup k aplikaci a jak dlouho by měly mít přístup.

  - Rozumíte modelu identity před zahájením práce na návrhu vaší aplikace? Jak se můžete zjistit, kdo, Řekněme, že jsou uživatelé jsou a jaké je uživatel autorizovaný udělat?

  - Moje aplikace provádět citlivé nebo důležité úkoly (například přenosu peníze, odemknutí dveří nebo doručování lékařství)?
    Vezměte v úvahu, jak se ověřit, zda je uživatel provádějící citlivá úloha oprávnění k provedení úlohy a ověřování, který říká, že jsou osoba je. Autorizace (AuthZ) je v rámci udělení oprávnění objektu zabezpečení v ověření zabezpečení něco udělat. Ověření (AuthN) je v rámci náročné stranu pro legitimních přihlašovacích údajů.

  - Moje aplikace provádět žádné rizikové softwaru aktivity, jako jsou uživatelé si můžou nahrávat nebo stahovat soubory nebo jiná data? Pokud vaše aplikace provést rizikové aktivity, zvažte, jak vaše aplikace bude chránit uživatele před zpracování škodlivé soubory nebo data.

### <a name="review-owasp-top-10"></a>Kontrola OWASP top 10
Vezměte v úvahu revize [ <span class="underline">OWASP Top 10 aplikace bezpečnostní rizika</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
Prvních 10 OWASP řeší důležité bezpečnostní rizika pro webové aplikace.
Sledování těchto rizik zabezpečení můžete požadavků a návrhu rozhodnutí, které minimalizují těchto rizik ve vaší aplikaci.

Je důležité uvažovat kontrolní mechanismy zabezpečení, aby se zabránilo narušení.
Ale můžete také chtít [předpokládaného narušení zabezpečení](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) dojde. Porušení zabezpečení za předpokladu, že vám pomůže zodpovědět některé důležité otázky týkající se zabezpečení předem, abyste nemuseli dělat oni zodpovězení ve stavu nouze:

  - Jak se detekuje útok?

  - Co se udělat, pokud jde o útoku nebo porušení zabezpečení?

  - Jak budu obnovit před útoky, jako jsou data úniku nebo manipulace?

## <a name="design"></a>Návrh

Fáze návrhu je velmi důležité pro stanovení osvědčené postupy pro návrh a funkční specifikace. Také je důležité pro provádění analýzy rizik, která pomáhá zmírnit problémy zabezpečení a ochrana osobních údajů v celém projektu.

Pokud jsou splněné požadavky na zabezpečení a použijete koncepty zabezpečeného návrhu, můžete vyhnout nebo je minimalizovat příležitosti pro chyby zabezpečení. Chyba zabezpečení je dohledu v návrhu aplikace, kterým by uživatel k provádění škodlivých aktivit nebo neočekávané akcí po vydání vaší aplikace.

Během fáze návrhu také přemýšlejte o tom, jak můžete použít zabezpečení vrstvy. jeden stupeň ochrany, není dostatečně nutně. Co se stane, pokud útočník získá za firewall webových aplikací (WAF)? Chcete, aby jiný ovládací prvek zabezpečení na místě se chránit před tohoto útoku.

S myslete na to probereme následující pojmy zabezpečeného návrhu a ovládací prvky zabezpečení, které je třeba vyřešit jako při návrhu zabezpečených aplikací:

- Použijte zabezpečené kódování knihovny a představuje softwarové rozhraní.
- Vyhledejte zranitelné komponenty.
- Použijte při návrhu aplikace modelování hrozeb.
- Snižte útoky.
- Přijmout zásady identity jako primárního obvodu zabezpečení.
- Vyžadovat opakované ověření pro důležité transakce.
- K zabezpečení klíčů, pověření a dalších tajných kódů pomocí řešení správy klíčů.
- Chraňte citlivá data.
- Implementujte odolný proti selhání opatření.
- Využijte výhod zpracování chyb a výjimek.
- Použití protokolování a upozorňování.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Použít zabezpečené kódování knihovny a představuje softwarové rozhraní

Pro vývoj použijte zabezpečené kódování knihovny a architektura softwaru, který obsahuje vložené zabezpečení. Vývojáři mohou využít stávající, prověřené funkcí (šifrování, kanalizace vstupní, výstupní kódování, klíče nebo připojovací řetězce a cokoliv jiného, co může být považovaná za řízení zabezpečení) namísto vývoj ovládacích prvků zabezpečení od začátku. To pomáhá chránit proti související se zabezpečením návrhu a implementace chyby.

Ujistěte se, že používáte nejnovější verzi rozhraní framework a všechny funkce zabezpečení, které jsou k dispozici v rámci. Společnost Microsoft nabízí komplexní [sadu vývojových nástrojů](https://azure.microsoft.com/product-categories/developer-tools/) pro všechny vývojáře pracující na libovolnou platformu a jazyk, dodávat cloudové aplikace. Můžete kódovat v jazyce podle vašeho výběru výběrem z různých [sady SDK](https://azure.microsoft.com/downloads/).
Můžete využít výhod plně vybavené integrované vývojové prostředí (IDE) a editorů, které mají pokročilé schopnosti ladění a integrovanou podporou Azure.

Společnost Microsoft nabízí širokou škálu [jazyky, platformy a nástroje](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) , můžete použít k vývoji aplikací v Azure. Příkladem je [Azure pro vývojáře v rozhraní .NET a .NET Core](https://docs.microsoft.com/dotnet/azure/). Pro každý jazyk a rozhraní, které nabízíme najdete rychlé starty, kurzy a Reference k rozhraní API můžete začít rychle pracovat.

Azure nabízí širokou škálu služeb, které můžete použít k hostování webů a webových aplikací. Tyto služby umožňují vyvíjet ve svém oblíbeném jazyce, zda je .NET, .NET Core, Javy, Ruby, Node.js, PHP nebo Python.
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (Web Apps) je jedním z těchto služeb.

Výkon Microsoft Azure Web Apps přidá do vaší aplikace. Zahrnuje zabezpečení, Vyrovnávání zatížení, automatické škálování a automatizovanou správu. Můžete také využít možnosti DevOps sady ve službě Web Apps, jako je Správa balíčků, přípravná prostředí, vlastní domény, certifikáty SSL/TLS a průběžné nasazování z Azure DevOps, Githubu, Docker Hubu a dalších zdrojů.

Azure nabízí další služby, které můžete použít k hostování webů a webových aplikací. Pro většinu scénářů je nejlepší volbou služba Web Apps. Architektura micro služby, zvažte [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých se kód spouští, zvažte službu [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Další informace o tom, jak si vybrat mezi tyto služby Azure, najdete v článku [srovnání Azure App Service, Virtual Machines, Service Fabric a Cloud Services](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Použití aktualizací pro komponenty

Aby se zabránilo ohrožení zabezpečení, by měl průběžně inventáře komponenty na straně klienta i stranu serveru (například architektury a knihovny) a jejich závislostí pro aktualizace. Neustále jsou vydávány nová ohrožení zabezpečení a verze aktualizace softwaru. Ujistěte se, že máte probíhající plán pro monitorování, třídění a aktualizace nebo změny konfigurace do knihovny a komponenty, které používáte.

Najdete v článku [Open Web Application zabezpečení projektu (OWASP)](https://www.owasp.org/index.php/Main_Page) na stránce [komponenty pomocí známých chyb zabezpečení](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) nástroj návrhy. Můžete také odebírat e-mailové výstrahy pro chyby zabezpečení, které se vztahují k součásti, které používáte.

### <a name="use-threat-modeling-during-application-design"></a>Použijte při návrhu aplikace modelování hrozeb

Modelování ohrožení je proces identifikace potenciálních bezpečnostních hrozeb pro vaše podnikání a aplikace a pak zajistit, že správné zmírnění rizik jsou na místě. Do procesu SDL Určuje, že týmy by měly zapojit do během fáze návrhu, při řešení potíží s potenciální je poměrně snadné a hospodárné modelování hrozeb. Použití před internetovými útoky modelování ve fázi návrhu může výrazně snížit vaše celkové náklady na vývoj.

Pro usnadnění hrozeb proces modelování, jsme navrhovali [nástroj pro modelování hrozeb SDL](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) s odborníky nesouvisí se zabezpečením v úvahu. Tento nástroj usnadňuje modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny o tom, jak vytvořit a analyzovat modely hrozeb.

Modelování návrhu aplikace a vytváření výčtů [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) hrozeb – falšování, manipulace, Odvolatelnost, zpřístupnění informací, dostupnost služby a zvýšení úrovně oprávnění – ve všech důvěryhodnosti hranice ukázal efektivní způsob k zachycení chyb návrhu raném stádiu. Následující tabulka uvádí hrozby ROZTEČ a poskytuje některá příklad zmírnění rizik, které používají funkce, které poskytuje Azure. Tyto způsoby zmírnění rizik nebudou fungovat ve všech situacích.

| Před internetovými útoky | Vlastnost zabezpečení | Ke zmírnění potenciální platformou Azure. |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falšování identity               | Ověřování        | [Vyžadovat připojení prostřednictvím protokolu HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulace              | Integrita             | Ověřte certifikáty SSL/TLS. Aplikace, které používají protokol SSL/TLS plně ověřte certifikáty X.509 entity, které se připojují. Použití certifikátů v Azure Key Vault [spravovat vaše x509 certifikáty](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| odmítnutí            | Nepopiratelnost odpovědnosti       | Povolení služby Azure [monitorováním a diagnostikou](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Zpřístupnění informací | Důvěrnost       | Šifrovat citlivá data [v klidovém stavu](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) a [přenášená](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Odepření služby      | Dostupnost          | Monitorovat metriky výkonu pro potenciální odmítnutí podmínek služby. Implementace připojení filtry. [Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), v kombinaci s osvědčené postupy pro navrhování aplikací, poskytuje ochranu před útoky DDoS.|
| Zvýšení oprávnění | Authorization         | Použití Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Snižte útoky

Rovinu útoku je celkový součet kterých může dojít k potenciální ohrožení zabezpečení. V tomto dokumentu abychom se zaměřit na aplikace pro možný útok.
Zaměřuje se na ochraně aplikace před útoky. Rychlý a jednoduchý způsob, jak minimalizovat útoky je odebrat nevyužité prostředky a kód ze své aplikace. Menší aplikace, menší útoky. Například odeberte:

- Kód pro funkce, které nebyly dosud všeobecně dostupné.
- Kód na podporu ladění.
- Síťová rozhraní a protokoly, které nepoužívají nebo jsou zastaralé.
- Virtuální počítače a další prostředky, které nepoužíváte.

Provádění pravidelných vyčištění prostředků a zajištění, že odeberete nepoužívané kódu jsou skvělé způsoby, jak zajistit, že jsou méně možností pro útočníky vůči útokům.

Podrobnější a podrobné způsob, jak snížit útoky se na dokončení analýzy možností útoku. Analýzu útoku umožňuje mapovat částí systému, který je potřeba zkontrolovat a jsou testovány z hlediska ohrožení zabezpečení.

Účelem analýzu útoku je pochopit riziko oblasti v aplikaci tak, aby byly vědět, jaké části aplikace, které jsou otevřené k útoku na vývojáře a odborníky na zabezpečení. Pak najdete způsoby, jak minimalizovat sledování této potenciální, kdy a jak pro možný útok změny a co to znamená, že z hlediska rizik.

Analýza možností útoku pomáhá identifikovat:

- Funkce a části systému, budete muset zkontrolovat a testovat ohrožení zabezpečení.
- S vysokým rizikem oblasti kódu, které vyžadují ochranu defense v hloubce (částí systému, který je potřeba chránit).
- Pokud příkaz alter pro možný útok a musíme ho obnovit posouzení hrozeb.

Snížení příležitosti pro útočníci zneužít ohrožená místa potenciální slabé místo nebo ohrožení zabezpečení vyžaduje, abyste důkladně analyzovat celkové útok vaší aplikace. Zahrnuje také zakázáním nebo omezením přístupu ke službám systému, použití principu nejnižší úrovně oprávnění a použití vrstvy ochrany, kdykoli je to možné.

Můžeme probírat [provádějící surface revize útoku](secure-develop.md#conduct-attack-surface-review) ve fázi ověření do procesu SDL.

> [!NOTE]
> **Jaký je rozdíl mezi modelování hrozeb a útoků analýzu?**
Modelování ohrožení je proces identifikace potenciálních bezpečnostních hrozeb pro vaši aplikaci a zajištění řádné zmírnění proti hrozbám na místo. Analýza prostoru pro útok identifikuje s vysokým rizikem oblasti kódu, které jsou otevřené pro útok. Zahrnuje hledání způsobů, jak chránit aplikace a revizí a testování těchto oblastí kódu před nasazením aplikace s vysokým rizikem oblasti.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásady identity jako primárního obvodu zabezpečení

Při návrhu cloudových aplikací, je potřeba rozbalit zaměření obvodu zabezpečení přístup orientovaný na síti zaměřená na identitu přístup. V minulosti byl obvodu zabezpečení místní sítě organizace. Většina návrhy zabezpečení místních používat síť jako primární zabezpečení pivot. Pro cloudové aplikace jsou lépe vyhovovat vycházet identity jako primárního obvodu zabezpečení.

Co vám pomohou vyvíjet zaměřená na identitu přístup k vývoji webových aplikací:

- Vynutit ověřování Multi-Factor Authentication pro uživatele.
- Použijte silné ověřování a autorizace platformy.
- Použití principu nejnižší úrovně oprávnění.
- Implementovat přístup just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Vynutit ověřování Multi-Factor Authentication pro uživatele

Pomocí dvojúrovňového ověřování. Dvoufaktorové ověřování je aktuálním standardem pro ověřování a autorizaci, protože se eliminuje slabá místa zabezpečení, které vyplývají z uživatelského jména a hesla typy ověřování. Přístup k rozhraním Azure management (Azure portal/vzdáleného prostředí PowerShell) a ke službám určených pro zákazníky by navržené a nakonfigurovány pro použití [ověřování Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Použijte silné ověřování a autorizace platformy

Mechanismy ověřování a autorizace, které byly zadané platformy nahrazujícím vlastní kód. Je to proto vývoj vlastního ověřovacího kódu mohou být náchylné k chybám. Komerční kódu (např. z Microsoft) často je široce zkontrolovat, jestli zabezpečení. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je řešení Azure pro správu identit a přístupů. Tyto nástroje služby Azure AD a služby pomáhají s vývojem pro zabezpečené:

- [Azure AD platformou identity (Azure AD pro vývojáře)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) je Cloudová služba identit, které vývojáři vytvářet aplikace, které bezpečně přihlásit uživatele. Azure AD pomáhá vývojářům, kteří zodpovídají za tvorbu jednoho tenanta, obchodní aplikace (LOB) a vývojáři, kteří chtějí vyvíjet aplikace s více tenanty. Kromě základních přihlásí aplikace, které jsou vytvořeny pomocí služby Azure AD můžete volat APIs Microsoft i vlastní rozhraní API, která jsou postavené na platformě Azure AD. Platforma identit Azure AD podporuje standardní protokoly jako jsou OAuth 2.0 a OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) je služba pro správu identit můžete použít k přizpůsobení a řízení, jak se zákazníci registrují do, přihlaste se a při používání vašich aplikací spravují své profily. Patří sem aplikace vyvinuté pro iOS, Android a .NET, mimo jiné. Azure AD B2C umožňuje tyto akce při ochraně identit zákazníků.

#### <a name="apply-the-principle-of-least-privilege"></a>Použití principu nejnižší úrovně oprávnění

Koncept [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) znamená, že poskytuje přesnou úroveň přístupu a řízení, potřebují dělat svou práci a žádné další uživatelé.

Softwarový vývojář bude potřebovat oprávnění správce domény? Asistent by potřebují přístup k správu ovládacích prvků na svém osobním počítači? Vyhodnocování přístup k softwaru se nijak neliší. Pokud používáte [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) poskytnout různé možnosti uživatelů a autority ve vaší aplikaci, nebude dát všem uživatelům přístup ke všemu, co. Omezením přístupu k co je potřeba pro každou roli, můžete omezit riziko výskytu problému zabezpečení.

Ujistěte se, že vaše aplikace vynucuje [nejnižší možná oprávnění](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) v celé jeho vzorce přístupu.

> [!NOTE]
>  Pravidla nejnižší možná oprávnění se mají použít na software a uživatelům vytvářejícím software. Vývojáři softwaru může být velmi velké riziko pro zabezpečení IT, pokud jim neudělíte příliš mnoho přístup. Vývojář se zlými úmysly nebo příliš mnoho přístup může být závažné důsledky. Doporučujeme vám, že pravidla nejnižší možná oprávnění použita pro vývojáře v průběhu životního cyklu vývoje.

#### <a name="implement-just-in-time-access"></a>Implementovat přístup just-in-time

Implementace *just-in-time* přístup (JIT) dále snížit dobu expozice oprávnění. Použití [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) na:

- Uživatelům udělte oprávnění, které potřebují pouze JIT.
- Přiřazení rolí zkrácený dobu bez obav, že jsou oprávnění automaticky odvolat.

### <a name="require-re-authentication-for-important-transactions"></a>Vyžadovat opakované ověření pro důležité transakce

[Padělání žádosti více webů](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (označované také jako *XSRF* nebo *CSRF*) je útok na hostované webové aplikace, v které škodlivý webové aplikace ovlivňuje interakce mezi klientského prohlížeče a webové aplikace, která důvěřuje prohlížeče. Útoky proti padělání žádosti více webů je možné, protože webové prohlížeče odeslat některé typy ověřování tokenů automaticky při každé žádosti na web.
Tato forma využívání se taky říká *jedním kliknutím útoku* nebo *relace záleželo* protože využívá výhod platformy útoku uživatele ověřený relace.

Nejlepší způsob, jak chránit před tento druh útoku je požádali uživatele o něco, co před každou transakci důležitá, například nákup, deaktivaci účtu nebo změna hesla můžete zadat jenom na uživatele. Může požádat uživatele, aby znovu zadat heslo, dokončení testu captcha, nebo odešlete token tajného kódu, který by měla pouze uživatele. Nejběžnější přístup je token tajného kódu.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Pomocí řešení správy klíčů pro zabezpečení klíčů, pověření a dalších tajných kódů

Došlo ke ztrátě klíčů a přihlašovacích údajů je běžný problém. Jediné, co horší ztráty klíčů a přihlašovacích údajů, je, aniž by bylo neoprávněným stran získat přístup k nim. Útočníci mohou využít výhod automatizované a manuální techniky, které chcete najít klíče a tajné kódy, které jsou uložené v úložištích kódu jako GitHub. Neumisťujte klíčů a tajných kódů v těchto úložištích veřejného kódu nebo na jiném serveru.

Vždy umístíte certifikáty, klíče, tajné klíče a připojovací řetězce v rámci řešení pro správu klíčů. Můžete použít centralizované řešení, ve kterém jsou uloženy klíče a tajné klíče v modulech hardwarového zabezpečení (HSM). Azure vám poskytne modulu hardwarového zabezpečení v cloudu s využitím [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

Key Vault je *úložiště tajných kódů*: je centralizovaná Cloudová služba pro ukládání tajných klíčů aplikací. Key Vault uchovává důvěrná data bezpečné uchovávání tajných klíčů aplikací v jednom centrálním umístění a poskytuje zabezpečený přístup, oprávnění k řízení a protokolování přístupu.

Tajné kódy jsou uložené v jednotlivých *trezory*. Každý trezor má vlastní konfigurace a zásady zabezpečení pro řízení přístupu. Dostanete k datům prostřednictvím rozhraní REST API nebo Klientská sada SDK, která je k dispozici pro většinu programovacích jazyků.

> [!IMPORTANT]
> Služba Azure Key Vault je navržená k ukládání tajných kódů konfigurace pro serverové aplikace. Není určena pro ukládání dat, která patří uživatelům aplikace. Toto je zohledněno v jeho výkonové charakteristiky, rozhraní API a modelu nákladů.
>
> Uživatelská data by měla být uložena kdekoli, jako v instanci Azure SQL Database, která je transparentní šifrování dat (TDE) nebo v účtu úložiště, který používá Azure Storage Service Encryption. Tajné kódy, které se používají v aplikaci pro přístup k úložišti těchto dat můžete uchovávat ve službě Azure Key Vault.

### <a name="protect-sensitive-data"></a>Ochrana citlivých dat

Ochrana dat je důležitou součástí strategie zabezpečení.
Klasifikace dat a identifikace ochrany vašich dat, musí vám usnadní návrh aplikace s ohledem na bezpečnost data. Klasifikace (kategorizace) data ukládaná citlivosti a dopad na chod firmy pomáhá vývojářům určit rizika, které jsou spojeny s daty.

Označte všechna data příslušných jako citlivé při návrhu datových formátů. Ujistěte se, že aplikace považuje za citlivé na příslušné data. Tyto postupy mohou přispět k ochraně citlivých dat:

- Použijte šifrování.
- Vyhnuli pevnému zakódování tajné kódy jako jsou klíče a hesla.
- Ujistěte se, že jsou splněné řízení přístupu a auditování.

#### <a name="use-encryption"></a>Používat šifrování

Ochrana dat by měl být nedílnou součást vaší strategie zabezpečení.
Pokud vaše data jsou uložená v databázi nebo přesun vpřed a zpět mezi umístění, použijte šifrování [dat v klidovém stavu](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (během činnosti v databázi) a šifrování [přenášená data](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (na cestě do a z uživatelů, databáze, API nebo koncový bod služby). Doporučujeme vždy používat protokoly SSL/TLS pro výměnu dat. Ověřte, že používáte nejnovější verzi TLS pro šifrování (v současné době Toto je verze 1.2).

#### <a name="avoid-hard-coding"></a>Vyhnuli pevnému zakódování

Některé možnosti by nikdy neměly být pevně zakódované v softwaru. Některé příklady jsou názvy hostitelů nebo IP adresy, adresy URL, e-mailové adresy, uživatelská jména, hesla, klíče účtu úložiště a jiných kryptografických klíčů. Zvažte implementaci požadavky týkající se, co může nebo nemůže být pevně zakódované v kódu, včetně v části komentáře kódu.

Při uvádění komentáře v kódu, ujistěte se, že Neukládat žádné citlivé údaje. Jedná se o e-mailovou adresu, hesla, připojovací řetězce, informace o vaší aplikaci, která by být známé pouze někým ve vaší organizaci a cokoliv jiného, která může přidělit útočník výhodu v útocích vaše aplikace nebo organizace .

V podstatě předpokládat, že všechno, co ve svém vývojářském projektu bude veřejná znalosti při nasazení. Vyhněte citlivá data jakéhokoli druhu v projektu.

Dříve jsme probírali [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault můžete ukládat tajné kódy jako jsou klíče a hesla místo pevného kódování je. Při použití služby Key Vault v kombinaci s spravovaných identit pro prostředky Azure, webové aplikace Azure přístupná hodnoty tajných kódů konfigurace snadno a bezpečně bez ukládání do správy zdrojového kódu nebo konfigurace jakýchkoli tajných kódů. Další informace najdete v tématu [správu tajných kódů v inteligentních aplikacích serveru se službou Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementujte odolný proti selhání míry

Aplikace musí být schopna zpracovávat [chyby](https://docs.microsoft.com/dotnet/standard/exceptions/) , dojde k během provádění konzistentním způsobem. Aplikace by měly zachytit všechny chyby a buď neúspěšné bezpečné "nebo" Uzavřeno.

Také se ujistěte, že chyby se protokolují pod dostatečný kontext uživatele k identifikaci škodlivé nebo podezřelé aktivity. Protokoly uchovávání pro dostatek času umožňující zpožděné forenzní analýzu. Protokoly by měl být ve formátu, který může snadno používat řešení pro správu protokolů. Ujistěte se, že se aktivují upozornění, chyby, které se týkají zabezpečení. Nedostatečná protokolování a monitorování útočníkům umožňuje, aby dál útoku systémy a udržovat trvalosti.

### <a name="take-advantage-of-error-and-exception-handling"></a>Využijte výhod zpracování chyb a výjimek

Implementace opravte chyby a [zpracování výjimek](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) je důležitou součástí obranné kódování. Zpracování chyb a výjimek jsou důležité k zajištění systému, spolehlivou a zabezpečenou. Chyby při zpracování chyb může vést k různé druhy ohrožení zabezpečení, jako jsou informace, které útočníci nevrácení a pomáhá útočníci lépe pochopit, jaké platformy a návrhu.

Ujistěte se, že:

- Zpracování výjimek centralizovanou vyhnout duplicitní [try/catch – bloky](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) v kódu.

- V aplikaci jsou zpracovány všechny neočekávané chování.

- Zprávy, které se uživatelům zobrazí není způsobit únik těchto důležitých dat, ale neposkytuje dostatek informací, které popisují potíže.

- Výjimky jsou protokolovány a poskytují dostatek informací pro forenzní účely nebo incidentem týmy odpovědi k prozkoumání.

[Služba Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) poskytuje prvotřídní prostředí pro [zpracování chyb a výjimek](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) , které jsou způsobeny závislých systémů. Logic Apps můžete použít k vytvoření pracovní postupy pro automatizaci úloh a procesů, které se integrují aplikace, data, systémy a služby napříč podniky a organizacemi.

### <a name="use-logging-and-alerting"></a>Použití protokolování a upozorňování

[Protokol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) problémy zabezpečení pro zabezpečení šetření a vyvolá upozornění o problémech, které chcete zajistit, že lidé vědět o problémech včas. Povolte auditování a protokolování pro všechny komponenty. Protokoly auditu byste zaznamenat uživatelský kontext a identifikovat všechny důležité události.

Zkontrolujte, jestli není přihlásíte citlivá data, která uživatel odešle na váš web. Příklady citlivá data:

- Přihlašovací údaje uživatele
- Čísla sociálního zabezpečení nebo jiné identifikační informace
- Čísla platebních karet nebo jiných finančních informací
- Informace o stavu
- Privátní klíče nebo jiná data, která slouží k dešifrování šifrovaných informací
- Systém nebo aplikace, informace, které lze použít k efektivněji útoku na aplikace

Ujistěte se, že aplikace sleduje události správy uživatele jako je přihlášení úspěšné i neúspěšné uživatelů, resetování hesla, změny hesel, uzamčení účtu a registrace uživatele. Protokolování pro tyto události vám pomůže zjistit a reagovat na potenciálně podezřelé chování. Také vám umožňuje shromažďovat data operace, například kdo přistupuje k aplikaci.

## <a name="next-steps"></a>Další postup
V následujících článcích doporučujeme kontrolních mechanismů pro zabezpečení a aktivity, které vám umožňují vývoj a nasazení zabezpečené aplikace.

- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečené aplikace](secure-deploy.md)
