---
title: Azure maják a programu Cloud Solution Provider
description: Při používání Azure delegovat správu prostředků, je potřeba zvážit zabezpečení a řízení přístupu.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809957"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure maják a programu Cloud Solution Provider

Pokud jste [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) partnera, dostanete předplatná Azure vytvořená pro vaše zákazníky prostřednictvím programu CSP pomocí [jménem Správa (funkce AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkce. Tento přístup umožňuje přímo podporují, konfigurovat a spravovat odběry pro vaše zákazníky.

Funkce AOBO mechanismus uděluje úplný přístup k prostředí zákazníků. Správa prostředků Azure delegované spolu s funkce AOBO pomáhá zlepšit zabezpečení tím, že umožňuje snížit nepotřebné přístup tím, že odstupňovaných oprávnění pro uživatele. 

## <a name="administer-on-behalf-of-aobo"></a>Správa jménem (funkce AOBO)

Pomocí funkce AOBO, každý uživatel s [Agent pro správu](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) role ve vašem tenantovi budou mít funkce AOBO přístupu k předplatným Azure, které vytvoříte prostřednictvím programu CSP. Všichni uživatelé, kteří potřebují přístup k předplatným žádný zákazník musí být členem této skupiny. Funkce AOBO neumožňuje flexibilitu vytvoření odlišné skupiny, které využívají různé zákazníky nebo chcete povolit různé role pro uživatele nebo skupiny.

![Správa tenantů prostřednictvím funkce AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Správa delegovaného prostředků Azure

Pomocí Azure delegované správy prostředků, můžete přiřadit různé skupiny pro různé zákazníky nebo role, jak je znázorněno v následujícím diagramu. Vzhledem k tomu, že uživatelé budou mít odpovídající úroveň přístupu prostřednictvím prostředků Azure delegované správy, můžete snížit počet uživatelů, kteří mají roli Agent pro správu (a tedy mít úplný přístup funkce AOBO). To nabízí vyšší úroveň zabezpečení tím, že omezíte nepotřebné přístup k prostředkům vašich zákazníků. Také nabízí větší flexibilitu pro správu více zákazníků ve velkém měřítku.

Připojování předplatné, které jste vytvořili prostřednictvím programu CSP bude postupovat podle kroků popsaných v [připojení předplatného Azure Delegovaná správa prostředků](../how-to/onboard-customer.md). Každý uživatel, který má roli Agent pro správu ve vašem tenantovi můžete provést toto připojení.

Všimněte si, že pro předplatná vytvořená prostřednictvím programů CSP, žádosti o podporu můžete generovat pouze uživatelé s rolí správce agenta v tenantovi poskytovatele služeb. Uživatelé přidaní prostřednictvím prostředků Azure delegované správy nebude možné otevřít žádosti o podporu pro delegované prostředky v těchto předplatných.

![Správa tenanta prostřednictvím funkce AOBO a Azure Delegovaná správa prostředků](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Partnerské propojení správce

Vaše ID Microsoft Partner Network (MPN) můžete přidružit vaše předplatná připojili ke sledování vliv mezi zákazníky.

Pokud jste [publikovat na webu Azure Marketplace nabídku spravovaných služeb](../how-to/publish-managed-services-offers.md), svoje ID MPN je přidružený k profilu vydavatele a je automaticky přiřazen k nabídky. Výnosy vygenerovaných vašimi prostředky Azure prostřednictvím v rámci této nabídky bude mít s přidělenými atributy pro vaši organizaci. V partnerovi reporting systémy, třeba partnerského centra nebo MPN přidělení se zobrazí jako Partner správce odkaz (PAL).

Pokud jste [onboardingu zákazníků Azure Delegovaná správa prostředků pomocí šablon Azure Resource Manageru](../how-to/onboard-customer.md), stále můžete přidružit svoje ID MPN. aby bylo možné přijímat rozpoznávání pro vaše dopad na zákazníky, ale budete mít Chcete-li to provést ručně. Další informace najdete v tématu [propojit ID partnera k účtům Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>Další postup

- Další informace o [napříč tenanty činnosti správy](cross-tenant-management-experience.md).
- Další informace o [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
