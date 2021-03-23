---
title: Apache Spark výkon – Azure HDInsight v/v cache (Preview)
description: Přečtěte si o službě Azure HDInsight v/v cache a o tom, jak je používat ke zvýšení výkonu Apache Spark.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 9df585c102e2c7307e949e38b6b69147372c38dd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866297"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Zvýšení výkonu Apache Spark úloh pomocí Azure HDInsight v/v cache

Vstupně-výstupní mezipaměť je služba pro ukládání dat do mezipaměti pro Azure HDInsight, která vylepšuje výkon Apache Spark úloh. Vstupně-výstupní mezipaměť funguje taky s [tez](https://tez.apache.org/) a [Apache Hive](https://hive.apache.org/) úlohami, které se dají spouštět v clusterech [Apache Spark](https://spark.apache.org/) . Vstupně-výstupní mezipaměť používá open source komponentu pro ukládání do mezipaměti s názvem RubiX. RubiX je místní mezipaměť disku pro použití s analytickými moduly pro velké objemy dat, které přistupují k datům ze systémů cloudového úložiště. RubiX je jedinečný mezi systémy pro ukládání do mezipaměti, protože místo toho, aby vyhradí provozní paměť pro účely ukládání do mezipaměti, používá Solid-State jednotky (SSD). Služba v/v cache spouští a spravuje servery metadat RubiX na všech pracovních uzlech clusteru. Také nakonfiguruje všechny služby clusteru pro transparentní používání služby RubiX cache.

Většina SSD poskytuje více než 1 GByte za sekundu šířky pásma. Tato šířka pásma, doplněná mezipamětí souborových souborů v paměti operačního systému, poskytuje dostatečnou šířku pásma pro načtení strojového výpočetního výpočetního prostředí pro velké objemy dat, jako je například Apache Spark. Pracovní paměť je ponechána k dispozici pro Apache Spark pro zpracování velmi závislých úloh, jako jsou například přestupné operace. Výhradní použití operační paměti umožňuje Apache Spark dosáhnout optimálního využití prostředků.  

> [!Note]  
> Vstupně-výstupní mezipaměť aktuálně používá RubiX jako součást pro ukládání do mezipaměti, ale ta se v budoucích verzích této služby může změnit. Použijte prosím rozhraní v/v mezipaměti a neprovádějte žádné závislosti přímo na implementaci RubiX.
>Mezipaměť v/v je v tuto chvíli podporována jenom s úložištěm Azure BLOB Storage.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Výhody Azure HDInsight v/v cache

Použití vstupně-výstupní mezipaměti zajišťuje zvýšení výkonu pro úlohy, které čtou data z Azure Blob Storage.

Nemusíte dělat žádné změny v úlohách Sparku, abyste viděli zvýšení výkonu při použití vstupně-výstupní mezipaměti. Pokud je vstupně-výstupní mezipaměť zakázaná, bude tento kód Spark číst data vzdáleně z Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()` . Pokud je povolena vstupně-výstupní mezipaměť, stejný řádek kódu způsobí, že je mezipaměť čtena do mezipaměti v/v. V následujících čteních se data čtou místně z SSD. Pracovní uzly v clusteru HDInsight jsou vybavené místně připojenými vyhrazenými jednotkami SSD. Mezipaměť v/v služby HDInsight používá tyto místní SSD pro ukládání do mezipaměti, což poskytuje nejnižší úroveň latence a maximalizuje šířku pásma.

## <a name="getting-started"></a>Začínáme

Služba Azure HDInsight IO cache je ve výchozím nastavení deaktivována ve verzi Preview. Mezipaměť v/v je dostupná v clusterech Azure HDInsight 3.6 + Spark, které spouštějí Apache Spark 2,3.  Pokud chcete aktivovat vstupně-výstupní mezipaměť ve službě HDInsight 4,0, proveďte následující kroky:

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. Na levé straně vyberte službu **mezipaměti v/** v.

1. Vyberte **Akce** (**Akce služby** v HDI 3,6) a **aktivujte**.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png " alt-text="Povolení služby mezipaměti v/v v Ambari" border="true":::

1. Potvrďte restart všech ovlivněných služeb v clusteru.

> [!NOTE]  
> I když se na indikátoru průběhu zobrazuje aktivováno, mezipaměť v/v není ve skutečnosti povolená, dokud nerestartujete ostatní ovlivněné služby.

## <a name="troubleshooting"></a>Řešení potíží
  
Po povolení vstupně-výstupních operací můžete získat chyby místa na disku při spouštění úloh Spark. K těmto chybám dochází, protože Spark používá k ukládání dat v průběhu operace přerozdělování také úložiště na místním disku. Spark může vyrazit z prostoru SSD, když je povolená vstupně-výstupní mezipaměť a prostor pro úložiště Spark se zmenší. Velikost místa využitého v/v mezipaměti je výchozím nastavením na polovinu celkového prostoru SSD. Využití místa na disku pro vstupně-výstupní mezipaměť je konfigurovatelné v Ambari. Pokud získáte chyby místa na disku, snižte velikost prostoru SSD používaného pro vstupně-výstupní mezipaměť a restartujte službu. Chcete-li změnit nastavení prostoru pro vstupně-výstupní operace, proveďte následující kroky:

1. V Apache Ambari vyberte na levé straně službu **HDFS** .

1. Vyberte karty **Konfigurace** a **Upřesnit** .

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png " alt-text="Upravit pokročilou konfiguraci HDFS" border="true":::

1. Posuňte se dolů a rozbalte **vlastní oblast základní-lokalita** .

1. Vyhledejte vlastnost **Hadoop. cache. data. Full. PERCENTAGE**.

1. Změňte hodnotu v poli.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png " alt-text="Upravit procento plného využití mezipaměti v/v" border="true":::

1. V pravém horním rohu vyberte **Uložit** .

1. Vyberte **znovu** restartovat  >  **všechny ovlivněné**.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png " alt-text="Apache Ambari restartuje všechny ovlivněné" border="true":::

1. Vyberte **Potvrdit restartování vše**.

Pokud to nepomůže, zakažte mezipaměť IO.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o mezipaměti v/v, včetně srovnávacích testů výkonu v tomto blogovém příspěvku: [Apache Spark úlohy pomůžou zrychlit až v 9x pomocí vstupně-výstupní mezipaměti HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
