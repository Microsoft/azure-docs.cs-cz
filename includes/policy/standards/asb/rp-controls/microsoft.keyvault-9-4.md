---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78694874819f8a90f0ab318fb96cda6b8816a689
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880890"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Trezory klíčů by měly mít povolenou ochranu vyprázdnění](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Zlomyslné odstranění trezoru klíčů může vést k trvalé ztrátě dat. Škodlivý program Insider ve vaší organizaci může potenciálně odstraňovat a vyprázdnit trezory klíčů. Vyčištění ochrany chrání vás před útoky z programu Insider tím, že vynucuje povinnou dobu uchování pro tiché odstraněné trezory klíčů. Nikdo ve vaší organizaci ani Microsoft nebude moct vyprázdnit vaše trezory klíčů během doby uchovávání obnovitelného odstranění. |Audit, zamítnutí, zakázáno |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
