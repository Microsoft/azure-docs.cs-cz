---
title: Klienti, role a uživatelé ve scénářích Azure Lighthouse
description: Seznamte se s koncepty Azure Active Directory klientů, uživatelů a rolí a také jak se dají použít ve scénářích Azure Lighthouse.
ms.date: 07/03/2020
ms.topic: conceptual
ms.openlocfilehash: c409b45d18504d071ffed3185de20ec5e1147bc2
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399240"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienti, role a uživatelé ve scénářích Azure Lighthouse

Před zprovozněním zákazníků pro [Azure Lighthouse](../overview.md)je důležité pochopit, jak klienti a role Azure Active Directory (Azure AD) fungují a jak se dají použít ve scénářích Azure Lighthouse.

*Tenant* je vyhrazená a důvěryhodná instance Azure AD. Každý tenant obvykle představuje jednu organizaci. [Správa delegovaných prostředků v Azure](azure-delegated-resource-management.md) umožňuje logickou projekci prostředků z jednoho tenanta do jiného tenanta. To umožňuje uživatelům v tenantovi pro správu (například k jednomu poskytovateli služeb) získat přístup k delegovaným prostředkům v tenantovi zákazníka nebo umožňuje [podnikům s více klienty centralizovat své operace správy](enterprise.md).

Aby bylo možné dosáhnout této logické projekce, *musí být předplatné* (nebo jedna nebo víc skupin prostředků v rámci předplatného) v tenantovi zákazníka připojené do Azure Lighthouse. Tento proces připojování se dá provést buď [prostřednictvím šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním veřejné nebo soukromé nabídky k Azure Marketplace](../how-to/publish-managed-services-offers.md).

Podle toho, jakou metodu připojování zvolíte, budete muset definovat *autorizaci*. Každá autorizace určuje uživatelský účet ve správě tenanta, který bude mít přístup k delegovaným prostředkům, a předdefinovanou roli, která nastaví oprávnění, která budou mít tito uživatelé pro tyto prostředky.

## <a name="role-support-for-azure-lighthouse"></a>Podpora rolí pro Azure Lighthouse

Při definování autorizace musí mít každý uživatelský účet přiřazenou jednu z [předdefinovaných rolí řízení přístupu na základě role (RBAC)](../../role-based-access-control/built-in-roles.md). Vlastní role a [role správců pro klasický odběr](../../role-based-access-control/classic-administrators.md) nejsou podporovány.

Všechny [předdefinované role](../../role-based-access-control/built-in-roles.md) jsou v současné době podporované pomocí Azure Lighthouse, s následujícími výjimkami:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) není podporována.
- Jakékoli předdefinované role s oprávněním [Dataactions](../../role-based-access-control/role-definitions.md#dataactions) nejsou podporovány.
- Integrovaná role [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) je podporovaná, ale jenom kvůli omezenému účelu [přiřazování rolí ke spravované identitě v tenantovi zákazníka](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Žádná další oprávnění, která tato role obvykle neudělí, se použijí. Pokud definujete uživatele s touto rolí, musíte zadat také předdefinované role, které tento uživatel může přiřadit ke spravovaným identitám.

> [!NOTE]
> Po přidání nové příslušné předdefinované role do Azure ji můžete přiřadit při [připojování zákazníka pomocí Azure Resource Manager šablon](../how-to/onboard-customer.md). Aby byla nově přidaná role k dispozici v partnerském centru při [publikování nabídky spravované služby](../how-to/publish-managed-services-offers.md), může dojít ke zpoždění.

## <a name="best-practices-for-defining-users-and-roles"></a>Osvědčené postupy pro definování uživatelů a rolí

Při vytváření autorizací doporučujeme následující osvědčené postupy:

- Ve většině případů budete chtít přiřadit oprávnění k skupině uživatelů nebo instančnímu objektu služby Azure AD, a ne k řadě jednotlivých uživatelských účtů. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup.
- Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy, což pomáhá snižovat pravděpodobnost neúmyslného výskytu chyb. Další informace najdete v tématu [Doporučené postupy zabezpečení](../concepts/recommended-security-practices.md).
- Zahrňte uživatele s [přiřazením registrace spravovaných služeb odstranit roli](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , abyste mohli později v případě potřeby [Odebrat přístup k delegování](../how-to/remove-delegation.md) . Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.
- Ujistěte se, že všichni uživatelé, kteří potřebují [Zobrazit stránku Moji zákazníci v Azure Portal](../how-to/view-manage-customers.md) , mají roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).

> [!IMPORTANT]
> Aby bylo možné přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** nastavený na **zabezpečení**. Tato možnost je vybrána při vytváření skupiny. Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [doporučených postupech zabezpečení pro Azure Lighthouse](recommended-security-practices.md).
- Připojte vaše zákazníky do Azure Lighthouse, a to buď [pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb, které Azure Marketplace](../how-to/publish-managed-services-offers.md).
