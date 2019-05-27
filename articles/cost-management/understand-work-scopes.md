---
title: Pochopení a práci s obory Azure Cost Management | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit fakturaci a prostředků obory správy k dispozici v Azure a použití rozsahů Cost Management a rozhraní API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 717c0f110ebbeee53e2c9b9207350385288d57c3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991382"
---
# <a name="understand-and-work-with-scopes"></a>Vysvětlení a práce s rozsahy

Tento článek vám pomůže pochopit fakturaci a prostředků obory správy k dispozici v Azure a použití rozsahů Cost Management a rozhraní API.

## <a name="scopes"></a>Obory

A _oboru_ je uzel v hierarchii prostředků Azure, kde Azure AD uživatelům přístup k a spravovat služby. Většinu služeb Azure prostředky jsou vytvořené a nasazené do skupin prostředků, které jsou součástí předplatných. Microsoft taky nabízí dvěma hierarchiemi výše předplatného Azure, které specializovaný rolí ke správě fakturačních dat:
- Fakturační údaje, jako je například plateb a faktury
- Cloudové služby, jako je například náklady a zásady zásad správného řízení

Obory jsou kde spravovat fakturačních dat, mají konkrétní role k platbám, zobrazení faktur a provádět správu obecné účtu. Fakturace a účtu role se spravují odděleně od těch, které používá ke správě prostředků, kterou použít [Azure RBAC](../role-based-access-control/overview.md). Jasně rozlišovat záměr samostatné obory, včetně rozdílů řízení přístupu, tyto jsou označovány jako _fakturace obory_ a _RBAC obory_v uvedeném pořadí.

## <a name="how-cost-management-uses-scopes"></a>Jak Cost Management používá obory

Cost Management funguje na všech rozsahy nad prostředky, které umožňuje organizacím spravovat náklady na úrovni, na které mají přístup, zda je celý fakturační účet nebo jedné skupiny prostředků. I když fakturační obory se liší v závislosti na vaší smlouvě Microsoft (typ předplatného), obory RBAC to nejde.

## <a name="azure-rbac-scopes"></a>Obory Azure RBAC

Azure podporuje tři obory pro řízení zdrojů. Každý obor podporuje správu přístupu a zásady správného řízení, včetně, ale mimo jiné, Správa nákladů.

- [**Skupiny pro správu** ](../governance/management-groups/index.md) – hierarchické kontejnery, až osm úrovní uspořádání předplatných Azure.

    Typ prostředku: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Předplatná** – primární kontejnery pro prostředky Azure.

    Typ prostředku: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Skupiny prostředků** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -logického seskupení souvisejících prostředků pro řešení Azure, které sdílejí stejný životní cyklus. Například prostředky, které se nasazují a odstraní společně.

    Typ prostředku: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Skupiny pro správu umožňují organizaci předplatných do hierarchie. Například může vytvořit hierarchii logické uspořádání využitím skupin pro správu. Dejte týmy předplatná pro produkční a vývojové a testovací úlohy. A pak vytvořte skupiny prostředků v rámci předplatných pro správu jednotlivých subsystémů nebo komponenty.

Vytváření organizační hierarchie umožňuje náklady a dodržování zásad souhrnné používání. Každý vedoucí pak můžete zobrazit a analyzovat jejich aktuálními náklady. A pak můžete vytvořit rozpočty omezení chybný útraty vzory a optimalizovat náklady pomocí doporučení Advisoru na nejnižší úrovni.

Udělení přístupu k zobrazení nákladů a volitelně spravovat náklady na konfiguraci, například exporty, rozpočet a libovolný typ se provádí na zásady správného řízení rozsahy pomocí Azure RBAC. Použijte Azure RBAC pro udělení uživatelů Azure AD a skupiny přístup k provedení předdefinovanou sadu akcí, které jsou definovány v roli v konkrétním oboru a níže. Pro instanci role přiřazená k oboru skupiny pro správu uděluje také stejná oprávnění, aby vnořené předplatné a skupiny prostředků.

Služba Cost Management podporuje následující předdefinované role pro každou z následujících oborů:

