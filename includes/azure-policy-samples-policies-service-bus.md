---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 10cd98a350ceb622f07fcb6c85477260b2c7cb60
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79382203"
---
|Name (Název) |Popis |Efekty |Version |GitHub |
|---|---|---|---|---|
|[Všechna autorizační pravidla s výjimkou rootmanagesharedaccesskey by měla být odebrána z oboru názvů Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Klienti služby Service Bus by neměli používat zásady přístupu na úrovni oboru názvů, které poskytují přístup ke všem frontám a tématům v oboru názvů. Chcete-li zarovnat s modelem zabezpečení s nejnižšími oprávněními, měli byste vytvořit zásady přístupu na úrovni entity pro fronty a témata, abyste měli přístup pouze k určité entitě. |Audit, Odepřít, Zakázáno |1.0.1 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Diagnostické protokoly v service bus by měly být povoleny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditování povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity pro účely šetření; dojde k incidentu zabezpečení nebo v případě ohrožení sítě |AuditIfNotExists, zakázáno |2.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
