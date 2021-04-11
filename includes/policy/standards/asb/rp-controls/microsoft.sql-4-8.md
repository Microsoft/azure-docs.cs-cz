---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fbe4761fe1b14425ff61edcf25e7335791af5eaf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097689"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementace transparentní šifrování dat (TDE) s vlastním klíčem vám poskytne větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. Toto doporučení se vztahuje na organizace s příslušným požadavkem na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL servery by se měly používat pro šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementace transparentní šifrování dat (TDE) s vlastním klíčem zajišťuje větší transparentnost a kontrolu nad ochranným prostředím TDE, zvýšené zabezpečení pomocí externí služby založené na HSM a povýšení oddělení povinností. Toto doporučení se vztahuje na organizace s příslušným požadavkem na dodržování předpisů. |AuditIfNotExists, zakázáno |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Je třeba povolit transparentní šifrování dat databází SQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů. |AuditIfNotExists, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
