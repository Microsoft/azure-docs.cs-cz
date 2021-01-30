---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b16d4141a8a20818efcadbbcc66421278350105c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097859"
---
## <a name="azure-security-benchmark"></a>Srovnávací test zabezpečení Azure

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Správa identit |IM-1 |Standardizace Azure Active Directory jako centrální systém pro ověřování a identifikaci |[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Ochrana dat |DP-5 |Šifrovat citlivá data v klidovém umístění |[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Srovnávací test zabezpečení Azure v1

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Identita a řízení přístupu |3.9 |Použití Azure Active Directory |[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Ochrana dat |4.8 |Šifrovat citlivé informace v klidovém umístění |[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC úrovně 3

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – CMMC úrovně 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [certifikace kyberbezpečnosti (CMMC) – certifikace modelu splatnosti](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Ochrana systémů a komunikací |SC. 3.177 |Využívejte kryptografii ověřenou FIPS při použití k ochraně důvěrnosti CUI. |[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|Ochrana systémů a komunikací |SC. 3.191 |Chránit důvěrnost CUI v klidovém znění. |[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – ISO 27001:2013](../../../../articles/governance/policy/samples/iso-27001.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |9.2.3 |Správa privilegovaných přístupových práv |[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Kryptografie |10.1.1 |Zásady používání kryptografických ovládacích prvků |[Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |AC-2 (7) |\|Schémata Role-Based správy účtů |[Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

