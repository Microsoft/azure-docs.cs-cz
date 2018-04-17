---
title: Řešení potíží YARN pomocí Azure HDInsight | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se práce s Apache Hadoop YARN a Azure HDInsight.
keywords: Azure HDInsight, YARN – nejčastější dotazy, řešení potíží s průvodce, časté otázky
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 338d678fb31a86046b8bc6424d0e8aac2de1a0c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Řešení potíží YARN pomocí Azure HDInsight

Další informace o hlavních problémů a jejich řešení při práci s Apache Hadoop YARN datové části v Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak vytvořit novou frontu YARN v clusteru?


### <a name="resolution-steps"></a>Postup řešení 

Vytvořte novou frontu YARN pomocí následujících kroků v Ambari a pak vyvážit přidělení kapacity mezi všechny fronty. 

V tomto příkladu dvě existující fronty (**výchozí** a **thriftsvr**) obě se změnil z 50 % kapacity na kapacity 25 %, která poskytuje nové kapacity 50 % fronty (spark).
| Fronta | Kapacita | Maximální kapacita |
| --- | --- | --- | --- |
| výchozí | 25 % | 50 % |
| thrftsvr | 25 % | 50 % |
| Spark | 50 % | 50 % |

1. Vyberte **zobrazení Ambari** ikonu a potom vyberte vzoru mřížky. Potom vyberte **správce front YARN**.

    ![Vyberte ikonu zobrazení Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Vyberte **výchozí** fronty.

    ![Vyberte výchozí fronty](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Pro **výchozí** fronty, změňte **kapacity** z 50 % 25 %. Pro **thriftsvr** fronty, změňte **kapacity** 25 %.

    ![Změňte kapacitu na 25 % pro výchozí a thriftsvr fronty](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Chcete-li vytvořit novou frontu, vyberte **přidat fronty**.

    ![Vyberte Přidat fronty](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Název nové fronty.

    ![Název fronty Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Ponechte **kapacity** hodnoty na 50 % a potom vyberte **akce** tlačítko.

    ![Kliknutím na tlačítko akce](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Vyberte **uložte a aktualizujte fronty**.

    ![Vyberte uložte a aktualizujte fronty](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Tyto změny se projeví okamžitě v Uživatelském rozhraní YARN plánovače.

### <a name="additional-reading"></a>Další čtení

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak lze stáhnout protokoly YARN z clusteru?


### <a name="resolution-steps"></a>Postup řešení 

1. Připojte se ke clusteru HDInsight pomocí klienta Secure Shell (SSH). Další informace najdete v tématu [další čtení](#additional-reading-2).

2. Pro zobrazení seznamu všech ID aplikace YARN aplikací, které jsou aktuálně spuštěny, spusťte následující příkaz:

    ```apache
    yarn top
    ```
    ID, které jsou uvedeny v **APPLICATIONID** sloupce. Protokoly z si můžete stáhnout **APPLICATIONID** sloupce.

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

3. Ke stažení protokolů YARN kontejner pro všechny servery aplikace, použijte následující příkaz:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem amlogs.txt. 

4. Ke stažení protokolů YARN kontejner pro pouze nejnovější hlavní aplikace, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem latestamlogs.txt. 

4. Ke stažení protokolů YARN kontejner pro první předlohy dvě aplikace, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Tento příkaz vytvoří soubor protokolu s názvem first2amlogs.txt. 

5. Chcete-li stáhnout všechny protokoly YARN kontejner, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Tento příkaz vytvoří soubor protokolu s názvem logs.txt. 

6. Pokud chcete stáhnout protokol YARN kontejner pro specifický kontejner, použijte následující příkaz:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Tento příkaz vytvoří soubor protokolu s názvem containerlogs.txt.

### <a name="additional-reading-2"></a>Další čtení

- [Připojení k HDInsight (Hadoop) pomocí protokolu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN koncepty a aplikací](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](hdinsight-troubleshoot-guide.md)







