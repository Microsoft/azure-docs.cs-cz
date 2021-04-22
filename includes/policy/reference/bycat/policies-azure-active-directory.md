---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866450"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services spravované domény by měly používat jenom režim TLS 1,2.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Pro vaše spravované domény používejte jenom režim TLS 1,2. Ve výchozím nastavení Azure AD Domain Services umožňuje použití šifr, jako je NTLM V1 a TLS v1. Tato šifra může být pro některé starší aplikace povinná, ale je pokládána za slabá a může být zakázaná, pokud je nepotřebujete. V případě, že je povolen pouze režim TLS 1,2, dojde k selhání jakéhokoli klienta, který vydává požadavek bez použití protokolu TLS 1,2. Další informace najdete na adrese [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
