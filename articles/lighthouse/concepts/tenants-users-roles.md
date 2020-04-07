---
title: Klienti, role a uživatelé ve scénářích Azure Lighthouse
description: Seznamte se s koncepty klientů, uživatelů a rolí služby Azure Active Directory a také s tím, jak je lze použít ve scénářích Azure Lighthouse.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 32d9214e4d0d204db39b6e6decab4665e9b55069
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754080"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienti, role a uživatelé ve scénářích Azure Lighthouse

Před registracemi zákazníků pro [správu delegovaných prostředků Azure](azure-delegated-resource-management.md)je důležité pochopit, jak fungují tenantové, uživatelé a role Azure Active Directory (Azure AD) a jak je lze použít ve scénářích Azure Lighthouse.

*Tenant* je vyhrazená a důvěryhodná instance Azure AD. Obvykle každý klient představuje jednu organizaci. Správa delegovaných prostředků Azure umožňuje logickou projekci prostředků z jednoho klienta do jiného klienta. To umožňuje uživatelům ve správě klienta (například jeden patřící k poskytovateli služeb) přístup k delegovaným prostředkům v tenantovi zákazníka nebo umožňuje [podnikům s více tenanty centralizovat své operace správy](enterprise.md).

K dosažení této logické projekce musí být pro správu delegovaných prostředků Azure *na palubě* předplatné (nebo jedna nebo více skupin prostředků v rámci předplatného) v tenantovi zákazníka. Tento proces registrace lze provést buď [prostřednictvím šablon Azure Resource Manager,](../how-to/onboard-customer.md) nebo [publikováním veřejné nebo soukromé nabídky na Azure Marketplace](../how-to/publish-managed-services-offers.md).

Bez ohledu na metodu onboardingu, kterou zvolíte, budete muset definovat *autorizace*. Každá autorizace určuje uživatelský účet v klientském klientovi pro správu, který bude mít přístup k delegovaným prostředkům, a předdefinovanou roli, která nastaví oprávnění, která budou mít pro tyto prostředky oprávnění.

## <a name="role-support-for-azure-delegated-resource-management"></a>Podpora rolí pro správu delegovaných prostředků Azure

Při definování autorizace musí být každému uživatelskému účtu přiřazena jedna z [předdefinovaných rolí řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/built-in-roles.md) Vlastní role a [klasické role správce předplatného](../../role-based-access-control/classic-administrators.md) nejsou podporovány.

Všechny [předdefinované role](../../role-based-access-control/built-in-roles.md) jsou aktuálně podporované se správou delegovaných prostředků Azure, s následujícími výjimkami:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) není podporována.
- Žádné předdefinované role s [oprávněním DataActions](../../role-based-access-control/role-definitions.md#dataactions) nejsou podporovány.
- Předdefinovaná role [Správce přístupu uživatelů](../../role-based-access-control/built-in-roles.md#user-access-administrator) je podporována, ale pouze pro omezený účel [přiřazování rolí spravované identitě v klientovi zákazníka](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Žádná další oprávnění, která tato role obvykle uděluje, nebudou platit. Pokud definujete uživatele s touto rolí, musíte také zadat předdefinované role, které tento uživatel může přiřadit ke spravovaným identitám.

> [!NOTE]
> Jakmile se do Azure přidá příslušná nová integrovaná role, dá se přiřadit při [připojení zákazníka pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md). Může dojít ke zpoždění, než bude nově přidaná role k dispozici na portálu partnerů cloudu při [publikování nabídky spravované služby](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Doporučené postupy pro definování uživatelů a rolí

Při vytváření autorizací doporučujeme následující doporučené postupy:

- Ve většině případů budete chtít přiřadit oprávnění skupině uživatelů Azure AD nebo instančnímu objektu služby Azure AD, nikoli řadě jednotlivých uživatelských účtů. To umožňuje přidat nebo odebrat přístup pro jednotlivé uživatele, aniž by bylo nutné aktualizovat a znovu publikovat plán při změně požadavků na přístup.
- Ujistěte se, že dodržovat zásadu nejnižší oprávnění tak, aby uživatelé měli pouze oprávnění potřebná k dokončení své úlohy, což pomáhá snížit pravděpodobnost neúmyslných chyb. Další informace naleznete v [tématu Doporučené postupy zabezpečení](../concepts/recommended-security-practices.md).
- Zahrňte uživatele s [rolí odstranění přiřazení registrace spravovaných služeb,](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) abyste v případě potřeby mohli [později odebrat přístup k delegování.](../how-to/onboard-customer.md#remove-access-to-a-delegation) Pokud tato role není přiřazena, delegované prostředky mohou být odebrány pouze uživatelem v tenantovi zákazníka.
- Ujistěte se, že každý uživatel, který potřebuje [zobrazit stránku Moji zákazníci na webu Azure Portal,](../how-to/view-manage-customers.md) má roli [čtečky](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtečce).

> [!IMPORTANT]
> Chcete-li přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** **zabezpečení** a ne **Office 365**. Tato možnost je vybrána při vytvoření skupiny. Další informace najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [doporučených postupech zabezpečení pro správu delegovaných prostředků Azure](recommended-security-practices.md).
- Založte své zákazníky do Azure delegované správy prostředků, a to buď [pomocí šablon Azure Resource Manager,](../how-to/onboard-customer.md) nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md).
