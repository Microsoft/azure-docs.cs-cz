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
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac78029ba2d1f45ef67ef0d858fdd2917bd4a97a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033337"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizace zřizování a rušení uživatelů pro aplikace SaaS pomocí Azure Active Directory

Azure Active Directory (Azure AD) umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v cloudových aplikacích ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), jako jsou Dropbox, Salesforce, ServiceNow a další. To se označuje jako automatizované zřizování uživatelů pro aplikace SaaS.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Tato funkce vám umožní:

- Automatické vytváření nových účtů v pravém systému pro nové uživatele, když se připojí k vašemu týmu nebo organizaci.
- Automaticky deaktivovat účty ve správných systémech, když lidé odejdou z týmu nebo organizace.
- Ujistěte se, že identity ve vašich aplikacích a systémech jsou aktuální na základě změn v adresáři nebo v systému lidských zdrojů.
- Pro aplikace, které je podporují, zřídí objekty nepatřící uživatelům, jako jsou skupiny.

Automatické zřizování uživatelů také zahrnuje tuto funkci:

- Schopnost párovat stávající identity mezi zdrojovým a cílovým systémem.
- Přizpůsobitelné mapování atributů, které definuje, jaká uživatelská data by měla tok ze zdrojového systému do cílového systému.
- Volitelná e-mailová upozornění pro chyby zřizování
- Vytváření sestav a protokolů aktivit, které vám pomůžou s monitorováním a řešením problémů.

## <a name="why-use-automated-provisioning"></a>Proč používat automatizované zřizování?

Mezi běžné motivace pro použití této funkce patří:

- Předcházení nákladům, neefektivitám a lidským chybám přidruženým k ručním procesům zřizování.
- Vyloučí se náklady spojené s hostováním a údržbou vlastních řešení a skriptů pro zřizování.
- Zabezpečení organizace tím, že se okamžitě odeberou identity uživatelů z aplikací služby Key SaaS, když odejdou z organizace.
- Snadné importování velkého počtu uživatelů do konkrétní SaaS aplikace nebo systému.
- Pomocí jedné sady zásad určíte, kdo je zřízený a kdo se může přihlásit k aplikaci.

## <a name="how-does-automatic-provisioning-work"></a>Jak Automatické zřizování funguje?

**Služba zřizování Azure AD** zřídí uživatele k SaaS aplikací a dalších systémů připojením k koncovým bodům rozhraní API pro správu uživatelů poskytovanými jednotlivými dodavateli aplikací. Tyto koncové body rozhraní API správy uživatelů umožňují službě Azure AD programově vytvářet, aktualizovat a odebírat uživatele. Pro vybrané aplikace může služba zřizování také vytvářet, aktualizovat a odebírat další objekty související s identitou, jako jsou například skupiny a role.

![Obrázek služby](./media/user-provisioning/provisioning0.PNG)
*zřizování Azure AD 1: Služba zřizování Azure AD*

![Obrázek pracovní postup](./media/user-provisioning/provisioning1.PNG)
*odchozího zřizování uživatelů 2: Odchozí pracovní postup zřizování uživatelů z Azure AD do oblíbených aplikací SaaS*

![Obrázek pracovního postupu](./media/user-provisioning/provisioning2.PNG)
*příchozího zřizování uživatelů 3: "Příchozí" pracovní postup zřizování uživatelů z oblíbených aplikací pro správu lidského kapitálu (HCM) pro Azure Active Directory a Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy je možné používat s automatickým zřizováním uživatelů Azure AD?

Azure AD nabízí předem integrovanou podporu pro spoustu oblíbených aplikací SaaS a systémů lidských zdrojů a obecnou podporu pro aplikace, které implementují určité části standardu SCIM 2,0.

### <a name="pre-integrated-applications"></a>Předem integrované aplikace

Seznam všech aplikací, pro které Azure AD podporuje předem integrovaný zřizovací konektor, najdete v [seznamu kurzů aplikací pro zřizování uživatelů](../saas-apps/tutorial-list.md).

