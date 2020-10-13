---
title: Doporučené postupy zabezpečení
description: Při používání Azure Lighthouse je důležité vzít v úvahu zabezpečení a řízení přístupu.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 097611837e3bba2d1a44ec521862e03cc467d707
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399376"
---
# <a name="recommended-security-practices"></a>Doporučené postupy zabezpečení

Při používání [Azure Lighthouse](../overview.md)je důležité vzít v úvahu zabezpečení a řízení přístupu. Uživatelé ve vašem tenantovi budou mít přímý přístup k předplatným zákazníků a skupinám prostředků, takže budete chtít udělat kroky, abyste zachovali zabezpečení tenanta. Budete taky chtít mít jistotu, že povolíte jenom přístup, který potřebujete k efektivní správě prostředků vašich zákazníků. V tomto tématu najdete doporučení, která vám pomůžou.

> [!TIP]
> Tato doporučení se vztahují také na podniky, které [spravují víc tenantů](enterprise.md) pomocí Azure Lighthouse.

## <a name="require-azure-multi-factor-authentication"></a>Vyžadování služby Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (označované také jako dvoustupňové ověřování) pomáhá zabránit útočníkům v získání přístupu k účtu tím, že vyžaduje více kroků ověřování. Měli byste vyžadovat Multi-Factor Authentication pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům.

Doporučujeme, abyste zákazníkům požádali o implementaci Azure Multi-Factor Authentication ve svých klientech.

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

- [Nasazení Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Přečtěte si o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
