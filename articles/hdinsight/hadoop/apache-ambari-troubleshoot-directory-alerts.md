---
title: Výstrahy adresáře Apache Ambari v Azure HDInsight
description: Diskuze a analýza možných důvodů a řešení upozornění pro adresáře Apache Ambari v HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943280"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scénář: výstrahy adresáře Apache Ambari v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Dostanete chyby z Apache Ambari, které jsou podobné:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Příčina

V ovlivněných pracovních uzlech chybí zmíněné adresáře z upozornění Ambari.

## <a name="resolution"></a>Řešení

Ručně vytvořte chybějící adresáře na ovlivněných pracovních uzlech.

1. SSH k příslušnému pracovnímu uzlu.

1. Získat uživatele root: `sudo su` .

1. Rekurzivní vytváření adresářů potřebných.

1. Změňte vlastníka a skupinu pro tyto adresáře.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. V uživatelském rozhraní Apache Ambari zakažte a pak povolte výstrahu.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]