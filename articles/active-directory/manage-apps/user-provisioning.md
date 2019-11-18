---
title: Automatizované zřizování uživatelů aplikací SaaS ve službě Azure AD | Microsoft Docs
description: Představte si, jak můžete pomocí Azure AD automaticky zřídit, zrušit zřízení a průběžně aktualizovat uživatelské účty napříč různými aplikacemi SaaS třetích stran.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144536"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizace zřizování uživatelů a jejich rušení s aplikacemi pomocí Azure Active Directory

V Azure Active Directory (Azure AD) pojem **zřizování aplikací** označuje automatické vytváření identit uživatelů a rolí v cloudových aplikacích ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje Automatické zřizování také údržbu a odebírání identit uživatelů při změně stavu nebo rolí. Mezi běžné scénáře patří zřizování uživatelů Azure AD v aplikacích, jako jsou [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)a další.

![Diagram přehledu zřizování](media/user-provisioning/provisioning-overview.png)

Tato funkce vám umožní:

- **Automatizace zřizování**: automatické vytváření nových účtů v pravém systému pro nové lidi při připojení k vašemu týmu nebo organizaci.
- **Automatické zrušení zřízení:** Automaticky deaktivovat účty ve správných systémech, když lidé odejdou z týmu nebo organizace.
- **Synchronizovat data mezi systémy:** Zajistěte, aby se identity ve vašich aplikacích a systémech aktualizovaly na základě změn v adresáři nebo v systému lidských zdrojů.
- **Zřídit skupiny:** Zřizování skupin pro aplikace, které je podporují.
- **Řídit přístup:** Monitorování a audit, který byl zřízen do vašich aplikací.
- **Bezproblémové nasazení ve scénářích hnědého pole:** Porovnává stávající identity mezi systémy a umožní snadnou integraci, i když uživatelé již existují v cílovém systému.
- **Použít bohatou úpravu:** Využijte přizpůsobitelných mapování atributů, která definují, jaká uživatelská data by se měla přesměrovat ze zdrojového systému do cílového systému.
- **Získat výstrahy pro kritické události:** Služba zřizování poskytuje výstrahy pro kritické události a umožňuje Log Analytics integraci, kde můžete definovat vlastní výstrahy pro potřeby vašich obchodních potřeb.

## <a name="benefits-of-automatic-provisioning"></a>Výhody automatického zřizování

Vzhledem k tomu, že počet aplikací používaných v moderních organizacích stále roste, správci IT mají na dosahu řízení přístupu ve velkém měřítku. Standardy, jako je například SAML (Security Assert Markup Language) nebo Open ID Connect (OIDC), umožňují správcům rychle nastavit jednotné přihlašování (SSO), ale přístup také vyžaduje, aby se do aplikace zřídili uživatelé. U mnoha správců zajišťuje zřizování ruční vytváření všech uživatelských účtů nebo nahrávání souborů CSV každý týden, ale tyto procesy jsou časově náročné, nákladné a náchylné k chybám. Byla přijata řešení, jako je JIT (just-in-time) SAML pro automatizaci zřizování, ale podniky také potřebují řešení pro zrušení zřízení uživatelů při opuštění organizace nebo už nevyžadují přístup k určitým aplikacím na základě změny role.

Mezi běžné motivace pro použití automatického zřizování patří:

- Maximalizace efektivity a přesnosti zřizování procesů.
- Úspora nákladů spojených s hostováním a udržováním vlastních řešení a skriptů pro zřizování s vlastním vývojem
- Zabezpečení organizace tím, že se okamžitě odeberou identity uživatelů z aplikací služby Key SaaS, když odejdou z organizace.
- Snadné importování velkého počtu uživatelů do konkrétní SaaS aplikace nebo systému.
- Pomocí jedné sady zásad určíte, kdo je zřízený a kdo se může přihlásit k aplikaci.

