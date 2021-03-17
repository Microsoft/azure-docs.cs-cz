---
title: Doporučené postupy zabezpečení
description: Při používání Azure Lighthouse je důležité vzít v úvahu zabezpečení a řízení přístupu.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 3aa50833b547882506bfad125992bb1c2f4e85bc
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419299"
---
# <a name="recommended-security-practices"></a>Doporučené postupy zabezpečení

Při používání [Azure Lighthouse](../overview.md)je důležité vzít v úvahu zabezpečení a řízení přístupu. Uživatelé ve vašem tenantovi budou mít přímý přístup k předplatným zákazníků a skupinám prostředků, takže budete chtít udělat kroky, abyste zachovali zabezpečení tenanta. Budete taky chtít mít jistotu, že povolíte jenom přístup, který potřebujete k efektivní správě prostředků vašich zákazníků. V tomto tématu najdete doporučení, která vám pomůžou.

> [!TIP]
> Tato doporučení se vztahují také na podniky, které [spravují víc tenantů](enterprise.md) pomocí Azure Lighthouse.

## <a name="require-azure-ad-multi-factor-authentication"></a>Vyžadování vícefaktorového ověřování Azure AD

[Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (označované také jako dvoustupňové ověřování) pomáhají zabránit útočníkům v získání přístupu k účtu tím, že vyžadují více kroků ověřování. Měli byste vyžadovat Multi-Factor Authentication pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům.

Doporučujeme, abyste zákazníkům požádali o implementaci Azure AD Multi-Factor Authentication ve svých klientech.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Přiřazení oprávnění skupinám pomocí principu nejnižší úrovně oprávnění

Aby bylo možné správu usnadnit, použijte skupiny Azure Active Directory (Azure AD) pro každou roli potřebnou ke správě prostředků vašich zákazníků. To vám umožní přidávat nebo odebírat jednotlivé uživatele do skupiny podle potřeby a nepřiřazovat jim oprávnění přímo pro každého uživatele.

> [!IMPORTANT]
> Aby bylo možné přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** nastavený na **zabezpečení**. Tato možnost je vybrána při vytváření skupiny. Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Při vytváření struktury oprávnění nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k dokončení své úlohy, což pomáhá snižovat riziko neúmyslných chyb.

Například můžete chtít použít strukturu, jako je tato:

|Název skupiny  |Typ  |principalId  |Definice role  |ID definice role  |
|---------|---------|---------|---------|---------|
|Architekty     |Skupina uživatelů         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Posouzení     |Skupina uživatelů         |\<principalId\>         |Čtenář         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specialisty virtuálních počítačů     |Skupina uživatelů         |\<principalId\>         |Přispěvatel virtuálních počítačů         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Hlavní název služby (SPN)         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po vytvoření těchto skupin můžete uživatele podle potřeby přiřadit. Přidejte pouze uživatele, kteří skutečně potřebují mít přístup. Nezapomeňte pravidelně kontrolovat členství ve skupinách a odebrat všechny uživatele, kteří už nejsou vhodné nebo které je potřeba zahrnout.

Mějte na paměti, že když zakoupíte [zákazníky prostřednictvím veřejné nabídky spravované služby](../how-to/publish-managed-services-offers.md), budou mít všechny skupiny (nebo uživatelé nebo instanční objekty), které zahrnete, stejné oprávnění pro každého zákazníka, který plán plánuje. Pokud chcete přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný soukromý plán, který je exkluzivní pro každého zákazníka, nebo připojit zákazníky samostatně pomocí Azure Resource Manager šablon. Můžete například publikovat veřejný plán, který má velmi omezený přístup, a pak spolupracovat s zákazníkem a připojit své prostředky k dodatečnému přístupu pomocí přizpůsobené šablony prostředků Azure, podle potřeby udělit další přístup.

## <a name="next-steps"></a>Další kroky

- Projděte si [základní informace o zabezpečení](../security-baseline.md) , abyste zjistili, jak se na Azure Lighthouse vztahují doprovodné materiály k Azure Security test.
- [Nasazení služby Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
