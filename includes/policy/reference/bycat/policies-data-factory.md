---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b448fbb9a7d382a785fd66c0edb197499c8c5c79
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561468"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Datové továrny Azure by se měly šifrovat pomocí klíče spravovaného zákazníkem (CMK).](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Pomocí klíčů spravovaných zákazníkem (CMKs) můžete spravovat šifrování v klidovém prostředí Azure Data Factory. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale CMKs se běžně vyžadují pro dodržování standardů dodržování legislativních předpisů. CMKs povolí šifrování dat pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. Další informace o šifrování CMK najdete na adrese [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk) . |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Přístup k veřejné síti na Azure Data Factory by měl být zakázán.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cf164be-6819-4a50-b8fa-4bcaa4f98fb6) |Zakázáním vlastnosti přístup k veřejné síti zlepšíte zabezpečení tím, že zajistíte přístup k vašemu Azure Data Factory jenom z privátního koncového bodu. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Audit.json) |
