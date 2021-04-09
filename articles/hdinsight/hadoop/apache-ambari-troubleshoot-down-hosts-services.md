---
title: Uživatelské rozhraní Apache Ambari zobrazuje hostitele a služby ve službě Azure HDInsight.
description: Řešení problémů s uživatelským rozhraním Apache Ambari při zobrazení hostitelů a služeb v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943273"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scénář: uživatelské rozhraní Apache Ambari zobrazuje hostitele a služby ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Uživatelské rozhraní Apache Ambari je přístupné, ale uživatelské rozhraní zobrazuje skoro všechny služby jsou mimo provoz, všichni hostitelé, kteří zobrazují ztracený prezenční signál.

## <a name="cause"></a>Příčina

Ve většině scénářů se jedná o problém se serverem Ambari, který neběží na aktivním hlavnímu uzlu. Zkontrolujte, které hlavnímu uzlu je aktivní hlavnímu uzlu, a ujistěte se, že Ambari-server běží na pravé straně. Nespouštějte ručně Ambari-Server, takže služba kontroleru převzetí služeb při selhání zodpovídá za spuštění Ambari-Server na pravé straně hlavnímu uzlu. Restartujte aktivní hlavnímu uzlu, aby se vynutilo převzetí služeb při selhání.

Tyto potíže mohou způsobovat i problémy se sítí. V každém uzlu clusteru zkontrolujte, jestli můžete provést příkaz k zadání příkazů `headnodehost` . Existuje vzácná situace, ke které se nemůže připojit uzel clusteru `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Řešení

Obvykle restartování služby Active hlavnímu uzlu vyřeší tento problém. Pokud ne, obraťte se prosím na tým podpory HDInsight.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]