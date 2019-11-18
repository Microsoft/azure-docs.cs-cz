---
title: Doporučené postupy zabezpečení
description: Při použití správy delegovaných prostředků Azure je důležité vzít v úvahu zabezpečení a řízení přístupu.
ms.date: 07/11/2019
ms.topic: overview
ms.openlocfilehash: e0f0a9d4cdd56ff1bca9b9faf493d3e0d68e558c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132467"
---
# <a name="recommended-security-practices"></a>Doporučené postupy zabezpečení

Při použití správy delegovaných prostředků Azure je důležité vzít v úvahu zabezpečení a řízení přístupu. Uživatelé ve vašem tenantovi budou mít přímý přístup k předplatným zákazníků a skupinám prostředků, takže budete chtít udělat kroky, abyste zachovali zabezpečení tenanta. Budete taky chtít mít jistotu, že povolíte jenom přístup, který potřebujete k efektivní správě prostředků vašich zákazníků. V tomto tématu najdete doporučení, která vám pomůžou.

## <a name="require-azure-multi-factor-authentication"></a>Vyžadovat Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (označované také jako dvoustupňové ověřování) pomáhá zabránit útočníkům v získání přístupu k účtu tím, že vyžaduje více kroků ověřování. Měli byste vyžadovat Multi-Factor Authentication pro všechny uživatele v tenantovi poskytovatele služeb, včetně všech uživatelů, kteří budou mít přístup k zákaznickým prostředkům.

Doporučujeme, abyste zákazníkům požádali o implementaci Azure Multi-Factor Authentication ve svých klientech.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Přiřazení oprávnění skupinám pomocí principu nejnižší úrovně oprávnění

Pro zjednodušení správy doporučujeme používat skupiny uživatelů Azure AD pro každou roli potřebnou ke správě prostředků vašich zákazníků. To vám umožní přidávat nebo odebírat jednotlivé uživatele do skupiny podle potřeby, ale nepřiřazovat oprávnění přímo tomuto uživateli.

Při vytváření struktury oprávnění nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy, což pomáhá snižovat riziko neúmyslných chyb.

Například můžete chtít použít strukturu, jako je tato:

|Název skupiny  |Typ  |principalId  |Definice role  |ID definice role  |
|---------|---------|---------|---------|---------|
|Architekty     |Uživatelská skupina         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Posouzení     |Uživatelská skupina         |\<principalId\>         |Čtenář         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specialisty virtuálních počítačů     |Uživatelská skupina         |\<principalId\>         |Přispěvatel virtuálních počítačů         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Hlavní název služby (SPN)         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po vytvoření těchto skupin můžete uživatele podle potřeby přiřadit. Přidejte pouze uživatele, kteří skutečně potřebují mít přístup. Nezapomeňte pravidelně kontrolovat členství ve skupinách a odebrat všechny uživatele, kteří už nejsou vhodné nebo které je potřeba zahrnout.

Mějte na paměti, že když zakoupíte [zákazníky prostřednictvím veřejné nabídky spravované služby](../how-to/publish-managed-services-offers.md), budou mít všechny skupiny (nebo uživatelé nebo instanční objekty), které zahrnete, stejné oprávnění pro každého zákazníka, který plán plánuje. Pokud chcete přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný soukromý plán, který je exkluzivní pro každého zákazníka, nebo připojit zákazníky samostatně pomocí Azure Resource Manager šablon. Můžete například publikovat veřejný plán, který má velmi omezený přístup, a pak spolupracovat s zákazníkem a připojit své prostředky k dodatečnému přístupu pomocí přizpůsobené šablony prostředků Azure, podle potřeby udělit další přístup.


## <a name="next-steps"></a>Další kroky

- [Nasazení Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
