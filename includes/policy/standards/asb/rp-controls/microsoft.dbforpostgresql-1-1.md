---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90142fbe2d6feb2a6299a765c563b48388a8fa5f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512102"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Privátní koncový bod by měl být povolený pro PostgreSQL servery.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |Připojení privátního koncového bodu vynutila zabezpečenou komunikaci tím, že umožňuje privátní připojení k Azure Database for PostgreSQL. Nakonfigurujte připojení privátního koncového bodu tak, aby umožňovalo přístup k provozu, který přichází jenom ze známých sítí, a neumožňuje přístup ze všech ostatních IP adres, včetně v Azure. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
