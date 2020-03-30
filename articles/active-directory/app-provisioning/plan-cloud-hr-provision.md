---
title: Plánování cloudové hr aplikace pro zřizování uživatelů Služby Azure Active Directory
description: Tento článek popisuje proces nasazení integrace cloudových hr systémů, jako je Workday a SuccessFactors, s Azure Active Directory. Integrace Azure AD s vaším cloudovým hr systémem má za následek kompletní systém správy životního cyklu identity.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522428"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Plánování cloudové hr aplikace pro zřizování uživatelů Služby Azure Active Directory

V minulosti se pracovníci IT spoléhali na ruční metody vytváření, aktualizace a odstraňování zaměstnanců. Použili metody, jako je nahrávání souborů CSV nebo vlastních skriptů pro synchronizaci dat zaměstnanců. Tyto zřizovací procesy jsou náchylné k chybám, nezabezpečené a těžko spravovatelné.

Ke správě životního cyklu identity zaměstnanců, dodavatelů nebo podmíněných pracovníků nabízí [služba zřizování uživatelů Azure Active Directory (Azure AD)](../app-provisioning/user-provisioning.md) integraci s cloudovými aplikacemi lidských zdrojů (HR). Příklady aplikací zahrnují Workday nebo SuccessFactors.

Azure AD používá tuto integraci k povolení následujících pracovních postupů cloudových aplikací pro hr aplikace:Azure AD uses this integration to enable the following cloud HR application application (app) workflows:

- **Zřizování uživatelů do služby Active Directory:** Zřízení vybraných sad uživatelů z cloudové hr aplikace do jedné nebo více domén služby Active Directory.
- **Zřizování uživatelů pouze cloudu do Azure AD:** Ve scénářích, kde se služba Active Directory nepoužívá, zřizujte uživatele přímo z cloudové aplikace HR do Azure AD.
- **Odepsat zpět do cloudu HR aplikace:** Napište e-mailové adresy a atributy uživatelského jména z Azure AD zpět do cloudové aplikace HR.

