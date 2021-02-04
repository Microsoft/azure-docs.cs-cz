---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 578cd451b53634bc63876e8ca2d25e3251eb8cc2
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561278"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Clustery Azure HDInsight by měly používat klíče spravované zákazníky k šifrování neaktivních dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Pomocí klíčů spravovaných zákazníkem (CMK) můžete spravovat šifrování ve zbývajících clusterech Azure HDInsight. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale CMKs se běžně vyžadují pro dodržování standardů dodržování legislativních předpisů. CMKs povolí šifrování dat pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. Další informace o šifrování CMK najdete na adrese [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) .  |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Clustery Azure HDInsight by se měly používat k šifrování neaktivních dat na hostiteli.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Povolení šifrování na hostiteli pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Clustery Azure HDInsight by k šifrování komunikace mezi uzly clusteru Azure HDInsight měli používat šifrování při přenosu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Data je možné během přenosu mezi uzly clusteru Azure HDInsight úmyslně poškodit. Povolení šifrování při přenosu řeší problémy zneužití a manipulace během tohoto přenosu. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
