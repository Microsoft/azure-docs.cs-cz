---
title: Předdefinované definice zásad pro Azure DDoS Protection Standard
description: Seznam Azure Policy předdefinované definice zásad pro Azure DDoS Protection Standard. Tyto integrované definice zásad poskytují běžné přístupy ke správě prostředků Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 86fa383fe2456eabe0cce142bd4a9c665fb95a2a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505696"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy předdefinované definice pro Azure DDoS Protection Standard

Tato stránka je indexem [Azure Policy](../governance/policy/overview.md) integrovaných definic zásad pro Azure DDoS Protection Standard. Další Azure Policy integrovaných pro jiné služby najdete v tématu [Azure Policy předdefinované definice](../governance/policy/samples/built-in-policies.md).

Název každé předdefinované definice zásad odkazuje na definici zásady v Azure Portal. Pomocí odkazu ve sloupci **verze** Zobrazte zdroj v [úložišti Azure Policy GitHubu](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Virtuální sítě by se měly chránit pomocí Azure DDoS Protection úrovně Standard.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Chraňte své virtuální sítě proti útokům prostřednictvím protokolu a zabezpečení pomocí Azure DDoS Protection Standard. Další informace najdete na adrese [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Upravit, auditovat, zakázáno|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Veřejné IP adresy by měly mít povolené protokoly prostředků pro Azure DDoS Protection Standard.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Povolte protokoly prostředků pro veřejné IP adresy v nastavení diagnostiky ke streamování do Log Analytics pracovního prostoru. Získejte podrobné informace o útokech a akcích pro zmírnění útoků DDoS prostřednictvím oznámení, sestav a protokolů toku.|AuditIfNotExists, DeployIfNotExists, zakázáno|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Další kroky

- Projděte si předdefinované možnosti v [úložišti služby Azure Policy na GitHubu](https://github.com/Azure/azure-policy).
- Projděte si [strukturu definic Azure Policy](../governance/policy/concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../governance/policy/concepts/effects.md).
