---
title: Poradce při potížích s yarnv Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s Apache Hadoop YARN a Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272198"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hadoop YARN pomocí služby Azure HDInsight

Získejte informace o hlavních problémech a jejich předsevzetí při práci s datovými částmi Apache Hadoop YARN v Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak vytvořím novou frontu YARN v clusteru?

### <a name="resolution-steps"></a>Postup řešení

Pomocí následujících kroků v Ambari vytvořte novou frontu YARN a pak vyvažte přidělení kapacity mezi všechny fronty.

V tomto příkladu jsou dvě existující fronty (**výchozí** a **sekáči**) obě změněny z 50% kapacity na kapacitu 25%, což dává nové frontě (jiskra) 50% kapacitu.

| Fronta | Kapacita | Maximální kapacita |
| --- | --- | --- |
| default | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| Spark | 50 % | 50 % |

1. Vyberte ikonu **Ambari Zobrazení** a pak vyberte vzorek mřížky. Dále vyberte **Správce front yarn**.

    ![Apache Ambari palubní deska YARN Správce front](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Vyberte **výchozí** frontu.

    ![Apache Ambari YARN vybrat výchozí frontu](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Pro **výchozí** frontu změňte **kapacitu** z 50 % na 25 %. U fronty **spořivosti** změňte **kapacitu** na 25 %.

    ![Změna kapacity na 25 % pro výchozí a sekáčové fronty](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Chcete-li vytvořit novou frontu, vyberte **přidat frontu**.

    ![Řídicí panel Apache Ambari YARN Přidat frontu](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Pojmenujte novou frontu.

    ![Apache Ambari YARN název řídicího panelu Fronta](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Ponechte hodnoty **kapacity** na 50 % a pak vyberte tlačítko **Akce.**

    ![Apache Ambari YARN vybrat akci](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Vyberte **možnost Uložit a aktualizovat fronty**.

    ![Vybrat uložit a aktualizovat fronty](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Tyto změny jsou viditelné okamžitě na yarn plánovač ui.

### <a name="additional-reading"></a>Dodatečné čtení

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak lze stáhnout protokoly YARN z clusteru?

### <a name="resolution-steps"></a>Postup řešení

1. Připojte se ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace naleznete v [tématu Další čtení](#additional-reading-2).

1. Chcete-li vypsat všechna ID aplikací aplikací YARN, které jsou aktuálně spuštěny, spusťte následující příkaz:

    ```apache
    yarn top
    ```

    ID jsou uvedeny ve **sloupci APPLICATIONID.** Protokoly si můžete stáhnout ze sloupce **APPLICATIONID.**

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Chcete-li stáhnout protokoly kontejnerů YARN pro všechny předlohy aplikací, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem amlogs.txt.

1. Chcete-li stáhnout protokoly kontejnerů YARN pouze pro nejnovější hlavní server aplikace, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem latestamlogs.txt.

1. Chcete-li stáhnout protokoly kontejnerů YARN pro první dvě hlavní servery aplikací, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem first2amlogs.txt.

1. Chcete-li stáhnout všechny protokoly kontejnerů YARN, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem logs.txt.

1. Chcete-li stáhnout protokol kontejneru YARN pro konkrétní kontejner, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Dodatečné čtení

- [Připojení k HDInsight (Apache Hadoop) pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN koncepty a aplikace](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
