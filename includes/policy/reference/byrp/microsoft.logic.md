---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 81ae507045bd065f3a5fa47abc7092dc1b596b07
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099199"
---
|Název<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Nasazení nastavení diagnostiky pro Logic Apps do centra událostí](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1dae6c7-13f3-48ea-a149-ff8442661f60) |Nasadí nastavení diagnostiky pro Logic Apps do služby streamování do místního centra událostí, pokud dojde k vytvoření nebo aktualizaci Logic Apps, u kterých chybí tato nastavení diagnostiky. |DeployIfNotExists, zakázáno |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Nasazení nastavení diagnostiky pro Logic Apps k pracovnímu prostoru Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb889a06c-ec72-4b03-910a-cb169ee18721) |Nasadí nastavení diagnostiky pro Logic Apps do služby streamování do místního pracovního prostoru Log Analytics, když se vytvoří nebo aktualizuje kterákoli Logic Apps, ve které chybí tato nastavení diagnostiky. |DeployIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Měly by být povolené protokoly prostředků v Logic Apps.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