Pokud chcete kontaktovat tým technické podpory Azure AD a požádat o podporu zřizování pro další aplikace, odešlete zprávu prostřednictvím [fóra Azure Active Directory Feedback](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Aby aplikace podporovala automatizované zřizování uživatelů, musí nejdřív poskytnout potřebná rozhraní API pro správu uživatelů, která umožní externím programům automatizovat vytváření, údržbu a odebírání uživatelů. Proto nejsou všechny aplikace SaaS kompatibilní s touto funkcí. Pro aplikace, které podporují rozhraní API pro správu uživatelů, může tým technické podpory Azure AD potom pro tyto aplikace vytvořit konektor pro zřizování a tato práce má prioritu podle potřeb současných a potenciálních zákazníků.

### <a name="connecting-applications-that-support-scim-20"></a>Propojení aplikací, které podporují SCIM 2,0

Informace o tom, jak obecně připojit aplikace, které implementují rozhraní API pro správu uživatelů na bázi SCIM 2,0, najdete v tématu [použití SCIM k automatickému zřizování uživatelů a skupin od Azure Active Directory k aplikacím](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Návody nastavit Automatické zřizování pro aplikaci?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pomocí portálu Azure Active Directory můžete nakonfigurovat službu zřizování Azure AD pro vybranou aplikaci.

1. Otevřete **[portál Azure Active Directory](https://aad.portal.azure.com)** .
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací.
1. Pro přidání aplikace vyberte **+ Nová aplikace** . V závislosti na scénáři přidejte jednu z následujících možností:

   - Možnost **Přidat vlastní aplikaci** podporuje SCIM integrace s vlastním vývojem.
   - Všechny aplikace v části **Přidat z Galerie** > **vybrané aplikace** podporují Automatické zřizování. Další informace najdete v [seznamu kurzů aplikací pro zřizování uživatelů](../saas-apps/tutorial-list.md) .

1. Zadejte všechny podrobnosti a vyberte **Přidat**. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.
1. Vyberte **zřizování** pro správu nastavení zřizování uživatelských účtů pro aplikaci.

   ![Zobrazuje obrazovku nastavení zřizování.](./media/user-provisioning/provisioning_settings0.PNG)

1. Vyberte možnost automatické volby pro **režim zřizování** a určete tak nastavení pro přihlašovací údaje správce, mapování, spuštění a zastavení a synchronizaci.

   - Rozbalte **přihlašovací údaje správce** a zadejte přihlašovací údaje požadované pro Azure AD pro připojení k rozhraní API pro správu uživatelů aplikace. Tato část také umožňuje povolit e-mailová oznámení v případě, že přihlašovací údaje selžou nebo pokud úloha zřizování přejde do [karantény](#quarantine).
   - Rozbalením **mapování** můžete zobrazit a upravit atributy uživatele, které se při zřizování nebo aktualizaci uživatelských účtů flowují mezi službou Azure AD a cílovou aplikací. Pokud cílová aplikace tuto aplikaci podporuje, Tato část vám umožní volitelně nakonfigurovat zřizování skupin a uživatelských účtů. Vyberte mapování v tabulce, chcete-li otevřít Editor mapování vpravo, kde můžete zobrazit a přizpůsobit atributy uživatele.

     **Filtry oborů** oznamují službě zřizování, které uživatele a skupiny ve zdrojovém systému mají být zřízené nebo zrušené pro cílový systém. V podokně **mapování atributů** vyberte **obor zdrojového objektu** , chcete-li filtrovat konkrétní hodnoty atributu. Můžete například určit, že v oboru pro zřízení mají být pouze uživatelé s atributem Department (Oddělení) s hodnotou Sales (Prodej). Další informace najdete v tématu [Používání filtrů oborů](define-conditional-rules-for-provisioning-user-accounts.md).

     Další informace najdete v tématu [Přizpůsobení mapování atributů](customize-application-attributes.md).

   - **Nastavení** řídí operaci služby zřizování pro aplikaci, včetně toho, jestli je aktuálně spuštěná. Nabídka **obor** vám umožní určit, jestli se má v oboru pro zřizování zadat jenom přiřazení uživatelé a skupiny, nebo jestli se má zřídit všichni uživatelé v adresáři Azure AD. Informace o přiřazování uživatelů a skupin najdete v tématu [Přiřazení uživatele nebo skupiny k podnikové aplikaci v Azure Active Directory](assign-user-or-group-access-portal.md).

Na obrazovce Správa aplikací vyberte **zřizovací protokoly (Preview)** a zobrazte záznamy všech operací spuštěných službou zřizování Azure AD. Další informace najdete v [průvodci zřizováním sestav](check-status-user-account-provisioning.md).

![Ukázka – obrazovka pro protokoly zřizování aplikace](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Službu zřizování uživatelů Azure AD je taky možné nakonfigurovat a spravovat pomocí [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="what-happens-during-provisioning"></a>Co se stane při zřizování?

Když je Azure AD zdrojový systém, služba zřizování používá [funkci rozdílového dotazu Graph API Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) k monitorování uživatelů a skupin. Služba zřizování spouští počáteční cyklus proti zdrojovému systému a cílovému systému, po kterém následují pravidelné přírůstkové cykly.

### <a name="initial-cycle"></a>Počáteční cyklus

Po spuštění služby zřizování se první synchronizace spustí:

1. Dotazujte všechny uživatele a skupiny ze zdrojového systému a načtěte všechny atributy definované v [mapování atributů](customize-application-attributes.md).
1. Filtrování vrácených uživatelů a skupin pomocí všech nakonfigurovaných [přiřazení](assign-user-or-group-access-portal.md) nebo [filtrů rozsahů založených na atributech](define-conditional-rules-for-provisioning-user-accounts.md).
1. Když je uživatel přiřazen nebo je v oboru pro zřizování, služba se dotazuje cílového systému pro odpovídajícího uživatele pomocí zadaných [atributů odpovídajícího atributu](customize-application-attributes.md#understanding-attribute-mapping-properties). Příklad: Pokud je název userPrincipal ve zdrojovém systému odpovídajícím atributem a mapuje se na uživatelské jméno v cílovém systému, pak služba zřizování dotazuje cílový systém pro uživatelská jména, která se shodují s hodnotami userPrincipal názvů ve zdrojovém systému.
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

### <a name="quarantine"></a>Karanténa

Pokud se většina nebo všechna volání prováděná na cílovém systému konzistentně nedaří kvůli chybě (například pro neplatná pověření správce), úloha zřizování přejde do stavu "karanténa". Tento stav je uveden v [sestavě souhrn zřizování](check-status-user-account-provisioning.md) a prostřednictvím e-mailu, pokud byly e-mailová oznámení konfigurována v Azure Portal.

Když je v karanténě, frekvence přírůstkových cyklů se postupně zkracuje na jeden den.

Úloha zřizování bude odebrána z karantény poté, co dojde k odstranění všech problematických chyb a spustí se další cyklus synchronizace. Pokud úloha zřizování zůstane v karanténě po dobu delší než čtyři týdny, úloha zřizování je zakázaná.

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat zřizování uživatelů?

Výkon závisí na tom, jestli vaše úloha zřizování spouští počáteční cyklus zřizování nebo přírůstkový cyklus. Podrobnosti o tom, jak dlouho trvá zřizování a jak monitorovat stav služby zřizování, najdete v tématu [Zkontrolujte stav zřizování uživatelů](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Jak poznám, jestli jsou uživatelé správně zřízené?

Všechny operace spouštěné službou zřizování uživatelů se zaznamenávají v [protokolech zřizování Azure AD (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). To zahrnuje všechny operace čtení a zápisu provedené ve zdrojovém a cílovém systému a uživatelská data, která byla během každé operace načtena nebo zapsána.

Informace o tom, jak číst protokoly zřizování v Azure Portal, najdete v [průvodci zřizováním sestav](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Návody řešení potíží s zřizováním uživatelů?

Pokyny k řešení potíží s automatickým zřizováním uživatelů najdete v tématu problémy s [konfigurací a zřizováním uživatelů do aplikace](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Jaké jsou osvědčené postupy při zavádění automatického zřizování uživatelů?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Příklad podrobného plánu nasazení pro odchozí zřizování uživatelů do aplikace najdete v [Průvodci nasazením identity pro zřizování uživatelů](https://aka.ms/userprovisioningdeploymentplan).

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
