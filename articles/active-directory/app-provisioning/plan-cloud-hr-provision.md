---
title: Plánování aplikace cloudového HR pro Azure Active Directory zřizování uživatelů
description: Tento článek popisuje proces nasazení integrující systémy cloudového HR, jako je například Workday a SuccessFactors, s Azure Active Directory. Integrace služby Azure AD s vaším systémem cloudového HR má za následek kompletní systém pro správu životního cyklu identit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256707"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Plánování aplikace cloudového HR pro Azure Active Directory zřizování uživatelů

V minulosti se zaměstnanci IT spoléhali na ruční metody pro vytváření, aktualizaci a odstraňování zaměstnanců. Používaly metody, jako je nahrání souborů CSV nebo vlastních skriptů k synchronizaci dat zaměstnanců. Tyto zřizovací procesy jsou náchylné k chybám, nezabezpečené a nenáročné na správu.

Aby bylo možné spravovat životní cykly identit zaměstnanců, dodavatelů nebo podmíněných pracovníků, [Služba zřizování uživatelů Azure Active Directory (Azure AD)](../app-provisioning/user-provisioning.md) nabízí integraci s aplikacemi pro lidské prostředky (HR) založené na cloudu. Mezi příklady aplikací patří Workday nebo SuccessFactors.

Azure AD pomocí této integrace povolí následující pracovní postupy aplikace cloudového HR:

- **Zřizování uživatelů pro službu Active Directory:** Zřídí vybrané sady uživatelů z aplikace cloudového HR do jedné nebo víc domén služby Active Directory.
- **Zřizování pouze cloudových uživatelů v Azure AD:** Ve scénářích, kdy se služba Active Directory nepoužívá, Zřiďte uživatele přímo z aplikace cloudového HR do Azure AD.
- **Zapište zpátky do aplikace cloudového HR:** Napište e-mailové adresy a atributy uživatelského jména z Azure AD zpátky do aplikace Cloude HR.

