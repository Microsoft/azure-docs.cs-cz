---
title: Uživatelské rozhraní Apache Ambari zobrazuje hostitele a služby ve službě Azure HDInsight.
description: Řešení problémů s uživatelským rozhraním Apache Ambari při zobrazení hostitelů a služeb v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895642"
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

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
