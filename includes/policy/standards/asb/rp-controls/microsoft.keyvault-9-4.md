---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513361"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Trezory klíčů by měly mít povolenou ochranu vyprázdnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Zlomyslné odstranění trezoru klíčů může vést k trvalé ztrátě dat. Škodlivý program Insider ve vaší organizaci může potenciálně odstraňovat a vyprázdnit trezory klíčů. Vyčištění ochrany chrání vás před útoky z programu Insider tím, že vynucuje povinnou dobu uchování pro tiché odstraněné trezory klíčů. Nikdo ve vaší organizaci ani Microsoft nebude moct vyprázdnit vaše trezory klíčů během doby uchovávání obnovitelného odstranění. |Audit, zamítnutí, zakázáno |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
