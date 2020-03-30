---
title: Výkon Apache Spark – Azure HDInsight IO Cache (preview)
description: Přečtěte si o azure HDInsight IO Cache a jak ji používat ke zlepšení výkonu Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494984"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Zlepšete výkon úloh Apache Spark pomocí azure hdinsightové mezipaměti

IO Cache je služba ukládání dat do mezipaměti pro Azure HDInsight, která zlepšuje výkon úloh Apache Spark. IO Cache funguje také s [úlohami Apache TEZ](https://tez.apache.org/) a [Apache Hive,](https://hive.apache.org/) které lze spustit na clusterech [Apache Spark.](https://spark.apache.org/) IO Cache používá komponentu ukládání do mezipaměti s otevřeným zdrojovým kódem nazvanou RubiX. RubiX je místní disková mezipaměť pro použití s moduly pro analýzu velkých objemů dat, které přistupují k datům ze systémů cloudového úložiště. RubiX je jedinečný mezi systémy ukládání do mezipaměti, protože používá jednotky SSD (SSD) místo toho, aby si vyhradil provozní paměť pro účely ukládání do mezipaměti. Služba Mezipaměti IO spouští a spravuje servery metadat RubiX na každém pracovním uzlu clusteru. Také konfiguruje všechny služby clusteru pro transparentní použití mezipaměti RubiX.

Většina ssd discích poskytuje šířku pásma více než 1 GByte za sekundu. Tato šířka pásma, doplněná mezipaměť souborů operačního systému v paměti, poskytuje dostatečnou šířku pásma pro načtení výpočetních strojů pro velká data, jako je Apache Spark. Operační paměť je ponechána k dispozici apache spark pro zpracování úlohy závislé na paměti, jako je například shuffles. Díky výhradnímu využití operační paměti umožňuje Apache Spark dosáhnout optimálního využití prostředků.  

> [!Note]  
> IO Cache aktuálně používá RubiX jako součást ukládání do mezipaměti, ale to se může změnit v budoucích verzích služby. Použijte rozhraní IO Cache a neber žádné závislosti přímo na implementaci RubiX.
>IO Cache je podporována pouze s Azure BLOB Storage v tuto chvíli.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Výhody mezipaměti Azure HDInsight IO

Použití mezipaměti iO poskytuje zvýšení výkonu pro úlohy, které čtou data z úložiště objektů blob Azure.

Nemusíte provádět žádné změny úloh Spark, abyste viděli zvýšení výkonu při používání IO Cache. Když je iO Cache je zakázáno, tento kód Spark `spark.read.load('wasbs:///myfolder/data.parquet').count()`by číst data vzdáleně z Azure Blob Storage: . Když je aktivována iO cache, stejný řádek kódu způsobí, že cache čtení prostřednictvím IO Cache. Při následujících čteních jsou data čtena místně z SSD. Pracovní uzly v clusteru HDInsight jsou vybaveny místně připojenými vyhrazenými jednotkami SSD. HDInsight IO Cache používá tyto místní ssd disky pro ukládání do mezipaměti, což poskytuje nejnižší úroveň latence a maximalizuje šířku pásma.

## <a name="getting-started"></a>Začínáme

Azure HDInsight IO Cache je deaktivovaný ve výchozím nastavení ve verzi Preview. IO Cache je k dispozici v clusterech Azure HDInsight 3.6+ Spark, ve kterých běží Apache Spark 2.3.  Chcete-li aktivovat iO cache na HDInsight 4.0, postupujte takto:

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Vyberte službu **IO Cache** vlevo.

1. Vyberte **akce** **(Akce služby** v HDI 3.6) a **Aktivovat**.

    ![Povolení služby IO Cache v Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Povolení služby IO Cache v Ambari")

1. Potvrďte restartování všech ohrožených služeb v clusteru.

> [!NOTE]  
> I když se indikátor průběhu zobrazuje aktivovaný, iO Cache není ve skutečnosti povolena, dokud nerestartujete ostatní ovlivněné služby.

## <a name="troubleshooting"></a>Řešení potíží
  
Po povolení mezipaměti IO můžete získat chyby místa na disku, které spouštějí úlohy Sparku. K těmto chybám dochází, protože Spark také používá místní diskové úložiště pro ukládání dat během operací míchání. Spark může dojít k nedostatku místa Na SSD, jakmile je povolena IO Cache a místo pro úložiště Spark je snížena. Velikost místa využitého v mezipaměti IO výchozí polovinu celkového místa SSD. Využití místa na disku pro IO Cache je konfigurovatelné v Ambari. Pokud se vám na disku chyby místa, snížit množství místa SSD používané pro IO Cache a restartujte službu. Chcete-li změnit sadu míst pro mezipaměť io, postupujte takto:

1. V Apache Ambari vyberte službu **HDFS** vlevo.

1. Vyberte karty **Configs** a **Advanced** .

    ![Úprava rozšířené konfigurace HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Úprava rozšířené konfigurace HDFS")

1. Posuňte se dolů a rozbalte oblast **Vlastní jádro webu.**

1. Vyhledejte vlastnost **hadoop.cache.data.fullness.percentage**.

1. Změňte hodnotu v poli.

    ![Upravit procento plnosti mezipaměti vi](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Upravit procento plnosti mezipaměti vi")

1. Vpravém horním horním mase vyberte **Uložit.**

1. Vyberte **možnost Restartovat** > **všechny ovlivněné položky**.

    ![Apache Ambari restartuje všechny postižené](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Restartovat všechny ovlivněné")

1. Vyberte **možnost Potvrdit restartovat vše**.

Pokud to nepomůže, zakažte IO Cache.

## <a name="next-steps"></a>Další kroky

Přečtěte si více o IO Cache, včetně výkonnostních měřítek v tomto blogu: [Pracovní místa Apache Spark získají až 9x zrychlení s HDInsight IO Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
