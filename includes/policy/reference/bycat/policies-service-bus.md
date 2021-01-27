---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b4ae407f0584ab9d025f983a6cb0bff18da64518
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807820"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Klienti Service Bus by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě. |Audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Měly by být povolené diagnostické protokoly v Service Bus.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
