---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 96e007618908d5e43c0a450052b5e1fd3b14df87
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680703"
---
## <a name="azure-security-benchmark-v1"></a>Srovnávací test zabezpečení Azure v1

[Srovnávací test zabezpečení Azure](../../../../articles/security/benchmarks/overview.md) poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Informace o tom, jak tato služba zcela namapuje srovnávací test zabezpečení Azure, najdete v [souborech mapování srovnávacích testů zabezpečení Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Zabezpečení sítě |1,1 |Ochrana prostředků pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network |[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Protokolování a monitorování |2.3 |Povolení protokolování auditu pro prostředky Azure |[Měly by být povolené diagnostické protokoly v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Oddělení v sítích |0805.01 m1Organizational. 12.01. m |Brány zabezpečení organizace (např. brány firewall) vynutily zásady zabezpečení a jsou nakonfigurované pro filtrování provozu mezi doménami, blokují neoprávněný přístup a používají se k udržení oddělení mezi interními pevnými, interními bezdrátovými a externími segmenty sítě (např. Internet), včetně zóny DMZ a prosazování zásad řízení přístupu pro jednotlivé domény. |[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Oddělení v sítích |0806.01 m2Organizational. 12356 – 01. m |Síť organizací je logicky a fyzicky rozdělená na vymezený hraniční zabezpečení a odstupňovanou sadu ovládacích prvků, včetně podsítí pro veřejně přístupné systémové komponenty, které jsou logicky oddělené od interní sítě, na základě požadavků organizace. a provoz se řídí podle požadovaných funkcí a klasifikace dat nebo systémů na základě posouzení rizik a jejich příslušných požadavků na zabezpečení. |[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Oddělení v sítích |0894.01 m2Organizational. 7 – 01. m |Sítě jsou při migraci fyzických serverů, aplikací nebo dat na virtualizované servery oddělené od sítí na úrovni služby. |[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Protokolování auditu |1208.09 aa3System. 1 – 09. AA |Protokoly auditu se udržují pro aktivity správy, spuštění systému a aplikace, chyby, změny souborů a změny zásad zabezpečení. |[Měly by být povolené diagnostické protokoly v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|Ovládací prvky sítě |0860.09 m1Organizational. 9 – 09. m |Organizace formálně spravuje zařízení v síti, včetně zařízení v oblastech uživatele. |[Service Bus by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

