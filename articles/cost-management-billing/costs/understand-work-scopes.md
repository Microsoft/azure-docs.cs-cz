---
title: Vysvětlení a práce s rozsahy Azure Cost Management
description: Tento článek vám pomůže pochopit rozsahy fakturace a správy prostředků, které jsou dostupné v Azure, a způsoby použití rozsahů ve službě Cost Management a rozhraních API.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 5e7d01c5383898fb14f57188cd1bee7ea64e8263
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200961"
---
# <a name="understand-and-work-with-scopes"></a>Vysvětlení a práce s rozsahy

Tento článek vám pomůže pochopit rozsahy fakturace a správy prostředků, které jsou dostupné v Azure, a způsoby použití rozsahů ve službě Cost Management a rozhraních API.

## <a name="scopes"></a>Obory

_Rozsah_ je uzel v hierarchii prostředků Azure, ve kterém mají uživatelé Azure AD přístup ke službám a spravují je. Většina prostředků Azure se vytváří a nasazuje do skupin prostředků, které jsou součástí předplatných. Microsoft taky nabízí dvě úrovně hierarchie nad předplatnými Azure, které mají specializované role pro správu fakturačních údajů:
- Fakturační údaje, například platby a faktury
- Cloudové služby, například náklady a řízení zásad

V rozsazích můžete spravovat fakturační údaje, mít zvláštní role pro platby, zobrazovat faktury a provádět všeobecnou správu účtu. Role pro fakturaci a účet se spravují nezávisle na rolích určených ke správě prostředků, které používají [Azure RBAC](../../role-based-access-control/overview.md). Aby bylo možné jasně rozlišit účel těchto samostatných rozsahů, včetně rozdílů v řízení přístupu, označují se jako _rozsahy fakturace_ a _rozsahy RBAC_.

## <a name="how-cost-management-uses-scopes"></a>Jak služba Cost Management používá rozsahy

Cost Management funguje ve všech rozsazích nad úrovní prostředků, aby organizace mohly spravovat náklady na úrovni, pro kterou mají přístup, a to bez ohledu na to, jestli jde o celý fakturační účet nebo jednu skupinu prostředků. Rozsahy fakturace se liší v závislosti na vaší smlouvě s Microsoftem (typu předplatné), ale rozsahy RBAC nikoli.

## <a name="azure-rbac-scopes"></a>Rozsahy Azure RBAC

Azure podporuje tři rozsahy pro správu prostředků. Každý rozsah podporuje správu přístupu a zásad správného řízení, mimo jiné včetně řízení nákladů.

