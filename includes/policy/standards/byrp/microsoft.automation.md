---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478547"
---
## <a name="azure-security-benchmark"></a>Srovnávací test zabezpečení Azure

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana dat |4.8 |Šifrovat citlivé informace v klidovém umístění |[Proměnné účtu Automation by se měly šifrovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Když vytvoříte proměnnou účtu Automation, můžete své šifrování a úložiště zadat Azure Automation jako zabezpečený prostředek. Po vytvoření proměnné nemůžete změnit její stav šifrování, aniž byste museli znovu vytvořit proměnnou. Pokud máte proměnné účtu Automation, které ukládají citlivé údaje, které ještě nejsou zašifrované, musíte je odstranit a znovu vytvořit jako šifrované proměnné. Azure Security Center doporučení je šifrovat všechny Azure Automation proměnné, jak je popsané v tématu [proměnné účtu Automation by měly být šifrované](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Pokud máte nešifrované proměnné, které chcete vyloučit z tohoto doporučení zabezpečení, přečtěte si téma [vyloučení prostředku z doporučení a zabezpečeného skóre](../../../../articles/security-center/exempt-resource.md) pro vytvoření pravidla výjimky.