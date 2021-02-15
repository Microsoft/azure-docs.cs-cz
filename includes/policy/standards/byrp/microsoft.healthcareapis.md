---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6dd60d30daa80b8d7b522c0695ada782e4bdb38b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105727"
---
## <a name="cmmc-level-3"></a>CMMC úrovně 3

Pokud chcete zjistit, jak jsou dostupné Azure Policy vestavěné pro všechny služby Azure namapovány na tento standard dodržování předpisů, přečtěte si téma [Azure Policy dodržování předpisů – CMMC úrovně 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Další informace o tomto standardu dodržování předpisů najdete v tématu [certifikace kyberbezpečnosti (CMMC) – certifikace modelu splatnosti](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Doména |ID ovládacího prvku |Název ovládacího prvku |Zásady<br /><sub>(Azure Portal)</sub> |Verze zásady<br /><sub>GitHubu</sub>  |
|---|---|---|---|---|
|Řízení přístupu |AC. 1.001 |Omezte přístup k informacím v systému autorizovaných uživatelů, procesů působících jménem autorizovaných uživatelů a zařízení (včetně dalších informačních systémů). |[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Řízení přístupu |AC. 1.002 |Omezte přístup k informacím v systému na typy transakcí a funkcí, které můžou autorizovaní uživatelé spouštět. |[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Řízení přístupu |AC. 2.016 |Řízení toku CUI podle schválených autorizací. |[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Správa konfigurace |CM. 3.068 |Omezte, zakažte nebo Zabraňte používání nepodstatných programů, funkcí, portů, protokolů a služeb. |[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Ochrana systémů a komunikací |SC. 3.177 |Využívejte kryptografii ověřenou FIPS při použití k ochraně důvěrnosti CUI. |[Azure API pro FHIR by měl používat klíč spravovaný zákazníkem k šifrování neaktivních dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Ochrana systémů a komunikací |SC. 3.183 |Ve výchozím nastavení zakažte provoz síťových komunikací a povolte provoz síťové komunikace podle výjimky (tzn. Odepřít vše, povolit výjimku). |[CORS by nemělo umožňovat všem doménám přistupovat k rozhraní API pro FHIR.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

