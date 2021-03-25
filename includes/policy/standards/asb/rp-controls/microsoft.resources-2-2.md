---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddef021b81a07c1c453a9a56ea00eddd7f71a43a
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104302"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[V předplatném by mělo být povolené Automatické zřizování agenta Log Analytics.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |K monitorování ohrožení zabezpečení a hrozeb Azure Security Center shromažďuje data z virtuálních počítačů Azure. Data shromažďuje agent Log Analytics, dříve označovaný jako Microsoft Monitoring Agent (MMA), který čte různé konfigurace a protokoly událostí související se zabezpečením z počítače a kopíruje data do vašeho pracovního prostoru Log Analytics pro účely analýzy. Doporučujeme povolit Automatické zřizování pro automatické nasazení agenta na všechny podporované virtuální počítače Azure a všechny nově vytvořené. |AuditIfNotExists, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Tato zásada zajišťuje, že profil protokolu shromažďuje protokoly pro kategorie Write, DELETE a Action. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Tato zásada Audituje profil Azure Monitorho protokolu, který neexportuje aktivity ze všech podporovaných oblastí Azure, včetně globálního. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
