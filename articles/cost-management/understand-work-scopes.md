---
title: Pochopení a práce s obory Azure Cost Management
description: Tento článek vám pomůže pochopit rozsahy správy fakturace a prostředků, které jsou dostupné v Azure, a jak používat obory Cost Management a rozhraní API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 91a71f914cff25dcdf4a85f3baafcfd9bc96b104
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218873"
---
# <a name="understand-and-work-with-scopes"></a>Vysvětlení a práce s rozsahy

Tento článek vám pomůže pochopit rozsahy správy fakturace a prostředků, které jsou dostupné v Azure, a jak používat obory Cost Management a rozhraní API.

## <a name="scopes"></a>Obory

_Obor_ je uzel v hierarchii prostředků Azure, kde uživatelé Azure AD mají přístup ke službám a spravují je. Většina prostředků Azure se vytváří a nasazuje do skupin prostředků, které jsou součástí předplatných. Microsoft taky nabízí dvě hierarchie nad předplatnými Azure, které mají specializované role pro správu fakturačních údajů:
- Fakturační údaje, například platby a faktury
- Cloudové služby, jako je například náklady a řízení zásad

Obory jsou místo, kde můžete spravovat fakturační data, mít role specifické pro platby, zobrazovat faktury a provádět Obecné řízení účtů. Role fakturace a účtu se spravují odděleně od těch, které se používají pro správu prostředků, které používají [Azure RBAC](../role-based-access-control/overview.md). K jasnému odlišení záměru samostatných oborů, včetně rozdílů v řízení přístupu, se tyto hodnoty označují jako _obory fakturace_ a _rozsahy RBAC_v uvedeném pořadí.

## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management používá obory

Cost Management funguje ve všech oborech nad prostředky, aby organizace mohly spravovat náklady na úrovni, ke kterým mají přístup, a to bez ohledu na to, jestli je to celý fakturační účet nebo jedna skupina prostředků. I když se fakturační obory liší v závislosti na vaší smlouvě Microsoft (typu předplatného), obory RBAC ne.

## <a name="azure-rbac-scopes"></a>Obory Azure RBAC

Azure podporuje tři obory pro správu prostředků. Každý obor podporuje správu přístupu a zásad správného řízení, včetně neomezeného řízení nákladů.

- [**Skupiny pro správu**](../governance/management-groups/overview.md) – hierarchické kontejnery, až osm úrovní, pro uspořádání předplatných Azure.

    Typ prostředku: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Předplatná** – primární kontejnery pro prostředky Azure.

    Typ prostředku: [Microsoft. Resources/Subscriptions](/rest/api/resources/subscriptions)

