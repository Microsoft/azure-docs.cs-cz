---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/24/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b5df9f37d530500b42341815f4b4aa6590746765
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147366"
---
|Název |Popis |Vliv (s) |Version |GitHubu |
|---|---|---|---|---|
|[Konfigurace aplikace by měla používat klíč spravovaný zákazníkem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Tato zásada Audituje jakoukoli instanci konfigurace aplikace, která nepoužívá zákaznická klíč spravovaný. |Audit, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Konfigurace aplikace by měla používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Tato zásada Audituje všechny instance konfigurace aplikace, které nepoužívají privátní propojení. |AuditIfNotExists, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
