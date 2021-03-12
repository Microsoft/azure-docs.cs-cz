---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1e8175239c1f4100cb3484f067e1aa73d2547f70
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612030"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure API pro FHIR by měl používat klíč spravovaný zákazníkem k šifrování neaktivních dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Klíč spravovaný zákazníkem slouží k řízení šifrování v klidovém množství dat uložených v Azure API pro FHIR, pokud se jedná o regulativní nebo právní požadavek na dodržování předpisů. Klíče spravované zákazníkem také dodávají dvojité šifrování přidáním druhé vrstvy šifrování nad výchozí hodnotu, kterou provedli klíče spravované službou. |audit, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Rozhraní Azure API pro FHIR by mělo používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Rozhraní Azure API pro FHIR by mělo mít aspoň jedno schválené připojení privátního koncového bodu. Klienti ve virtuální síti můžou bezpečně přistupovat k prostředkům, které mají připojení privátního koncového bodu prostřednictvím privátních odkazů. Další informace najdete na adrese: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR. Pokud chcete chránit rozhraní API pro FHIR, odeberte přístup pro všechny domény a explicitně definujte domény s povoleným připojením. |audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
