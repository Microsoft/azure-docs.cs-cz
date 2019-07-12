---
title: Zabezpečení doporučené postupy pro Azure maják
description: Při používání Azure delegovat správu prostředků, je potřeba zvážit zabezpečení a řízení přístupu.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 843b965e6ea74a7c11dc11459ff5d30ddbe5c987
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809866"
---
# <a name="recommended-security-practices"></a>Postupy doporučené zabezpečení

Při používání Azure delegovat správu prostředků, je potřeba zvážit zabezpečení a řízení přístupu. Uživatelé ve vašem tenantovi bude mít přímý přístup k zákaznických předplatných a skupin prostředků, takže je vhodné provést kroky k zabezpečení vašeho tenanta. Budete také chtít Ujistěte se, že povolíte přístup, který je nezbytný pro efektivní správu prostředků vašich zákazníků. Toto téma obsahuje doporučení, která pomůžou vám to.

## <a name="require-azure-multi-factor-authentication"></a>Vyžadovat ověřování Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (označované také jako dvoustupňové ověřování) pomáhá zabránit útočníkům získat přístup k účtu tak, že vyžaduje několik kroků ověření. By měla vyžadovat Vícefaktorové ověřování pro všechny uživatele ve vašem tenantovi poskytovatele služeb, včetně všechny uživatele, kteří budou mít přístup k prostředkům zákazníka.

Doporučujeme vám, že můžete implementovat ověřování Azure Multi-Factor Authentication v jejich tenantů i své zákazníky začali žádat.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Přiřazení oprávnění skupinám pomocí principu nejnižší úrovně oprávnění

Abychom usnadnili správu, doporučujeme použít skupiny uživatelů Azure AD pro každou roli nutná ke správě prostředků vašich zákazníků. To umožňuje přidání nebo odebrání jednotlivých uživatelů do skupiny podle potřeby, nikoli přiřazení oprávnění přímo na tohoto uživatele.

Při vytváření struktury vašich oprávnění, nezapomeňte postupovat podle principu nejnižší úrovně oprávnění tak, aby uživatelé pouze oprávnění potřebná k dokončení jejich úloh pomáhá snižovat riziko neúmyslných chyb.

Můžete například použít při takové struktuře:

|Název skupiny  |type  |principalId  |Definice role  |ID definice role  |
|---------|---------|---------|---------|---------|
|Architekti     |Skupina uživatelů         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Posouzení     |Skupina uživatelů         |\<principalId\>         |Čtenář         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Odborníci na virtuální počítač     |Skupina uživatelů         |\<principalId\>         |Přispěvatel virtuálních počítačů         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Hlavní název služby (SPN)         |\<principalId\>         |Přispěvatel         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po vytvoření těchto skupin, můžete podle potřeby přiřaďte uživatele. Přidáte pouze uživatele, které skutečně potřebují mít přístup. Je potřeba pravidelně Zkontrolujte členství ve skupině a odeberte všichni uživatelé, kteří již nejsou vhodné nebo nutné zahrnout.

Mějte na paměti, že jste [onboardingu zákazníků prostřednictvím nabídky veřejné spravované služby](../how-to/publish-managed-services-offers.md), žádné skupině (nebo uživatel nebo instanční objekt služby), kterou bude mít stejná oprávnění pro každý zákazník, kteří zakoupí plánu. Přiřadit různé skupiny pro práci s každého zákazníka, musíte publikovat samostatné privátní plán, který je výhradně pro každého zákazníka nebo onboardingu zákazníků samostatně pomocí šablon Azure Resource Manageru. Například může publikovat veřejný plán, který má velmi omezený přístup a pak pracovat s zákazníkovi přímo připojit svoje prostředky pro další možnosti přístupu pomocí vlastní šablony Azure Resource udělení další přístup podle potřeby.


## <a name="next-steps"></a>Další postup

- [Nasazení služby Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Další informace o [napříč tenanty činnosti správy](cross-tenant-management-experience.md).
