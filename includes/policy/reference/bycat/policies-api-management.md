---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866444"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Služba API Management by měla používat SKU, která podporuje virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |S podporovanými SKU API Management, nasazení služby do virtuální sítě odemkne pokročilé API Management sítě a funkce zabezpečení, které poskytují větší kontrolu nad konfigurací zabezpečení sítě. Další informace najdete tady: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management služby by měly používat virtuální síť](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Nasazení služby Azure Virtual Network poskytuje rozšířené zabezpečení, izolaci a umožňuje umístit službu API Management do sítě, která není v Internetu, ke které můžete řídit přístup. Tyto sítě je pak možné připojit k místním sítím pomocí různých technologií sítě VPN, což umožňuje přístup k back-endové službě v síti nebo v místním prostředí. Portál pro vývojáře a brána API je možné nakonfigurovat tak, aby byly přístupné buď z Internetu, nebo jenom v rámci virtuální sítě. |Audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
