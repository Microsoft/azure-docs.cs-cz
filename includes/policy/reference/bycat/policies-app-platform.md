---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e7c40c4a9c676c9b18ae079f33d46cc951b3ae18
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610677"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[\[Verze Preview \] : auditujte instance cloudových cloudů Azure, u kterých není povolené distribuované trasování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Nástroje distribuované trasování v Azure jarním cloudu umožňují ladit a monitorovat složitá propojení mezi mikroslužbami v aplikaci. Nástroje pro distribuované trasování by měly být povolené a v dobrém stavu. |Audit, zakázáno |[1.0.0 – Preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Ve jarním cloudu Azure by se mělo používat vkládání ze sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Instance Azure jaře cloudu by měly používat vkládání virtuální sítě pro tyto účely: 1. Izolujte si cloudovou službu Azure jaře z Internetu. 2. Umožněte službě Azure jaře Cloud komunikovat se systémy v místních datových centrech nebo ve službě Azure v jiných virtuálních sítích. 3. Poskytněte zákazníkům možnost řídit příchozí a odchozí síťovou komunikaci pro jarní cloud Azure. |Audit, zakázáno, odepřít |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
