---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: c4524d2e7eec1768988f38fd2929d067c44575c2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780196"
---
|Název |Popis |Vliv (s) |Verze |Zdroj |
|---|---|---|---|---|
|[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric nabízí tři úrovně ochrany (None, přihlašování a EncryptAndSign) pro komunikaci mezi uzly pomocí certifikátu primární clusteru. Nastavte úroveň ochrany tak, aby se zajistilo, že všechny zprávy mezi uzly budou šifrované a digitálně podepsané. |Audit, zakázáno |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Auditování využití ověřování klienta pouze prostřednictvím Azure Active Directory v Service Fabric |Audit, zakázáno |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)
