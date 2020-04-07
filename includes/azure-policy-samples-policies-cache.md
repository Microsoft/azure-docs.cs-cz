---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 7100231e1cc4590d61724668f3941591c6b00206
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758588"
---
|Name (Název) |Popis |Efekty |Version |GitHubu |
|---|---|---|---|---|
|[Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Audit povolení pouze připojení přes SSL redis cache. Použití zabezpečených připojení zajišťuje ověřování mezi serverem a službou a chrání data při přenosu před útoky síťové vrstvy, jako je man-in-the-middle, odposlouchávání a únos relace |Audit, Odepřít, Zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
