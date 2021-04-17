---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4db8e0b6209c70075ee61739477ff43c8b3a8c93
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513489"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro servery MySQL by měl být povolen soukromý koncový bod.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Připojení privátního koncového bodu vynutila zabezpečenou komunikaci tím, že umožňuje privátní připojení k Azure Database for MySQL. Nakonfigurujte připojení privátního koncového bodu tak, aby umožňovalo přístup k provozu, který přichází jenom ze známých sítí, a neumožňuje přístup ze všech ostatních IP adres, včetně v Azure. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
