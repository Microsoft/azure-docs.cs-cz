---
title: Doporučené postupy zabezpečení
description: Při použití azure delegované správy prostředků, je důležité zvážit zabezpečení a řízení přístupu.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246904"
---
# <a name="recommended-security-practices"></a>Doporučené postupy zabezpečení

Při použití [azure delegované správy prostředků](azure-delegated-resource-management.md), je důležité zvážit zabezpečení a řízení přístupu. Uživatelé ve vašem tenantovi budou mít přímý přístup k zákaznickým předplatným a skupinám prostředků, takže budete chtít podniknout kroky k udržení zabezpečení vašeho tenanta. Budete také chtít, abyste se ujistili, že povolíte pouze přístup, který je potřebný k efektivní správě prostředků vašich zákazníků. Toto téma obsahuje doporučení, která vám v tom pomohou.

## <a name="require-azure-multi-factor-authentication"></a>Vyžadování služby Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (označované také jako dvoustupňové ověřování) pomáhá zabránit útočníkům v získání přístupu k účtu tím, že vyžaduje více kroků ověřování. Měli byste vyžadovat vícefaktorové ověřování pro všechny uživatele v tenantovi poskytovatele služeb, včetně všech uživatelů, kteří budou mít přístup k prostředkům zákazníků.

Doporučujeme, abyste požádali své zákazníky, aby implementovali Azure Multi-Factor Authentication ve svých tenantech.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Přiřazení oprávnění skupinám pomocí principu nejnižších oprávnění

Chcete-li usnadnit správu, doporučujeme použít skupiny uživatelů Azure AD pro každou roli potřebnou ke správě prostředků vašich zákazníků. To umožňuje přidat nebo odebrat jednotlivé uživatele do skupiny podle potřeby, nikoli přiřazovat oprávnění přímo tomuto uživateli.

> [!IMPORTANT]
> Chcete-li přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** **zabezpečení** a ne **Office 365**. Tato možnost je vybrána při vytvoření skupiny. Další informace najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Při vytváření struktury oprávnění se ujistěte, že jste dodržovali zásadu nejnižších oprávnění, aby uživatelé měli pouze oprávnění potřebná k dokončení své úlohy, což pomáhá snížit pravděpodobnost neúmyslných chyb.

Můžete například použít strukturu, jako je tato:

|Název skupiny  |Typ  |principalId  |Definice role  |ID definice role  |
|---------|---------|---------|---------|---------|
|Architekti     |Uživatelská skupina         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Posouzení     |Uživatelská skupina         |\<principalId\>         |Čtenář         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specialisté na virtuální podniky     |Uživatelská skupina         |\<principalId\>         |Přispěvatel virtuálního virtuálního virtuálního ms         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automatizace     |Název hlavního_hlavního serveru služby (SPN)         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po vytvoření těchto skupin můžete přiřadit uživatele podle potřeby. Přidejte pouze uživatele, kteří skutečně potřebují mít přístup. Pravidelně kontrolujte členství ve skupině a odeberte všechny uživatele, kteří již nejsou vhodní nebo nejsou pro zahrnutí.

Mějte na paměti, že pokud [přijmete zákazníky prostřednictvím nabídky veřejné spravované služby](../how-to/publish-managed-services-offers.md), bude mít jakákoli skupina (nebo uživatel nebo instanční objekt), kterou zahrnete, stejná oprávnění pro každého zákazníka, který si plán zakoupí. Chcete-li přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný soukromý plán, který je výhradní pro každého zákazníka, nebo samostatně zaokřovat zákazníky jednotlivě pomocí šablon Azure Resource Manager. Můžete například publikovat veřejný plán, který má velmi omezený přístup, a pak pracovat se zákazníkem přímo na palubě svých prostředků pro další přístup pomocí vlastní šablony prostředků Azure udělení dalšího přístupu podle potřeby.


## <a name="next-steps"></a>Další kroky

- [Nasazení Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Další informace o [prostředích správy mezi tenanty](cross-tenant-management-experience.md).
