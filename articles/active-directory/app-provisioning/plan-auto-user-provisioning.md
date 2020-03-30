---
title: Plánování automatického zřizování uživatelů pro Azure Active Directory
description: Pokyny pro plánování a provádění automatického zřizování uživatelů
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522406"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Naplánování nasazení automatického zřizování uživatelů

Mnoho organizací spoléhá na software jako službu (SaaS) aplikace, jako je ServiceNow, Zscaler a Slack pro produktivitu koncových uživatelů. Historicky it pracovníci se spoléhali na metody ručního zřizování, jako je nahrávání souborů CSV nebo použití vlastních skriptů pro bezpečnou správu identit uživatelů v každé aplikaci SaaS. Tyto procesy jsou náchylné k chybám, nezabezpečené a těžko spravovatelné.

Automatické zřizování uživatelů azure active directory (Azure AD) zjednodušuje tento proces tím, že bezpečně automatizuje vytváření, údržbu a odebírání uživatelských identit v aplikacích SaaS na základě obchodních pravidel. Tato automatizace umožňuje efektivně škálovat vaše systémy správy identit v cloudových i hybridních prostředích při rozšiřování jejich závislosti na cloudových řešeních.

Viz [Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS s Azure Active Directory](../app-provisioning/user-provisioning.md) pro lepší pochopení funkcí.

## <a name="learn"></a>Informace

Zřizování uživatelů vytváří základ pro průběžné zásady správného řízení identit a zvyšuje kvalitu podnikových procesů, které jsou závislé na autoritativních datech identity.

### <a name="key-benefits"></a>Klíčové výhody

Hlavní výhody povolení automatického zřizování uživatelů jsou:

* **Zvýšená produktivita**. Můžete spravovat identity uživatelů v aplikacích SaaS s jedním uživatelem zřizování rozhraní pro správu. Toto rozhraní má jednu sadu zásad zřizování.

* **Řízení rizik**. Zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnance nebo členství ve skupinách, které definují role nebo přístup.

* **Řešení dodržování předpisů a zásad správného řízení**. Azure AD podporuje nativní protokoly auditu pro každý požadavek zřizování uživatelů. Požadavky jsou prováděny ve zdrojovém i cílovém systému. To umožňuje sledovat, kdo má přístup k aplikacím z jedné obrazovky.

* **Snižte náklady**. Automatické zřizování uživatelů snižuje náklady tím, že zabraňuje neefektivitě a lidským chybám spojeným s ručním zřizováním. Snižuje potřebu vlastní vyvinuté řešení zřizování uživatelů, skripty a protokoly auditu.

### <a name="licensing"></a>Licencování

