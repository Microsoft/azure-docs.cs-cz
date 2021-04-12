---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1f8d96797935a72ffb80787463f571fa5b4b6540
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096994"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Pro servery MySQL by měl být povolen soukromý koncový bod.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Připojení privátního koncového bodu vynutila zabezpečenou komunikaci tím, že umožňuje privátní připojení k Azure Database for MySQL. Nakonfigurujte připojení privátního koncového bodu tak, aby umožňovalo přístup k provozu, který přichází jenom ze známých sítí, a neumožňuje přístup ze všech ostatních IP adres, včetně v Azure. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
