---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a33e1cd0b189f0d2b2dda29987fd218aaa5fd13b
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86275546"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Mezipaměť Azure pro Redis by se měla nacházet v rámci virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Tato zásada Audituje, jestli má prostředek Azure cache for Redis k virtuálnímu koncovému bodu, který je nasazený do virtuální sítě, neveřejný koncový bod. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
