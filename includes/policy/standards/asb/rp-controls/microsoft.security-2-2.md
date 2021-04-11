---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 355a74ded8b8d01632aff70b35f96f2502c970b7
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "107285767"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |K monitorování ohrožení zabezpečení a hrozeb Azure Security Center shromažďuje data z virtuálních počítačů Azure. Data shromažďuje agent Log Analytics, dříve označovaný jako Microsoft Monitoring Agent (MMA), který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do vašeho pracovního prostoru Log Analytics pro účely analýzy. Doporučujeme povolit Automatické zřizování pro automatické nasazení agenta na všechny podporované virtuální počítače Azure a všechny nově vytvořené. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