> [!NOTE]
> Tento plán nasazení ukazuje, jak nasadit pracovní postupy cloudových aplikací pro lidské zdroje pomocí zřizování uživatelů Azure AD. Informace o tom, jak nasadit automatické zřizování uživatelů do aplikací pro software jako službu (SaaS), naleznete v [tématu Plánování automatického zřizování uživatelů nasazení](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Povolené scénáře lidských zdrojů

Služba zřizování uživatelů Azure AD umožňuje automatizaci následujících scénářů správy životního cyklu identit založených na lidských zdrojů:

- **Nábor nových zaměstnanců:** Když je nový zaměstnanec přidán do cloudové hr aplikace, uživatelský účet se automaticky vytvoří ve službě Active Directory a Azure AD s možností odepsat e-mailovou adresu a uživatelské jméno atributy do cloudu HR aplikace.
- **Aktualizace atributů a profilů zaměstnance:** Když se záznam zaměstnance, jako je jméno, titul nebo manažer, aktualizuje v cloudové hr aplikaci, jeho uživatelský účet se automaticky aktualizuje ve službě Active Directory a Azure AD.
- **Ukončení pracovních poměru zaměstnanců:** Když je zaměstnanec ukončen v cloudové hr aplikaci, jeho uživatelský účet se automaticky deaktivuje ve službě Active Directory a Azure AD.
- **Opětovné přijetí zaměstnanců:** Když je zaměstnanec znovu přijat v cloudové hr aplikaci, jeho starý účet může být automaticky znovu aktivovat nebo znovu zřídit do služby Active Directory a Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Pro koho je tato integrace nejvhodnější?

Integrace cloudových aplikací pro lidské zdroje dat s zřizováním uživatelů Azure AD je ideální pro organizace, které:

- Chcete předem vymyšlené cloudové řešení pro zřizování uživatelů lidských zdrojů v cloudu.
- Vyžadovat přímé zřizování uživatelů z cloudové aplikace HR do služby Active Directory nebo Azure AD.
- Vyžadovat, aby uživatelé byli zřízeni pomocí dat získaných z cloudové hr aplikace.
- Vyžadovat připojení, přesunutí a ponechání uživatelů synchronizovat s jednou nebo více doménovými strukturami služby Active Directory, doménami a vou pouze na základě informací o změnách zjištěných v cloudové hr aplikaci.
- Použijte Office 365 pro e-mail.

## <a name="learn"></a>Informace

Zřizování uživatelů vytváří základ pro průběžné zásady správného řízení identity. Zvyšuje kvalitu podnikových procesů, které se spoléhají na autoritativní data identity.

### <a name="terms"></a>Výrazy

Tento článek používá následující termíny:

- **Zdrojový systém**: Úložiště uživatelů, které Azure AD zřizované z. Příkladem je cloudhr aplikace, jako je Workday nebo SuccessFactors.
- **Cílový systém**: Úložiště uživatelů, které Azure AD zřazuje. Příklady jsou Active Directory, Azure AD, Office 365 nebo jiné aplikace SaaS.
- **Proces Truhláři-Stěhováci- Opouštějící**: Termín používaný pro nové zaměstnance, převody a ukončení pomocí cloudové hr aplikace jako systému záznamů. Proces se dokončí, když služba úspěšně zřídí potřebné atributy cílového systému.

### <a name="key-benefits"></a>Klíčové výhody

Tato schopnost zřizování IT založeného na lidských společnostech nabízí následující významné obchodní výhody:

- **Zvýšení produktivity:** Nyní můžete automatizovat přiřazení uživatelských účtů a licencí Office 365 a poskytnout přístup ke skupinám klíčů. Automatizace úkolů poskytuje novým zaměstnancům okamžitý přístup k jejich pracovním nástrojům a zvyšuje produktivitu.
- **Řízení rizik:** Zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnance nebo členství ve skupinách s daty proudícími z cloudové hr aplikace. Automatizace změn zajišťuje, že se identity uživatelů a přístup ke klíčovým aplikacím automaticky aktualizují, když uživatelé přecházejí nebo opustí organizaci.
- **Řešení dodržování předpisů a zásad správného řízení:** Azure AD podporuje nativní protokoly auditu pro požadavky zřizování uživatelů prováděné aplikacemi zdrojového i cílového systému. Pomocí auditování můžete sledovat, kdo má přístup k aplikacím z jedné obrazovky.
- **Správa nákladů:** Automatické zřizování snižuje náklady tím, že zabraňuje neefektivitě a lidským chybám spojeným s ručním zřizováním. Snižuje potřebu vlastní chod uživatelských zřizovacích řešení vytvořených v průběhu času pomocí starších a zastaralých platforem.

### <a name="licensing"></a>Licencování

Chcete-li nakonfigurovat cloudovou hr aplikaci pro integraci zřizování uživatelů Azure AD, potřebujete platnou [licenci Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) a licenci pro cloudovou hr aplikaci, jako je Workday nebo SuccessFactors.

Potřebujete také platnou licenci předplatného Azure AD Premium P1 nebo vyšší pro každého uživatele, který bude pocházet z cloudové aplikace HR a zřídit službu Active Directory nebo Azure AD. Jakýkoli nesprávný počet licencí vlastněných v cloudové hr aplikaci může vést k chybám během zřizování uživatelů.

### <a name="prerequisites"></a>Požadavky

- Přístup globálního správce Azure AD ke konfiguraci agenta zřizování Azure AD Connect.
- Testovací a produkční instance cloudové hr aplikace.
- Oprávnění správce v cloudové hr aplikaci k vytvoření uživatele systémové integrace a provádění změn v testování dat zaměstnanců pro účely testování.
- Pro zřizování uživatelů do služby Active Directory je k hostování [agenta zřizování Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801)vyžadován server se systémem Windows Server 2012 nebo vyšším s časovou úpravou .NET 4.7.1+ .
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) pro synchronizaci uživatelů mezi službou Active Directory a Azure AD.

### <a name="training-resources"></a>Zdroje pro školení

