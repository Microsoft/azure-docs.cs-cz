---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b3ffe059290efe28a160133f9247d5d79a522a1d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429080"
---
|Název<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů centra událostí.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Klienti centra událostí by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. K zajištění souladu s modelem zabezpečení s minimálními oprávněními byste měli vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup jenom ke konkrétní entitě. |Audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |
|[Autorizační pravidla v instanci centra událostí by měla být definovaná.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Audit existence autorizačních pravidel v entitách centra událostí pro udělení minimálního privilegovaného přístupu |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |
|[Obory názvů centra událostí by měly pro šifrování používat klíč spravovaný zákazníkem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1ad735a-e96f-45d2-a7b2-9a4932cab7ec) |Azure Event Hubs podporuje možnost šifrování dat v klidovém stavu pomocí klíčů spravovaných Microsoftem (výchozí) nebo klíčů spravovaných zákazníkem. Když zvolíte šifrování dat pomocí klíčů spravovaných zákazníkem, můžete přiřazovat, otáčet, zakazovat a odvolávat přístup k klíčům, které bude centrum událostí používat k šifrování dat ve vašem oboru názvů. Všimněte si, že centrum událostí podporuje jenom šifrování pomocí klíčů spravovaných zákazníkem pro obory názvů v vyhrazených clusterech. |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_CustomerManagedKeyEnabled_Audit.json) |
|[Protokoly prostředků v centru událostí by měly být povolené.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Audit povolování protokolů prostředků. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
