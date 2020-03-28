---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 2ae61445013a00eb4aee0a5df7ae6d356206290c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79382053"
---
|Name (Název) |Popis |Efekty |Version |GitHub |
|---|---|---|---|---|
|[Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Audit povolení pouze připojení přes SSL redis cache. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání data při přenosu před útoky síťové vrstvy, jako je man-in-the-middle, odposlouchávání a únos relace |Audit, Odepřít, Zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
