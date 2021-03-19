---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c695a6b4ed213de3cd989a2c3a6718e04ac43a27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590103"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Veškerý internetový provoz by měl být směrován prostřednictvím nasazené Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center identifikoval, že některé podsítě nejsou chráněné pomocí brány firewall nové generace. Chraňte své podsítě před potenciálními hrozbami tím, že omezíte přístup k nim pomocí Azure Firewall nebo podporované brány firewall nové generace. |AuditIfNotExists, zakázáno |[3.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Měla by být povolená Azure DDoS Protection Standard.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |DDoS Protection Standard by měla být povolená pro všechny virtuální sítě s podsítí, která je součástí aplikační brány s veřejnou IP adresou. |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
