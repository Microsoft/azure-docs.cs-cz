---
title: Plánování automatického zřizování uživatelů pro Azure Active Directory
description: Pokyny k plánování a provádění automatického zřizování uživatelů
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: b7d05cd394453ff7c3a40b80a4cd6c850b86ee21
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070363"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Naplánování nasazení automatického zřizování uživatelů

Mnoho organizací spoléhá na aplikace typu software jako služba (SaaS), jako je ServiceNow, Zscaler a časová rezerva produktivity koncových uživatelů. Historicky se zaměstnanci IT spoléhali na metody ručního zřizování, jako je například nahrávání souborů CSV, nebo použití vlastních skriptů pro bezpečnou správu identit uživatelů v jednotlivých aplikacích SaaS. Tyto procesy jsou náchylné k chybám, nezabezpečené a nespravované.

Azure Active Directory (Azure AD) Automatické zřizování uživatelů zjednodušuje tento proces tím, že automatizuje automatizaci vytváření, údržby a odebírání identit uživatelů v aplikacích SaaS na základě obchodních pravidel. Tato automatizace vám umožní efektivně škálovat vaše systémy správy identit v cloudových i hybridních prostředích, když rozšíříte jejich závislost na cloudových řešeních.

Další informace najdete v tématu [Automatizace zřizování a rušení uživatelů při SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md) pro lepší pochopení funkčnosti.

## <a name="learn"></a>Learn

Zřizování uživatelů vytvoří základ pro průběžné řízení identit a vylepšuje kvalitu obchodních procesů, které spoléhají na data autoritativní identity.

### <a name="key-benefits"></a>Klíčové výhody

Mezi klíčové výhody povolení automatického zřizování uživatelů patří:

* **Zvýšení produktivity**. Pomocí jednoho rozhraní pro správu zřizování uživatelů můžete spravovat identity uživatelů napříč SaaS aplikacemi. Toto rozhraní má jednu sadu zásad zřizování.

* **Řízení rizik**. Zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnanců nebo členství ve skupinách, které definují role nebo přístup.

* **Vyřešte dodržování předpisů a zásady správného řízení**. Azure AD podporuje protokoly nativního auditu pro každou žádost o zřízení uživatele. Žádosti jsou spouštěny ve zdrojovém i cílovém systému. Díky tomu můžete sledovat, kdo má přístup k aplikacím z jedné obrazovky.

* **Snižte náklady**. Automatické zřizování uživatelů snižuje náklady tím, že se vyhne neefektivitám a lidským chybám přidruženým k ručnímu zřizování. Omezuje nutnost vlastních řešení pro zřizování uživatelů, skriptů a protokolů auditu.

### <a name="licensing"></a>Licensing

