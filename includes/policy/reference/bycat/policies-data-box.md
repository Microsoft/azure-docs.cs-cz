---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 46050a7804b4bc7349e20bacc22b1d2083a73e66
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556404"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Data Box úlohy by měly povolit dvojité šifrování pro neaktivní neaktivní data na zařízení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Povolit druhou vrstvu softwarového šifrování pro neaktivní data na zařízení. Zařízení je už chráněné pomocí standard AES (Advanced Encryption Standard) 256 bitového šifrování pro neaktivní neaktivní data. Tato možnost přidá druhou vrstvu šifrování dat. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box úlohy by měly používat klíč spravovaný zákazníkem k zašifrování hesla pro odemknutí zařízení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Použijte klíč spravovaný zákazníkem k řízení šifrování hesla pro odemknutí zařízení Azure Data Box. Klíče spravované zákazníkem vám také pomůžou spravovat přístup k odemknutí hesla zařízení službou Data Box, aby bylo možné zařízení připravit a automaticky je kopírovat. Data v samotném zařízení jsou už v klidovém stavu zašifrovaná pomocí standard AES (Advanced Encryption Standard) 256 bitového šifrování a heslo k odemknutí zařízení je ve výchozím nastavení šifrované pomocí spravovaného klíče Microsoftu. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
