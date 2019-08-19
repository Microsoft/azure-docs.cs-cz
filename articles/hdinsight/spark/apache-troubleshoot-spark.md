---
title: Řešení potíží s Spark v Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s Apache Spark a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: c88136fee7a75b8f3b8e504b1ff1e6673a31bcf7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543173"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Řešení potíží s Apache Sparku s využitím Azure HDInsight

Další informace o nejčastější problémy a jejich řešení při práci s [Apache Spark](https://spark.apache.org/) datové části v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Apache Ambari v clusterech?

### <a name="resolution-steps"></a>Postup řešení

Hodnoty konfigurace Sparku je možné vyladit tak, aby nedošlo k výjimce Apache Spark aplikace OutofMemoryError. Následující kroky ukazují výchozí hodnoty konfigurace Sparku ve službě Azure HDInsight: 

1. Vyberte v seznamu clusterů **Spark2**.

    ![Vyberte cluster ze seznamu](./media/apache-troubleshoot-spark/update-config-1.png)

2. Vyberte **Configs** kartu.

    ![Vyberte kartu Konfigurace](./media/apache-troubleshoot-spark/update-config-2.png)

3. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**.

    ![Vyberte výchozí nastavení vlastní spark](./media/apache-troubleshoot-spark/update-config-3.png)

4. Vyhledejte nastavení hodnoty, které je potřeba upravit, jako například **spark.executor.memory**. V takovém případě hodnota **4608m** je příliš vysoká.

    ![Vyberte pole spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Nastavte hodnotu na doporučené nastavení. Hodnota **2048m** se doporučuje pro toto nastavení.

    ![Změňte hodnotu na 2 048 m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Uložte hodnotu a pak konfiguraci uložte. Na panelu nástrojů vyberte **Uložit**.

    ![Uložte nastavení a konfigurace](./media/apache-troubleshoot-spark/update-config-6a.png)

    Pokud žádné konfigurace, které je potřeba věnovat pozornost, se zobrazí oznámení. Položky a potom vyberte **i přesto pokračovat**. 

    ![Vyberte přesto pokračovat](./media/apache-troubleshoot-spark/update-config-6b.png)

    Zápis poznámky o změnách konfigurace a pak vyberte **Uložit**.

    ![Zadejte poznámku o provedené změny](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Pokaždé, když je uložen na konfiguraci, budete vyzváni k restartování služby. Vyberte **restartovat**.

    ![Vyberte možnost restartování](./media/apache-troubleshoot-spark/update-config-7a.png)

    Potvrďte restartování.

    ![Výběr možnosti potvrdit restartujte všechny](./media/apache-troubleshoot-spark/update-config-7b.png)

    Můžete zkontrolovat procesy, které jsou spuštěny.

    ![Zkontrolujte spuštěné procesy](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Můžete přidat konfigurace. Vyberte v seznamu konfigurací **výchozí hodnoty vlastní spark2**a pak vyberte **přidat vlastnost**.

    ![Výběr možnosti Přidat vlastnost](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definování nové vlastnosti. Pomocí dialogového okna pro konkrétní nastavení, jako je datový typ, můžete definovat jednu vlastnost. Nebo můžete definovat více vlastností pomocí jednu definici na řádek. 

    V tomto příkladu **spark.driver.memory** vlastnost je definována s hodnotou **4g**.

    ![Definovat nové vlastnosti](./media/apache-troubleshoot-spark/update-config-9.png)

10. Uložte konfiguraci a potom restartujte službu, jak je popsáno v kroku 6 a 7.

Tyto změny jsou platné pro celý cluster, ale lze přepsat při odesílání úlohy Spark.

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Poznámkový blok Jupyter v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Pokud chcete zjistit, které konfigurace Sparku je potřeba nastavit, a určit jejich hodnoty, přečtěte si téma Co způsobuje výjimku Apache Spark OutofMemoryError aplikace.

2. V první buňky Poznámkový blok Jupyter po **%% konfigurace** směrnice, určení konfigurací Sparku v platném formátu JSON. Podle potřeby změňte skutečnými hodnotami:

    ![Přidat konfiguraci](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Spark pomocí Apache Livy v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Pokud chcete zjistit, které konfigurace Sparku je potřeba nastavit, a určit jejich hodnoty, přečtěte si téma Co způsobuje výjimku Apache Spark OutofMemoryError aplikace. 

2. Odeslání aplikace Spark na Livy pomocí klienta REST jako cURL. Použijte příkaz podobný následujícímu. Podle potřeby změňte skutečnými hodnotami:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak nakonfigurovat aplikaci s použitím skriptu spark-submit Apache Spark v clusterech?

### <a name="resolution-steps"></a>Postup řešení

1. Pokud chcete zjistit, které konfigurace Sparku je potřeba nastavit, a určit jejich hodnoty, přečtěte si téma Co způsobuje výjimku Apache Spark OutofMemoryError aplikace.

2. Spusťte prostředí sparku s využitím příkaz podobný následujícímu. Podle potřeby změňte skutečné hodnoty konfigurace: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Další čtení

[Odeslání úlohy Apache Spark v clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)

* [Ladění aplikace Spark v clusterech HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
