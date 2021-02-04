---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddf744231cc763c0113e98b38753e1de65b47dd8
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561644"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Klienti Service Bus by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě. |Audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Měly by být povolené diagnostické protokoly v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Obory názvů Service Bus úrovně Premium by měly používat klíč spravovaný zákazníkem pro šifrování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus podporuje možnost šifrování neaktivních dat buď pomocí klíčů spravovaných Microsoftem (výchozí) nebo klíčů spravovaných zákazníkem. Zvolíte-li šifrování dat pomocí klíčů spravovaných zákazníkem, můžete přiřazovat, otáčet, zakazovat a odvolávat přístup k klíčům, které Service Bus použijí k šifrování dat ve vašem oboru názvů. Upozorňujeme, že Service Bus podporuje jenom šifrování s klíči spravovanými zákazníkem pro obory názvů Premium. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
