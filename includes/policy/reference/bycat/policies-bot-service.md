---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1d7df6224dcd6533772734060f4c1cf32e3c972f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866468"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Koncový bod služby bot by měl být platným identifikátorem URI HTTPS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Data je možné během přenosu falšovat. Existují protokoly, které zajišťují šifrování a tím problémy se zneužitím a falšováním řeší. Pokud chcete zajistit, aby vaše roboty komunikovaly jenom s šifrovanými kanály, nastavte koncový bod na platný identifikátor URI HTTPS. Tím se zajistí, že se protokol HTTPS použije k šifrování vašich dat při přenosu a často se jedná o požadavky na dodržování zákonných nebo průmyslových standardů. Navštivte prosím: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Služba bot by měla být zašifrovaná pomocí klíče spravovaného zákazníkem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service automaticky zašifruje váš prostředek, aby chránil vaše data a splňoval závazky zabezpečení a dodržování předpisů v organizaci. Ve výchozím nastavení se používají šifrovací klíče spravované Microsoftem. Pokud potřebujete větší flexibilitu při správě klíčů nebo řízení přístupu k předplatnému, vyberte klíče spravované zákazníkem, označované taky jako Přineste si vlastní klíč (BYOK). Další informace o šifrování Azure Bot Service: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
