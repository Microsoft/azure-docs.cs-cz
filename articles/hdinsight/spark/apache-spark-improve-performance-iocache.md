---
title: Zlepšení výkonu úlohy Apache Sparku s využitím Azure HDInsight vstupně-výstupních operací Cache (Ukázková verze)
description: Přečtěte si o mezipaměti Azure HDInsight vstupně-výstupní operace a jak ho použít ke zlepšení výkonu Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 204a3230c782279b0cad4194e97aa4014c940635
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608490"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache-preview"></a>Zlepšení výkonu úlohy Apache Sparku s využitím Azure HDInsight vstupně-výstupních operací Cache (Ukázková verze)

Mezipaměti vstupně-výstupních operací je služba pro ukládání do mezipaměti dat pro Azure HDInsight, který zlepšuje výkon úloh Apache Spark. V/v mezipaměti funguje taky s [Apache TEZ](https://tez.apache.org/) a [Apache Hive](https://hive.apache.org/) úlohy, které můžou běžet na [Apache Spark](https://spark.apache.org/) clustery. V/v mezipaměti používá open sourcové mezipaměti komponenta s názvem RubiX. RubiX je místní disk mezipaměti pro použití s moduly analýzy velkých objemů dat, které přístup k datům z cloudových úložných systémech. RubiX je jedinečný mezi systémy, ukládání do mezipaměti, protože používá Solid-State disky (SSD) namísto provozní rezerva paměti pro účely ukládání do mezipaměti. Služba mezipaměti vstupně-výstupních operací spouští a spravuje RubiX metadat servery na každý pracovní uzel clusteru. Nakonfiguruje taky všechny služby clusteru pro transparentní použití RubiX mezipaměti.

Většina disky SSD poskytují více než 1 GByte za sekundu šířky pásma. Tuto šířku pásma, doplněných mezipaměť v paměti souborů operačního systému, poskytuje dostatečnou šířku pásma pro načtení velkého objemu dat výpočetní moduly pro zpracování, jako je Apache Spark. Provozní paměť zůstane k dispozici pro Apache Spark pro zpracování úloh silně závislé na paměti, jako je například podle okolí posouvá. Výhradní použití operační paměti umožňuje Apache Spark, abyste dosáhli optimální využití.  

>[!Note]  
>V/v mezipaměti v současnosti využívá RubiX jako součást ukládání do mezipaměti, ale může změnit v budoucích verzích služby. Použití mezipaměti vstupně-výstupních operací rozhraní a přímo na implementaci RubiX nevyřídí všechny závislosti.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Výhody služby Azure HDInsight vstupně-výstupní mezipaměti

Použití mezipaměti vstupně-výstupní operace poskytuje zvýšení výkonu pro úlohy, které načítají data z úložiště objektů Blob v Azure.

Nemusíte dělat žádné změny vaší Sparkových úloh zobrazíte zvýšení výkonu při používání řešení mezipaměť Microsoft vstupně-výstupních operací. Při vstupně-výstupní mezipaměť je zakázaná, tento kód Spark bude číst data vzdáleně z Azure Blob Storage: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Při aktivaci mezipaměti vstupně-výstupních operací na stejný řádek kódu způsobí, že v mezipaměti pro čtení do mezipaměti vstupně-výstupních operací. Na následující čtení data načítají místně z SSD. Pracovní uzly v clusteru HDInsight jsou vybaveny místně připojených, vyhrazené jednotky SSD. Mezipaměť vstupně-výstupních operací HDInsight používá tyto místní jednotky SSD pro ukládání do mezipaměti, který představuje nejnižší úroveň latence a maximalizuje šířky pásma.

## <a name="getting-started"></a>Začínáme

Azure HDInsight v/v mezipaměti je deaktivováno ve výchozím nastavení ve verzi preview. V/v mezipaměti je k dispozici v clusterech Azure HDInsight 3.6 + Spark, které běží Apache Spark 2.3.  K aktivaci mezipaměti vstupně-výstupních operací, postupujte takto:

1. Vyberte svůj cluster HDInsight v [na webu Azure portal](https://portal.azure.com).

1. V **přehled** stránky (otevře ve výchozím nastavení při výběru clusteru) vyberte **Ambari domovské** pod **řídicí panely clusteru**.

1. Vyberte **mezipaměti vstupně-výstupních operací** služby na levé straně.

1. Vyberte **akce** a **aktivovat**.

    ![Povolení služby mezipaměti vstupně-výstupní operace v Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "povolení služby mezipaměti vstupně-výstupní operace v Ambari")

1. Potvrďte restart všechny ovlivněné služby v clusteru.

>[!NOTE]  
> I když indikátor průběhu vám ukáže aktivovaná, nebude ve skutečnosti povolená mezipaměti vstupně-výstupních operací, až po restartování ovlivněné služby.

## <a name="troubleshooting"></a>Řešení potíží
  
Může docházet k chybám místa na disku, spouštění úloh Spark po povolení mezipaměti vstupně-výstupních operací. Vzhledem k tomu Spark také používá pro ukládání dat během přesouvání operace úložiště na místním disku dojde k těmto chybám. Spark nemusí mít dostatek místa na discích SSD, jakmile se vstupně-výstupní mezipaměť je povolená a snižuje prostor pro Spark úložiště. Množství místa, které používají výchozí hodnoty mezipaměti vstupně-výstupních operací na polovinu celkového místa na discích SSD. Využití místa na disku pro v/v mezipaměti je možné konfigurovat ve Ambari. Pokud dojde k chybám místa na disku, snížit objem místa na discích SSD použitých pro mezipaměť v/v a restartujte službu. Chcete-li změnit místo, nastavte pro v/v mezipaměti, proveďte následující kroky:

1. V Apache Ambari, vyberte **HDFS** služby na levé straně.

1. Vyberte **Configs** a **Upřesnit** karty.

    ![Upravit HDFS pokročilá konfigurace](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "upravit HDFS pokročilá konfigurace")

1. Posuňte se dolů a rozbalte **základního webu vlastní** oblasti.

1. Vyhledejte vlastnost **hadoop.cache.data.fullness.percentage**.

1. Změňte hodnotu v poli.

    ![Upravit vstupně-výstupní mezipaměti vyplnění procento](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "upravit procento vyplnění v/v mezipaměti")

1. Vyberte **Uložit** v pravém horním rohu.

1. Vyberte **restartovat** > **restartování všech ovlivněných**.

    ![Restartování všech ovlivněných](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "restartování všech ovlivněných")

1. Vyberte **potvrďte restartování všech**.

Pokud to nepomůže, zakažte mezipaměti vstupně-výstupních operací.

## <a name="next-steps"></a>Další kroky

- Další informace o v/v mezipaměti, včetně srovnávacího testu výkonu v tomto blogovém příspěvku: [Apache Spark úloh získat až 9 x zrychlit s HDInsight v/v mezipaměti](https://azure.microsoft.com/en-us/blog/apache-spark-speedup-with-hdinsight-io-cache/)
