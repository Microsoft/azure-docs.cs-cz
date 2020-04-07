---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 34180ae6ffc2c2ead2864d4dbc604e3970bbc1f2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758422"
---
|Name (Název) |Popis |Efekty |Version |GitHubu |
|---|---|---|---|---|
|[Diagnostické protokoly v dávkových účtech by měly být povoleny.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditování povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity pro účely šetření; dojde k incidentu zabezpečení nebo v případě ohrožení sítě |AuditIfNotExists, zakázáno |2.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json)
|[Pravidla upozornění metriky by měla být konfigurována u dávkových účtů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Auditovat konfiguraci pravidel upozornění metriky pro dávkový účet, aby byla povolena požadovaná metrika |AuditIfNotExists, zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json)
