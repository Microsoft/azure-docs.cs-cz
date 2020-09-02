---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dfbbf6ec79644da77dbccda83a31428c31f4fba9
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379195"
---
## <a name="azure-security-benchmark"></a>Srovnávací test zabezpečení Azure

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Zabezpečení sítě |1,1 |Ochrana prostředků pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Zabezpečení sítě |1,1 |Ochrana prostředků pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network |[Účty úložiště by měly používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|Ochrana dat |4.4 |Šifrování všech citlivých informací během přenosu |[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Správa inventáře a aktiv |6.9 |Používejte jenom schválené služby Azure. |[Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>Srovnávací test CIS Microsoft Azure Foundations

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – CIS Microsoft Azure Foundation 1.1.0 Foundation](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Další informace o tomto standardu dodržování předpisů najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Účty úložiště |3.1 |Ujistěte se, že je požadovaný zabezpečený přenos nastavený na povoleno. |[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Účty úložiště |3.7 |Ujistěte se, že výchozí pravidlo přístupu k síti pro účty úložiště je nastavené na odepřít. |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Účty úložiště |3,8 |Zajistěte, aby se pro přístup k účtu úložiště povolily důvěryhodné služby Microsoftu. |[Účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |
|Protokolování a monitorování |5.1.6 |Zajistěte, aby byl účet úložiště obsahující kontejner s protokoly aktivit zašifrovaný pomocí BYOK (použijte vlastní klíč). |[Účet úložiště obsahující kontejner s protokoly aktivit musí být zašifrovaný pomocí BYOK.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |3.1.1 |Omezte přístup k systému autorizovaných uživatelů, procesů působících jménem autorizovaných uživatelů a zařízení (včetně dalších systémů). |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Řízení přístupu |3.1.12 |Monitorování a řízení relací vzdáleného přístupu. |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Ochrana systémů a komunikací |3.13.1 |Monitorujte, kontrolujte a Zabezpečte komunikaci (tj. informace přenášené nebo přijímané organizačními systémy) na vnějších hranicích a na základních vnitřních hranicích systémů organizace. |[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Ochrana systémů a komunikací |3.13.1 |Monitorujte, kontrolujte a Zabezpečte komunikaci (tj. informace přenášené nebo přijímané organizačními systémy) na vnějších hranicích a na základních vnitřních hranicích systémů organizace. |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Ochrana systémů a komunikací |3.13.5 |Implementujte podsítě pro veřejně dostupné systémové součásti, které jsou fyzicky nebo logicky oddělené od interních sítí. |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Ochrana systémů a komunikací |3.13.8 |Implementujte kryptografické mechanismy, aby se zabránilo neoprávněnému odhalení CUI během přenosu, pokud není jinak chráněná alternativním fyzickým zabezpečením. |[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásada<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |AC-17 (1) |\|Automatizované monitorování a řízení vzdáleného přístupu |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Ochrana systémů a komunikací |SC-7 |Ochrana hranic |[Účty úložiště by měly omezovat síťový přístup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Ochrana systémů a komunikací |SC-8 (1) |Důvěrnost přenosů a \| kryptografická integrita nebo alternativní fyzická ochrana |[Měl by se povolit zabezpečený přenos do účtů úložiště](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

