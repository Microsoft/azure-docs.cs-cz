---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2c1e93c97092d850615bf9fe9811cac841f0f676
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588283"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Účty mezipaměti HPC by měly používat klíč spravovaný zákazníkem pro šifrování.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Spravujte šifrování v klidové mezipaměti HPC Azure pomocí klíčů spravovaných zákazníkem. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale klíče spravované zákazníkem se obvykle vyžadují ke splnění standardů dodržování předpisů v legislativních zákonech. Klíče spravované zákazníkem umožňují, aby byla data zašifrovaná pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. |Audit, zakázáno, odepřít |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
