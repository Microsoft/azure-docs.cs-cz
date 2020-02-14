---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 9054340799ac6f95a9a3064351193f0680a000c9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193002"
---
|Název |Popis |Vliv (s) |Version |
|---|---|---|---|
|[Povolená umístění](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů. Vyloučí skupiny prostředků, Microsoft. Azureactivedirectory selhala/b2cDirectories a prostředky, které používají oblast Global. |odmítnout |1.0.0 |
|[Povolená umístění pro skupiny prostředků](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Tato zásada vám umožní omezit umístění, ve kterých může organizace vytvářet skupiny prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů. |odmítnout |1.0.0 |
|[Povolené typy prostředků](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Tato zásada vám umožní určit typy prostředků, které může vaše organizace nasazovat. Touto zásadou ovlivní jenom typy prostředků, které podporují Tags a Location. Pokud chcete omezit všechny prostředky, duplikujte prosím tuto zásadu a změňte ' Mode ' na ' All '. |odmítnout |1.0.0 |
|[Audit umístění prostředku odpovídá umístění skupiny prostředků.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Auditovat, že umístění prostředku odpovídá umístění skupiny prostředků |audit |1.0.0 |
|[Auditovat využití vlastních pravidel RBAC](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |1.0.0 |
|[Vlastní role vlastníka předplatného by neměly existovat.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |1.0.0 |
|[Nepovolené typy prostředků](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Tato zásada umožňuje zadat typy prostředků, které vaše organizace nemůže nasadit. |Odepřít |1.0.0 |