- [**Skupiny prostředků**](../azure-resource-manager/resource-group-overview.md#resource-groups) – logická seskupení souvisejících prostředků pro řešení Azure, která sdílejí stejný životní cyklus. Například prostředky, které se nasazují a odstraňují dohromady.

    Typ prostředku: [Microsoft. Resources/Subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Skupiny pro správu umožňují organizovat odběry do hierarchie. Můžete například vytvořit hierarchii logických organizací pomocí skupin pro správu. Pak týmům přiřadí odběry pro produkční a vývojové a testovací úlohy. Pak vytvořte skupiny prostředků v předplatných, abyste mohli spravovat každý dílčí systém nebo komponentu.

Vytvoření hierarchie organizace umožňuje, aby byly požadavky a zásady dodržování předpisů zahrnuty v organizaci. Každý vedoucí pak může zobrazit a analyzovat své aktuální náklady. A pak mohou vytvářet rozpočty na omezení špatných výdajů a optimalizovat náklady pomocí doporučení poradce na nejnižší úrovni.

Udělení přístupu k zobrazení nákladů a volitelně Správa konfigurace nákladů, jako jsou rozpočty a exporty, se provádí na oborech zásad správného řízení pomocí Azure RBAC. Pomocí Azure RBAC udělíte přístup k uživatelům a skupinám Azure AD k provedení předdefinované sady akcí, které jsou definované v roli v konkrétním oboru a níže. Například role přiřazená k oboru skupiny pro správu také uděluje stejným oprávněním pro vnořená předplatná a skupiny prostředků.

Cost Management podporuje následující předdefinované role pro každý z následujících oborů:

- [**Vlastník**](../role-based-access-control/built-in-roles.md#owner) – umožňuje zobrazit náklady a spravovat vše včetně konfigurace nákladů.
- [**Přispěvatel**](../role-based-access-control/built-in-roles.md#contributor) – umožňuje zobrazit náklady a spravovat vše, včetně konfigurace nákladů, ale bez řízení přístupu.
- [**Čtenář**](../role-based-access-control/built-in-roles.md#reader) – může zobrazit vše, včetně nákladových dat a konfigurace, ale nemůže provádět žádné změny.
- [**Přispěvatel cost management**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – může zobrazovat náklady, spravovat konfiguraci nákladů a zobrazovat doporučení.
- [**Cost management Reader**](../role-based-access-control/built-in-roles.md#cost-management-reader) – může zobrazit data o nákladech, konfiguraci nákladů a zobrazit doporučení.

Cost Management Přispěvatel je doporučená role nejnižší úrovně oprávnění. Umožňuje lidem vytvářet a spravovat rozpočty a exporty a efektivně monitorovat a vykazovat náklady. Přispěvatelé Cost Management mohou také vyžadovat další role pro podporu scénářů pro komplexní správu nákladů. Vezměte v úvahu následující scénáře:

- **Působit při překročení rozpočtu** – cost management přispěvatelé také potřebují přístup k vytvoření nebo správě skupin akcí, které automaticky reagují na překročení limitu. Zvažte udělení [přispěvatele monitorování](../role-based-access-control/built-in-roles.md#monitoring-contributor) skupině prostředků obsahující skupinu akcí, která se má použít při překročení prahových hodnot rozpočtu. Automatizace konkrétních akcí vyžaduje další role pro používané konkrétní služby, jako je například automatizace a Azure Functions.
- **Plánování exportu dat s náklady** – cost management přispěvatelé také potřebují přístup ke správě účtů úložiště a naplánování exportu pro kopírování dat do účtu úložiště. Zvažte možnost udělit [přispěvateli účtu úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor) do skupiny prostředků, která obsahuje účet úložiště, do kterého se exportují nákladová data.
- **Zobrazení doporučení pro úsporu nákladů** – cost management čtenářů a cost management přispěvatelé mají ve výchozím nastavení přístup k doporučením pro *zobrazení* nákladů. Přístup k tomuto doporučení na cenu ale vyžaduje přístup k jednotlivým prostředkům. Pokud chcete pracovat na doporučeních založených na ceně, zvažte udělení [role specifické pro danou službu](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) .

## <a name="enterprise-agreement-scopes"></a>Obory smlouva Enterprise

Fakturační účty smlouva Enterprise (EA), označované taky jako registrace, mají následující obory:

- [**Fakturační účet**](../billing/billing-view-all-accounts.md) – představuje registraci EA. Faktury se generují v tomto oboru. Nákupy, které nejsou založené na využití, jako je například Marketplace a rezervace, jsou k dispozici pouze v tomto oboru. Nejsou reprezentovány v odděleních nebo účtech pro registraci.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Oddělení** – volitelné seskupení registračních účtů.

    Typ prostředku: `Billing/billingAccounts/departments`

- **Účet pro zápis** – představuje jednoho vlastníka účtu. Nepodporuje udělení přístupu více lidem.

    Typ prostředku: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

I když jsou rozsahy zásad správného řízení vázané na jeden adresář, fakturační obory EA nejsou. Fakturační účet EA může mít předplatné v jakémkoli počtu adresářů Azure AD.

Fakturační obory EA podporují následující role:

- **Enterprise Admin** – může spravovat nastavení fakturačního účtu a přístup, může zobrazit všechny náklady a může spravovat konfiguraci nákladů. Například rozpočty a exporty. V rámci funkce je obor fakturace EA stejný jako [cost management role Azure RBAC přispěvatele](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Uživatel jen pro čtení** – může zobrazit nastavení fakturačního účtu, nákladová data a konfiguraci nákladů. Například rozpočty a exporty. V rámci funkce je obor fakturace EA stejný jako [role Azure RBAC čtecího zařízení cost management Reader](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce oddělení** – může spravovat nastavení oddělení, například nákladové středisko, může získat přístup, Zobrazit všechny náklady a spravovat konfiguraci nákladů. Například rozpočty a exporty.  Pro správce oddělení a pro uživatele s oprávněním jen pro čtení, aby viděli náklady, musí být nastavení fakturačního účtu pro **zobrazení da** zapnuté. Pokud jsou **poplatky za zobrazení da** zakázané, uživatelé oddělení neuvidí náklady na jakékoli úrovni, i když se jedná o účet nebo vlastníka předplatného.
- **Uživatel jen pro čtení oddělení** – může zobrazit nastavení oddělení, nákladová data a konfiguraci nákladů. Například rozpočty a exporty. Pokud jsou **poplatky za zobrazení da** zakázané, uživatelé oddělení neuvidí náklady na jakékoli úrovni, i když se jedná o účet nebo vlastníka předplatného.
- **Vlastník účtu** – může spravovat nastavení účtu pro zápis (například nákladové centrum), Zobrazit všechny náklady a spravovat konfiguraci nákladů (jako jsou rozpočty a exporty) pro účet registrace. Pro vlastníky účtů a uživatele RBAC se musí povolit nastavení fakturačního účtu pro **zobrazení Ao** , aby viděli náklady.

Uživatelé fakturačního účtu EA nemají přímý přístup k fakturám. Faktury jsou dostupné z externího multilicenčního systému.

Předplatná Azure jsou vnořená v rámci registračních účtů. Uživatelé fakturace mají přístup k nákladům na předplatná a skupiny prostředků, které jsou v příslušných oborech. Nemají přístup pro zobrazení nebo správu prostředků v Azure Portal. Uživatelé fakturace můžou zobrazit náklady tím, že v seznamu Azure Portal služby přejdou na **cost management + fakturace** . Pak mohou filtrovat náklady na konkrétní předplatná a skupiny prostředků, které potřebují k sestavování.

Uživatelé fakturace nemají přístup ke skupinám pro správu, protože nespadají explicitně do konkrétního fakturačního účtu. Přístup ke skupinám pro správu musí být udělen explicitně. Skupiny pro správu mají za následek Souhrnné náklady ze všech vnořených předplatných. Zahrnují však pouze nákupy na základě využití. Nezahrnují nákupy, jako jsou rezervace a nabídky na webu Marketplace třetích stran. Pokud si chcete tyto náklady zobrazit, použijte fakturační účet EA.

## <a name="individual-agreement-scopes"></a>Jednotlivé obory smluv

Předplatná Azure vytvořená z jednotlivých nabídek, jako jsou průběžné platby, a související typy, jako jsou bezplatné zkušební verze a nabídky pro vývoj a testování, nemají explicitní obor fakturačního účtu. U každého předplatného má například vlastníka účtu nebo správce účtu, jako je třeba vlastník účtu EA.

- [**Fakturační účet**](../billing/billing-view-all-accounts.md) – představuje jednoho vlastníka účtu pro jedno nebo více předplatných Azure. V současné době nepodporuje udělení přístupu více lidem nebo přístup k agregovaným zobrazením nákladů.

    Typ prostředku: nejde použít.

Jednotlivé účty pro správu předplatného Azure můžou z [centrum účtů Azure](https://account.azure.com/subscriptions)zobrazovat a spravovat fakturační data, jako jsou faktury a platby. Nemůžou ale zobrazovat nákladová data ani spravovat prostředky v Azure Portal. Pokud chcete udělit přístup správci účtu, použijte výše zmíněné role Cost Management.

Na rozdíl od EA můžou jednotliví správci účtů předplatného Azure vidět své faktury v Azure Portal. Mějte na paměti, že Cost Management čtenář a Cost Management role přispěvatele neposkytují přístup k fakturám. Další informace najdete v tématu [jak udělit přístup k fakturám](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Obory zákaznické smlouvy Microsoftu

Fakturační účty Microsoft Customer Agreement mají tyto rozsahy:

- **Fakturační účet** – představuje zákaznickou smlouvu o několika produktech a službách společnosti Microsoft. Fakturační účty zákaznických smluv nejsou funkčně stejné jako registrace EA. Registrace EA jsou podrobněji zarovnané na profily fakturace.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturační profil** – definuje předplatná, která jsou zahrnutá na faktuře. Fakturační profily jsou funkční ekvivalentem registrace EA, protože se jedná o obor, ve kterém se faktury generují. Podobně nákupy, které nejsou založené na využití (například Marketplace a rezervace), jsou k dispozici pouze v tomto oboru. Nejsou zahrnuté v sekcích faktury.

    Typ prostředku: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Oddíl Invoice** – představuje skupinu předplatných v rámci faktury nebo fakturačního profilu. Části faktury jsou jako oddělení – více lidí může mít přístup k části faktury.

    Typ prostředku: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Zákazník** – představuje skupinu předplatných, která je přidružená k určitému zákazníkovi, který je spojený s zákaznickou smlouvou Microsoftu od partnera. Tento obor je specifický pro CSP.

Na rozdíl od fakturačních oborů EA _jsou_ fakturační účty zákaznických smluv vázány na jeden adresář a nemohou mít odběry v rámci více adresářů služby Azure AD.

Obory fakturace zákaznických smluv se nevztahují na partnery. Role a oprávnění partnerského serveru jsou zdokumentovány při [přiřazování rolí uživatelů a oprávnění](/partner-center/permissions-overview).

Obory fakturace zákaznických smluv podporují tyto role:

- **Owner** – může spravovat nastavení fakturace a přístup, Zobrazit všechny náklady a spravovat konfiguraci nákladů. Například rozpočty a exporty. V rámci funkce je tento obor fakturace smlouvy o zákaznících stejný jako [role Azure RBAC přispěvatele cost management](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Přispěvatel** – může spravovat nastavení fakturace s výjimkou přístupu, Zobrazit všechny náklady a spravovat konfiguraci nákladů. Například rozpočty a exporty. V rámci funkce je tento obor fakturace smlouvy o zákaznících stejný jako [role Azure RBAC přispěvatele cost management](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Čtenář** – může zobrazit nastavení fakturace, nákladová data a konfiguraci nákladů. Například rozpočty a exporty. Ve funkci je tento obor fakturace smlouvy o zákaznících stejný jako [role Azure RBAC pro čtení cost management](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce fakturace** – může zobrazovat a platit faktury a může si zobrazit data a konfiguraci nákladů. Například rozpočty a exporty. Ve funkci je tento obor fakturace smlouvy o zákaznících stejný jako [role Azure RBAC pro čtení cost management](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure Subscription Creator** – může vytvářet předplatná Azure, zobrazovat náklady a spravovat konfiguraci nákladů. Například rozpočty a exporty. Ve funkci je tento obor fakturace smlouvy o zákaznících stejný jako role vlastníka účtu registrace EA.

Předplatná Azure jsou vnořená v oddílech faktury, například v rámci účtů registrace EA. Uživatelé fakturace mají přístup k nákladům na předplatná a skupiny prostředků, které jsou v příslušných oborech. Nemají ale přístup k zobrazení nebo správě prostředků v Azure Portal. Uživatelé fakturace můžou zobrazit náklady tím, že v seznamu Azure Portal služby přejdou na **cost management + fakturace** . Pak vyfiltrujte náklady na konkrétní předplatná a skupiny prostředků, které potřebují k nahlášení.

Uživatelé fakturace nemají přístup ke skupinám pro správu, protože nespadají explicitně do fakturačního účtu. Pokud jsou však pro organizaci povoleny skupiny pro správu, budou všechny náklady na předplatné zahrnuty do fakturačního účtu a do kořenové skupiny pro správu, protože jsou omezeny na jeden adresář. Skupiny pro správu zahrnují jenom nákupy, které jsou založené na využívání. Nákupy jako rezervace a nabídky na webu Marketplace třetích stran nejsou zahrnuté do skupin pro správu. Proto fakturační účet a skupina pro správu root můžou nahlásit různé součty. Chcete-li tyto náklady zobrazit, použijte fakturační účet nebo příslušný Fakturační profil.

## <a name="aws-scopes"></a>AWS rozsahy

Po dokončení integrace AWS se podívejte na téma [instalace a konfigurace integrace AWS](aws-integration-set-up-configure.md). K dispozici jsou následující obory:

- **Externí fakturační účet** – představuje zákaznickou smlouvu s dodavatelem třetí strany. To se podobá fakturačnímu účtu EA.

    Typ prostředku: `Microsoft.CostManagement/externalBillingAccounts`

- **Externí předplatné** – představuje provozní účet zákazníka s dodavatelem jiného výrobce. To je podobné jako u předplatného Azure.

    Typ prostředku: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Obory pro Cloud Solution Provider (CSP)

Pro zprostředkovatele CSP se zákazníky, kteří používají smlouvu o zákaznících Microsoftu, se podporují následující obory:

- **Fakturační účet** – představuje zákaznickou smlouvu o několika produktech a službách společnosti Microsoft. Fakturační účty zákaznických smluv nejsou funkčně stejné jako registrace EA. Registrace EA jsou podrobněji zarovnané na profily fakturace.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturační profil** – definuje předplatná, která jsou zahrnutá na faktuře. Fakturační profily jsou funkční ekvivalentem registrace EA, protože se jedná o obor, ve kterém se faktury generují. Podobně nákupy, které nejsou založené na využití (například Marketplace a rezervace), jsou k dispozici pouze v tomto oboru.

    Typ prostředku: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Zákazník** – představuje skupinu předplatných, která je přidružená k určitému zákazníkovi, který je spojený s zákaznickou smlouvou Microsoftu od partnera.

Jenom uživatelé s rolemi *globální správce* a *Agent pro správu* můžou spravovat a zobrazovat náklady na fakturační účty, profily fakturace a zákazníky přímo v tenantovi Azure partnera. Další informace o rolích partnerského centra najdete v tématu [přiřazení rolí uživatelů a oprávnění](/partner-center/permissions-overview).

Pokud mají zákazníci zákaznickou smlouvu Microsoft, Azure Cost Management podporuje pouze zákazníky partnera CSP. Pro zákazníky s podporou CSP, kteří ještě nejsou ve smlouvě o zákaznících Microsoftu, najdete informace v [partnerském centru](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Přepínání mezi obory v Cost Management

Všechna zobrazení Cost Management v Azure Portal zahrnují výběr **oboru** v levém horním rohu zobrazení. Použijte ho k rychlé změně oboru. Kliknutím na ikonu **Rozsah** otevřete okno Výběr oboru. Zobrazuje fakturační účty, kořenovou skupinu pro správu a všechna předplatná, která nejsou vnořená do kořenové skupiny pro správu. Pokud chcete vybrat rozsah, zvýrazněte ho kliknutím na pozadí a potom v dolní části klikněte na **Vybrat** . Pokud chcete přejít na vnořené obory, jako jsou skupiny prostředků v rámci předplatného, klikněte na odkaz název oboru. Pokud chcete vybrat nadřazený obor na libovolné vnořené úrovni, klikněte na **Vybrat tento &lt;obor&gt;** v horní části výběru oboru.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifikace ID prostředku pro obor

Při práci s rozhraními API Cost Management je důležité znát obor. Následující informace použijte k sestavení správného identifikátoru URI oboru pro rozhraní Cost Management API.

### <a name="billing-accounts"></a>Fakturační účty

1. Otevřete Azure Portal a pak v seznamu služeb přejděte na **cost management + fakturace** .
2. V nabídce fakturační účet vyberte **vlastnosti** .
3. Zkopírujte ID fakturačního účtu.
4. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Fakturační profily

1. Otevřete Azure Portal a pak v seznamu služeb přejděte na **cost management + fakturace** .
2. V nabídce fakturační účet vyberte **profily fakturace** .
3. Klikněte na název požadovaného fakturačního profilu.
4. V nabídce Profil fakturace vyberte **vlastnosti** .
5. Zkopírujte fakturační účet a ID fakturačního profilu.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Oddíly faktury

1. Otevřete Azure Portal a pak v seznamu služeb přejděte na **cost management + fakturace** .
2. V nabídce účet pro fakturaci vyberte **oddíly faktury** .
3. Klikněte na název oddílu požadované faktury.
4. V nabídce oddíl faktury vyberte **vlastnosti** .
5. Zkopírujte ID fakturačního účtu a části faktury.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Oddělení EA

1. Otevřete Azure Portal a pak v seznamu služeb přejděte na **cost management + fakturace** .
2. V nabídce fakturační účet vyberte **oddělení** .
3. Klikněte na název požadovaného oddělení.
4. V nabídce oddělení vyberte **vlastnosti** .
5. Zkopírujte fakturační účet a ID oddělení.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Účet pro registraci EA

1. Otevřete Azure Portal a v seznamu služeb přejděte na **cost management + fakturace** .
2. V nabídce fakturační účet vyberte **účty pro zápis** .
3. Klikněte na název požadovaného účtu pro zápis.
4. V nabídce účet pro zápis vyberte **vlastnosti** .
5. Zkopírujte účet pro fakturaci a ID účtu pro zápis.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Skupina pro správu

1. Otevřete Azure Portal a v seznamu služeb přejděte do **skupiny pro správu** .
2. Přejděte do požadované skupiny pro správu.
3. Zkopírujte ID skupiny pro správu z tabulky.
4. Váš rozsah je: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Předplatné

1. Otevřete Azure Portal a v seznamu služeb přejděte na **předplatná** .
2. Zkopírujte ID předplatného z tabulky.
3. Váš rozsah je: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Skupiny prostředků

1. Otevřete Azure Portal a v seznamu služeb přejděte do **skupin prostředků** .
2. Klikněte na název požadované skupiny prostředků.
3. V nabídce skupina prostředků vyberte **vlastnosti** .
4. Zkopírujte hodnotu pole ID prostředku.
5. Váš rozsah je: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management se aktuálně podporuje v [globálním](https://management.azure.com) a [Azure Government](https://management.usgovcloudapi.net)Azure. Další informace o Azure Government najdete v tématu [koncové body Azure Global a oficiálních rozhraní API](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nedokončili první rychlý Start pro Cost Management, přečtěte si ho v části [zahájení analýzy nákladů](quick-acm-cost-analysis.md).