- [**Skupiny pro správu**](../../governance/management-groups/overview.md) – hierarchické kontejnery až v osmi úrovních k uspořádání předplatných Azure.

    Typ prostředku: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Předplatná** – primární kontejnery pro prostředky Azure.

    Typ prostředku: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Skupiny prostředků**](../../azure-resource-manager/management/overview.md#resource-groups) – logická seskupení souvisejících prostředků pro řešení Azure, která sdílejí stejný životní cyklus. Jde například o prostředky, které se nasazují a odstraňují dohromady.

    Typ prostředku: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Skupiny pro správu umožňují organizovat předplatná do hierarchie. Můžete například vytvořit logickou hierarchii organizací pomocí skupin pro správu. Pak týmům přiřadíte předplatná pro produkční a vývojové/testovací úlohy. Následně vytvoříte skupiny prostředků v předplatných, abyste mohli spravovat každý dílčí systém nebo komponentu.

Vytvoření hierarchie organizace umožňuje, aby byly náklady a dodržování zásad uspořádány v rámci organizace. Každý vedoucí pak může zobrazit a analyzovat své aktuální náklady. Dále může vytvářet rozpočty na omezení nežádoucích výdajů a optimalizovat náklady na nejnižší úrovni pomocí doporučení služby Advisor.

Udělení přístupu k zobrazení nákladů a volitelně ke správě konfigurace nákladů, jako jsou rozpočty a exporty, se provádí na rozsazích zásad správného řízení pomocí Azure RBAC. Pomocí Azure RBAC udělíte přístup uživatelům a skupinám Azure AD k provedení předdefinované sady akcí definovaných v roli v konkrétním rozsahu a níže. Například role přiřazená k rozsahu skupiny pro správu také udělí stejná oprávnění pro vnořená předplatná a skupiny prostředků.

Cost Management podporuje následující předdefinované role pro každý z následujících rozsahů:

- [**Vlastník**](../../role-based-access-control/built-in-roles.md#owner) – může zobrazit náklady a spravovat vše včetně konfigurace nákladů.
- [**Přispěvatel**](../../role-based-access-control/built-in-roles.md#contributor) – může zobrazit náklady a spravovat vše včetně konfigurace nákladů, ale bez řízení přístupu.
- [**Čtenář**](../../role-based-access-control/built-in-roles.md#reader) – může zobrazit vše, včetně údajů o nákladech a konfigurace, ale nemůže provádět žádné změny.
- [**Přispěvatel Cost Management** ](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – může zobrazit náklady, spravovat konfiguraci nákladů a zobrazit doporučení.
- [**Čtenář Cost Management** ](../../role-based-access-control/built-in-roles.md#cost-management-reader) – může zobrazit data a konfiguraci nákladů a zobrazit doporučení.

Přispěvatel Cost Management je doporučená role s nejnižší úrovní oprávnění. Tato role dává uživatelům přístup k vytváření a správě rozpočtů a exportů za účelem efektivnějšího monitorování a vykazování nákladů. Přispěvatelé Cost Management mohou také vyžadovat další role pro podporu scénářů komplexní správy nákladů. Zvažte následující scénáře:

- **Opatření při překročení rozpočtů** – Přispěvatelé Cost Management také potřebují přístup k vytvoření nebo správě skupin akcí, které automaticky reagují na nadlimitní využití. Zvažte možnost udělit oprávnění [Přispěvatele monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) skupině prostředků obsahující skupinu akcí, která se má použít při překročení rozpočtových prahů. Automatizace konkrétních akcí vyžaduje další role pro konkrétní použité služby, jako je Automation a Azure Functions.
- **Plánování exportu dat nákladů** – Přispěvatelé Cost Management také potřebují přístup ke správě účtů úložiště a naplánování exportu pro kopírování dat do účtu úložiště. Zvažte možnost udělit oprávnění [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor) skupině prostředků obsahující účet úložiště, do kterého se mají exportovat data nákladů.
- **Zobrazení doporučení pro úspory** – Čtenáři Cost Management a Přispěvatelé Cost Management mají ve výchozím nastavení přístup k *zobrazení* doporučení k nákladům. Přístup k provádění změn doporučení k nákladům je ale podmíněn přístupem k jednotlivým prostředkům. Pokud chcete pracovat s doporučeními na základě nákladů, zvažte udělení [role specifické pro službu](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Rozsahy smlouvy Enterprise

Fakturační účty smlouvy Enterprise (EA), označované taky jako registrace, mají následující rozsahy:

- [**Fakturační účet**](../manage/view-all-accounts.md) – představuje registraci EA. V tomto rozsahu se generují faktury. Nákupy, které nejsou založené na využití, jako je například Marketplace a rezervace, jsou k dispozici pouze v tomto rozsahu. Nejsou zastoupeny v odděleních ani registračních účtech.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Oddělení** – volitelné seskupení registračních účtů.

    Typ prostředku: `Billing/billingAccounts/departments`

- **Registrační účet** – představuje jednoho vlastníka účtu. Nepodporuje udělení přístupu více lidem.

    Typ prostředku: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

I když jsou rozsahy zásad správného řízení vázané na jeden adresář, rozsahy fakturace EA nejsou. Fakturační účet EA může mít předplatné v jakémkoli počtu adresářů Azure AD.

Rozsahy fakturace EA podporují následující role:

- **Podnikový správce** – může spravovat nastavení fakturačního účtu a přístup, může zobrazit všechny náklady a může spravovat konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace EA stejný jako [role Přispěvatel Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Podnikový uživatel s přístupem jen pro čtení** – může zobrazit nastavení fakturačního účtu, data nákladů a konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace EA stejný jako [role Čtenář Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce oddělení** – může spravovat nastavení oddělení, například nákladové středisko, může mít přístup ke všem nákladům a zobrazovat je a spravovat konfiguraci nákladů. Jde například o rozpočty a exporty.  Aby viděli správci oddělení a uživatelé s přístupem jen pro čtení náklady, musí pro ně být povolené nastavení fakturačního účtu **Správce oddělení může zobrazit náklady**. Pokud je nastavení **Správce oddělení může zobrazit náklady** zakázané, nevidí uživatelé oddělení žádné náklady na žádné úrovni, i když jsou vlastníky účtu nebo předplatného.
- **Uživatel oddělení s přístupem jen pro čtení** – může zobrazit nastavení oddělení, data nákladů a konfiguraci nákladů. Jde například o rozpočty a exporty. Pokud je nastavení **Správce oddělení může zobrazit náklady** zakázané, nevidí uživatelé oddělení žádné náklady na žádné úrovni, i když jsou vlastníky účtu nebo předplatného.
- **Vlastník účtu** – může spravovat nastavení účtu pro registraci (například nákladové středisko), zobrazit všechny náklady a spravovat konfiguraci nákladů (jako jsou rozpočty a exporty) pro účet registrace. Aby viděli vlastníci účtů a uživatelé RBAC náklady, musí pro ně být povolené nastavení fakturačního účtu **Vlastník účtu může zobrazit náklady**.

Uživatelé fakturačního účtu EA nemají přímý přístup k fakturám. Faktury jsou dostupné z externího multilicenčního systému.

Předplatná Azure jsou vnořená v registračních účtech. Uživatelé fakturace mají přístup k datům nákladů na předplatná a skupiny prostředků, která jsou obsažena v příslušných rozsazích. Nemají přístup pro zobrazení nebo správu prostředků na webu Azure Portal. Uživatelé fakturace můžou náklady zobrazit tak, že v seznamu služeb na portálu Azure Portal přejdou na **Cost Management a fakturace**. Pak mohou filtrovat náklady na konkrétní předplatná a skupiny prostředků, u kterých potřebují vytvořit sestavy.

Uživatelé fakturace nemají přístup ke skupinám pro správu, protože nespadají explicitně do konkrétního fakturačního účtu. Přístup musí být udělen explicitně skupinám pro správu. Skupiny pro správu shrnují náklady ze všech vnořených předplatných. Zahrnují však pouze nákupy na základě využití. Nezahrnují nákupy, jako jsou rezervace a nabídky třetích stran na webu Marketplace. Pokud si chcete tyto náklady zobrazit, použijte fakturační účet EA.

## <a name="individual-agreement-scopes"></a>Rozsahy jednotlivých smluv

Předplatná Azure vytvořená z jednotlivých nabídek, jako jsou průběžné platby, a související typy, jako jsou nabídky bezplatných zkušebních verzí a nabídky pro vývoj a testování, nemají explicitní rozsah fakturačního účtu. Místo toho má každé předplatné vlastníka účtu nebo správce účtu, jako je vlastník účtu EA.

- [**Fakturační účet**](../manage/view-all-accounts.md) – představuje jednoho vlastníka účtu pro jedno nebo více předplatných Azure. V současné době nepodporuje udělení přístupu více lidem ani přístup k agregovaným zobrazením nákladů.

    Typ prostředku: Neuvedeno

Jednotliví správci účtů předplatného Azure mohou zobrazovat a spravovat fakturační data, jako jsou faktury a platby, z [Centra účtů Azure](https://account.azure.com/subscriptions). Nemůžou ale zobrazovat data nákladů ani spravovat prostředky na portálu Azure Portal. Pokud chcete udělit přístup správci účtu, použijte výše zmíněné role Cost Management.

Na rozdíl od EA můžou jednotliví správci účtů předplatného Azure vidět své faktury na portálu Azure Portal. Mějte na paměti, že role Čtenář Cost Management a Přispěvatel Cost Management neumožňují přístup k fakturám. Další informace najdete v tématu [Jak udělit přístup k fakturám](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Rozsahy smluv se zákazníky Microsoftu

Fakturační účty Smlouvy se zákazníkem Microsoftu mají tyto rozsahy:

- **Fakturační účet** – představuje zákaznickou smlouvu na více produktů a služeb Microsoftu. Fakturační účty zákaznických smluv nejsou funkčně stejné jako registrace EA. Registrace EA více odpovídají fakturačním profilům.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturační profil** – definuje předplatná, která jsou uvedená na faktuře. Fakturační profily jsou funkčním ekvivalentem registrace EA, protože se jedná o rozsah, ve kterém se generují faktury. Podobně platí, že nákupy, které nejsou založené na využití (například Marketplace a rezervace), jsou k dispozici pouze v tomto rozsahu. Nejsou zahrnuté v sekcích faktury.

    Typ prostředku: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Oddíl faktury** – představuje skupinu předplatných v rámci faktury nebo fakturačního profilu. Oddíly faktury jsou jako oddělení – k jednomu oddílu může mít přístup více lidí.

    Typ prostředku: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Zákazník** – představuje skupinu předplatných přidruženou k určitému zákazníkovi, která je partnerem zaregistrovaná ve Smlouvě se zákazníkem Microsoftu. Tento rozsah je specifický pro CSP.

Na rozdíl od rozsahů fakturace EA _jsou_ fakturační účty zákaznických smluv vázané na jeden adresář a nemohou mít předplatná v několika adresářích služby Azure AD.

Rozsahy fakturace zákaznických smluv se nevztahují na partnery. Role a oprávnění partnerů jsou popsány v tématu [Přiřazování uživatelských rolí a oprávnění](/partner-center/permissions-overview).

Rozsahy fakturace zákaznických smluv podporují tyto role:

- **Vlastník** – může spravovat nastavení fakturace a přístup, zobrazit všechny náklady a spravovat konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace zákaznické smlouvy stejný jako [role Přispěvatel Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Přispěvatel** – může spravovat nastavení fakturace kromě přístupu, zobrazit všechny náklady a spravovat konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace zákaznické smlouvy stejný jako [role Přispěvatel Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Čtenář** – může zobrazit nastavení fakturace, data nákladů a konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace zákaznické smlouvy stejný jako [role Čtenář Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce faktur** – může zobrazit a zaplatit faktury a může zobrazit data a konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je rozsah fakturace zákaznické smlouvy stejný jako [role Čtenář Cost Management v Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Tvůrce předplatného Azure** – může vytvářet předplatná Azure, zobrazovat náklady a spravovat konfiguraci nákladů. Jde například o rozpočty a exporty. Z hlediska funkce je tento rozsah fakturace zákaznické smlouvy stejný jako role vlastníka účtu registrace EA.

Předplatná Azure jsou vnořená v oddílech faktury, podobně jako například v účtech registrace EA. Uživatelé fakturace mají přístup k datům nákladů na předplatná a skupiny prostředků, která jsou obsažena v příslušných rozsazích. Nemají ale přístup pro zobrazení nebo správu prostředků na portálu Azure Portal. Uživatelé fakturace můžou náklady zobrazit tak, že v seznamu služeb na portálu Azure Portal přejdou na **Cost Management a fakturace**. Pak mohou filtrovat náklady na konkrétní předplatná a skupiny prostředků, u kterých potřebují vytvořit sestavy.

Uživatelé fakturace nemají přístup ke skupinám pro správu, protože nespadají explicitně do daného fakturačního účtu. Pokud jsou však pro organizaci povoleny skupiny pro správu, budou všechny náklady na předplatné zahrnuty do fakturačního účtu a do kořenové skupiny pro správu, protože jsou omezeny na jeden adresář. Skupiny pro správu zahrnují jenom nákupy na základě využití. Nákupy jako rezervace a nabídky třetích stran na webu Marketplace nejsou zahrnuté do skupin pro správu. Proto fakturační účet a kořenová skupina pro správu můžou nahlásit různé součty. Chcete-li tyto náklady zobrazit, použijte fakturační účet nebo příslušný fakturační profil.

## <a name="aws-scopes"></a>Rozsahy AWS

Po dokončení integrace AWS si přečtěte téma [Nastavení a konfigurace integrace AWS](aws-integration-set-up-configure.md). K dispozici jsou následující rozsahy:

- **Externí fakturační účet** – představuje zákaznickou smlouvu s dodavatelem třetí strany. Podobá se fakturačnímu účtu EA.

    Typ prostředku: `Microsoft.CostManagement/externalBillingAccounts`

- **Externí předplatné** – představuje provozní účet zákazníka s dodavatelem třetí strany. Podobá se předplatnému Azure.

    Typ prostředku: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Rozsahy CSP (Cloud Solution Provider)

Pro poskytovatele CSP se zákazníky, kteří mají Smlouvu se zákazníkem Microsoftu, se podporují následující rozsahy:

- **Fakturační účet** – představuje zákaznickou smlouvu na více produktů a služeb Microsoftu. Fakturační účty zákaznických smluv nejsou funkčně stejné jako registrace EA. Registrace EA více odpovídají fakturačním profilům.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturační profil** – definuje předplatná, která jsou uvedená na faktuře. Fakturační profily jsou funkčním ekvivalentem registrace EA, protože se jedná o rozsah, ve kterém se generují faktury. Podobně platí, že nákupy, které nejsou založené na využití (například Marketplace a rezervace), jsou k dispozici pouze v tomto rozsahu.

    Typ prostředku: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Zákazník** – představuje skupinu předplatných přidruženou k určitému zákazníkovi, která je partnerem zaregistrovaná ve Smlouvě se zákazníkem Microsoftu.

Spravovat a zobrazovat náklady na fakturační účty, fakturační profily a zákazníky přímo v partnerském tenantovi Azure můžou jenom uživatelé s oprávněním *globálního správce* a *agenta správy*. Další informace o rolích Partnerského centra najdete v tématu [Přiřazení uživatelských rolí a oprávnění](/partner-center/permissions-overview).

Pokud mají zákazníci Smlouvu se zákazníkem Microsoftu, podporuje Azure Cost Management pouze zákazníky partnerů CSP. Informace o zákaznících podporovaných poskytovateli CSP, kteří ještě nemají Smlouvu se zákazníkem Microsoftu, najdete v [Partnerském centru](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Přepínání mezi rozsahy ve službě Cost Management

Všechna zobrazení služby Cost Management na portálu Azure Portal zahrnují tlačítko **Rozsah** v levém horním rohu zobrazení. Použijte ho k rychlé změně rozsahu. Kliknutím na tlačítko **Rozsah** otevřete výběr rozsahu. Zobrazuje fakturační účty, kořenovou skupinu pro správu a všechna předplatná, která nejsou vnořená do kořenové skupiny pro správu. Pokud chcete vybrat rozsah, zvýrazněte ho kliknutím na pozadí a potom v dolní části klikněte **Vybrat**. Pokud chcete přejít na vnořené rozsahy, jako jsou skupiny prostředků v rámci předplatného, klikněte na odkaz s názvem rozsahu. Pokud chcete vybrat nadřazený rozsah na libovolné vnořené úrovni, klikněte **Vybrat tento &lt;rozsah&gt;** v horní části výběru rozsahu.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifikace ID prostředku pro rozsah

Při práci s rozhraními API Cost Management je důležité znát rozsah. Následující informace použijte k vytvoření správného identifikátoru URI rozsahu pro rozhraní API Cost Management.

### <a name="billing-accounts"></a>Fakturační účty

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Cost Management a fakturace**.
2. V nabídce fakturačního účtu vyberte **Vlastnosti**.
3. Zkopírujte ID fakturačního účtu.
4. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Fakturační profily

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Cost Management a fakturace**.
2. V nabídce fakturačního účtu vyberte **Fakturační profily**.
3. Klikněte na název požadovaného fakturačního profilu.
4. V nabídce fakturační profilu vyberte **Vlastnosti**.
5. Zkopírujte ID fakturačního účtu a fakturačního profilu.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Oddíly faktury

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Cost Management a fakturace**.
2. V nabídce fakturačního účtu vyberte **Oddíly faktury**.
3. Klikněte na název požadovaného oddílu faktury.
4. V nabídce oddílu faktury vyberte **Vlastnosti**.
5. Zkopírujte ID fakturačního účtu a oddílu faktury.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Oddělení EA

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Cost Management a fakturace**.
2. V nabídce fakturačního účtu vyberte **Oddělení**.
3. Klikněte na název požadovaného oddělení.
4. V nabídce oddělení vyberte **Vlastnosti**.
5. Zkopírujte ID fakturačního účtu a oddělení.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Registrační účet EA

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Cost Management a fakturace**.
2. V nabídce fakturačního účtu vyberte **Registrační účty**.
3. Klikněte na název požadovaného registračního účtu.
4. V nabídce registračního účtu vyberte **Vlastnosti**.
5. Zkopírujte ID fakturačního a registračního účtu.
6. Váš rozsah je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Skupina pro správu

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Skupiny pro správu**.
2. Přejděte do požadované skupiny pro správu.
3. Zkopírujte ID skupiny pro správu z tabulky.
4. Váš rozsah je: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Předplatné

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Předplatná**.
2. Zkopírujte ID předplatného z tabulky.
3. Váš rozsah je: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Skupiny prostředků

1. Otevřete web Azure Portal a v seznamu služeb přejděte na **Skupiny prostředků**.
2. Klikněte na název požadované skupiny prostředků.
3. V nabídce skupiny prostředků vyberte **Vlastnosti**.
4. Zkopírujte hodnotu pole ID prostředku.
5. Váš rozsah je: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management je aktuálně podporován ve službách [Azure Global](https://management.azure.com) a [Azure Government](https://management.usgovcloudapi.net). Další informace o službě Azure Government najdete v tématu [Koncové body rozhraní API Azure Global a Azure Government](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Další kroky

- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](quick-acm-cost-analysis.md).
