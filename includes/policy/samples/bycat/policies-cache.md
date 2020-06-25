---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 901b36e5b0e31752a9145245298d446e61a91aee
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313390"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Mezipaměť Azure pro Redis by se měla nacházet v rámci virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Tato zásada Audituje, jestli má prostředek Azure cache for Redis k virtuálnímu koncovému bodu, který je nasazený do virtuální sítě, neveřejný koncový bod. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditovat povolování jenom připojení přes SSL do Azure cache pro Redis. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání přenášená data před útoky ze síťové vrstvy, jako jsou například prostředníky, odposlouchávání a zneužití relace. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
