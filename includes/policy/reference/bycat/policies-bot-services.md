---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6a5c86dc1b10ed79e5dabbf5be7bf3ee2b677dee
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807682"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Koncový bod služby bot by měl být platným identifikátorem URI HTTPS.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Data je možné během přenosu falšovat. Existují protokoly, které zajišťují šifrování a tím problémy se zneužitím a falšováním řeší. Pokud chcete zajistit, aby vaše roboty komunikovaly jenom s šifrovanými kanály, nastavte koncový bod na platný identifikátor URI HTTPS. Tím se zajistí, že se protokol HTTPS použije k šifrování vašich dat při přenosu a často se jedná o požadavky na dodržování zákonných nebo průmyslových standardů. Navštivte prosím: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
