---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 191f6fe6dc8086dbf187851c542fc75b9d0e5643
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429036"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Proměnné účtu Automation by se měly šifrovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |Při ukládání citlivých dat je důležité povolit šifrování prostředků proměnných účtu služby Automation. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Automation účty by měly používat klíče spravované zákazníkem k šifrování neaktivních dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |Pomocí klíčů spravovaných zákazníkem můžete spravovat šifrování v klidech Azure Automationch účtů. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale klíče spravované zákazníkem se obvykle vyžadují ke splnění standardů dodržování předpisů v legislativních zákonech. Klíče spravované zákazníkem umožňují, aby byla data zašifrovaná pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. Další informace najdete na adrese [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk) . |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
