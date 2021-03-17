---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2471564ead93f8b7aa678eb7eece07abea30b96
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419884"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty mezipaměti HPC by měly používat klíč spravovaný zákazníkem pro šifrování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Spravujte šifrování v klidové mezipaměti HPC Azure pomocí klíčů spravovaných zákazníkem. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale klíče spravované zákazníkem se obvykle vyžadují ke splnění standardů dodržování předpisů v legislativních zákonech. Klíče spravované zákazníkem umožňují, aby byla data zašifrovaná pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. |Audit, zakázáno, odepřít |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
