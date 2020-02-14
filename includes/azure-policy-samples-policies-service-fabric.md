---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 221633fcb459e39a4c11e869b9214d985cd5ef0f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192984"
---
|Název |Popis |Vliv (s) |Version |
|---|---|---|---|
|[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric nabízí tři úrovně ochrany (None, přihlašování a EncryptAndSign) pro komunikaci mezi uzly pomocí certifikátu primární clusteru. Nastavte úroveň ochrany tak, aby se zajistilo, že všechny zprávy mezi uzly budou šifrované a digitálně podepsané. |Audit, zakázáno |1.0.0 |
|[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Auditování využití ověřování klienta pouze prostřednictvím Azure Active Directory v Service Fabric |Audit, zakázáno |1.0.0 |
