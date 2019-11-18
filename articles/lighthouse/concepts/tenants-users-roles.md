---
title: Klienti, role a uživatelé ve scénářích Azure Lighthouse
description: Seznamte se s koncepty Azure Active Directory klientů, uživatelů a rolí a také jak se dají použít ve scénářích Azure Lighthouse.
ms.date: 11/05/2019
ms.topic: overview
ms.openlocfilehash: 73c5cd592f07a23edaad23796e498ea9243c5d26
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131337"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienti, role a uživatelé ve scénářích Azure Lighthouse

Před zprovozněním zákazníků pro [správu delegovaných prostředků Azure](azure-delegated-resource-management.md)je důležité pochopit, jak Azure Active Directory (Azure AD) klienti, uživatelé a role fungují, a jak se dají použít ve scénářích Azure Lighthouse.

*Tenant* je vyhrazená a důvěryhodná instance Azure AD. Každý tenant obvykle představuje jednu organizaci. Správa delegovaných prostředků v Azure umožňuje logickou projekci prostředků z jednoho tenanta do jiného tenanta. To umožňuje uživatelům v tenantovi pro správu (například k jednomu poskytovateli služeb) získat přístup k delegovaným prostředkům v tenantovi zákazníka nebo umožňuje [podnikům s více klienty centralizovat své operace správy](enterprise.md).

Aby bylo možné dosáhnout této logické projekce, *musí být předplatné* (nebo jedna nebo víc skupin prostředků v rámci předplatného) v tenantovi zákazníka připojené pro správu delegovaných prostředků Azure. Tento proces připojování se dá provést buď [prostřednictvím šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním veřejné nebo soukromé nabídky k Azure Marketplace](../how-to/publish-managed-services-offers.md).

Podle toho, jakou metodu připojování zvolíte, budete muset definovat *autorizaci*. Každá autorizace určuje uživatelský účet ve správě tenanta, který bude mít přístup k delegovaným prostředkům, a předdefinovanou roli, která nastaví oprávnění, která budou mít tito uživatelé pro tyto prostředky.

## <a name="role-support-for-azure-delegated-resource-management"></a>Podpora rolí pro správu delegovaných prostředků Azure

Při definování autorizace musí mít každý uživatelský účet přiřazenou jednu z [předdefinovaných rolí řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Vlastní role a [role správců pro klasický odběr](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) nejsou podporovány.

Všechny [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) se v současné době podporují se správou delegovaných prostředků Azure, a to s těmito výjimkami:

- Role [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) není podporována.
- Jakékoli předdefinované role s oprávněním [Dataactions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) nejsou podporovány.
- Integrovaná role [Správce přístupu uživatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) je podporovaná, ale jenom kvůli omezenému účelu [přiřazování rolí ke spravované identitě v tenantovi zákazníka](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Žádná další oprávnění, která tato role obvykle neudělí, se použijí. Pokud definujete uživatele s touto rolí, musíte zadat také předdefinované role, které tento uživatel může přiřadit ke spravovaným identitám.

## <a name="best-practices-for-defining-users-and-roles"></a>Osvědčené postupy pro definování uživatelů a rolí

Při vytváření autorizací doporučujeme následující osvědčené postupy:

- Ve většině případů budete chtít přiřadit oprávnění k skupině uživatelů nebo instančnímu objektu služby Azure AD, a ne k řadě jednotlivých uživatelských účtů. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup.
- Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy, což pomáhá snižovat pravděpodobnost neúmyslného výskytu chyb. Další informace najdete v tématu [Doporučené postupy zabezpečení](../concepts/recommended-security-practices.md).
- Zahrňte uživatele s [přiřazením registrace spravovaných služeb odstranit roli](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) , abyste mohli později v případě potřeby [Odebrat přístup k delegování](../how-to/onboard-customer.md#remove-access-to-a-delegation) . Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.
- Ujistěte se, že všichni uživatelé, kteří potřebují [Zobrazit stránku Moji zákazníci v Azure Portal](../how-to/view-manage-customers.md) , mají roli [Čtenář](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [doporučených postupech zabezpečení pro správu delegovaných prostředků Azure](recommended-security-practices.md).
- Připojte vaše zákazníky do správy delegovaných prostředků Azure, a to buď [pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb, které Azure Marketplace](../how-to/publish-managed-services-offers.md).