| **Materiály** | **Odkaz a popis** |
|:-|:-|
| Videa | [Co je zřizování uživatelů ve službě Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Jak nasadit zřizování uživatelů ve službě Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Kurzy | [Seznam výukových programů o integraci aplikací SaaS s Azure AD](../saas-apps/tutorial-list.md) |
| | [Kurz: Konfigurace pracovního dne pro automatické zřizování uživatelů](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Nejčastější dotazy | [Automatické zřizování uživatelů](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Zřizování z pracovního dne do Služby Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura řešení

Následující příklad popisuje architekturu řešení zřizování koncových uživatelů pro běžná hybridní prostředí a zahrnuje:

- **Autoritativní hr data toku z cloudhr aplikace do služby Active Directory.** V tomto toku hr událost (Truhláři-Movers-Leavers proces) je iniciován v tenantovi cloudhr aplikace. Zřizovací služba Azure AD a agent zřizování Azure AD Connect zřazují uživatelská data z klienta cloudových aplikací HR do služby Active Directory. V závislosti na události může vést k vytvoření, aktualizaci, povolení a zakázání operací ve službě Active Directory.
- **Synchronizujte se službou Azure AD a odepište e-mail a uživatelské jméno z místní služby Active Directory do aplikace HR v cloudu.** Po aktualizaci účtů ve službě Active Directory se synchronizuje s Azure AD prostřednictvím Služby Azure AD Connect. E-mailové adresy a atributy uživatelského jména lze zapsat zpět do klienta cloudové hr aplikace.

![Diagram pracovního postupu](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Popis pracovního postupu

V diagramu jsou uvedeny následující klíčové kroky:  

1. **Hr tým** provádí transakce v tenantovi cloudových HR aplikací.
2. **Zřizovací služba Azure AD** spouští naplánované cykly z klienta cloudových hr aplikací a identifikuje změny, které je potřeba zpracovat pro synchronizaci se službou Active Directory.
3. **Služba zřizování Azure AD** vyvolá agenta zřizování Azure AD Connect s datovou částí požadavku, která obsahuje operace pro vytvoření, aktualizaci, povolení a zakázání účtu Služby Active Directory.
4. **Agent zřizování Azure AD Connect** používá ke správě dat účtu služby Active Directory účet služby.
5. **Azure AD Connect** spustí [synchronizaci](../hybrid/how-to-connect-sync-whatis.md) delta pro vyžádat aktualizace ve službě Active Directory.
6. Aktualizace **služby Active Directory** se synchronizují se službou Azure AD.
7. **Služba zřizování Azure AD** odepíná e-mailový atribut a uživatelské jméno z Azure AD do klienta cloudových aplikací pro hr.

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Zvažte vaše organizační potřeby při určování strategie pro toto nasazení ve vašem prostředí.

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, obvykle tak činí kvůli neodpovídajícím očekáváním ohledně dopadu, výsledků a odpovědností. Chcete-li se těmto nástrahám vyhnout, [ujistěte se, že zapojujete správné zúčastněné strany](https://aka.ms/deploymentplans). Také se ujistěte, že role zúčastněných stran v projektu jsou dobře pochopeny. Zdokumentujte zúčastněné strany a jejich vstupy a odpovědnosti za projekt.

Zahrňte zástupce z hr organizace, který může poskytnout vstupy na stávající obchodní procesy hr a identitu pracovníka plus požadavky na zpracování dat úlohy.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně komunikujte s uživateli o tom, kdy a jak se jejich zkušenosti změní. Dejte jim vědět, jak získat podporu, pokud se vyskytnou problémy.

### <a name="plan-a-pilot"></a>Naplánujte si pilotní projekt

Integrace obchodních procesů lidských zdrojů a pracovních postupů identit z cloudové hr aplikace do cílových systémů vyžaduje značné množství ověření dat, transformace dat, čištění dat a komplexního testování, než budete moci nasadit řešení do produkčního prostředí.

Spusťte počáteční konfiguraci v [pilotním prostředí](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) před škálování pro všechny uživatele v produkčním prostředí.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Vyberte cloudhr zřizovací konektor aplikace

Chcete-li usnadnit pracovní postupy zřizování Azure AD mezi cloudovou aplikací HR a službou Active Directory, můžete přidat více aplikací konektorů pro zřizování z galerie aplikací Azure AD:

- **Cloud HR aplikace pro zřizování uživatelů služby Active Directory**: Tato zřizovací konektor aplikace usnadňuje zřizování uživatelských účtů z cloudové hr aplikace do jedné domény služby Active Directory. Pokud máte více domén, můžete přidat jednu instanci této aplikace z galerie aplikací Azure AD pro každou doménu služby Active Directory, kterou potřebujete zřídit.
- **Cloud HR aplikace pro zřizování uživatelů Azure AD**: Zatímco Azure AD Connect je nástroj, který by se měl použít k synchronizaci uživatelů Služby Active Directory do Azure AD, tato zřizovací konektor aplikace se dá použít k usnadnění zřizování uživatelů cloudu pouze z cloudové hr aplikace do jednoho klienta Azure AD.
- **Cloud HR app write-back**: Tato zřizovací konektor aplikace usnadňuje odpis e-mailové adresy uživatele z Azure AD do cloudu HR aplikace.

Na příklad na následujícím obrázku je uveden pracovní den konektor aplikace, které jsou k dispozici v galerii aplikací Azure AD.

![Galerie aplikací portálu Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Vývojový diagram rozhodnutí

Pomocí následujícího diagramu rozhodovacího plynu určete, které cloudové aplikace pro zřizování lidských zdrojů jsou relevantní pro váš scénář.

![Vývojový diagram rozhodnutí](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Návrh topologie nasazení agenta zřizování Azure AD Connect

Zřizování integrace mezi cloud HR aplikace a Active Directory vyžaduje čtyři součásti:

- Tenant cloudových HR aplikací
- Zřizování konektor aplikace
- Agent zřizování Azure AD Connect
- Doména služby Active Directory

Topologie nasazení zřizovacího agenta Azure AD Connect závisí na počtu klientů cloudových aplikací pro hr a podřízených domén služby Active Directory, které plánujete integrovat. Pokud máte více domén služby Active Directory, závisí to na tom, zda jsou domény služby Active Directory souvislé nebo [nesouvislé](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Na základě vašeho rozhodnutí zvolte jeden ze scénářů nasazení:

- Klient jedné cloudové aplikace pro lidské zdroje lidských zdrojů -> cílně na jednu nebo více podřízených domén služby Active Directory v důvěryhodné doménové struktuře
- Klient aplikace cloud hr -> cílit na více podřízených domén v nesouvislé doménové struktuře služby Active Directory

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Klient jedné cloudové aplikace pro lidské zdroje lidských zdrojů -> cílně na jednu nebo více podřízených domén služby Active Directory v důvěryhodné doménové struktuře

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect k nasazení|Dva (pro vysokou dostupnost a převzetí služeb při selhání)
|Počet zřizovacích konektorových aplikací pro konfiguraci|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2+ s přízrakem do geolokované řadiče domény služby Active Directory</br>Může existovat společně se službou Azure AD Connect|

![Tok do místních agentů](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Klient aplikace cloud hr -> cílit na více podřízených domén v nesouvislé doménové struktuře služby Active Directory

Tento scénář zahrnuje zřizování uživatelů z cloudové aplikace HR do domén v nesouvislých doménových strukturách služby Active Directory.

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect k nasazení v místním prostředí|Dvě nesouvislé doménové struktury služby Active Directory|
|Počet zřizovacích konektorových aplikací pro konfiguraci|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2+ s přízrakem do geolokované řadiče domény služby Active Directory</br>Může existovat společně se službou Azure AD Connect|

![Nesouvislá doménová struktura služby Active Directory pro jednoho cloudového klienta lidských zdrojů](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Požadavky na agenta zřizování Azure AD Connect

Cloudová hr aplikace pro zřizování uživatelů služby Active Directory vyžaduje nasazení jednoho nebo více agentů zřizování Azure AD Connect na serverech se systémem Windows 2012 R2 nebo vyšším. Servery musí mít minimálně 4 GB paměti RAM a .NET 4.7.1+ runtime. Zkontrolujte, zda má hostitelský server síťový přístup k cílové doméně služby Active Directory.

Při přípravě místního prostředí průvodce konfigurací agenta azure aslužby zaregistruje průvodce konfigurací zprostředkovatele azure ad, zaregistruje agenta s klientem Azure AD, [otevře porty](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [povolí přístup k adresám URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)a podporuje [konfiguraci odchozího proxy serveru HTTPS](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Zřizovací agent používá ke komunikaci s doménami služby Active Directory účet služby. Před instalací agenta vytvořte účet služby v části Uživatelé a počítače služby Active Directory, který splňuje následující požadavky:

- Heslo, jehož platnost nevyprší
- Delegovaná oprávnění k řízení ke čtení, vytváření, odstraňování a správě uživatelských účtů

Můžete vybrat řadiče domény, které by měly zpracovávat požadavky na zřizování. Pokud máte několik geograficky distribuovaných řadičů domény, nainstalujte zřizovacího agenta do stejné sítě jako upřednostňované řadiče domény. Toto umístění zlepšuje spolehlivost a výkon komplexního řešení.

Pro vysokou dostupnost můžete nasadit více než jednoho agenta zřizování Azure AD Connect. Zaregistrujte agenta pro zpracování stejné sady místních domén služby Active Directory.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Plánování filtrů oborů a mapování atributů

Když povolíte zřizování z cloudové aplikace HR do služby Active Directory nebo Azure AD, portál Azure řídí hodnoty atributů prostřednictvím mapování atributů.

### <a name="define-scoping-filters"></a>Definování filtrů oborů

Pomocí [filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) definujte pravidla založená na atributech, která určují, kteří uživatelé by měli být zřízeni z cloudové aplikace HR do služby Active Directory nebo Azure AD.

Při zahájení procesu truhláře shromážděte následující požadavky:

- Používá se cloudová HR aplikace k přivádění zaměstnanců i podmíněných pracovníků?
- Plánujete používat cloudovou hr aplikaci pro zřizování uživatelů Azure AD ke správě zaměstnanců i podmíněných pracovníků?
- Plánujete zavést cloudovou hr aplikaci pro zřizování uživatelů Azure AD jenom pro podmnožinu uživatelů cloudových HR aplikací? Příkladem mohou být pouze zaměstnanci.

V závislosti na vašich požadavcích můžete při konfiguraci mapování atributů nastavit pole **Obor zdrojového objektu** tak, aby vyberte, které sady uživatelů v cloudové hr aplikaci by měly být v oboru pro zřizování do služby Active Directory. Další informace najdete v kurzu cloudhr aplikace pro běžně používané filtry oborů.

### <a name="determine-matching-attributes"></a>Určení odpovídajících atributů

S zřizováním získáte možnost spárovat existující účty mezi zdrojovým a cílovým systémem. Když integrujete cloudovou hr aplikaci se službou zřizování Azure AD, můžete [nakonfigurovat mapování atributů,](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) abyste zjistili, jaká uživatelská data by měla tok z cloudové aplikace HR do služby Active Directory nebo Azure AD.

Při zahájení procesu truhláře shromážděte následující požadavky:

- Jaké je jedinečné ID v této cloudové hr aplikaci, která se používá k identifikaci jednotlivých uživatelů?
- Z hlediska životního cyklu identity, jak zvládáte rehires? Mají rehires své staré ID zaměstnanců?
- Zpracováváte budoucí pronájmy a vytváříte pro ně účty služby Active Directory předem?
- Z hlediska životního cyklu identity, jak zpracovat zaměstnance k převodu podmíněného pracovníka, nebo jinak?
- Zachovávají si převedení uživatelé své staré účty služby Active Directory nebo si počnou nové?

V závislosti na vašich požadavcích azure ad podporuje přímé mapování atributů atributů tím, že poskytuje konstantní hodnoty nebo [psaní výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md). Tato flexibilita vám dává maximální kontrolu nad tím, co je naplněno v atributu cílové aplikace. Rozhraní Microsoft [Graph API](../app-provisioning/export-import-provisioning-configuration.md) a Průzkumník a průzkumník graphu můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat je zpět do Azure AD.

Ve výchozím nastavení se atribut v cloudové hr aplikaci, která představuje jedinečné ID zaměstnance, používá jako odpovídající atribut *mapovaný na jedinečný atribut ve službě Active Directory.* Například ve scénáři aplikace Workday je atribut **Workday** **WorkerID** mapován na atribut **ID zaměstnance** služby Active Directory.

Můžete nastavit více odpovídajících atributů a přiřadit odpovídající prioritu. Jsou vyhodnoceny podle odpovídající priority. Jakmile je nalezena shoda, nejsou vyhodnoceny žádné další odpovídající atributy.

Můžete také [přizpůsobit výchozí mapování atributů](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), například změnu nebo odstranění existujících mapování atributů. Můžete také vytvořit nové mapování atributů podle vašich obchodních potřeb. Další informace najdete v kurzu cloudhr aplikace (například [Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) pro seznam vlastních atributů na mapě.

### <a name="determine-user-account-status"></a>Určení stavu uživatelského účtu

Ve výchozím nastavení zřizovací konektor aplikace mapuje stav profilu uživatele HR na stav uživatelského účtu ve službě Active Directory nebo Azure AD k určení, zda chcete povolit nebo zakázat uživatelský účet.

Při zahájení procesu Truhláři-Opouštějící, shromáždit následující požadavky.

| Proces | Požadavky |
| - | - |
| **Truhláře** | Z hlediska životního cyklu identity, jak zvládáte rehires? Mají rehires své staré ID zaměstnanců? |
| | Zpracováváte budoucí pronájmy a vytváříte pro ně účty služby Active Directory předem? Jsou tyto účty vytvořeny v povoleném nebo zakázaném stavu? |
| | Z hlediska životního cyklu identity, jak zpracovat zaměstnance k převodu podmíněného pracovníka, nebo jinak? |
| | Zachovávají si převedení uživatelé své staré účty služby Active Directory nebo si počnou nové? |
| **Opouštějíce** | Jsou ukončení pracovních poměru pro zaměstnance a závislé pracovníky ve službě Active Directory vedena odlišně? |
| | Jaká data účinnosti jsou zvažována pro zpracování ukončení uživatele? |
| | Jak převody zaměstnanců a podmíněných pracovníků ovlivňují existující účty služby Active Directory? |
| | Jak zpracováváte operaci Rescind ve službě Active Directory? Operace s rescindem je třeba zpracovat, pokud jsou budoucí datované najímá ní vytvořeny ve službě Active Directory jako součást procesu truhláře. |

V závislosti na vašich požadavcích můžete přizpůsobit logiku mapování pomocí [výrazů Azure AD](../app-provisioning/functions-for-customizing-application-data.md) tak, aby byl účet služby Active Directory povolen nebo zakázán na základě kombinace datových bodů.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapování cloudové personální aplikace na uživatelské atributy služby Active Directory

Každá cloudová HR aplikace je dodávána s výchozí cloudovou hr aplikací do mapování Služby Active Directory.

Při zahájení procesu Truhláři-Movers-Leavers, shromáždit následující požadavky.

| Proces | Požadavky |
| - | - |
| **Truhláře** | Je proces vytváření účtu služby Active Directory ruční, automatizovaný nebo částečně automatizovaný? |
| | Plánujete šířit vlastní atributy z cloudové personální aplikace do služby Active Directory? |
| **Stěhováci** | Jaké atributy chcete zpracovat vždy, když se operace Movers odehrává v cloudové hr aplikaci? |
| | Provádíte v době aktualizací uživatelů ověření určitých atributů? Pokud ano, uveďte podrobnosti. |
| **Opouštějíce** | Jsou ukončení pracovních poměru pro zaměstnance a závislé pracovníky ve službě Active Directory vedena odlišně? |
| | Jaká data účinnosti jsou zvažována pro zpracování ukončení uživatele? |
| | Jaký vliv mají konverze zaměstnanců a podmíněných pracovníků na existující účty služby Active Directory? |

V závislosti na vašich požadavcích můžete upravit mapování tak, aby splňovala vaše cíle integrace. Další informace najdete v konkrétní cloudhr app kurz (například [Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) pro seznam vlastních atributů na mapě.

### <a name="generate-a-unique-attribute-value"></a>Generovat hodnotu jedinečného atributu

Při zahájení procesu truhláři může být nutné generovat jedinečné hodnoty atributů při nastavování atributů, jako je CN, samAccountName a hlavní název, který má jedinečná omezení.

Funkce Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) vyhodnotí každé pravidlo a potom zkontroluje hodnotu generovanou pro jedinečnost v cílovém systému. Příklad naleznete v tématu [Generovat jedinečnou hodnotu atributu userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Tato funkce je aktuálně podporována pouze pro zřizování uživatelů služby Workday do služby Active Directory. Nedá se používat s jinými zřizovacími aplikacemi.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurace přiřazení kontejneru OU služby Active Directory

Je běžným požadavkem umístit uživatelské účty služby Active Directory do kontejnerů na základě organizačních jednotek, umístění a oddělení. Při zahájení procesu stěhovače a pokud dojde ke změně organizace dohledu, může být nutné přesunout uživatele z jedné organizační organizační organizační výužné organizační výužné organizační organizační skupiny do druhé ve službě Active Directory.

Pomocí funkce [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) nakonfigurujte obchodní logiku pro přiřazení organizační jednotky a namapujte ji na atribut Služby Active Directory **parentDistinguishedName**.

Chcete-li například vytvořit uživatele v uživatelské masce na základě atributu HR **Municipality**, můžete použít následující výraz:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

S tímto výrazem, pokud je hodnota Municipality Dallas, Austin, Seattle nebo Londýn, bude uživatelský účet vytvořen v odpovídající ou. Pokud neexistuje žádná shoda, pak je účet vytvořen ve výchozí hlavní výměvě.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plán pro doručování nových uživatelských účtů heslem

Při zahájení procesu truhláři je třeba nastavit a doručit dočasné heslo nových uživatelských účtů. S cloud HR do Azure AD zřizování uživatelů, můžete zavést azure ad [samoobslužné resetování hesla](../authentication/quickstart-sspr.md) (SSPR) funkce pro uživatele na první den.

SSPR je jednoduchý způsob, jak pro správce IT, aby uživatelé mohli resetovat hesla nebo odemknout své účty. Atribut Mobilní **číslo** můžete zřídit z cloudové aplikace HR do služby Active Directory a synchronizovat s Azure AD. Po **mobilní číslo** atribut je ve službě Azure AD, můžete povolit sspr pro účet uživatele. Pak první den může nový uživatel použít registrované a ověřené číslo mobilního telefonu pro ověření.

## <a name="plan-for-initial-cycle"></a>Plán pro počáteční cyklus

Když služba zřizování Azure AD běží poprvé, provede [počáteční cyklus](../app-provisioning/how-provisioning-works.md#initial-cycle) proti cloudhr aplikace k vytvoření snímku všech uživatelských objektů v cloudu HR aplikace. Doba doby počátečních cyklů je přímo závislá na tom, kolik uživatelů je přítomno ve zdrojovém systému. Počáteční cyklus pro některé klienty cloudových HR aplikací s více než 100 000 uživateli může trvat dlouhou dobu.

**Pro velké klienty cloudových HR aplikací (>30 000 uživatelů)** spusťte počáteční cyklus v progresivních fázích. Přírůstkové aktualizace spusťte až po ověření, zda jsou ve službě Active Directory nastaveny správné atributy pro různé scénáře zřizování uživatelů. Postupujte podle pořadí zde.

1. Počáteční cyklus spusťte pouze pro omezenou sadu uživatelů nastavením [filtru oboru](#plan-scoping-filters-and-attribute-mapping).
2. Ověřte zřizování účtu služby Active Directory a hodnoty atributů nastavené pro uživatele vybrané pro první spuštění. Pokud výsledek splňuje vaše očekávání, rozbalte filtr oboru postupně zahrnout více uživatelů a ověřte výsledky pro druhé spuštění.

Až budete spokojeni s výsledky počátečního cyklu pro testovací uživatele, spusťte [přírůstkové aktualizace](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Plánování testování a zabezpečení

V každé fázi vašeho nasazení od počátečního pilotního až po povolení zřizování uživatelů ujistěte se, že testujete, že výsledky jsou podle očekávání a auditujete cykly zřizování.

### <a name="plan-testing"></a>Plánování testování

Po konfiguraci cloudové hr aplikace pro zřizování uživatelů Azure AD, spusťte testovací případy a ověřte, zda toto řešení splňuje požadavky vaší organizace.

|Scénáře|Očekávané výsledky|
|:-|:-|
|Nový zaměstnanec je najat v cloudové hr aplikaci.| - Uživatelský účet je zřízen ve službě Active Directory.</br>- Uživatel se může přihlásit do aplikace domény služby Active Directory a provést požadované akce.</br>- Pokud je nakonfigurovaná synchronizace Azure AD Connect, uživatelský účet se také vytvoří ve službě Azure AD.
|Uživatel je ukončen v cloudové hr aplikaci.|- Uživatelský účet je ve službě Active Directory zakázán.</br>- Uživatel se nemůže přihlásit k žádným podnikovým aplikacím chráněným službou Active Directory.
|Organizace dohledu uživatelů se aktualizuje v cloudové hr aplikaci.|Na základě mapování atributů se uživatelský účet přesune z jedné jednotlivé účetní hodnoty do druhé ve službě Active Directory.|
|HR aktualizuje manažera uživatele v cloudové hr aplikaci.|Pole správce ve službě Active Directory je aktualizováno tak, aby odráželo název nového manažera.|
|HR znovu zasít zaměstnance do nové role.|Chování závisí na tom, jak je cloudová hr aplikace nakonfigurována pro generování ID zaměstnanců:</br>- Pokud je staré ID zaměstnance znovu použito pro opětovné přijetí, konektor povolí uživateli existující účet služby Active Directory.</br>- Pokud opětovné přijetí získá nové ID zaměstnance, spojnice vytvoří pro uživatele nový účet služby Active Directory.|
|HR převede zaměstnance na smluvního pracovníka nebo naopak.|Pro novou osobu je vytvořen nový účet služby Active Directory a starý účet bude k datu účinnosti převodu deaktivován.|

Pomocí předchozích výsledků určete, jak převést implementaci automatického zřizování uživatelů do produkčního prostředí na základě stanovených časových os.

> [!TIP]
> Při aktualizaci testovacího prostředí produkčními daty používejte techniky, jako je redukce dat a čištění dat, k odstranění nebo maskování citlivých osobních údajů, aby byly v souladu s normami ochrany osobních údajů a zabezpečení. 

### <a name="plan-security"></a>Plánování zabezpečení

Je běžné, že v rámci nasazení nové služby je vyžadována kontrola zabezpečení. Pokud je požadováno nebo nebylo provedeno kontroly zabezpečení, podívejte se na mnoho [dokumentů White Paper služby](https://www.microsoft.com/download/details.aspx?id=36391) Azure AD, které poskytují přehled identity jako služby.

### <a name="plan-rollback"></a>Vrácení plánu zpět

Implementace zřizování uživatelů lidských zdrojů v cloudu nemusí fungovat podle potřeby v produkčním prostředí. Pokud ano, následující kroky vrácení zpět vám může pomoci při návratu do předchozího známého dobrého stavu.

1. Zkontrolujte [zřizování souhrnné sestavy](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) a [zřizování protokoly](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) určit, jaké nesprávné operace byly provedeny na ovlivněné uživatele nebo skupiny. Další informace o zřizování souhrnné sestavy a protokoly, najdete [v tématu Správa cloud HR app zřizování uživatelů aplikací](#manage-your-configuration).
2. Poslední známý dobrý stav uživatelů nebo skupin, kterých se to týká, lze určit pomocí protokolů auditování zřizování nebo kontrolou cílových systémů (Azure AD nebo Active Directory).
3. Spolupracujte s vlastníkem aplikace a aktualizujte uživatele nebo skupiny, kterých se to týká přímo v aplikaci, pomocí posledních známých hodnot dobrého stavu.

## <a name="deploy-the-cloud-hr-app"></a>Nasazení cloudové personální aplikace

Vyberte si cloudovou HR aplikaci, která odpovídá vašim požadavkům na řešení.

**Pracovní den**: Chcete-li importovat profily pracovních dnů ze služby Workday do služby Active Directory a služby Azure AD, [přečtěte si článek Konfigurace pracovního dne pro automatické zřizování uživatelů](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Volitelně můžete odepsat e-mailovou adresu a uživatelské jméno workday.

## <a name="manage-your-configuration"></a>Správa konfigurace

Azure AD můžete poskytnout další přehled o využití uživatelů vaší organizace a provozní stav prostřednictvím protokolů auditu a sestav.

### <a name="gain-insights-from-reports-and-logs"></a>Získejte přehledy ze sestav a protokolů

Po úspěšném [počátečním cyklu](../app-provisioning/how-provisioning-works.md#initial-cycle)služba zřizování Azure AD nadále spouštěla přírůstkové aktualizace zády k sobě po neomezenou dobu, v intervalech definovaných v kurzech specifických pro každou aplikaci, dokud nedojde k jedné z následujících událostí:

- Služba je ručně zastavena. Nový počáteční cyklus se aktivuje pomocí [portálu Azure nebo](https://portal.azure.com/) příslušného příkazu [rozhraní Microsoft Graph API.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- Nový počáteční cyklus se aktivuje z důvodu změny mapování atributů nebo filtrů oborů.
- Proces zřizování přejde do karantény z důvodu vysoké míry chyb. Zůstává v karanténě déle než čtyři týdny, kdy je automaticky zakázán.

Chcete-li zkontrolovat tyto události a všechny ostatní aktivity prováděné službou zřizování, [přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Všechny aktivity prováděné službou zřizování se zaznamenávají v protokolech auditu Azure AD. Protokoly auditu Azure AD můžete směrovat do protokolů Azure Monitor pro další analýzu. Pomocí protokolů Azure Monitor (označované také jako pracovní prostor Log Analytics) můžete dotazovat data za účelem vyhledání událostí, analýzy trendů a korelace mezi různými zdroji dat. V tomto [videu](https://youtu.be/MP5IaCTwkQg) se dozvíte výhody používání protokolů Azure Monitor pro protokoly Azure AD v praktických uživatelských scénářích.

Nainstalujte [zobrazení analýzy protokolů pro protokoly aktivit Azure AD,](../reports-monitoring/howto-install-use-log-analytics-views.md) abyste získali přístup k [předem vytvořeným sestavám](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) kolem zřizování událostí ve vašem prostředí.

Další informace najdete v tématu [analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Správa osobních údajů

Agent zřizování Azure AD Connect nainstalovaný na serveru Windows vytvoří protokoly v protokolu událostí systému Windows, které mohou obsahovat osobní data v závislosti na cloudové aplikaci HR na mapování atributů služby Active Directory. Chcete-li splnit povinnosti ochrany osobních údajů uživatelů, nastavte naplánovanou úlohu systému Windows, abyste vyčistili protokol událostí a zajistili, že žádná data nebudou uchovávána déle než 48 hodin.

Služba zřizování Azure AD negeneruje sestavy, neprovádí analýzy ani neposkytuje přehledy po dobu 30 dnů, protože služba neukládá, nezpracovává ani neuchovává žádná data déle než 30 dní.

### <a name="troubleshoot"></a>Řešení potíží

Chcete-li vyřešit všechny problémy, které se mohou objevit během zřizování, naleznete v následujících článcích:

- [Problém s konfigurací zřizování uživatelů do aplikace Azure AD Gallery](application-provisioning-config-problem.md)
- [Synchronizace atributu z místní služby Active Directory do služby Azure AD pro zřizování do aplikace](user-provisioning-sync-attributes-for-mapping.md)
- [Zřizování uživatelů do aplikace Azure AD Gallery trvá hodiny nebo víc](application-provisioning-when-will-provisioning-finish.md)
- [Při konfiguraci zřizování uživatelů do aplikace Azure Active Directory Gallery došlo k potížím s ukládáním přihlašovacích údajů správce](application-provisioning-config-problem-storage-limit.md)
- [Do aplikace Azure AD Gallery se nezřají žádní uživatelé.](application-provisioning-config-problem-no-users-provisioned.md)
- [Nesprávná sada uživatelů se zřaží do aplikace Azure AD Gallery](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Nastavení prohlížeče událostí systému Windows pro řešení potíží s agentem](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Nastavení protokolů auditu portálu Azure pro řešení potíží se službou](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Principy protokolů pro operace vytvoření uživatelského účtu ad](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Principy protokolů pro operace aktualizace správce](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Řešení běžně se vyskytujících chyb](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Další kroky

- [Psaní výrazů pro mapování atributů](functions-for-customizing-application-data.md)
- [Přehled rozhraní API synchronizace Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah](skip-out-of-scope-deletions.md)
- [Agent zřizování Azure AD Connect: Historie verzí vydání](provisioning-agent-release-version-history.md)