Zřizování uživatelů Azure AD může tyto výzvy vyřešit. Pokud chcete získat další informace o tom, jak zákazníci používají zřizování uživatelů Azure AD, můžete si přečíst [případovou studii Asos](https://aka.ms/asoscasestudy). Následující video poskytuje přehled zřizování uživatelů v Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy je možné používat s automatickým zřizováním uživatelů Azure AD?

Azure AD nabízí předem integrovanou podporu pro spoustu oblíbených aplikací SaaS a systémů lidských zdrojů a obecnou podporu pro aplikace, které implementují určité části [standardu SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Předem integrované aplikace (Galerie aplikací SaaS)** . Všechny aplikace, pro které Azure AD podporuje předem integrovaný zřizovací konektor, najdete v [seznamu kurzů aplikací pro zřizování uživatelů](../saas-apps/tutorial-list.md). Předem integrované aplikace uvedené v galerii obecně používají rozhraní API pro správu uživatelů na bázi SCIM 2,0 ke zřízení. 

   ![Logo Salesforce](media/user-provisioning/gallery-app-logos.png)

   Pokud chcete požádat o novou aplikaci pro zřizování, můžete [požádat o integraci aplikace do naší galerie aplikací](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). Pro požadavek na zřízení uživatele vyžaduje aplikace, aby měl koncový bod kompatibilní s SCIM. Požádejte prosím, aby dodavatel aplikace následoval za standardu SCIM, abychom mohli rychle připojit aplikaci k naší platformě.

* **Aplikace, které podporují SCIM 2,0**. Informace o tom, jak obecně připojit aplikace, které implementují rozhraní API pro správu uživatelů na bázi SCIM 2,0, najdete v tématu [použití SCIM k automatickému zřizování uživatelů a skupin od Azure Active Directory k aplikacím](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Co je SCIM?

Aby se usnadnilo automatizace zřizování a rušení, aplikace zveřejňují proprietární rozhraní API pro uživatele a skupiny. Kdokoli, kdo se snaží spravovat uživatele ve více než jedné aplikaci, vám ale oznámí, že se každá aplikace pokusí provést stejné jednoduché akce, třeba při vytváření nebo aktualizaci uživatelů, přidávání uživatelů do skupin nebo rušení zřizování uživatelů. Nicméně všechny tyto jednoduché akce jsou implementovány pouze trochu odlišně, s použitím různých cest koncových bodů, různých metod pro zadání informací o uživateli a jiného schématu, které představují jednotlivé prvky informací.

Specifikace SCIM poskytuje společné uživatelské schéma, které uživatelům umožňuje přesunout se do aplikací, mimo jiné a kolem nich. SCIM se stává jako de facto standard pro zřizování a při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

Podrobné pokyny k používání SCIM k automatizaci zřizování a rušení zřizování uživatelů a skupin do aplikace najdete v tématu [SCIM zřizování uživatelů pomocí Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="how-does-automatic-provisioning-work"></a>Jak Automatické zřizování funguje?

**Služba zřizování Azure AD** zřídí uživatele k SaaS aplikací a dalších systémů připojením k koncovým bodům rozhraní API pro správu uživatelů poskytovanými jednotlivými dodavateli aplikací. Tyto koncové body rozhraní API správy uživatelů umožňují službě Azure AD programově vytvářet, aktualizovat a odebírat uživatele. Pro vybrané aplikace může služba zřizování také vytvářet, aktualizovat a odebírat další objekty související s identitou, jako jsou například skupiny a role.

![služby Azure AD Provisioning](./media/user-provisioning/provisioning0.PNG)
*Obrázek 1: služba zřizování Azure AD*

![pracovní postup odchozího zřizování uživatelů](./media/user-provisioning/provisioning1.PNG)
*Obrázek 2: "odchozí" pracovní postup zřizování uživatelů z Azure AD do oblíbených aplikací SaaS*

pracovní postup ![příchozího zřizování uživatelů](./media/user-provisioning/provisioning2.PNG)
*Obrázek 3: "příchozí" pracovní postup zřizování uživatelů z oblíbených aplikací HCM pro správu lidského kapitálu do Azure Active Directory a Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Návody nastavit Automatické zřizování pro aplikaci?

V případě předem integrovaných aplikací uvedených v galerii jsou podrobné pokyny k dispozici pro nastavení automatického zřizování. Podívejte se na [Seznam kurzů pro integrované aplikace Galerie](https://docs.microsoft.com/azure/active-directory/saas-apps/). Následující video ukazuje, jak nastavit Automatické zřizování uživatelů pro SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pro jiné aplikace, které podporují SCIM 2,0, postupujte podle kroků v článku [SCIM zřizování uživatelů pomocí Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="what-happens-during-provisioning"></a>Co se stane při zřizování?

Když je Azure AD zdrojový systém, služba zřizování používá [funkci rozdílového dotazu Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) k monitorování uživatelů a skupin. Služba zřizování spouští počáteční cyklus proti zdrojovému systému a cílovému systému, po kterém následují pravidelné přírůstkové cykly.

### <a name="initial-cycle"></a>Počáteční cyklus

Po spuštění služby zřizování se první synchronizace spustí:

1. Dotazujte všechny uživatele a skupiny ze zdrojového systému a načtěte všechny atributy definované v [mapování atributů](customize-application-attributes.md).
1. Filtrování vrácených uživatelů a skupin pomocí všech nakonfigurovaných [přiřazení](assign-user-or-group-access-portal.md) nebo [filtrů rozsahů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).
1. Když je uživatel přiřazen nebo je v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [atributů odpovídajícího atributu](customize-application-attributes.md#understanding-attribute-mapping-properties). Příklad: Pokud je název userPrincipal ve zdrojovém systému odpovídajícím atributem a mapuje se na uživatelské jméno v cílovém systému, pak služba zřizování dotazuje cílový systém pro uživatelská jména, která se shodují s hodnotami userPrincipal názvu ve zdrojovém systému.
1. Pokud se v cílovém systému nenajde shodný uživatel, bude vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá ke spuštění všech budoucích operací s tímto uživatelem.
1. Pokud se najde shodný uživatel, aktualizuje se pomocí atributů poskytovaných zdrojovým systémem. Po porovnání uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá ke spuštění všech budoucích operací s tímto uživatelem.
1. Pokud mapování atributů obsahuje "referenční" atributy, služba provede další aktualizace v cílovém systému pro vytvoření a propojení odkazovaných objektů. Uživatel může například mít v cílovém systému atribut "nadřízený", který je propojený s jiným uživatelem vytvořeným v cílovém systému.
1. Na konci počátečního cyklu můžete zachovat vodoznak, který poskytuje výchozí bod pro pozdější přírůstkové cykly.

Některé aplikace, jako je ServiceNow, G Suite a box, podporují nejen zřizování uživatelů, ale také zřizování skupin a jejich členů. V takových případech, pokud je zřizování skupin povoleno v [mapování](customize-application-attributes.md), služba zřizování synchronizuje uživatele a skupiny a potom později synchronizuje členství ve skupině.

### <a name="incremental-cycles"></a>Přírůstkové cykly

Po úvodním cyklu budou všechny ostatní cykly:

1. Dotaz na zdrojový systém pro všechny uživatele a skupiny, které byly aktualizovány od posledního uložení vodoznaku.
1. Filtrování vrácených uživatelů a skupin pomocí všech nakonfigurovaných [přiřazení](assign-user-or-group-access-portal.md) nebo [filtrů rozsahů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).
1. Když je uživatel přiřazen nebo je v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [atributů odpovídajícího atributu](customize-application-attributes.md#understanding-attribute-mapping-properties).
1. Pokud se v cílovém systému nenajde shodný uživatel, bude vytvořen pomocí atributů vrácených ze zdrojového systému. Po vytvoření uživatelského účtu služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, který se používá ke spuštění všech budoucích operací s tímto uživatelem.
1. Pokud se najde shodný uživatel, aktualizuje se pomocí atributů poskytovaných zdrojovým systémem. Pokud se jedná o nově přiřazený účet, služba zřizování detekuje a ukládá do mezipaměti ID cílového systému pro nového uživatele, které se používá ke spuštění všech budoucích operací s tímto uživatelem.
1. Pokud mapování atributů obsahuje "referenční" atributy, služba provede další aktualizace v cílovém systému pro vytvoření a propojení odkazovaných objektů. Uživatel může například mít v cílovém systému atribut "nadřízený", který je propojený s jiným uživatelem vytvořeným v cílovém systému.
1. Pokud je uživatel, který byl dříve v oboru pro zřizování, odebrán z oboru (včetně nepřiřazeného), služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.
1. Pokud je uživatel, který byl dříve v oboru pro zřizování, zakázán nebo odstraněn ze zdrojového systému, služba zakáže uživatele v cílovém systému prostřednictvím aktualizace.
1. Pokud je uživatel, který byl dříve v oboru pro zřizování, ve zdrojovém systému pevným smazán, služba odstraní uživatele v cílovém systému. Ve službě Azure AD se uživatelé po odinstalaci nezávazně odstraní po uplynutí 30 dnů.
1. Zachovejte nový vodoznak na konci přírůstkového cyklu, který poskytuje výchozí bod pro pozdější přírůstkové cykly.

> [!NOTE]
> Volitelně můžete zakázat operace **vytvořit**, **aktualizovat**nebo **Odstranit** pomocí **akcí cílového objektu** v sekci [mapování](customize-application-attributes.md) . Logika zakázání uživatele během aktualizace je také řízena prostřednictvím mapování atributů z pole, jako je například "accountEnabled".

Služba zřizování pokračuje v průběžném spouštění přírůstkových cyklů zpět, v intervalech definovaných v [kurzu specifických pro jednotlivé aplikace](../saas-apps/tutorial-list.md), dokud nedojde k jedné z následujících událostí:

- Služba se ručně zastavila pomocí Azure Portal nebo pomocí příslušného příkazu Graph API 
- Nový počáteční cyklus se aktivuje pomocí možnosti **Vymazat stav a restartovat** v Azure Portal nebo pomocí příslušného příkazu Graph API. Tato akce vymaže libovolný uložený vodoznak a způsobí, že všechny zdrojové objekty budou znovu vyhodnoceny.
- Spustí se nový počáteční cyklus, protože se změní mapování atributů nebo filtry oborů. Tato akce také vymaže všechny uložené meze a způsobí, že všechny zdrojové objekty budou znovu vyhodnoceny.
- Proces zřizování přejde do karantény (viz níže) z důvodu vysoké míry chyb a zůstane v karanténě po dobu více než čtyř týdnů. V takovém případě se služba automaticky zakáže.

### <a name="errors-and-retries"></a>Chyby a opakování

Pokud se jednotliví uživatelé v cílovém systému nedají přidat, aktualizovat ani odstranit, protože v cílovém systému dojde k chybě, operace se zopakuje v dalším synchronizačním cyklu. Pokud uživatel i nadále selže, začnou se opakované pokusy vyskytnout s omezenou frekvencí a postupně škálovat zpátky na jeden pokus za den. Aby bylo možné tuto chybu vyřešit, musí správci v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) zjistit hlavní příčinu a provést příslušnou akci. Běžné chyby můžou zahrnovat:

- Uživatelé nemají ve zdrojovém systému naplněný atribut, který je požadován v cílovém systému.
- Uživatelé mají ve zdrojovém systému hodnotu atributu, pro kterou je v cílovém systému jedinečné omezení a v jiném záznamu uživatele se nachází stejná hodnota.

Tyto chyby lze vyřešit úpravou hodnot atributů pro ovlivněného uživatele ve zdrojovém systému nebo úpravou mapování atributů na nezpůsobující konflikty.

### <a name="quarantine"></a>Umístit

Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nedaří kvůli chybě (například pro neplatná pověření správce), úloha zřizování přejde do stavu "karanténa". Tento stav je uveden v [sestavě souhrn zřizování](check-status-user-account-provisioning.md) a prostřednictvím e-mailu, pokud byly e-mailová oznámení konfigurována v Azure Portal.

Když je v karanténě, frekvence přírůstkových cyklů se postupně zkracuje na jeden den.

Úloha zřizování bude odebrána z karantény poté, co dojde k odstranění všech problematických chyb a spustí se další cyklus synchronizace. Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná. Tady najdete další informace [o stavu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)karantény.

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat zřizování uživatelů?

Výkon závisí na tom, jestli vaše úloha zřizování spouští počáteční cyklus zřizování nebo přírůstkový cyklus. Podrobnosti o tom, jak dlouho trvá zřizování a jak monitorovat stav služby zřizování, najdete v tématu [Zkontrolujte stav zřizování uživatelů](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Jak poznám, jestli jsou uživatelé správně zřízené?

Všechny operace spouštěné službou zřizování uživatelů se zaznamenávají v [protokolech zřizování Azure AD (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). To zahrnuje všechny operace čtení a zápisu provedené ve zdrojovém a cílovém systému a uživatelská data, která byla během každé operace načtena nebo zapsána.

Informace o tom, jak číst protokoly zřizování v Azure Portal, najdete v [průvodci zřizováním sestav](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Návody řešení potíží s zřizováním uživatelů?

Pokyny k řešení potíží s automatickým zřizováním uživatelů najdete v tématu problémy s [konfigurací a zřizováním uživatelů do aplikace](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Jaké jsou osvědčené postupy při zavádění automatického zřizování uživatelů?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Příklad podrobného plánu nasazení pro odchozí zřizování uživatelů do aplikace najdete v [Průvodci nasazením identity pro zřizování uživatelů](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Funguje Automatické zřizování uživatelů pro aplikace SaaS s uživateli B2B ve službě Azure AD?

Ano, je možné použít službu Azure AD pro zřizování uživatelů ke zřízení uživatelů B2B (nebo hostů) ve službě Azure AD k SaaSí aplikací.

Aby se však uživatelé B2B přihlásili k aplikaci SaaS pomocí služby Azure AD, musí mít aplikace SaaS možnost jednotného přihlašování založené na SAML nakonfigurovanou určitým způsobem. Další informace o tom, jak nakonfigurovat aplikace SaaS tak, aby podporovaly přihlášení uživatelů B2B, najdete v tématu [Konfigurace aplikací SaaS pro spolupráci B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Funguje Automatické zřizování uživatelů pro aplikace SaaS s dynamickými skupinami ve službě Azure AD?

Ano. Když je nakonfigurovaná možnost synchronizovat jenom přiřazené uživatele a skupiny, služba zřizování uživatelů Azure AD může zřídit nebo zrušit zřízení uživatelů v aplikaci SaaS podle toho, jestli jsou členy [dynamické skupiny](../users-groups-roles/groups-create-rule.md). Dynamické skupiny také fungují s možností synchronizovat všechny uživatele a skupiny.

Použití dynamických skupin ale může mít vliv na celkový výkon komplexního zřizování uživatelů od Azure AD až po SaaS aplikace. Při použití dynamických skupin mějte na paměti tato upozornění a doporučení:

- Způsob zřízení nebo zrušení zřízení uživatele v dynamické skupině v aplikaci SaaS závisí na tom, jak rychlá dynamická skupina dokáže vyhodnotit změny členství ve skupinách. Informace o tom, jak kontrolovat stav zpracování dynamické skupiny, najdete v tématu [Zkontrolujte stav zpracování pravidla členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Při použití dynamických skupin musí být pravidla pečlivě zvážená zřizováním uživatelů a zrušením zřizování, protože při ztrátě členství dojde k události zrušení zřízení.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Funguje Automatické zřizování uživatelů pro aplikace SaaS s vnořenými skupinami ve službě Azure AD?

Ne. Pokud je nakonfigurovaná možnost synchronizovat jenom přiřazené uživatele a skupiny, služba zřizování uživatelů Azure AD nebude moct číst ani zřizovat uživatele ve vnořených skupinách. Dá se jenom číst a zřizovat jenom uživatelé, kteří jsou bezprostředními členy explicitně přiřazené skupiny.

Jedná se o omezení "přiřazení na základě skupin na aplikace", což má vliv na jednotné přihlašování a popisuje téma [použití skupiny pro správu přístupu k SaaS aplikacím](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Alternativním řešením je, že byste měli explicitně přiřadit (nebo jinak určit [obor](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) skupiny obsahující uživatele, kteří se musí zřídit.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Je zřizování mezi Azure AD a cílovou aplikací pomocí šifrovaného kanálu?

Ano. Pro cíl serveru používáme šifrování SSL HTTPS.

## <a name="related-articles"></a>Související články

- [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)
- [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
- [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
- [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
- [Použití SCIM k povolení automatického zřizování uživatelů a skupin ze služby Azure AD do aplikací](use-scim-to-provision-users-and-groups.md)
- [Přehled rozhraní API pro synchronizaci Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
