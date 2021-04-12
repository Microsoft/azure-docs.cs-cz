---
title: Příkaz import/export Sqoop se nezdařil pro některé uživatele v clusterech ESP – Azure HDInsight
description: 'Příkaz Sqoop pro import/export se nezdařil s chybou import: Java. IO. IOException: vlastnictví v přípravné složce/User/YOURUSERNAME/.staging není podle očekávání pro některé uživatele v clusteru Azure HDInsight ESP.'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387330"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scénář: příkaz import/export Sqoop se nezdařil pro uživatelská jména delší než 20 znaků v clusterech Azure HDInsight ESP.

Tento článek popisuje známý problém a alternativní řešení při použití clusterů Azure HDInsight ESP (Enterprise Security Pack) s povoleným účtem úložiště ADLS Gen2 (ABFS).

## <a name="issue"></a>Problém

Při spuštění příkazu import/export Sqoop se tato chyba nezdařila s následující chybou pro některé uživatele:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

V předchozím příkladu `/user/yourlongdomainuserna/.staging` zobrazuje zkrácený znak uživatelské jméno pro uživatelské jméno `yourlongdomainusername` .

## <a name="cause"></a>Příčina

Délka uživatelského jména překračuje 20 znaků. 

Další podrobnosti najdete [v článku Synchronizace objektů a přihlašovacích údajů ve spravované doméně Azure Active Directory Domain Services](../active-directory-domain-services/synchronization.md) .

## <a name="workaround"></a>Alternativní řešení

Použijte uživatelské jméno menší nebo rovno 20 znakům.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
