---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 57594b209f1f46cb1d0f47224a2a725eb1fceb78
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881098"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro servery MySQL by měl být povolen soukromý koncový bod.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Připojení privátního koncového bodu vynutila zabezpečenou komunikaci tím, že umožňuje privátní připojení k Azure Database for MySQL. Nakonfigurujte připojení privátního koncového bodu tak, aby umožňovalo přístup k provozu, který přichází jenom ze známých sítí, a neumožňuje přístup ze všech ostatních IP adres, včetně v Azure. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
