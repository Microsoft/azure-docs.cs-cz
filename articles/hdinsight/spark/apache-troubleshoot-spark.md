---
title: Řešení potíží s Apache Spark ve službě Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s Apache Spark a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271938"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Řešení potíží s Apache Sparku s využitím Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešení při práci s Apache Sparkmi datovými částmi v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Apache Ambari v clusterech?

Hodnoty konfigurace Sparku je možné vyladit tak, aby se Apache Spark aplikace `OutofMemoryError` výjimka. Následující kroky ukazují výchozí hodnoty konfigurace Sparku ve službě Azure HDInsight:

1. Přihlaste se k Ambari na `https://CLUSTERNAME.azurehdidnsight.net` s přihlašovacími údaji clusteru. Úvodní obrazovka zobrazuje řídicí panel přehled. Mezi HDInsight 3,6 a 4,0 jsou mírné rozdíly v kosmetických rozdílech.

1. Přejděte na **Spark2** > **Konfigurace**.

    ![Vyberte kartu Konfigurace](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. V seznamu konfigurací vyberte a rozbalte **Custom-spark2-Defaults**.

1. Vyhledejte nastavení hodnoty, které je potřeba upravit, jako je **Spark. exekutor. Memory**. V tomto případě je hodnota **9728m** příliš vysoká.

    ![Vyberte výchozí nastavení vlastní spark](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Nastavte hodnotu na doporučené nastavení. Pro toto nastavení se doporučuje hodnota **2048m** .

1. Uložte hodnotu a pak konfiguraci uložte. Vyberte **Save** (Uložit).

    ![Změňte hodnotu na 2 048 m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Zapište si poznámku týkající se změn konfigurace a pak vyberte **Uložit**.

    ![Zadejte poznámku o provedené změny](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Pokud žádné konfigurace, které je potřeba věnovat pozornost, se zobrazí oznámení. Poznamenejte si položky a pak vyberte **pokračovat**.

    ![Vyberte přesto pokračovat](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Pokaždé, když je uložen na konfiguraci, budete vyzváni k restartování služby. Vyberte **restartovat**.

    ![Vyberte možnost restartování](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Potvrďte restartování.

    ![Výběr možnosti potvrdit restartujte všechny](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Můžete zkontrolovat procesy, které jsou spuštěny.

    ![Zkontrolujte spuštěné procesy](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Můžete přidat konfigurace. V seznamu konfigurací vyberte **Custom-spark2-Defaults**a pak vyberte **Přidat vlastnost**.

    ![Výběr možnosti Přidat vlastnost](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definování nové vlastnosti. Pomocí dialogového okna pro konkrétní nastavení, jako je datový typ, můžete definovat jednu vlastnost. Nebo můžete definovat více vlastností pomocí jednu definici na řádek.

    V tomto příkladu je vlastnost **Spark. Driver. Memory** definovaná s hodnotou **4G**.

    ![Definovat nové vlastnosti](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Uložte konfiguraci a potom restartujte službu, jak je popsáno v kroku 6 a 7.

Tyto změny jsou platné pro celý cluster, ale lze přepsat při odesílání úlohy Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Sparku s využitím Poznámkový blok Jupyter v clusterech?

V první buňce poznámkového bloku Jupyter po direktivě **%% Configure** zadejte konfigurace Sparku v platném formátu JSON. Podle potřeby změňte skutečnými hodnotami:

![Přidat konfiguraci](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak nakonfigurovat aplikaci Apache Spark pomocí Apache Livy v clusterech?

Odeslání aplikace Spark na Livy pomocí klienta REST jako cURL. Použijte příkaz podobný následujícímu. Podle potřeby změňte skutečnými hodnotami:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak nakonfigurovat aplikaci s použitím skriptu spark-submit Apache Spark v clusterech?

Spusťte prostředí sparku s využitím příkaz podobný následujícímu. Podle potřeby změňte skutečné hodnoty konfigurace:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Další čtení

[Apache Spark odesílání úloh v clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)

* [Ladění aplikace Spark v clusterech HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