- [**Vlastník** ](../role-based-access-control/built-in-roles.md#owner) – můžete zobrazit náklady a spravovat všechno včetně konfigurace náklady.
- [**Přispěvatel** ](../role-based-access-control/built-in-roles.md#contributor) – můžete zobrazit náklady a spravovat všechno včetně konfigurace náklady, s výjimkou řízení přístupu.
- [**Čtečka** ](../role-based-access-control/built-in-roles.md#reader) – vše, včetně konfigurace a nákladech můžete zobrazit, ale nemůže provádět žádné změny.
- [**Přispěvatel správy nákladů** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – můžete zobrazit náklady, spravovat náklady na konfiguraci a zobrazení doporučení.
- [**Cost Management čtečky** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – můžou zobrazovat data nákladů, náklady na konfiguraci a zobrazit doporučení.

Přispěvatel Cost Management je doporučená role nejnižších oprávnění. Umožňuje uživatelům přístup k vytváření a Správa rozpočtů a exportuje do více efektivně, monitorování a vytváření sestav na náklady. Náklady na správu přispěvatelé může také vyžadovat další role, které podporují scénáře správy náklady na začátku do konce. Zvažte následující scénáře:

- **Fungují při překročení rozpočty** – náklady na správu přispěvatelé také potřebují přístup k vytvořit a spravovat skupiny akcí automaticky reagovat na Nadlimitní využití. Zvažte možnost udělení [Přispěvatel monitorování](../role-based-access-control/built-in-roles.md#monitoring-contributor) do skupiny prostředků, která obsahuje skupiny akcí při překročení prahové hodnoty. Automatizace konkrétní akce vyžaduje další role pro určité služby, použití, například služby Automation a Azure Functions.
- **Plánování nákladů export dat** – náklady na správu přispěvatelé také potřebovat přístup ke správě účtů úložiště naplánování export můžete kopírovat data do účtu úložiště. Zvažte možnost udělení [Přispěvatel účtů úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor) do skupiny prostředků, který obsahuje úložiště se exportuje účtu, kde informací o nákladech.
- **Zobrazení doporučení nákladově úspornému** – náklady na správu čtenáři a přispěvatelé Cost Management mají přístup k *zobrazení* nákladů doporučení ve výchozím nastavení. Přístup tak, aby fungoval na doporučení k nákladům však vyžaduje přístup k jednotlivým prostředkům. Zvažte možnost udělení [specifickou pro službu role](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) Pokud budete chtít pracovat s náklady na základě doporučení.

## <a name="enterprise-agreement-scopes"></a>Obory smlouvy Enterprise

Fakturační účty Enterprise Agreement (EA), také nazývané registrace, mají následující obory:

- [**Fakturační účet** ](../billing/billing-view-all-accounts.md) – představuje smlouvu EA enrollment. Faktury se generují v tomto oboru. Nákup, které nejsou podle použití, jako je například Marketplace a rezervace, jsou k dispozici pouze v tomto oboru. Se nenachází v oddělení nebo registračních účtů.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Oddělení** – volitelné seskupení registračních účtů.

    Typ prostředku: `Billing/billingAccounts/departments`

- **Registrace účtu** – představuje jeden účet vlastníka. Nepodporuje udělování přístupu k více lidí.

    Typ prostředku: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

I když obory zásad správného řízení je vázána na jednom adresáři, nejsou fakturační obory EA. Fakturační účet EA může mít předplatné do libovolného počtu adresářů služby Azure AD.

Fakturační obory EA podporují následující role:

- **Podnikový správce** – můžete spravovat fakturační účet nastavení a přístup, můžete zobrazit náklady a můžete spravovat náklady na konfiguraci. Například s nižším rozpočtem a vyexportuje. Ve funkci, EA fakturace oboru je stejný jako [náklady na správu Přispěvatel Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Organizace uživatele jen pro čtení** – můžete zobrazit nastavení fakturační účet, datům služby cost a náklady na konfiguraci. Například s nižším rozpočtem a vyexportuje. Ve funkci, EA fakturace oboru je stejné jako [náklady na správu čtečky Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce oddělení** – můžete spravovat nastavení oddělení, jako je například nákladové středisko, můžete přístup, zobrazit náklady a spravovat náklady na konfiguraci. Například s nižším rozpočtem a vyexportuje.  **DA zobrazit náklady** fakturace nastavení účtu musí být povoleno pro oddělení správci a uživatelé jen pro čtení zobrazit náklady. Pokud **DA zobrazit náklady** je zakázaná, oddělení uživatelé nevidí náklady na libovolné úrovni i v případě, že figurují jako vlastníci účtu nebo předplatného.
- **Oddělení uživatele jen pro čtení** – můžete zobrazit nastavení oddělení, nákladech a náklady na konfiguraci. Například s nižším rozpočtem a vyexportuje. Pokud **DA zobrazit náklady** je zakázaná, oddělení uživatelé nevidí náklady na libovolné úrovni i v případě, že figurují jako vlastníci účtu nebo předplatného.
- **Vlastník účtu** – můžete spravovat nastavení registrace účtu (například nákladové středisko), zobrazit náklady a spravovat náklady na konfiguraci (například rozpočet a libovolný typ exporty) pro účet pro zápis. **AO zobrazit náklady** fakturace nastavení účtu musí být povolena pro RBAC uživatelů a vlastníků účtu chcete zobrazit náklady.

EA fakturační účet uživatelé nemají přímý přístup k faktury. Faktury jsou k dispozici ze svazku externí licencování systému.

Předplatná Azure jsou vnořené pod registračních účtů. Fakturace uživatelé mají přístup k datům služby cost pro předplatné a skupiny prostředků, které jsou v jejich příslušných oborech. Nemají přístup k zobrazit nebo spravovat prostředky na webu Azure Portal. Fakturace uživatelé mohou zobrazit náklady tak, že přejdete do **Správa nákladů a fakturace** v portálu Azure seznam služeb. Potom se můžete filtrovat náklady na konkrétní předplatné a skupiny prostředků, které potřebují k vytvoření sestavy.

Fakturace uživatelé nemají přístup ke skupinám pro správu, protože není explicitně spadají pod konkrétní fakturační účet. Musí být udělen přístup ke skupinám pro správu explicitně. Souhrnné náklady ze všech vnořených předplatná skupin pro správu. Nicméně pouze patří mezi ně nákupy na základě využití. Patří mezi ně nemusíte nákupy například rezervace a nabídky na webu Marketplace třetích stran. Chcete-li zobrazit tyto náklady, použijte fakturačnímu účtu EA.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Obory jednotlivých smlouvy (průběžné platby)

Předplatná s průběžnými platbami (PAYG), včetně souvisejících typů, jako jsou bezplatné nebo zkušební a nabízí pro vývoj/testování, nemáte výslovného oboru fakturační účet. Místo toho každé předplatné má vlastník účtu nebo správce účtu, jako je vlastník účtu EA.

- [**Fakturační účet** ](../billing/billing-view-all-accounts.md) – představuje jeden účet vlastníka pro jeden nebo více předplatných Azure. Je v současné době nepodporuje udělování přístupu k více lidí nebo přístup k zobrazení souhrnné náklady.

    Typ prostředku: Neuvedeno

Správci účtu předplatného průběžné platby můžete zobrazit a spravovat fakturačních dat, jako je například faktury a platby, z [centra účtů Azure](https://account.azure.com/subscriptions). Nemůže však zobrazit datům služby cost nebo spravovat prostředky na webu Azure Portal. K udělení přístupu k účtu správce, používání rolí správy nákladů, již bylo zmíněno dříve.

Na rozdíl od EA můžete správci účtu předplatného PAYG zobrazit jejich faktury na webu Azure Portal. Uvědomte si, že náklady na správu Čtenář a Přispěvatel náklady na správu role neposkytují přístup k faktury. Další informace najdete v tématu [jak udělit přístup k průběžné platby faktury](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Obory smlouvy zákazníka

Smlouvy zákazníka se společností Microsoft fakturační účty mají následující obory:

- **Fakturační účet** – představuje zákaznické smlouvy pro více produktů a služeb Microsoftu. Zákaznické smlouvy fakturační účty nejsou funkčně stejný jako Zaregistrovaných smluv Enterprise. Zaregistrovaných smluv Enterprise jsou lépe zarovnané s fakturační profily.

    Typ prostředku: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturační profil** – definuje odběry, které jsou součástí faktury. Fakturace profily jsou ekvivalentní smlouvu EA enrollment, protože to je obor, který faktury se generují. Podobně nákup, které nejsou podle využití (například Marketplace a rezervace) jsou k dispozici pouze v tomto oboru. Nejsou zahrnuty v části faktury.

    Typ prostředku: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Část faktury** – představuje skupinu předplatná v faktura nebo fakturační profil. Části faktury jsou podobné oddělení – několik lidí může mít přístup k oddílu faktury.

    Typ prostředku: `Microsoft.Billing/billingAccounts/invoiceSections`

Na rozdíl od EA fakturace obory, smlouvy zákazníka fakturační účty _jsou_ vázán na jednom adresáři a nemůže mít odběry napříč více adresářů Azure AD.

Fakturační obory smlouvy zákazníka podporují následující role:

- **Vlastník** – můžete spravovat nastavení fakturace a přístup, zobrazit náklady a náklady na konfiguraci můžete spravovat. Například s nižším rozpočtem a vyexportuje. Ve funkci této smlouvy zákazníka fakturace oboru je stejné jako [náklady na správu Přispěvatel Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Přispěvatel** – můžete spravovat nastavení fakturace kromě přístupu, zobrazit náklady a náklady na konfiguraci můžete spravovat. Například s nižším rozpočtem a vyexportuje. Ve funkci této smlouvy zákazníka fakturace oboru je stejné jako [náklady na správu Přispěvatel Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Čtečka** – můžete zobrazit fakturační nastavení, nákladech a konfigurace náklady. Například s nižším rozpočtem a vyexportuje. Ve funkci této smlouvy zákazníka fakturace oboru je stejné jako [náklady na správu čtečky Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Správce faktur** – můžete zobrazit a zaplatit faktury a můžete zobrazit náklady dat a konfigurace. Například s nižším rozpočtem a vyexportuje. Ve funkci této smlouvy zákazníka fakturace oboru je stejné jako [náklady na správu čtečky Azure RBAC role](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Předplatné Azure creator** – můžete vytvářet předplatná Azure, zobrazit náklady a náklady na konfiguraci můžete spravovat. Například s nižším rozpočtem a vyexportuje. Tento obor fakturační smlouvy zákazníka ve funkci, je stejný jako role vlastníka účtu EA registrace.

Předplatná Azure jsou vnořené v části faktury, třeba jak se v rámci smlouvy EA registračních účtů. Fakturace uživatelé mají přístup k datům služby cost pro předplatná a skupiny prostředků, které jsou v jejich příslušných oborech. Však nemají přístup k zobrazit nebo spravovat prostředky na webu Azure Portal. Fakturace uživatelé mohou zobrazit náklady tak, že přejdete do **Správa nákladů a fakturace** v portálu Azure seznam služeb. Vyfiltrujte náklady na konkrétní předplatné a skupiny prostředků, které potřebují k vytvoření sestavy.

Fakturace uživatelé nemají přístup ke skupinám pro správu, protože není explicitně spadají pod fakturační účet. Ale pokud skupin pro správu jsou povolené pro organizaci, všechny náklady na předplatné se souhrnné fakturační účet a skupinu root management protože obě jsou omezeny na jeden adresář. Skupiny pro správu obsahovat pouze nákupy, které jsou založeny na využití. Nákup rezervace a nabídky na webu Marketplace třetích stran nejsou součástí skupiny pro správu. Fakturační účet a kořenové skupině pro správu tedy může hlásit různé součty. Chcete-li zobrazit tyto náklady, použijte fakturační účet nebo příslušné fakturační profil.

## <a name="cloud-solution-provider-csp-scopes"></a>Obory cloud Solution Provider (CSP)

Partnery cloud Solution Provider (CSP) nejsou podporovány ve službě Cost Management ještě dnes. Místo toho můžete použít [partnerského centra](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Přepínání mezi obory ve službě Cost Management

Zahrnout všechna zobrazení Cost Management na webu Azure Portal **oboru** obranné v levé horní části zobrazení. Můžete rychle změnit obor. Klikněte na tlačítko **oboru** obranné otevřete výběr oboru. Zobrazuje fakturační účty, skupiny pro správu kořenového a pokud je jakékoliv předplatné, které nejsou vnořené pod skupinu root management. Chcete-li vybrat rozsah, klikněte na pozadí požadovanou položku zvýrazněte a pak klikněte na tlačítko **vyberte** v dolní části. Přejít do vnořené obory, jako jsou skupiny prostředků v předplatném, klikněte na odkaz název oboru. Pokud chcete vybrat nadřazený obor na libovolné úrovni vnořené, klikněte na tlačítko **tuto možnost vyberte, &lt;oboru&gt;**  v horní části Výběr oboru.

## <a name="identify-the-resource-id-for-a-scope"></a>Určení ID prostředku pro obor

Při práci s rozhraními API služby Cost Management je vědět, že obor je velmi důležité. Tyto informace použijte k vytvoření oboru správný identifikátor URI pro rozhraní API pro správu nákladů.

### <a name="billing-accounts"></a>Fakturační účty

1. Otevřete na webu Azure portal a potom přejděte k **Správa nákladů a fakturace** v seznamu služeb.
2. Vyberte **vlastnosti** v nabídce fakturační účet.
3. Zkopírujte ID fakturačního účtu.
4. Váš obor je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Fakturační profily

1. Otevřete na webu Azure portal a potom přejděte k **Správa nákladů a fakturace** v seznamu služeb.
2. Vyberte **fakturace profily** v nabídce fakturační účet.
3. Klikněte na název požadovaného fakturační profil.
4. Vyberte **vlastnosti** v nabídce fakturační profil.
5. Zkopírujte fakturační účet a fakturace ID profilu.
6. Váš obor je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Oddíly faktury

1. Otevřete na webu Azure portal a potom přejděte k **Správa nákladů a fakturace** v seznamu služeb.
2. Vyberte **fakturovat oddíly** v nabídce fakturační účet.
3. Klikněte na název požadovaného faktury oddílu.
4. Vyberte **vlastnosti** v nabídce části faktury.
5. Zkopírujte fakturační účet a fakturace části identifikátory.
6. Váš obor je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Oddělení EA

1. Otevřete na webu Azure portal a potom přejděte k **Správa nákladů a fakturace** v seznamu služeb.
2. Vyberte **oddělení** v nabídce fakturační účet.
3. Klikněte na název požadovaného oddělení.
4. Vyberte **vlastnosti** v nabídce oddělení.
5. Zkopírujte ID fakturační účet a oddělení.
6. Váš obor je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Registrace účtu EA

1. Otevřete na webu Azure portal a přejděte do **Správa nákladů a fakturace** v seznamu služeb.
2. Vyberte **registračních účtů** v nabídce fakturační účet.
3. Klikněte na název požadovaného registrace účtu.
4. Vyberte **vlastnosti** v nabídce účtu pro zápis.
5. Zkopírujte fakturační účet a ID registrace účtu.
6. Váš obor je: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Skupina pro správu

1. Otevřete na webu Azure portal a přejděte do **skupin pro správu** v seznamu služeb.
2. Přejděte ke skupině pro správu.
3. Zkopírujte ID skupiny pro správu z tabulky.
4. Váš obor je: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Předplatné

1. Otevřete na webu Azure portal a přejděte do **předplatná** v seznamu služeb.
2. Zkopírujte ID předplatného z tabulky.
3. Váš obor je: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Skupiny prostředků

1. Otevřete na webu Azure portal a přejděte do **skupiny prostředků** v seznamu služeb.
2. Klikněte na název skupiny pro požadovaný prostředek.
3. Vyberte **vlastnosti** v nabídce skupiny prostředků.
4. Zkopírujte hodnotu pole ID prostředku.
5. Váš obor je: `"/subscriptions/{id}/resourceGroups/{name}"`

Služba Cost Management je aktuálně podporován ve [Azure globální](https://management.azure.com) a [Azure Government](https://management.usgovcloudapi.net). Další informace o službě Azure Government najdete v tématu [koncových bodů rozhraní API Government a Azure globální](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
