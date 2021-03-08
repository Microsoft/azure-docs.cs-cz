---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6feeb7a4c97cdb5fe5eb409abd2b92c4d96ed5cb
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444374"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro služby Kubernetes by měly být definované rozsahy povolených IP adres.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |Omezte přístup k Kubernetes rozhraní API pro správu služeb tím, že udělíte přístup k rozhraní API jenom na IP adresy v určitých rozsahech. Doporučuje se omezit přístup k povoleným rozsahům IP adres, aby se zajistilo, že ke clusteru budou mít přístup jenom aplikace z povolených sítí. |Audit, zakázáno |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
