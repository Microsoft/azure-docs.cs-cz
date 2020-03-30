---
title: Poradce při potížích s Apache Spark v Azure HDInsight
description: Získejte odpovědi na časté otázky týkající se práce s Apache Spark a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271938"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Řešení potíží s Apache Sparkem s využitím služby Azure HDInsight

Seznamte se s nejlepšími problémy a jejich předsevzetími při práci s datovými částmi Apache Spark v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Ambari?

Hodnoty konfigurace Spark lze vyladit, `OutofMemoryError` což pomáhá vyhnout se výjimce aplikace Apache Spark. Následující kroky zobrazují výchozí hodnoty konfigurace Spark v Azure HDInsight:

1. Přihlaste se k `https://CLUSTERNAME.azurehdidnsight.net` Ambari pomocí přihlašovacích údajů clusteru. Na úvodní obrazovce se zobrazí přehledový řídicí panel. Mezi hdinsight3.6 a 4.0 existují mírné kosmetické rozdíly.

1. Přejděte na **Spark2** > **Configs**.

    ![Výběr karty Configs](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. V seznamu konfigurací vyberte a rozbalte **vlastní-spark2-výchozí hodnoty**.

1. Vyhledejte nastavení hodnoty, které je třeba upravit, například **spark.executor.memory**. V tomto případě je hodnota **9728 m** příliš vysoká.

    ![Vybrat vlastní výchozí hodnoty jiskry](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Nastavte hodnotu na doporučené nastavení. Pro toto nastavení se doporučuje hodnota **2048m.**

1. Uložte hodnotu a pak uložte konfiguraci. Vyberte **Uložit**.

    ![Změnit hodnotu na 2048 m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Napište poznámku o změnách konfigurace a pak vyberte **Uložit**.

    ![Zadejte poznámku o provedených změnách.](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Budete upozorněni, pokud některé konfigurace vyžadují pozornost. Poznamenejte si položky a pak vyberte **možnost Pokračovat v opačném případě**.

    ![Vyberte přesto pokračovat](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Při každém uložení konfigurace budete vyzváni k restartování služby. Vyberte **restartovat**.

    ![Vybrat restartování](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Potvrďte restartování.

    ![Vybrat potvrdit restartovat vše](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Můžete zkontrolovat procesy, které jsou spuštěny.

    ![Kontrola spuštěných procesů](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Můžete přidat konfigurace. V seznamu konfigurací vyberte **Custom-spark2-defaults**a pak vyberte **Add Property**.

    ![Vybrat vlastnost Přidat](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definujte novou vlastnost. Jednu vlastnost můžete definovat pomocí dialogového okna pro určitá nastavení, jako je například datový typ. Nebo můžete definovat více vlastností pomocí jedné definice na řádek.

    V tomto příkladu je vlastnost **spark.driver.memory** definována s hodnotou **4g**.

    ![Definovat novou vlastnost](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Uložte konfiguraci a restartujte službu, jak je popsáno v krocích 6 a 7.

Tyto změny jsou celého clusteru, ale mohou být přepsány při odeslání úlohy Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí poznámkového bloku Jupyter?

V první buňce poznámkového bloku Jupyter po direktivě **%%configure** zadejte konfigurace Spark v platném formátu JSON. Podle potřeby změňte skutečné hodnoty:

![Přidání konfigurace](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Livy?

Odešlete aplikaci Spark livy pomocí klienta REST, jako je cURL. Použijte příkaz podobný následujícímu. Podle potřeby změňte skutečné hodnoty:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí příkazu spark-submit?

Spusťte jiskru pomocí příkazu podobného následujícímu. Podle potřeby změňte skutečnou hodnotu konfigurací:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Dodatečné čtení

[Odesílání úloh Apache Spark na clusterech HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* [Přehled správy paměti Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Ladění aplikace Spark v clusterech HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
