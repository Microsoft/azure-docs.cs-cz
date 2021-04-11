---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 76bc4084a3bdee8bc8ccbf1c77279b74456e6816
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094471"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Clustery Azure HDInsight by měly být vloženy do virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Vložením clusterů Azure HDInsight ve virtuální síti se odemknou pokročilé sítě a funkce zabezpečení HDInsight a poskytne vám kontrolu nad konfigurací zabezpečení sítě. |Audit, zakázáno, odepřít |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Clustery Azure HDInsight by měly používat klíče spravované zákazníky k šifrování neaktivních dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Pomocí klíčů spravovaných zákazníkem můžete spravovat šifrování ve zbývajících clusterech Azure HDInsight. Ve výchozím nastavení se zákaznická data šifrují pomocí klíčů spravovaných službou, ale klíče spravované zákazníkem se obvykle vyžadují ke splnění standardů dodržování předpisů v legislativních zákonech. Klíče spravované zákazníkem umožňují, aby byla data zašifrovaná pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. Máte plnou kontrolu a zodpovědnost za životní cyklus klíčů, včetně rotace a správy. Další informace najdete na adrese [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Audit, zamítnutí, zakázáno |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Clustery Azure HDInsight by se měly používat k šifrování neaktivních dat na hostiteli.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Povolení šifrování na hostiteli pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Clustery Azure HDInsight by k šifrování komunikace mezi uzly clusteru Azure HDInsight měli používat šifrování při přenosu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Data je možné během přenosu mezi uzly clusteru Azure HDInsight úmyslně poškodit. Povolení šifrování při přenosu řeší problémy zneužití a manipulace během tohoto přenosu. |Audit, zamítnutí, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