Služba Azure AD poskytuje samoobslužnou integraci jakékoli aplikace pomocí šablon poskytovaných v nabídce Galerie aplikací. Úplný seznam licenčních požadavků najdete na [stránce licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licencování aplikací

Pro aplikace, které chcete automaticky zřídit, budete potřebovat příslušné licence. Prodiskutujte s vlastníky aplikace bez ohledu na to, zda uživatelé přiřazení k aplikaci mají správné licence na své aplikační role. Pokud Azure AD spravuje Automatické zřizování na základě rolí, musí se role přiřazené v Azure AD zarovnat k licencím na aplikace. Nesprávné licence vlastněné v aplikaci mohou vést k chybám během zřizování nebo aktualizace uživatele.

### <a name="terms"></a>Terminologie

Tento článek používá následující výrazy:

* Operace CRUD – akce provedené u uživatelských účtů: vytvořit, číst, aktualizovat, odstranit.

* Jednotné přihlašování (SSO) – umožňuje uživateli přihlašovat se jednou a získat přístup ke všem aplikacím podporujícím jednotné přihlašování. V kontextu zřizování uživatelů je jednotné přihlašování v důsledku toho, že uživatelé mají jeden účet pro přístup ke všem systémům, které používají Automatické zřizování uživatelů.

* Zdrojový systém – úložiště uživatelů, ze kterých Azure AD zřídí. Azure AD je zdrojový systém pro většinu předem integrovaných zřizovacích konektorů. Existují však určité výjimky pro cloudové aplikace, například SAP, Workday a AWS. Například viz [zřizování uživatelů z Workday do AD](../saas-apps/workday-inbound-tutorial.md).

* Cílový systém – úložiště uživatelů, na které Azure AD zřídí. Cílový systém je obvykle aplikace SaaS, jako je ServiceNow, Zscaler a časová rezerva. Cílový systém může být také místní systém, například AD.

* [Systém pro správu identit mezi doménami (SCIM)](https://aka.ms/scimoverview) – otevřený standard, který umožňuje automatizovat zřizování uživatelů. SCIM komunikuje data identity uživatelů mezi poskytovateli identity, jako je Microsoft, a poskytovateli služeb, jako je Salesforce nebo jiné aplikace SaaS, které vyžadují informace o identitě uživatele.

### <a name="training-resources"></a>Školicí materiály

| Zdroje a prostředky| Odkaz a popis |
| - | - |
| Webináře na vyžádání| [Správa podnikových aplikací pomocí Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Přečtěte si, jak vám Azure AD může přispět k zajištění jednotného přihlašování k podnikovým aplikacím SaaS a osvědčeným postupům pro řízení přístupu. |
| Videa| [Co je zřizování uživatelů v aktivním adresáři Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Jak nasadit zřizování uživatelů v aktivním adresáři Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integrování Salesforce s Azure AD: Jak automatizovat zřizování uživatelů](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Online kurzy| SkillUp online:  [Správa identit](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Naučte se integrovat Azure AD s mnoha aplikacemi SaaS a zabezpečit uživatelům přístup k těmto aplikacím. |
| Knihy| [Moderní ověřování pomocí Azure Active Directory pro webové aplikace (Referenční příručka pro vývojáře) 1](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Toto je autoritativní podrobně průvodce pro vytváření řešení pro ověřování služby Active Directory pro tato nová prostředí. |
| Kurzy| Podívejte se na [Seznam kurzů, jak integrovat aplikace SaaS s Azure AD](../saas-apps/tutorial-list.md). |
| Nejčastější dotazy| [Nejčastější dotazy](../app-provisioning/user-provisioning.md) k automatizovanému zřizování uživatelů |

### <a name="solution-architectures"></a>Architektury řešení

Služba zřizování Azure AD zřídí uživatele k SaaS aplikací a dalších systémů připojením k koncovým bodům rozhraní API pro správu uživatelů poskytovanými jednotlivými dodavateli aplikací. Tyto koncové body rozhraní API správy uživatelů umožňují službě Azure AD programově vytvářet, aktualizovat a odebírat uživatele.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatické zřizování uživatelů pro hybridní podniky

V tomto příkladu se uživatelé a skupiny vytvoří v databázi HR připojené k místnímu adresáři. Služba zřizování Azure AD spravuje Automatické zřizování uživatelů pro cílové aplikace SaaS.

 ![zřizování uživatelů](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Popis pracovního postupu:**

1. Uživatelé nebo skupiny se vytvářejí v místní aplikaci nebo systému pro personální řízení, jako je například SAP. 

1. **Agent Azure AD Connect** spouští naplánované synchronizace identit (uživatelů a skupin) z místní služby AD do Azure AD.

1. **Služba zřizování Azure AD** zahajuje [počáteční cyklus](../app-provisioning/user-provisioning.md) proti zdrojovému systému a cílovému systému. 

1. **Služba zřizování Azure AD** se dotáže zdrojového systému na všechny uživatele a skupiny, které se od počátečního cyklu změnily, a vloží změny do [přírůstkových cyklů](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatické zřizování uživatelů jenom pro cloudové podniky

V tomto příkladu dojde k vytvoření uživatele ve službě Azure AD a služba zřizování Azure AD spravuje Automatické zřizování uživatelů pro cílové (SaaS) aplikace.

![Obrázek 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Popis pracovního postupu:**

1. Uživatelé nebo skupiny se vytvářejí ve službě Azure AD.

1. **Služba zřizování Azure AD** zahajuje [počáteční cyklus](../app-provisioning/user-provisioning.md) proti zdrojovému systému a cílovému systému. 

1. **Služba zřizování Azure AD** se dotáže zdrojového systému na všechny uživatele a skupiny aktualizované od počátečního cyklu a provede všechny [přírůstkové cykly](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatické zřizování uživatelů pro cloudové aplikace HR 

V tomto příkladu se uživatelé a skupiny vytvářejí v rámci aplikace cloudového HR, jako třeba Workday a SuccessFactors. Služba zřizování služby Azure AD a Agent pro zřizování Azure AD Connect zřídí data uživatelů z tenanta aplikace cloudového HR do AD. Po aktualizaci účtů ve službě AD se služba synchronizuje se službou Azure AD prostřednictvím Azure AD Connect a e-mailové adresy a atributy uživatelského jména se dají zapsat zpátky do tenanta aplikace cloudového HR.

![Obrázek 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Tým HR** provádí transakce v tenantovi cloudové aplikace.
2.  **Služba zřizování Azure AD** spouští naplánované cykly z tenanta aplikace cloudového HR a identifikuje změny, které je potřeba zpracovat pro synchronizaci se službou AD.
3.  **Služba zřizování Azure AD** vyvolá agenta Azure AD Connect zřizování s datovou částí požadavku obsahující účet služby AD, který obsahuje operace vytvořit/aktualizovat/povolit/zakázat.
4.  **Agent zřizování Azure AD Connect** pro správu dat účtu AD používá účet služby.
5.  **Azure AD Connect** spustí rozdílovou synchronizaci s aktualizacemi Pull ve službě AD.
6.  Aktualizace **AD** se synchronizují se službou Azure AD. 
7.  Buněk e-mailový atribut **služby Azure AD zřizování** a uživatelské jméno z Azure AD do tenanta aplikace cloudového hr.

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Vezměte v úvahu, že vaše organizace musí určit strategii nasazení zřizování uživatelů ve vašem prostředí.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, je obvykle z důvodu neshodných očekávání na dopad, výsledky a odpovědnosti. Aby se tyto nástrahy nezobrazovaly, ujistěte se, že [jste připravujete správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role účastníka v projektu jsou dobře srozumitelné při dokumentaci zúčastněných stran a jejich vstupu do projektu a accountabilities.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s vašimi uživateli, jak se změní, když se změní, a jak získat podporu, pokud se jim setkávají problémy.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Doporučujeme, aby počáteční konfigurace automatického zřizování uživatelů byla v testovacím prostředí s malou podmnožinou uživatelů, než se škáluje na všechny uživatele v produkčním prostředí. Podívejte se na [osvědčené postupy](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) pro spuštění pilotního projektu.

#### <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilotní nasazení  

Pilotní nasazení vám umožní otestovat malou skupinu před tím, než se nasadí funkce pro každého. Ujistěte se, že jako součást testování je každý případ použití v rámci vaší organizace důkladně testován.

V první vlny, cílení na IT, použitelnost a další vhodné uživatele, kteří můžou testovat a poskytovat zpětnou vazbu. Tuto zpětnou vazbu použijte k dalšímu vývoji komunikace a pokynů, které odesíláte vašim uživatelům, a poskytněte přehledy o typech problémů, které mohou pracovníci podpory vidět.

Rozšiřte zavedení na větší skupiny uživatelů tím, že zvýšíte rozsah cílových skupin. To se dá udělat prostřednictvím [členství v dynamické skupině](../users-groups-roles/groups-dynamic-membership.md)nebo ručním přidáním uživatelů do cílových skupin.

## <a name="plan-application-connections-and-administration"></a>Plánování připojení a správy aplikací

Pomocí portálu Azure AD můžete zobrazit a spravovat všechny aplikace, které podporují zřizování. Podívejte se na téma [hledání aplikací na portálu](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Určení typu konektoru, který se má použít

Skutečný postup potřebný k povolení a konfiguraci automatického zřizování se liší v závislosti na aplikaci. Pokud je aplikace, kterou chcete automaticky zřídit, uvedená v [galerii aplikací Azure AD SaaS](../saas-apps/tutorial-list.md), měli byste vybrat [kurz integrace specifický pro aplikaci](../saas-apps/tutorial-list.md) a nakonfigurovat svůj předem integrovaný konektor pro zřizování uživatelů.

V takovém případě postupujte podle následujících kroků:

1. [Vytvoří žádost](../azuread-dev/howto-app-gallery-listing.md) o předem integrovaný konektor pro zřizování uživatelů. Náš tým bude spolupracovat s vámi a vývojářem aplikace, aby svou aplikaci připojil k naší platformě, pokud podporuje SCIM.

1. Pro aplikaci použijte podporu obecného zřizování uživatelů [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) . To je požadavek, aby služba Azure AD zřídila uživatele do aplikace bez integrovaného zřizovacího konektoru.

1. Pokud je aplikace schopná využít konektor BYOA SCIM, nakonfigurujte pro aplikaci konektor BYOA SCIM v kurzu věnovaném [integraci BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) .

Další informace najdete v tématu [Jaké aplikace a systémy můžu použít při automatickém zřizování uživatelů Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Shromažďovat informace pro autorizaci přístupu k aplikaci

Nastavení automatického zřizování uživatelů je proces jednotlivých aplikací. Pro každou aplikaci je potřeba poskytnout [přihlašovací údaje správce](../app-provisioning/configure-automatic-user-provisioning-portal.md) pro připojení ke koncovému bodu správy uživatelů cílového systému.

Následující obrázek ukazuje jednu verzi požadovaných přihlašovacích údajů správce:

![Zřizování obrazovky pro správu nastavení zřizování uživatelských účtů](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

I když některé aplikace vyžadují uživatelské jméno a heslo správce, můžou jiné vyžadovat nosný token.

## <a name="plan-user-and-group-provisioning"></a>Plánování zřizování uživatelů a skupin

Pokud povolíte zřizování uživatelů pro podnikové aplikace, [Azure Portal](https://portal.azure.com/) řídí její hodnoty atributu prostřednictvím mapování atributů.

### <a name="determine-operations-for-each-saas-app"></a>Určení operací pro jednotlivé aplikace SaaS

Každá aplikace může mít jedinečné atributy uživatele nebo skupiny, které musí být namapovány na atributy ve službě Azure AD. Aplikace může mít k dispozici pouze podmnožinu operací CRUD.

Pro každou aplikaci zdokumentujte následující informace:

* Operace zřizování CRUD, které se mají provádět na objektech uživatelů a nebo skupin pro cílové systémy. Například každý vlastník firemní aplikace SaaS nemusí chtít všechny možné operace.

* Atributy dostupné ve zdrojovém systému

* Atributy dostupné v cílovém systému

* Mapování atributů mezi systémy.

### <a name="choose-which-users-and-groups-to-provision"></a>Vybrat uživatele a skupiny, které se mají zřídit

Před implementací automatického zřizování uživatelů musíte určit uživatele a skupiny, které se mají zřídit pro vaši aplikaci.

* Použijte [filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) k definování pravidel založených na atributech, která určují, kteří uživatelé jsou zřízeni v aplikaci.

* Dále podle potřeby použijte [přiřazení uživatelů a skupin](../manage-apps/assign-user-or-group-access-portal.md) pro další filtrování.

### <a name="define-user-and-group-attribute-mapping"></a>Definování mapování atributů uživatelů a skupin

K implementaci automatického zřizování uživatelů musíte definovat atributy uživatelů a skupin, které jsou pro aplikaci potřeba. Existuje předem nakonfigurovaná sada atributů a [mapování atributů](../app-provisioning/configure-automatic-user-provisioning-portal.md) mezi uživatelskými objekty Azure AD a jednotlivými uživatelskými objekty aplikace SaaS. Ne všechny aplikace SaaS povolují atributy skupiny.

Azure AD podporuje přímé mapování atributů na atributy, poskytuje konstantní hodnoty nebo [výrazy zápisu pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md). Tato flexibilita vám umožní přesně určit, co bude naplněno v atributu cílového systému. Pomocí [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) a Graph Exploreru můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat ho zpátky do Azure AD.

Další informace najdete v tématu [přizpůsobení zřizování uživatelů Attribute-Mappings pro aplikace SaaS v Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Zvláštní požadavky na zřizování uživatelů

Pokud chcete snížit problémy po nasazení, zvažte následující:

* Zajistěte, aby atributy používané pro mapování objektů uživatelů a skupin mezi zdrojovými a cílovými aplikacemi byly odolné. Nezpůsobí, že uživatelé nebo skupiny budou nesprávně zřízeni, pokud se změní atributy (například uživatel přejde do jiné části společnosti).

* Aplikace mohou mít určitá omezení nebo požadavky, které je potřeba splnit, aby zřizování uživatelů fungovalo správně. Například časová rezerva zkrátí hodnoty pro určité atributy. Přečtěte si o [automatických kurzech zřizování uživatelů](../saas-apps/tutorial-list.md) specifických pro jednotlivé aplikace.

* Potvrďte konzistenci schémat mezi zdrojovým a cílovým systémem. Mezi běžné problémy patří atributy, jako je hlavní název uživatele nebo e-mail, se neshodují. Například hlavní název uživatele ve službě Azure AD nastavený jako *john_smith@contoso.com* a v aplikaci je *jsmith@contoso.com* . Další informace najdete v referenčních informacích o [schématu uživatelů a skupin](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Plánování testování a zabezpečení

V každé fázi nasazení se ujistěte, že testujete výsledky podle očekávání a auditujte cykly zřizování.

### <a name="plan-testing"></a>Plánování testování

Po nakonfigurování automatického zřizování uživatelů pro aplikaci spustíte testovací případy, abyste ověřili, že toto řešení splňuje požadavky vaší organizace.

| Scénáře| Očekávané výsledky |
| - | - |
| Uživatel se přidá do skupiny přiřazené k cílovému systému. | Objekt uživatele je zřízený v cílovém systému. <br>Uživatel se může přihlásit k cílovému systému a provádět požadované akce. |
| Uživatel se odebere ze skupiny, která je přiřazená k cílovému systému. | V cílovém systému se zrušilo zřízení objektu uživatele.<br>Uživatel se nemůže přihlásit k cílovému systému. |
| Informace o uživateli se ve službě Azure AD aktualizují jakýmkoli způsobem. | Aktualizované atributy uživatele se projeví v cílovém systému po přírůstkovém cyklu. |
| Uživatel je mimo rozsah. | Objekt uživatele je zakázán nebo odstraněn. <br>Poznámka: Toto chování je přepsané pro [zřizování Workday](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Plánování zabezpečení

V rámci nasazení je běžné, že je nutné provést kontrolu zabezpečení. Pokud potřebujete kontrolu zabezpečení, přečtěte si mnoho [dokumentů White Paper](https://www.microsoft.com/download/details.aspx?id=36391) k Azure AD, které poskytují přehled o identitě jako služby.

### <a name="plan-rollback"></a>Naplánování vrácení zpět

Pokud automatická implementace uživatelského zřizování nedokáže v produkčním prostředí fungovat podle potřeby, následující kroky vrácení zpět vám můžou pomoct při návratu do předchozího známého funkčního stavu:

1. Zkontrolujte [souhrnnou sestavu zřizování](../app-provisioning/check-status-user-account-provisioning.md) a [protokoly zřizování](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) a určete, k jakým chybám došlo u ovlivněných uživatelů a skupin.

1. Pomocí protokolu auditu zřizování určete poslední známý dobrý stav uživatelů a/nebo skupin, které ovlivnily. Projděte si taky zdrojové systémy (Azure AD nebo AD).

1. Spolupracujte s vlastníkem aplikace a aktualizujte uživatele nebo skupiny, které byly ovlivněny přímo v aplikaci pomocí posledních známých hodnot stavu dobrý stav.

## <a name="deploy-automatic-user-provisioning-service"></a>Nasadit automatickou službu zřizování uživatelů

Vyberte kroky, které odpovídají vašim požadavkům na řešení.

### <a name="prepare-for-the-initial-cycle"></a>Příprava na počáteční cyklus

Když se služba zřizování Azure AD poprvé spustí, počáteční cyklus proti zdrojovému systému a cílovým systémům vytvoří snímek všech uživatelských objektů pro každý cílový systém.

Při povolování automatického zřizování pro aplikaci může počáteční cyklus trvat až 20 minut až několik hodin. Doba trvání závisí na velikosti adresáře služby Azure AD a počtu uživatelů v oboru pro zřizování.

Služba zřizování ukládá stav obou systémů po počátečním cyklu a zvyšuje výkon následných přírůstkových cyklů.

### <a name="configure-automatic-user-provisioning"></a>Konfigurace automatického zřizování uživatelů

Pomocí [Azure Portal](https://portal.azure.com/) můžete spravovat Automatické zřizování uživatelských účtů a zrušení zajišťování pro aplikace, které ho podporují. Postupujte podle kroků v [návody nastavení automatického zřizování pro aplikaci?](../app-provisioning/user-provisioning.md)

Službu zřizování uživatelů Azure AD je taky možné nakonfigurovat a spravovat pomocí [rozhraní Microsoft Graph API](/graph/api/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Správa automatického zřizování uživatelů

Teď, když jste nasadili, budete muset řešení spravovat.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorovat stav operace zřizování uživatelů

Po úspěšném [počátečním cyklu](../app-provisioning/user-provisioning.md)služba zřizování Azure AD spustí přírůstkové aktualizace v intervalech specifických pro každou aplikaci po dobu, kdy dojde k jedné z následujících událostí:

* Služba se ručně zastavila a spustí se nový počáteční cyklus pomocí [Azure Portal](https://portal.azure.com/)nebo pomocí příslušného příkazu [Microsoft Graph API](/graph/api/resources/synchronization-overview) .

* Nový počáteční cyklus se spustí změnou mapování atributů nebo filtrováním oborů.

* Proces zřizování přejde do karantény z důvodu vysoké míry chyb a zůstane v karanténě po dobu více než čtyř týdnů, pokud bude automaticky zakázaný.

Pokud chcete zkontrolovat tyto události a všechny další aktivity, které provádí služba zřizování, přečtěte si [protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)služby Azure AD.

Chcete-li zjistit, jak dlouho zřizovací cykly přebírají a sledují průběh úlohy zřizování, můžete [zjistit stav zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Získání přehledů ze sestav

Azure AD může poskytovat [Další poznatky](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) k zřizování uživatelů a provoznímu stavu vaší organizace prostřednictvím protokolů auditu a sestav.

Správci by měli v sestavě Shrnutí zřizování sledovat provozní stav úlohy zřizování. Všechny aktivity prováděné službou zřizování se zaznamenávají v protokolech auditu Azure AD. Viz [kurz: vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

Doporučujeme převzít vlastnictví a využívat tyto sestavy na tempo, který splňuje požadavky vaší organizace. Azure AD uchovává většinu dat auditu po dobu 30 dnů.

### <a name="troubleshoot"></a>Řešení potíží

Pokud chcete vyřešit všechny problémy, které se můžou během zřizování zapnout, přečtěte si následující odkazy:

* [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](../app-provisioning/application-provisioning-config-problem.md)

* [Synchronizace atributu z místní služby Active Directory do Azure AD kvůli zřizování pro aplikaci](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů pro aplikaci Galerie Azure Active Directory došlo k potížím.](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Pro aplikaci Galerie Azure AD se nezřídí žádní uživatelé.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Pro aplikaci Galerie Azure AD se zřizuje nesprávná sada uživatelů.](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Užitečná dokumentace

* [Zápis výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md)

* [Přehled rozhraní API pro synchronizaci Azure AD](/graph/api/resources/synchronization-overview)

* [Přeskočit odstranění uživatelských účtů, které přesahují rozsah](skip-out-of-scope-deletions.md)

* [Agent zřizování Azure AD Connect: Historie verzí](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Zdroje a prostředky

* [Poskytnutí zpětné vazby k produktu](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Udržujte si aktuální informace o tom, co je nového ve službě Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Fórum Azure AD přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Další kroky
* [Konfigurace automatického zřizování uživatelů](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Export nebo import konfigurace zřizování pomocí rozhraní Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Zápis výrazů pro mapování atributů v Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)