> [!NOTE]
> Tento plán nasazení vám ukáže, jak nasadit pracovní postupy vaší aplikace cloudového HR pomocí zřizování uživatelů Azure AD. Informace o tom, jak nasadit Automatické zřizování uživatelů do aplikací SaaS (software jako služba), najdete v tématu [plánování automatického zřizování uživatelů](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Povolené scénáře pro personální oddělení

Služba zřizování uživatelů Azure AD umožňuje automatizaci následujících scénářů správy životního cyklu identity na základě lidských zdrojů:

- **Nové zařazení zaměstnanců:** Když se do aplikace cloudového HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory a Azure AD s možností napsat zpátky e-mailovou adresu a atributy uživatelského jména do aplikace cloudového HR.
- **Aktualizace atributů a profilů zaměstnanců:** Když se v aplikaci cloudového HR aktualizuje záznam zaměstnance, jako je jméno, titul nebo manažer, jejich uživatelský účet se automaticky aktualizuje ve službě Active Directory a v Azure AD.
- **Ukončení zaměstnanců:** Když se zaměstnanec v aplikaci cloudového HR ukončí, je jeho uživatelský účet automaticky zakázaný ve službě Active Directory a Azure AD.
- **Pracovní zařazení zaměstnanců:** Po opětovném zařazení zaměstnance do aplikace cloudové HR se jejich starý účet může automaticky znovu aktivovat nebo znovu zřídit ve službě Active Directory a Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Pro kterou tuto integraci nejlépe vyhovuje?

Integrace aplikace pro cloudovou HR se zřizováním uživatelů Azure AD je ideální pro organizace, které:

- Chcete předem připravené cloudové řešení pro zřizování uživatelů v cloudu.
- Vyžaduje přímé zřizování uživatelů z aplikace cloudového HR do služby Active Directory nebo Azure AD.
- Vyžadovat, aby se uživatelé zřídili pomocí dat získaných z aplikace cloudového HR.
- Vyžadovat, aby se uživatelé synchronizovaly do jedné nebo víc doménových struktur služby Active Directory, domén a organizačních jednotek, a to na základě informací o změně zjištěných v aplikaci cloudového HR.
- K odeslání e-mailu použijte Microsoft 365.

## <a name="learn"></a>Learn

Zřizování uživatelů vytvoří základ pro průběžné řízení identit. Vylepšuje kvalitu obchodních procesů, které spoléhají na data autoritativní identity.

### <a name="terms"></a>Terminologie

Tento článek používá následující výrazy:

- **Zdrojový systém**: úložiště uživatelů, ze kterých Azure AD zřídí. Příkladem je aplikace cloudového HR, jako je například Workday nebo SuccessFactors.
- **Cílový systém**: úložiště uživatelů, na které Azure AD zřídí. Příklady jsou služby Active Directory, Azure AD, Microsoft 365 nebo jiné aplikace SaaS.
- **Připojení – stěhovací společnosti – proces Leavers**: termín používaný pro nová přijetí, přenosy a ukončení pomocí aplikace cloudového HR jako systému záznamů. Proces se dokončí, když služba úspěšně zřídí nezbytné atributy pro cílový systém.

### <a name="key-benefits"></a>Klíčové výhody

Tato schopnost zajišťování IT na základě lidských zdrojů nabízí tyto významné obchodní výhody:

- **Zvýšení produktivity:** Nyní můžete automatizovat přiřazování uživatelských účtů a Microsoft 365 licencí a poskytovat přístup ke skupinám klíčů. Automatizace přiřazení poskytuje novým lidem okamžitý přístup ke svým nástrojům úloh a zvyšuje produktivitu.
- **Řízení rizik:** Zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnanců nebo členství ve skupinách pomocí toku dat z aplikace cloudového HR. Automatizace změn zajišťuje, že identity uživatelů a přístup k klíčovým aplikacím se automaticky aktualizují, když uživatelé přecházejí nebo odejdou z organizace.
- **Řešení dodržování předpisů a zásad správného řízení:** Azure AD podporuje protokoly nativního auditu pro požadavky uživatelů na zřizování prováděné aplikacemi ze zdrojového i cílového systému. Díky auditování můžete sledovat, kdo má přístup k aplikacím z jedné obrazovky.
- **Spravovat náklady:** Automatické zřizování snižuje náklady tím, že se vyhne neefektivitám a lidským chybám přidruženým k ručnímu zřizování. Díky starším a zastaralým platformám omezuje nutnost sestavování řešení pro zřizování uživatelů vytvořených v průběhu času.

### <a name="licensing"></a>Licencování

Pokud chcete nakonfigurovat aplikaci cloudového HR na integraci zřizování uživatelů Azure AD, budete potřebovat platnou [licenci Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) a licenci pro aplikaci cloudového HR, jako je například Workday nebo SuccessFactors.

Pro každého uživatele, který se bude nacházet z aplikace cloudového HR a je zajištěná v rámci služby Active Directory nebo Azure AD, potřebujete také platnou licenci pro předplatného Azure AD Premium P1 nebo vyšší. Jakýkoli nesprávný počet licencí vlastněných v aplikaci cloudového HR může vést k chybám při zřizování uživatele.

### <a name="prerequisites"></a>Požadavky

- [Správce hybridní identity](../roles/permissions-reference.md#hybrid-identity-administrator) Azure AD ke konfiguraci Azure AD Connectho agenta zřizování.
- Role [Správce aplikací](../roles/permissions-reference.md#application-administrator) Azure AD pro konfiguraci zřizování aplikace v Azure Portal
- Instance testu a výroby aplikace cloudového HR.
- Oprávnění správce v aplikaci cloudového HR k vytvoření uživatele Integration System a provedení změn v datech testování zaměstnanců pro účely testování.
- V případě zřizování uživatelů pro službu Active Directory se pro hostování agenta Azure AD Connectho zřizování vyžaduje server se systémem Windows Server 2012 nebo vyšší s modulem runtime .NET 4.7.1 +.
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) pro synchronizaci uživatelů mezi službou Active Directory a službou Azure AD.

### <a name="training-resources"></a>Školicí materiály

| **Prostředky** | **Odkaz a popis** |
|:-|:-|
| Videa | [Co je zřizování uživatelů v aktivním adresáři Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Postup nasazení zřizování uživatelů v aktivním adresáři Azure](https://youtu.be/pKzyts6kfrw) |
| Kurzy | [Seznam kurzů pro integraci aplikací SaaS s Azure AD](../saas-apps/tutorial-list.md) |
| | [Kurz: Konfigurace pracovního dne pro Automatické zřizování uživatelů](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Nejčastější dotazy | [Automatizované zřizování uživatelů](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Zřizování z Workday do Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura řešení

Následující příklad popisuje architekturu řešení zřizování koncových uživatelů pro běžná hybridní prostředí a zahrnuje:

- **Autoritativní tok dat o lidských přenosech z aplikace cloudového HR do služby Active Directory.** V tomto toku se v tenantovi aplikace cloudového HR zahajuje událost HR (spojovací procesy – stěhovací společnosti-Leavers). Služba zřizování Azure AD a Azure AD Connect agent zřizování zřídí uživatelská data z tenanta aplikace cloudového HR do služby Active Directory. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací ve službě Active Directory.
- **Synchronizujte se s Azure AD a zapište zpátky e-mail a uživatelské jméno z místní služby Active Directory do aplikace cloudového HR.** Po aktualizaci účtů ve službě Active Directory se tato služba synchronizuje se službou Azure AD prostřednictvím Azure AD Connect. E-mailové adresy a atributy uživatelského jména se dají zapsat zpátky do tenanta aplikace cloudového HR.

![Diagram pracovního postupu](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Popis pracovního postupu

V diagramu jsou uvedené následující klíčové kroky:  

1. **Tým HR** provádí transakce v tenantovi cloudové aplikace.
2. **Služba zřizování Azure AD** spouští naplánované cykly z tenanta aplikace cloudového HR a identifikuje změny, které je potřeba zpracovat pro synchronizaci se službou Active Directory.
3. **Služba zřizování Azure AD** vyvolá agenta Azure AD Connect zřizování s datovou částí požadavku, která obsahuje operace vytvoření, aktualizace, povolení a zakázání účtu služby Active Directory.
4. **Agent zřizování Azure AD Connect** používá účet služby ke správě dat účtu služby Active Directory.
5. **Azure AD Connect** spustí rozdílovou [synchronizaci](../hybrid/how-to-connect-sync-whatis.md) s aktualizacemi Pull ve službě Active Directory.
6. Aktualizace **služby Active Directory** se synchronizují se službou Azure AD.
7. **Služba zřizování Azure AD** zapisuje přihlašovací atributy e-mailu a uživatelské jméno z Azure AD do tenanta aplikace cloudového hr.

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Při určování strategie pro toto nasazení v prostředí zvažte potřeby vaší organizace.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshody očekávání na dopad, výsledky a odpovědnosti. Abyste se vyhnuli těmto nástrah, [Ujistěte se, že jste připraví správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md). Také se ujistěte, že role účastníků v projektu jsou dobře pochopitelné. Zdokumentujte zúčastněné strany a jejich vstup a accountabilities projektu.

Zahrňte zástupce z organizace pro personální oddělení, který může poskytovat vstupy na stávající obchodní procesy a identitu pracovního procesu a požadavky na zpracování dat úloh.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s uživateli a zjistěte, kdy a jak se bude jejich prostředí měnit. Dejte jim možnost informovat se o tom, jak získat podporu, pokud se jim setkávají problémy.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Integrace obchodních procesů a pracovních postupů identity z aplikace cloudového HR do cílových systémů vyžaduje značné množství ověření dat, transformaci dat, čištění dat a kompletní testování, než můžete nasadit řešení do produkčního prostředí.

Spusťte počáteční konfiguraci v [pilotním prostředí](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) , abyste ji mohli škálovat na všechny uživatele v produkčním prostředí.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Výběr aplikací konektoru pro cloudové personální zajištění

Pokud chcete zajistit pracovní postupy zřizování Azure AD mezi aplikací cloudového HR a službou Active Directory, můžete do Galerie aplikací Azure AD přidat více aplikací konektoru pro zřizování:

- **Aplikace cloudového HR do zřizování uživatelů služby Active Directory**: Tato aplikace zřizovacího konektoru usnadňuje zřizování uživatelských účtů z aplikace cloudového HR do jedné domény služby Active Directory. Pokud máte více domén, můžete pro každou doménu Active Directory, pro kterou potřebujete zřídit, přidat jednu instanci této aplikace z Galerie aplikací Azure AD.
- **Aplikace cloudového HR do zřizování uživatelů Azure AD**: i když Azure AD Connect je nástroj, který by se měl použít k synchronizaci uživatelů Active Directory s Azure AD, tato aplikace konektoru pro zřizování se dá použít k usnadnění zřizování pouze cloudových uživatelů z aplikace cloudového HR do jednoho tenanta služby Azure AD.
- **Zpětný zápis aplikace cloudového HR**: Tato aplikace konektoru pro zřizování usnadňuje zpětný zápis e-mailových adres uživatele ze služby Azure AD do aplikace Cloude hr.

Například následující obrázek uvádí aplikace konektoru Workday, které jsou k dispozici v galerii aplikací Azure AD.

![Galerie aplikací Azure Active Directoryového portálu](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Diagram toku rozhodnutí

Pomocí následujícího diagramu průběhu rozhodování určete, které aplikace pro zajištění cloudového HR jsou relevantní pro váš scénář.

![Diagram toku rozhodnutí](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Návrh topologie nasazení agenta zřizování Azure AD Connect

Integrace zřizování mezi aplikací cloudového HR a službou Active Directory vyžaduje čtyři komponenty:

- Tenant aplikace cloudového HR
- Aplikace konektoru zřizování
- Agent zřizování Azure AD Connect
- Doména služby Active Directory

Topologie nasazení agenta Azure AD Connect zřizování závisí na počtu klientů aplikace cloudového HR a podřízených domén služby Active Directory, které chcete integrovat. Pokud máte více domén služby Active Directory, záleží na tom, jestli jsou domény služby Active Directory souvislé [nebo nesouvislé.](/windows-server/identity/ad-ds/plan/disjoint-namespace)

Na základě vašeho rozhodnutí vyberte jeden z scénářů nasazení:

- Tenant aplikace s jedním cloudovým HRm – > cílit na jednu nebo více podřízených domén služby Active Directory v důvěryhodné doménové struktuře
- Tenant aplikace v rámci jednoho cloudu – > cílit na více podřízených domén v nesouvislé doménové struktuře služby Active Directory

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Tenant aplikace s jedním cloudovým HRm – > cílit na jednu nebo více podřízených domén služby Active Directory v důvěryhodné doménové struktuře

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect k nasazení|Dvě (pro vysokou dostupnost a převzetí služeb při selhání)
|Počet aplikací zřizovacích konektorů, které se mají nakonfigurovat|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2 + s pohledem na geograficky umístěné řadiče domény služby Active Directory</br>Může existovat vedle služby Azure AD Connect Service|

![Tok do místních agentů](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Tenant aplikace v rámci jednoho cloudu – > cílit na více podřízených domén v nesouvislé doménové struktuře služby Active Directory

Tento scénář zahrnuje zřizování uživatelů z aplikace cloudového HR do domén v nesouvislých doménových strukturách služby Active Directory.

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect pro místní nasazení|Dvě za nesouvislou doménovou strukturu služby Active Directory|
|Počet aplikací zřizovacích konektorů, které se mají nakonfigurovat|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2 + s pohledem na geograficky umístěné řadiče domény služby Active Directory</br>Může existovat vedle služby Azure AD Connect Service|

![Nesouvislá doménová struktura služby Active Directory v tenantovi aplikace pro jednoho cloudového HR](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Požadavky na agenty zřizování Azure AD Connect

Řešení pro zřizování uživatelů v cloudu do služby Active Directory vyžaduje, abyste na serverech se systémem Windows 2012 R2 nebo novějším nasadili jednoho nebo více Azure AD Connect agentů zřizování. Servery musí mít minimálně 4 GB paměti RAM a .NET 4.7.1 + runtime. Zajistěte, aby měl hostitelský server přístup k síti cílové doméně služby Active Directory.

V rámci přípravy místního prostředí Průvodce konfigurací agenta Azure AD Connect pro zřizování zaregistruje agenta u vašeho tenanta Azure AD, [otevře porty](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [umožní přístup k adresám URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)a podporuje [konfiguraci proxy serveru odchozího https](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Agent zřizování používá účet služby ke komunikaci s doménami služby Active Directory. Před instalací agenta vytvořte účet služby v modulu Uživatelé a počítače služby Active Directory, které splňují následující požadavky:

- Heslo, jehož platnost nevyprší
- Delegovaná oprávnění řízení pro čtení, vytváření, odstraňování a správu uživatelských účtů

Můžete vybrat řadiče domény, které by měly zpracovávat požadavky zřizování. Pokud máte několik geograficky distribuovaných řadičů domény, nainstalujte agenta zřizování ve stejné lokalitě jako vaše preferované řadiče domény. Toto umístění zvyšuje spolehlivost a výkon kompletního řešení.

Pro zajištění vysoké dostupnosti můžete nasadit více než jednoho Azure AD Connect agenta zřizování. Zaregistrujte agenta pro zpracování stejné sady místních domén služby Active Directory.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Plánování filtrů oborů a mapování atributů

Když povolíte zřizování z aplikace cloudového HR do služby Active Directory nebo Azure AD, Azure Portal řídí hodnoty atributu prostřednictvím mapování atributů.

### <a name="define-scoping-filters"></a>Definování filtrů oborů

Pomocí [filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) definujte pravidla založená na atributech, která určují, kteří uživatelé by se měli zřídit z aplikace cloudového HR pro službu Active Directory nebo Azure AD.

Když inicializujete spojovací procesy, shromážděte následující požadavky:

- Používá aplikace cloudového HR k uvedení na oddělení zaměstnanců i podmíněných pracovníků?
- Plánujete použití aplikace cloudového HR k zřizování uživatelů Azure AD ke správě zaměstnanců a podmíněných pracovníků?
- Plánujete zavedení aplikace cloudového HR do zřizování uživatelů Azure AD jenom pro podmnožinu uživatelů aplikací pro cloudovou HR? Příkladem může být pouze zaměstnanci.

V závislosti na vašich požadavcích můžete při konfiguraci mapování atributů nastavit pole **obor zdrojového objektu** a vybrat, které sady uživatelů v aplikaci cloudového HR by měly být v oboru pro zřizování do služby Active Directory. Další informace najdete v kurzu aplikace cloudového HR pro běžně používané filtry oborů.

### <a name="determine-matching-attributes"></a>Určení atributů, které odpovídají

Díky zřizování získáte možnost párovat stávající účty mezi zdrojovým a cílovým systémem. Když integrujete aplikaci cloudového HR se službou zřizování Azure AD, můžete [nakonfigurovat mapování atributů](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) a určit, jaká uživatelská data by se měla přesměrovat z aplikace cloudového HR do služby Active Directory nebo Azure AD.

Když inicializujete spojovací procesy, shromážděte následující požadavky:

- Jaké je jedinečné ID v této aplikaci cloudového HR, které se používá k identifikaci jednotlivých uživatelů?
- Jak můžete v perspektivě životního cyklu identity zpracovávat rekoupování? Zachovají si staré identifikátory zaměstnanců?
- Můžete zpracovávat budoucí přinájmy a vytvářet účty služby Active Directory předem?
- Jak se v perspektivě životního cyklu identit řídíte zaměstnancem pro podmíněnou konverzi pracovního procesu nebo jinak?
- Mají převedené uživatele zachovat staré účty služby Active Directory nebo získávat nové?

V závislosti na vašich požadavcích Azure AD podporuje přímé mapování atributů na atributy tím, že poskytuje konstantní hodnoty nebo [výrazy zápisu pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md). Díky této flexibilitě získáte maximální kontrolu nad tím, co se naplní v atributu cílové aplikace. Pomocí [rozhraní Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) a Graph Exploreru můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a naimportovat ho zpátky do služby Azure AD.

Ve výchozím nastavení se atribut v aplikaci cloudového HR, který představuje jedinečné ID zaměstnance, používá jako odpovídající atribut *mapovaný k jedinečnému atributu ve službě Active Directory.* Například ve scénáři aplikace Workday je atribut **Workday** **WorkerID** namapován na atribut **ČísloZaměstnance** služby Active Directory.

Můžete nastavit více vyhovujících atributů a přiřadit prioritu porovnání. Jsou vyhodnocovány v porovnání s prioritou. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

Můžete také [přizpůsobit výchozí mapování atributů](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), jako je například změna nebo odstranění mapování stávajících atributů. Můžete také vytvořit nová mapování atributů podle vašich obchodních potřeb. Další informace najdete v tématu kurz aplikace cloudového HR (například [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) pro seznam vlastních atributů k mapování.

### <a name="determine-user-account-status"></a>Určení stavu uživatelského účtu

Ve výchozím nastavení aplikace zřizovacího konektoru mapuje stav uživatelského profilu HR na stav uživatelského účtu ve službě Active Directory nebo Azure AD, aby určil, jestli chcete uživatelský účet povolit nebo zakázat.

Při zahájení procesu Joiners-Leavers shromážděte následující požadavky.

| Proces | Požadavky |
| - | - |
| **Odpojování** | Jak můžete v perspektivě životního cyklu identity zpracovávat rekoupování? Zachovají si staré identifikátory zaměstnanců? |
| | Můžete zpracovávat budoucí přinájmy a vytvářet účty služby Active Directory předem? Vytvářejí se tyto účty v povoleném nebo zakázaném stavu? |
| | Jak se v perspektivě životního cyklu identit řídíte zaměstnancem pro podmíněnou konverzi pracovního procesu nebo jinak? |
| | Mají převedené uživatele zachovat staré účty služby Active Directory nebo získají nové? |
| **Leavers** | Jsou ukončení u zaměstnanců a podmíněných pracovníků ve službě Active Directory zpracovávány jinak? |
| | Jaká data jsou zvažována pro zpracování ukončení uživatele? |
| | Vliv převodů zaměstnanců a podprocesů na stávající účty služby Active Directory? |
| | Jak zpracovat operaci zrušení ve službě Active Directory? Operace zrušení se musí zpracovat, pokud se ve službě Active Directory vytvoří budoucí pronájem v rámci procesu připojujícího se k programu. |

V závislosti na vašich požadavcích můžete logiku mapování přizpůsobit pomocí [výrazů Azure AD](../app-provisioning/functions-for-customizing-application-data.md) , aby byl účet Active Directory povolený nebo zakázaný na základě kombinace datových bodů.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapování aplikace cloudového HR na atributy uživatele služby Active Directory

Každá aplikace cloudového HR je dodávána s výchozí aplikací cloudového HR do mapování služby Active Directory.

Když zahájíte proces připojení – stěhovací společnosti – Leavers, shromážděte následující požadavky.

| Proces | Požadavky |
| - | - |
| **Odpojování** | Má proces vytváření účtu služby Active Directory ruční, automatizovanou nebo částečně automatizovanou automatizaci? |
| | Plánujete rozšířit vlastní atributy z aplikace cloudového HR do služby Active Directory? |
| **Stěhovací společnosti** | Jaké atributy chcete zpracovat pokaždé, když dojde k provozu stěhovací aplikace v aplikaci cloudového HR? |
| | Provádíte v době aktualizace uživatelů všechna konkrétní ověření atributů? Pokud ano, zadejte podrobnosti. |
| **Leavers** | Jsou ukončení u zaměstnanců a podmíněných pracovníků ve službě Active Directory zpracovávány jinak? |
| | Jaká data jsou zvažována pro zpracování ukončení uživatele? |
| | Jak ovlivňují převody zaměstnanců a podprocesů na stávající účty služby Active Directory? |

V závislosti na vašich požadavcích můžete mapování upravit tak, aby splňovalo vaše cíle integrace. Další informace najdete v kurzu konkrétní aplikace cloudového HR (například [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) pro seznam vlastních atributů k mapování.

### <a name="generate-a-unique-attribute-value"></a>Vygenerovat jedinečnou hodnotu atributu

Když zahájíte spojovací procesy, možná budete muset vygenerovat jedinečné hodnoty atributů při nastavování atributů jako CN, samAccountName a UPN, které mají jedinečná omezení.

Funkce Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) vyhodnotí všechna pravidla a potom zkontroluje hodnotu generovanou v cílovém systému pro jedinečnost. Příklad naleznete v tématu [generování jedinečné hodnoty pro atribut userPrincipalName (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurace přiřazení kontejneru organizační jednotky služby Active Directory

Je to běžný požadavek na umístění uživatelských účtů služby Active Directory do kontejnerů na základě obchodních jednotek, umístění a oddělení. Když zahájíte proces stěhovací společnosti a v případě, že dojde ke změně organizace pro dozor, bude pravděpodobně nutné přesunout uživatele z jedné organizační jednotky do jiné ve službě Active Directory.

Pomocí funkce [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) nakonfigurujte obchodní logiku pro přiřazení organizační jednotky a namapujte ji na atribut služby Active Directory **parentDistinguishedName**.

Například pokud chcete vytvořit uživatele v organizační jednotce na **základě atributu personální** oddělení, můžete použít následující výraz:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Pokud je v tomto výrazu hodnota správní hodnoty: Praha, Austin, Seattle nebo Londýn, uživatelský účet se vytvoří v odpovídající organizační jednotce. Pokud se neshodují, pak se účet vytvoří ve výchozí organizační jednotce.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plán pro doručování nových uživatelských účtů v hesle

Když zahájíte spojovací procesy, musíte nastavit a doručovat dočasné heslo nových uživatelských účtů. Díky zřizování uživatelů v cloudu na Azure AD můžete pro uživatele zavést funkci [samoobslužného resetování hesla](../authentication/tutorial-enable-sspr.md) (SSPR) služby Azure AD.

SSPR je jednoduchý způsob, jak správcům IT umožnit uživatelům resetovat hesla nebo odemykat jejich účtů. Atribut **mobilní číslo** můžete zřídit z aplikace cloudového HR do služby Active Directory a synchronizovat s Azure AD. Po zadání atributu **mobilní číslo** v Azure AD můžete pro účet uživatele povolit SSPR. Pak nový uživatel může k ověřování použít registrované a ověřené mobilní číslo.

## <a name="plan-for-initial-cycle"></a>Plánování počátečního cyklu

Když se služba zřizování Azure AD poprvé spustí, provede [počáteční cyklus](../app-provisioning/how-provisioning-works.md#initial-cycle) v rámci aplikace cloudového HR a vytvoří snímek všech uživatelských objektů v aplikaci cloudového hr. Doba potřebná pro počáteční cykly je přímo závislá na tom, kolik uživatelů se ve zdrojovém systému nachází. Počáteční cyklus pro některé klienty cloudových lidských aplikací s více než 100 000 uživateli může trvat dlouhou dobu.

**Pro klienty aplikace pro velké cloudové HR (>30 000 uživatelů)** spusťte počáteční cyklus v progresivních fázích. Přírůstkové aktualizace spusťte až po ověření, že jsou ve službě Active Directory nastaveny správné atributy pro různé scénáře zřizování uživatelů. Postupujte podle uvedeného pořadí.

1. Spusťte počáteční cyklus pouze pro omezené skupiny uživatelů nastavením [filtru oborů](#plan-scoping-filters-and-attribute-mapping).
2. Ověřte zřizování účtu služby Active Directory a hodnoty atributu nastavené pro uživatele vybrané pro první spuštění. Pokud výsledek splňuje vaše očekávání, rozbalte filtr rozsah a postupně uveďte více uživatelů a ověřte výsledky pro druhý běh.

Až budete spokojeni s výsledky počátečního cyklu pro testovací uživatele, spusťte [přírůstkové aktualizace](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Plánování testování a zabezpečení

V každé fázi nasazení od počátečního pilotu až po povolení zřizování uživatelů se ujistěte, že testujete očekávané výsledky a auditujte cykly zřizování.

### <a name="plan-testing"></a>Plánování testování

Po konfiguraci aplikace cloudového HR do zřizování uživatelů Azure AD spusťte testové případy, abyste ověřili, jestli Toto řešení splňuje požadavky vaší organizace.

|Scénáře|Očekávané výsledky|
|:-|:-|
|Do aplikace cloudové HR je zařazen nový zaměstnanec.| – Uživatelský účet je zřízený ve službě Active Directory.</br>– Uživatel se může přihlásit ke službě Active Directory – doména aplikace a provádět požadované akce.</br>– Pokud je nakonfigurovaná Azure AD Connect synchronizace, vytvoří se taky uživatelský účet ve službě Azure AD.
|Uživatel je ukončený v aplikaci cloudového HR.|– Uživatelský účet je ve službě Active Directory zakázaný.</br>– Uživatel se nemůže přihlásit k žádným podnikovým aplikacím chráněným službou Active Directory.
|V aplikaci cloudového HR se aktualizuje uživatelská kontrolní organizace.|V závislosti na mapování atributů se uživatelský účet přesune z jedné organizační jednotky do jiné ve službě Active Directory.|
|HR aktualizuje správce uživatele v aplikaci cloudového HR.|Pole správce ve službě Active Directory se aktualizuje tak, aby odráželo jméno nového manažera.|
|HR rekoupí zaměstnance do nové role.|Chování závisí na tom, jak je aplikace cloudového HR nakonfigurovaná tak, aby generovala ID zaměstnanců:</br>– Pokud se staré ID zaměstnance znovu použije pro opětovné zařazení, konektor povolí pro daného uživatele existující účet služby Active Directory.</br>– Pokud opětovné zařazení získá nové ID zaměstnance, konektor vytvoří pro uživatele nový účet služby Active Directory.|
|HR převede zaměstnance na pracovníka kontraktu nebo naopak.|Vytvoří se nový účet Active Directory pro nového a starý účet se pro datum účinnosti převodu zakáže.|

Pomocí předchozích výsledků určete, jak převést automatickou implementaci zřizování uživatelů do produkčního prostředí založeného na vašich zavedených časových osách.

> [!TIP]
> Při obnovování testovacího prostředí s využitím produkčních dat k odebrání nebo maskování citlivých osobních údajů pro zajištění dodržování standardů ochrany osobních údajů a zabezpečení použijte techniky, jako je například snížení počtu dat a čištění dat. 

### <a name="plan-security"></a>Plánování zabezpečení

V rámci nasazení nové služby je běžné, že je nutné provést kontrolu zabezpečení. Pokud je nutná kontrola zabezpečení nebo není provedena, podívejte se na mnoho [dokumentů White Paper](https://www.microsoft.com/download/details.aspx?id=36391) k Azure AD, které poskytují přehled o identitě jako službě.

### <a name="plan-rollback"></a>Naplánování vrácení zpět

Implementace uživatelského zřizování pro cloudový personál může v produkčním prostředí fungovat podle potřeby. Pokud ano, může vám následující postup vrácení změn pomoct při návratu do předchozího známého funkčního stavu.

1. Zkontrolujte [souhrnnou sestavu zřizování](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) a [protokoly zřizování](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) , abyste zjistili, jaké nesprávné operace byly provedeny na ovlivněných uživatelích nebo skupinách. Další informace o souhrnných sestavách a protokolech zřizování najdete v tématu [Správa zřizování uživatelů pro cloudovou správu lidských aplikací](#manage-your-configuration).
2. Poslední známý dobrý stav uživatelů nebo skupin je možné určit prostřednictvím protokolů pro zřizování nebo kontrolou cílových systémů (Azure AD nebo Active Directory).
3. Spolupracujte s vlastníkem aplikace a aktualizujte uživatele nebo skupiny, které ovlivnily přímo v aplikaci, pomocí posledních známých hodnot stavu dobrý stav.

## <a name="deploy-the-cloud-hr-app"></a>Nasazení aplikace cloudového HR

Vyberte aplikaci cloudového HR, která se zarovnává s požadavky na vaše řešení.

**Workday**: Pokud chcete importovat pracovní profily z Workday do služby Active Directory a Azure AD, přečtěte si [kurz: Konfigurace pracovního dne pro Automatické zřizování uživatelů](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Volitelně můžete napsat e-mailovou adresu, uživatelské jméno a telefonní číslo do Workday.

**SAP SuccessFactors**: Pokud chcete importovat profily pracovních procesů z SuccessFactors do služby Active Directory a Azure AD, přečtěte si téma [kurz: konfigurace SAP SuccessFactors pro Automatické zřizování uživatelů](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Volitelně můžete napsat e-mailovou adresu a uživatelské jméno pro SuccessFactors.

## <a name="manage-your-configuration"></a>Správa konfigurace

Azure AD může poskytovat další poznatky k zřizování uživatelů a provoznímu stavu vaší organizace prostřednictvím protokolů auditu a sestav.

### <a name="gain-insights-from-reports-and-logs"></a>Získání přehledů ze sestav a protokolů

Po úspěšném [počátečním cyklu](../app-provisioning/how-provisioning-works.md#initial-cycle)služba zřizování Azure AD nadále spouští přírůstkové přírůstkové aktualizace po neomezenou dobu v intervalech definovaných v kurzech specifických pro jednotlivé aplikace, dokud nedojde k jedné z následujících událostí:

- Služba se ručně zastavila. Nový počáteční cyklus se spustí pomocí [Azure Portal](https://portal.azure.com/) nebo odpovídajícího příkazu [Microsoft Graph API](/graph/api/resources/synchronization-overview) .
- V důsledku změny mapování atributů nebo filtrů oborů se aktivuje nový počáteční cyklus.
- Proces zřizování přejde do karantény z důvodu vysoké míry chyb. Zůstane v karanténě po dobu delší než čtyři týdny, kdy je automaticky zakázaná.

Pokud chcete zkontrolovat tyto události a všechny další aktivity prováděné službou zřizování, [Naučte se kontrolovat protokoly a získávat sestavy pro aktivitu zřizování](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Všechny aktivity prováděné službou zřizování se zaznamenávají v protokolech auditu Azure AD. Protokoly auditu Azure AD můžete směrovat do protokolů Azure Monitor k další analýze. Pomocí protokolů Azure Monitor (označuje se také jako Log Analytics pracovní prostor) můžete zadávat dotazy na data a vyhledávat události, analyzovat trendy a provádět korelace napříč různými zdroji dat. Podívejte se na toto [video](https://youtu.be/MP5IaCTwkQg) , kde se dozvíte o výhodách použití protokolů Azure monitor pro protokoly Azure AD v praktických scénářích uživatelů.

Nainstalujte [zobrazení Log Analytics pro protokoly aktivit Azure AD](../reports-monitoring/howto-install-use-log-analytics-views.md) , abyste získali přístup k [předem vytvořeným sestavám](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) , které se seznámí s událostmi zřizování ve vašem prostředí.

Další informace najdete v tématu [Analýza protokolů aktivit Azure AD pomocí protokolů Azure monitor](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Správa osobních údajů

Agent Azure AD Connect zřizování nainstalovaný na Windows serveru vytvoří protokoly v protokolu událostí Windows, které můžou obsahovat osobní údaje v závislosti na mapování atributů služby Active Directory v rámci vaší aplikace cloudového HR. Chcete-li dodržovat závazky týkající se ochrany osobních údajů uživatele, nastavte naplánovanou úlohu Windows pro vymazání protokolu událostí a zajistěte, aby žádná data nebyla delší než 48 hodin.

Služba zřizování Azure AD negeneruje sestavy, provádí analýzy ani neposkytuje přehledy po dobu 30 dnů, protože služba neukládá, nezpracovává ani neuchovává žádná data déle než 30 dnů.

### <a name="troubleshoot"></a>Řešení potíží

Chcete-li vyřešit všechny problémy, které se mohou během zřizování vypnout, přečtěte si následující články:

- [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](application-provisioning-config-problem.md)
- [Synchronizace atributu z místní služby Active Directory do Azure AD kvůli zřizování pro aplikaci](user-provisioning-sync-attributes-for-mapping.md)
- [Při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů pro aplikaci Galerie Azure Active Directory došlo k potížím.](./user-provisioning.md)
- [Pro aplikaci Galerie Azure AD se nezřídí žádní uživatelé.](application-provisioning-config-problem-no-users-provisioned.md)
- [Pro aplikaci Galerie Azure AD se zřizuje nesprávná sada uživatelů.](../manage-apps/add-application-portal-assign-users.md)
- [Nastavení Prohlížeč událostí Windows pro řešení potíží s agentem](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Nastavení protokolů auditu Azure Portal pro řešení potíží se službou](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Porozumění protokolům pro operace vytvoření účtu uživatele služby AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Principy protokolů pro operace aktualizace pro správce](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Řešení běžně zjištěných chyb](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Další kroky

- [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
- [Přehled rozhraní API pro synchronizaci Azure AD](/graph/api/resources/synchronization-overview)
- [Přeskočit odstranění uživatelských účtů, které přesahují rozsah](skip-out-of-scope-deletions.md)
- [Agent zřizování Azure AD Connect: Historie verzí](provisioning-agent-release-version-history.md)