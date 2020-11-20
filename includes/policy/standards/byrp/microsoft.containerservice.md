---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53591e9a4f2477ab28d194516f97790095bdbadd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989733"
---
## <a name="azure-security-benchmark"></a>Srovnávací test zabezpečení Azure

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Zabezpečení sítě |1,1 |Ochrana prostředků pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network |[Pro služby Kubernetes by měly být definované rozsahy povolených IP adres.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|Ochrana dat |4,6 |Řízení přístupu k prostředkům pomocí služby Azure RBAC |[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Správa ohrožení zabezpečení |5.3 |Nasazení automatizovaného řešení pro správu oprav softwaru třetí strany |[Služby Kubernetes by se měly upgradovat na nezranitelnou Kubernetes verzi.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>Srovnávací test CIS Microsoft Azure Foundations

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – CIS Microsoft Azure Foundation 1.1.0 Foundation](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Další informace o tomto standardu dodržování předpisů najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Další informace o zabezpečení |8.5 |Povolení řízení přístupu na základě role (RBAC) v rámci služeb Azure Kubernetes Services |[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Správa oprávnění |1149.01 c2System. 9 – 01. c |Tato organizace usnadňuje sdílení informací tím, že umožňuje autorizovaným uživatelům určit přístup obchodních partnerů, pokud je podle potřeby vymezená organizace, a díky tomu, že využívají ruční procesy nebo automatizované mechanismy, aby uživatelům usnadnili rozhodování o sdílení a spolupráci. |[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Správa oprávnění |1153.01 c3System. 35 – 01. c |Veškerý přístup k systému souborů není explicitně vyžadován a přístup pouze autorizovaným uživatelům je povolen pouze k tomu, který je výslovně vyžadován pro výkon pracovních povinností uživatelů. |[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Oddělení povinností |1229.09 c1Organizational. 1 – 09. c |Oddělení cel slouží k omezení rizika neoprávněné nebo neúmyslné úpravy informací a systémů. |[Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Integrita systémů a informací |3.14.1 |Včas Identifikujte, Sestavujte a opravte chyby systému. |[Služby Kubernetes by se měly upgradovat na nezranitelnou Kubernetes verzi.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

