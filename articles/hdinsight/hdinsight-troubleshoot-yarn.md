---
title: Řešení potíží s YARN v Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s Apache Hadoop YARN a Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: d5582038c35ba3b599be89b7b7939e644d55ea78
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408826"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hadoop YARN pomocí Azure HDInsight

Další informace o nejčastější problémy a jejich řešení při práci s datovými částmi Apache Hadoop YARN v Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak vytvořit novou frontu YARN v clusteru?

### <a name="resolution-steps"></a>Postup řešení 

Pomocí následujícího postupu Ambari vytvořit novou frontu YARN a potom vyrovnávat přidělení kapacity mezi všechny fronty. 

V tomto příkladu dvě existující fronty (**výchozí** a **thriftsvr**) jsou změněny z 50 % kapacity ke kapacitě 25 %, která poskytuje novou kapacitu 50 % fronty (spark).
| Fronta | Kapacita | Maximální kapacita |
| --- | --- | --- | --- |
| default | 25 % | 50% |
| thrftsvr | 25 % | 50% |
| Spark | 50% | 50% |

1. Vyberte **zobrazení Ambari** ikonu a pak vyberte vzor mřížky. V dalším kroku vyberte **správce fronty YARN**.

    ![Vyberte ikonu zobrazení Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Vyberte **výchozí** fronty.

    ![Vyberte výchozí fronta](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Pro **výchozí** fronty, změnit **kapacity** z 50 %, 25 %. Pro **thriftsvr** fronty, změnit **kapacity** 25 %.

    ![Změňte kapacitu na 25 % pro výchozí web a thriftsvr fronty](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Chcete-li vytvořit novou frontu, vyberte **přidat frontu**.

    ![Výběr možnosti Přidat frontu](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Pojmenujte novou frontu.

    ![Název fronty Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Nechte **kapacity** hodnoty na 50 % a pak vyberte **akce** tlačítko.

    ![Výběr akce tlačítka](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Vyberte **uložte a aktualizujte stránku fronty**.

    ![Vyberte Uložit a aktualizovat fronty](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Tyto změny se projeví okamžitě v Uživatelském rozhraní YARN plánovače.

### <a name="additional-reading"></a>Další čtení

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak stáhnout protokoly YARNU z clusteru?


### <a name="resolution-steps"></a>Postup řešení 

1. Připojení ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [další čtení](#additional-reading-2).

2. Chcete-li vypsat všechna ID aplikace, které jsou aktuálně spuštěné aplikace YARN, spusťte následující příkaz:

    ```apache
    yarn top
    ```
    ID jsou uvedeny v **APPLICATIONID** sloupce. Umožňuje stažení protokolů z **APPLICATIONID** sloupce.

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

3. Chcete-li stáhnout protokoly kontejneru YARN pro všechny servery aplikace, použijte následující příkaz:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem amlogs.txt. 

4. Stáhnout protokoly kontejneru YARN pro nejnovější základní aplikaci, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem latestamlogs.txt. 

4. Stáhnout protokoly kontejneru YARN pro první dvě aplikace hlavních serverů, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Tento příkaz vytvoří soubor protokolu s názvem first2amlogs.txt. 

5. Chcete-li stáhnout všechny protokoly kontejneru YARN, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem logs.txt. 

6. Pokud chcete stáhnout protokol YARN kontejneru pro konkrétní kontejner, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Tento příkaz vytvoří soubor protokolu s názvem containerlogs.txt.

### <a name="additional-reading-2"></a>Další čtení

- [Připojení k HDInsight (Apache Hadoop) pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN koncepty a aplikace](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](hdinsight-troubleshoot-guide.md)
