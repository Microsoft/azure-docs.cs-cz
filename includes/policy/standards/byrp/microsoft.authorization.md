---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b75b8a5c13dabcb351e7fab89483ad1d88154e6f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328543"
---
## <a name="azure-security-benchmark"></a>Srovnávací test zabezpečení Azure

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana dat |4,6 |Řízení přístupu k prostředkům pomocí služby Azure RBAC |[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>Srovnávací test CIS Microsoft Azure Foundations

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – CIS Microsoft Azure Foundation 1.1.0 Foundation](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Další informace o tomto standardu dodržování předpisů najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Správa identit a přístupu |1.23 |Zajistěte, aby nevytvářely žádné vlastní role vlastníka předplatného. |[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |AC-2 (7) |Správa účtů \| – schémata na základě rolí |[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

