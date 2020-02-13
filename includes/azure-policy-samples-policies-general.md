---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169911"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Povolená umístění](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů. Vyloučí skupiny prostředků, Microsoft. Azureactivedirectory selhala/b2cDirectories a prostředky, které používají oblast Global. |odmítnout |1.0.0 |
|[Povolená umístění pro skupiny prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Tato zásada vám umožní omezit umístění, ve kterých může organizace vytvářet skupiny prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů. |odmítnout |1.0.0 |
|[Povolené typy prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Tato zásada vám umožní určit typy prostředků, které může vaše organizace nasazovat. Touto zásadou ovlivní jenom typy prostředků, které podporují Tags a Location. Pokud chcete omezit všechny prostředky, duplikujte prosím tuto zásadu a změňte ' Mode ' na ' All '. |odmítnout |1.0.0 |
|[Audit umístění prostředku odpovídá umístění skupiny prostředků.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Auditovat, že umístění prostředku odpovídá umístění skupiny prostředků |audit |1.0.0 |
|[Auditovat využití vlastních pravidel RBAC](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |1.0.0 |
|[Vlastní role vlastníka předplatného by neměly existovat.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |1.0.0 |
|[Nepovolené typy prostředků](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Tato zásada umožňuje zadat typy prostředků, které vaše organizace nemůže nasadit. |Odepřít |1.0.0 |
