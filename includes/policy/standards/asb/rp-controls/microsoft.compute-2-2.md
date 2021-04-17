---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 08284f67f5d1010d0df92f2df181e4d75e4af945
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512528"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Audit počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Vyžaduje nasazení požadovaných požadavků do oboru přiřazení zásad. Podrobnosti najdete na adrese [https://aka.ms/gcpol](https://aka.ms/gcpol) . Počítače jsou nekompatibilní, pokud není nainstalován agent nebo pokud je nainstalován, ale objekt COM AgentConfigManager. MgmtSvcCfg vrátí, že je zaregistrován pro jiný pracovní prostor, než je ID zadané v parametru zásad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Tato zásada Audituje jakékoli Virtual Machine Scale Sets systému Windows nebo Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Tato zásada Audituje všechny virtuální počítače se systémem Windows/Linux, pokud není nainstalován agent Log Analytics. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
