---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 194b4ad507a48f1010dc884326c16b95d6513081
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608648"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Azure Data Box úlohy by měly povolit dvojité šifrování pro neaktivní neaktivní data na zařízení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Povolit druhou vrstvu softwarového šifrování pro neaktivní data na zařízení. Zařízení je už chráněné pomocí standard AES (Advanced Encryption Standard) 256 bitového šifrování pro neaktivní neaktivní data. Tato možnost přidá druhou vrstvu šifrování dat. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box úlohy by měly používat klíč spravovaný zákazníkem k zašifrování hesla pro odemknutí zařízení.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Použijte klíč spravovaný zákazníkem k řízení šifrování hesla pro odemknutí zařízení Azure Data Box. Klíče spravované zákazníkem vám také pomůžou spravovat přístup k odemknutí hesla zařízení službou Data Box, aby bylo možné zařízení připravit a automaticky je kopírovat. Data v samotném zařízení jsou už v klidovém stavu zašifrovaná pomocí standard AES (Advanced Encryption Standard) 256 bitového šifrování a heslo k odemknutí zařízení je ve výchozím nastavení šifrované pomocí spravovaného klíče Microsoftu. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