Azure AD poskytuje samoobslužnou integraci libovolné aplikace pomocí šablon uvedených v nabídce galerie aplikací. Úplný seznam licenčních požadavků najdete na [stránce licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licencování aplikací

Budete potřebovat příslušné licence pro aplikace, které chcete automaticky zřídit. Proberte s vlastníky aplikace, zda uživatelé přiřazené k aplikaci mají správné licence pro své role aplikace. Pokud Azure AD spravuje automatické zřizování na základě rolí, role přiřazené ve službě Azure AD musí zarovnat na licence aplikací. Nesprávné licence vlastněné v aplikaci může vést k chybám během zřizování nebo aktualizace uživatele.

### <a name="terms"></a>Výrazy

Tento článek používá následující termíny:

* Crud operace - akce provedené na uživatelské účty: Vytvořit, Číst, Aktualizovat, Odstranit.

* Jednotné přihlašování (SSO) – možnost pro uživatele přihlásit jednou a přístup ke všem aplikacím s povoleno jednotnépřihlašování. V kontextu zřizování uživatelů jednotné přihlížení je výsledkem uživatelů, kteří mají jeden účet pro přístup ke všem systémům, které používají automatické zřizování uživatelů.

* Zdrojový systém – úložiště uživatelů, které azure ad zřizované z. Azure AD je zdrojový systém pro většinu předem integrovaných zřizovacích konektorů. Existují však některé výjimky pro cloudové aplikace, jako je SAP, Workday a AWS. Například najdete [v tématu Zřizování uživatelů z Workday do AD](../saas-apps/workday-inbound-tutorial.md).

* Cílový systém – úložiště uživatelů, které Azure AD zřazuje. Cílový systém je obvykle aplikace SaaS jako ServiceNow, Zscaler a Slack. Cílový systém může být také místní systém, jako je například AD.

* [Systém pro správu identit mezi doménami (SCIM)](https://aka.ms/scimoverview) – otevřený standard, který umožňuje automatizaci zřizování uživatelů. SCIM sděluje data identity uživatelů mezi poskytovateli identit, jako je Microsoft, a poskytovateli služeb, jako je Salesforce nebo jiné aplikace SaaS, které vyžadují informace o identitě uživatele.

### <a name="training-resources"></a>Zdroje pro školení

| Prostředky| Odkaz a popis |
| - | - |
| Webináře na vyžádání| [Správa podnikových aplikací pomocí Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Zjistěte, jak vám Azure AD může pomoct dosáhnout přihlašování k vlastnímu zabezpečení pro vaše podnikové aplikace SaaS a osvědčené postupy pro řízení přístupu. |
| Videa| [Co je zřizování uživatelů ve službě Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Jak nasadit zřizování uživatelů ve službě Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integrace Salesforce s Azure AD: Jak automatizovat zřizování uživatelů](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Online kurzy| SkillUp Online: [Správa identit](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Zjistěte, jak integrovat Azure AD s mnoha aplikacemi SaaS a zabezpečit přístup uživatelů k těmto aplikacím. |
| Knihy| [Moderní ověřování pomocí služby Azure Active Directory for Web Applications (Reference pro vývojáře) 1.](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)  <br> Toto je autoritativní, podrobné vodítko pro vytváření řešení ověřování služby Active Directory pro tato nová prostředí. |
| Kurzy| Podívejte se [na seznam kurzů o tom, jak integrovat aplikace SaaS s Azure AD](../saas-apps/tutorial-list.md). |
| Nejčastější dotazy| [Nejčastější dotazy týkající](../app-provisioning/user-provisioning.md) se automatického zřizování uživatelů |

### <a name="solution-architectures"></a>Architektury řešení

Zřizovací služba Azure AD zřazuje uživatele do aplikací SaaS a dalších systémů připojením ke koncovým bodům rozhraní API pro správu uživatelů poskytovaným jednotlivými dodavateli aplikací. Tyto koncové body rozhraní API pro správu uživatelů umožňují Azure AD programově vytvářet, aktualizovat a odebírat uživatele.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatické zřizování uživatelů pro hybridní podniky

V tomto příkladu jsou uživatelé nebo skupiny vytvořeny v databázi HR připojené k místnímu adresáři. Zřizování Azure AD služba spravuje automatické zřizování uživatelů na cílové aplikace SaaS.

 ![zřizování uživatelů](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Popis pracovního postupu:**

1. Uživatelé/skupiny jsou vytvořeny v místní hr aplikace nebo systému, jako je například SAP. 

1. **Agent Azure AD Connect** spouští naplánované synchronizace identit (uživatelů a skupin) z místního ad do Azure AD.

1. **Služba zřizování Azure AD** začíná [počáteční cyklus](../app-provisioning/user-provisioning.md) proti zdrojového systému a cílového systému. 

1. **Služba zřizování Azure AD** se dotazuje zdrojového systému pro všechny uživatele a skupiny, které se od počátečního cyklu změnily, a odesílá změny v [přírůstkových cyklech](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatické zřizování uživatelů pro podniky pouze s cloudem

V tomto příkladu dojde k vytvoření uživatele ve službě Azure AD a služba zřizování Azure AD spravuje automatické zřizování uživatelů do cílových (SaaS) aplikací.

![Obrázek 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Popis pracovního postupu:**

1. Uživatelé nebo skupiny se vytvářejí ve službě Azure AD.

1. **Služba zřizování Azure AD** začíná [počáteční cyklus](../app-provisioning/user-provisioning.md) proti zdrojového systému a cílového systému. 

1. **Služba zřizování Azure AD** se dotazuje zdrojového systému pro všechny uživatele a skupiny aktualizované od počátečního cyklu a provádí všechny [přírůstkové cykly](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatické zřizování uživatelů pro cloudové HR aplikace 

V tomto příkladu jsou uživatelé nebo skupiny vytvořeny v cloudové hr aplikaci, jako je například Workday a SuccessFactors. Zřizovací služba Azure AD a agent zřizování Azure AD Connect zřazují uživatelská data z klienta cloudových aplikací HR do služby AD. Jakmile se účty aktualizují ve službě AD, synchronizují se s Azure AD prostřednictvím Azure AD Connect a e-mailové adresy a atributy uživatelského jména se můžou zapsat zpět do tenanta cloudových hr aplikací.

![Obrázek 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Hr tým** provádí transakce v tenantovi cloudových HR aplikací.
2.  **Služba zřizování Azure AD** spouští naplánované cykly z klienta cloudových hr aplikací a identifikuje změny, které je potřeba zpracovat pro synchronizaci se službou AD.
3.  **Služba zřizování Azure AD** vyvolá agenta zřizování Azure AD Connect s datovou částí požadavku obsahující operace vytvoření/aktualizace/povolení/zakázání účtu Služby AD.
4.  **Agent zřizování Azure AD Connect** používá účet služby ke správě dat účtu Služby AD.
5.  **Azure AD Connect** spouští synchronizaci delta pro vyžádat aktualizace ve službě AD.
6.  Aktualizace **služby AD** se synchronizují se službou Azure AD. 
7.  **Azure AD zřizování služby** zpětně e-mailatribut a uživatelské jméno z Azure AD do klienta cloudhr aplikace.

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Zvažte vaše organizační potřeby k určení strategie pro nasazení zřizování uživatelů ve vašem prostředí.

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, je to obvykle z důvodu neshody očekávání na dopad, výsledky a odpovědnosti. Abyste se těmto nástrahám vyhnuli, [ujistěte se, že zapojujete správné zúčastněné strany](https://aka.ms/deploymentplans) a že role zúčastněných stran v projektu jsou dobře pochopeny dokumentováním zúčastněných stran a jejich vstupů a odpovědnosti za projekt.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně komunikujte s uživateli o tom, jak se jejich prostředí změní, kdy se změní a jak získat podporu, pokud se u nich vyskytnou problémy.

### <a name="plan-a-pilot"></a>Naplánujte si pilotní projekt

Doporučujeme, aby počáteční konfigurace automatického zřizování uživatelů být v testovacím prostředí s malou podmnožinu uživatelů před škálování na všechny uživatele v produkčním prostředí. Podívejte se na [osvědčené postupy](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) pro spuštění pilota.

#### <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilota  

Pilot umožňuje testovat s malou skupinou před nasazením schopnosti pro každého. Ujistěte se, že v rámci testování je každý případ použití v rámci vaší organizace důkladně testován.

V první vlně zacilte na IT, použitelnost a další vhodné uživatele, kteří mohou testovat a poskytovat zpětnou vazbu. Tato zpětná vazba slouží k dalšímu rozvoji komunikace a pokynů, které odesíláte uživatelům, a k získání přehledu o typech problémů, které mohou pracovníci podpory vidět.

Rozšíření zavádění na větší skupiny uživatelů zvýšením rozsahu skupiny cílené. To lze provést prostřednictvím [dynamického členství ve skupině](../users-groups-roles/groups-dynamic-membership.md)nebo ručním přidáním uživatelů do cílových skupin.

## <a name="plan-application-connections-and-administration"></a>Plánování připojení a správy aplikací

Portál Azure AD slouží k zobrazení a správě všech aplikací, které podporují zřizování. Viz [Hledání aplikací na portálu](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Určení typu konektoru, který se má použít

Skutečné kroky potřebné k povolení a konfiguraci automatického zřizování se liší v závislosti na aplikaci. Pokud aplikace, kterou chcete automaticky zřídit, je uvedena v [galerii aplikací Azure AD SaaS](../saas-apps/tutorial-list.md), měli byste vybrat [kurz integrace pro konkrétní aplikaci](../saas-apps/tutorial-list.md) a nakonfigurovat předem integrovaný konektor zřizování uživatelů.

Pokud ne, postupujte podle následujících kroků:

1. [Vytvořte požadavek](../develop/howto-app-gallery-listing.md) na předem integrovaný konektor zřizování uživatelů. Náš tým bude spolupracovat s vámi a vývojářem aplikace, aby se na palubě vaší aplikace na naší platformě, pokud podporuje SCIM.

1. Použijte obecnou podporu zřizování uživatelů [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) pro aplikaci. Toto je požadavek pro Azure AD zřídit uživatele do aplikace bez předem integrovanézřizační konektor.

1. Pokud je aplikace schopna využít konektor BYOA SCIM, pak se podívejte na [integrační kurz BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) pro konfiguraci konektoru BYOA SCIM pro aplikaci.

Další informace [najdete v tématu Jaké aplikace a systémy lze použít s azure ad automatické zřizování uživatelů?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Shromažďování informací za účelem autorizace přístupu k aplikacím

Nastavení automatického zřizování uživatelů je proces pro jednotlivé aplikace. Pro každou aplikaci je třeba zadat [pověření správce](../app-provisioning/configure-automatic-user-provisioning-portal.md) pro připojení ke koncovému bodu správy uživatelů cílového systému.

Na obrázku níže je zobrazena jedna verze požadovaných přihlašovacích údajů správce:

![Obrazovka zřizování pro správu nastavení zřizování uživatelských účtů](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Zatímco některé aplikace vyžadují uživatelské jméno správce a heslo, jiné mohou vyžadovat nosný token.

## <a name="plan-user-and-group-provisioning"></a>Plánování zřizování uživatelů a skupin

Pokud povolíte zřizování uživatelů pro podnikové aplikace, [portál Azure](https://portal.azure.com/) řídí hodnoty atributů prostřednictvím mapování atributů.

### <a name="determine-operations-for-each-saas-app"></a>Určení operací pro každou aplikaci SaaS

Každá aplikace může mít jedinečné atributy uživatele nebo skupiny, které musí být mapovány na atributy ve vašem Azure AD. Aplikace může mít k dispozici pouze podmnožinu operací CRUD.

Pro každou žádost zdokumentujte tyto informace:

* Crud zřizování operace, které mají být provedeny na uživatele nebo group objekty pro cílové systémy. Například každý vlastník aplikace SaaS nemusí chtít všechny možné operace.

* Atributy dostupné ve zdrojovém systému

* Atributy dostupné v cílovém systému

* Mapování atributů mezi systémy.

### <a name="choose-which-users-and-groups-to-provision"></a>Zvolte, kteří uživatelé a skupiny mají být zřazováni

Před implementací automatickézřivačné zřizování uživatelů je nutné určit uživatele a skupiny, které mají být zřízeny do vaší aplikace.

* Pomocí [filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) definujte pravidla založená na atributech, která určují, kteří uživatelé jsou zřízeni do aplikace.

* Dále použijte [přiřazení uživatelů a skupin](../manage-apps/assign-user-or-group-access-portal.md) podle potřeby pro další filtrování.

### <a name="define-user-and-group-attribute-mapping"></a>Definovat mapování atributů uživatelů a skupin

Chcete-li implementovat automatické zřizování uživatelů, musíte definovat atributy uživatele a skupiny, které jsou potřebné pro aplikaci. Existuje předem nakonfigurovaná sada atributů a [mapování atributů](../app-provisioning/configure-automatic-user-provisioning-portal.md) mezi uživatelskými objekty Azure AD a uživatelskými objekty každé aplikace SaaS. Ne všechny aplikace SaaS povolují skupinové atributy.

Azure AD podporuje přímým mapováním atributů atributů, poskytováním konstantních hodnot nebo [zápisem výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md). Tato flexibilita poskytuje jemnou kontrolu nad tím, co bude naplněno v atributu cílového systému. Rozhraní [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) a Graph Explorer můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat je zpět do Azure AD.

Další informace naleznete [v tématu Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Zvláštní aspekty pro zřizování uživatelů

Zvažte následující snížit problémy po nasazení:

* Ujistěte se, že atributy používané k mapování objektů uživatelů nebo skupin mezi zdrojovými a cílovými aplikacemi jsou odolné. Neměly by způsobit, že uživatelé nebo skupiny budou zřízeny nesprávně, pokud se změní atributy (například uživatel se přesune do jiné části společnosti).

* Aplikace mohou mít konkrétní omezení nebo požadavky, které je třeba splnit, aby zřizování uživatelů fungovalo správně. Například Slack zkrátí hodnoty pro určité atributy. Podívejte se na [automatické uživatelské zřizování kurzy](../saas-apps/tutorial-list.md) specifické pro každou aplikaci.

* Potvrďte konzistenci schématu mezi zdrojovým a cílovým systémem. Mezi běžné problémy patří atributy, jako je například upn nebo pošta, které neodpovídají. Například hlavní upn ve službě *john_smith@contoso.com* Azure AD nastavit *jsmith@contoso.com*jako a v aplikaci, je to . Další informace naleznete v tématu Odkaz na schéma [uživatele a skupiny](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Plánování testování a zabezpečení

V každé fázi nasazení zajistíte, že testujete, že výsledky jsou podle očekávání, a auditujete cykly zřizování.

### <a name="plan-testing"></a>Plánování testování

Jakmile nakonfigurujete automatické zřizování uživatelů pro aplikaci, spustíte testovací případy, abyste ověřili, že toto řešení splňuje požadavky vaší organizace.

| Scénáře| Očekávané výsledky |
| - | - |
| Uživatel je přidán do skupiny přiřazené k cílovému systému. | Objekt uživatele je zřízena v cílovém systému. <br>Uživatel se může přihlásit k cílovému systému a provést požadované akce. |
| Uživatel je odebrán ze skupiny, která je přiřazena k cílovému systému. | Objekt uživatele je zrušen v cílovém systému.<br>Uživatel se nemůže přihlásit k cílovému systému. |
| Informace o uživateli se aktualizují ve službě Azure AD libovolnou metodou | Aktualizované atributy uživatele se projeví v cílovém systému po přírůstkovém cyklu. |
| Uživatel je mimo rozsah | Objekt uživatele je zakázán nebo odstraněn. <br>Poznámka: Toto chování je přepsáno pro [zřizování pracovního dne](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Plánování zabezpečení

Je běžné, že kontrola zabezpečení je vyžadována jako součást nasazení. Pokud požadujete kontrolu zabezpečení, podívejte se na mnoho [dokumentů white paper](https://www.microsoft.com/download/details.aspx?id=36391) azure ad, který poskytuje přehled identity jako služby.

### <a name="plan-rollback"></a>Vrácení plánu zpět

Pokud automatické zřizování uživatelů implementace nefunguje podle potřeby v produkčním prostředí, následující kroky vrácení zpět níže vám může pomoci při návratu do předchozího známého dobrého stavu:

1. Zkontrolujte [zřizování souhrnné sestavy](../app-provisioning/check-status-user-account-provisioning.md) a [zřizování protokoly](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) k určení, jaké nesprávné operace došlo u postižených uživatelů nebo skupin.

1. Pomocí protokolů auditování zřizování určete poslední známý dobrý stav dotčených uživatelů nebo skupin. Zkontrolujte také zdrojové systémy (Azure AD nebo AD).

1. Spolupracujte s vlastníkem aplikace a aktualizujte uživatele nebo skupiny, kterých se to týká přímo v aplikaci, pomocí posledních známých hodnot dobrého stavu.

## <a name="deploy-automatic-user-provisioning-service"></a>Nasazení služby automatického zřizování uživatelů

Zvolte kroky, které odpovídají požadavkům vašeho řešení.

### <a name="prepare-for-the-initial-cycle"></a>Připravte se na počáteční cyklus

Při prvním spuštění služby zřizování Azure AD počáteční cyklus proti zdrojového systému a cílové systémy vytvoří snímek všech uživatelských objektů pro každý cílový systém.

Při povolení automatického zřizování pro aplikaci může počáteční cyklus trvat od 20 minut do několika hodin. Doba trvání závisí na velikosti adresáře Azure AD a počet uživatelů v oboru pro zřizování. Viz [Jak zlepšit výkon zřizování](../app-provisioning/application-provisioning-when-will-provisioning-finish.md).

Zřizovací služba ukládá stav obou systémů po počátečním cyklu, zlepšení výkonu následné přírůstkové cykly.

### <a name="configure-automatic-user-provisioning"></a>Konfigurace automatického zřizování uživatelů

Portál [Azure](https://portal.azure.com/) slouží ke správě automatického zřizování uživatelských účtů a zrušení zřizování aplikací, které ho podporují. Postupujte podle pokynů v [části Jak nastavit automatické zřizování aplikace?](../app-provisioning/user-provisioning.md)

Službu zřizování uživatelů Azure AD lze také nakonfigurovat a spravovat pomocí [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Správa automatického zřizování uživatelů

Teď, když jste nasadili, musíte spravovat řešení.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorování stavu operací zřizování uživatelů

Po úspěšném [počátečním cyklu](../app-provisioning/user-provisioning.md)bude služba zřizování Azure AD spouštět přírůstkové aktualizace neomezeně dlouho, v intervalech specifických pro každou aplikaci, dokud nedojde k jedné z následujících událostí:

* Služba se ručně zastaví a nový počáteční cyklus se aktivuje pomocí [portálu Azure nebo](https://portal.azure.com/)pomocí příslušného příkazu [rozhraní Microsoft Graph API.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* Nový počáteční cyklus je spuštěn změnou mapování atributů nebo filtrů oborů.

* Proces zřizování přejde do karantény z důvodu vysoké chybovosti a zůstane v karanténě po dobu delší než čtyři týdny, kdy bude automaticky zakázán.

Chcete-li zkontrolovat tyto události a všechny ostatní aktivity prováděné službou zřizování, naleznete v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)Azure AD .

Chcete-li pochopit, jak dlouho zřizovací cykly trvat a sledovat průběh úlohy zřizování, můžete [zkontrolovat stav zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Získejte přehledy z přehledů

Azure AD můžete poskytnout [další přehled o](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) využití uživatelů vaší organizace a provozní stav prostřednictvím protokolů auditu a sestav.

Správci by měli zkontrolovat souhrnnou zprávu zřizování a sledovat provozní stav úlohy zřizování. Všechny aktivity prováděné službou zřizování se zaznamenávají v protokolech auditu Azure AD. Viz [kurz: Vytváření přehledů o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

Doporučujeme převzít vlastnictví a využívat tyto sestavy na kadenci, která splňuje požadavky vaší organizace. Azure AD uchovávětšinu většinu dat auditu po dobu 30 dnů.

### <a name="troubleshoot"></a>Řešení potíží

Podívejte se na následující odkazy k řešení problémů, které se mohou objevit během zřizování:

* [Problém s konfigurací zřizování uživatelů do aplikace Azure AD Gallery](../app-provisioning/application-provisioning-config-problem.md)

* [Synchronizace atributu z místní služby Active Directory do služby Azure AD pro zřizování do aplikace](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Zřizování uživatelů do aplikace Azure AD Gallery trvá hodiny nebo víc](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Při konfiguraci zřizování uživatelů do aplikace Azure Active Directory Gallery došlo k potížím s ukládáním přihlašovacích údajů správce](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Do aplikace Azure AD Gallery se nezřají žádní uživatelé.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Nesprávná sada uživatelů se zřaží do aplikace Azure AD Gallery](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Užitečná dokumentace

* [Psaní výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md)

* [Přehled rozhraní API synchronizace Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah](skip-out-of-scope-deletions.md)

* [Agent zřizování Azure AD Connect: Historie verzí vydání](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Prostředky

* [Poskytnutí zpětné vazby k produktu](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mějte aktuální informace o tom, co je nového ve službě Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Zásobník přetečení Azure AD fórum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Další kroky
* [Konfigurace automatického zřizování uživatelů](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Export nebo import konfigurace zřizování pomocí rozhraní Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Psaní výrazů pro mapování atributů ve službě Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)
