---
title: Apache Spark výkon úloh s využitím Azure HDInsight v/v cache (Preview)
description: Přečtěte si o službě Azure HDInsight v/v cache a o tom, jak je používat ke zvýšení výkonu Apache Spark.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: f3f171d4dfd3642dc71724afbe084c3fcbf8beaa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091073"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Zvýšení výkonu Apache Spark úloh pomocí Azure HDInsight v/v cache 

Vstupně-výstupní mezipaměť je služba pro ukládání dat do mezipaměti pro Azure HDInsight, která vylepšuje výkon Apache Spark úloh. Vstupně-výstupní mezipaměť funguje taky s [tez](https://tez.apache.org/) a [Apache Hive](https://hive.apache.org/) úlohami, které se dají spouštět v clusterech [Apache Spark](https://spark.apache.org/) . Vstupně-výstupní mezipaměť používá open source komponentu pro ukládání do mezipaměti s názvem RubiX. RubiX je místní mezipaměť disku pro použití s analytickými moduly pro velké objemy dat, které přistupují k datům ze systémů cloudového úložiště. RubiX je jedinečný mezi systémy ukládání do mezipaměti, protože používá jednotky SSD (Solid-State Drive) místo toho, aby vyhradí provozní paměť pro účely ukládání do mezipaměti. Služba v/v cache spouští a spravuje servery metadat RubiX na všech pracovních uzlech clusteru. Také nakonfiguruje všechny služby clusteru pro transparentní používání služby RubiX cache.

Většina SSD poskytuje více než 1 GByte za sekundu šířky pásma. Tato šířka pásma, doplněná mezipamětí souborových souborů v paměti operačního systému, poskytuje dostatečnou šířku pásma pro načtení strojového výpočetního výpočetního prostředí pro velké objemy dat, jako je například Apache Spark. Pracovní paměť je ponechána k dispozici pro Apache Spark pro zpracování velmi závislých úloh, jako jsou například přestupné operace. Výhradní použití operační paměti umožňuje Apache Spark dosáhnout optimálního využití prostředků.  

>[!Note]  
>Vstupně-výstupní mezipaměť aktuálně používá RubiX jako součást pro ukládání do mezipaměti, ale ta se v budoucích verzích této služby může změnit. Použijte prosím rozhraní v/v mezipaměti a neprovádějte žádné závislosti přímo na implementaci RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Výhody Azure HDInsight v/v cache

Použití vstupně-výstupní mezipaměti zajišťuje zvýšení výkonu pro úlohy, které čtou data z Azure Blob Storage.

Nemusíte dělat žádné změny v úlohách Sparku, abyste viděli zvýšení výkonu při použití vstupně-výstupní mezipaměti. Pokud je vstupně-výstupní mezipaměť zakázaná, bude tento kód Spark číst data vzdáleně z `spark.read.load('wasbs:///myfolder/data.parquet').count()`Azure Blob Storage:. Pokud je povolena vstupně-výstupní mezipaměť, stejný řádek kódu způsobí, že je mezipaměť čtena do mezipaměti v/v. V následujících čteních se data čtou místně z SSD. Pracovní uzly v clusteru HDInsight jsou vybavené místně připojenými vyhrazenými jednotkami SSD. Mezipaměť v/v služby HDInsight používá tyto místní SSD pro ukládání do mezipaměti, což poskytuje nejnižší úroveň latence a maximalizuje šířku pásma.

## <a name="getting-started"></a>Začínáme

Služba Azure HDInsight IO cache je ve výchozím nastavení deaktivována ve verzi Preview. Mezipaměť v/v je dostupná v clusterech Azure HDInsight 3.6 + Spark, které spouštějí Apache Spark 2,3.  Pokud chcete aktivovat vstupně-výstupní mezipaměť, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)vyberte svůj cluster HDInsight.

1. Na stránce **Přehled** (ve výchozím nastavení otevřete, když vyberete cluster) vyberte v části **řídicí panely clusteru**položku **Ambari domů** .

1. Na levé straně vyberte službu **mezipaměti v/** v.

1. Vyberte **Akce** a **aktivovat**.

    ![Povolení služby mezipaměti v/v v Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Povolení služby mezipaměti v/v v Ambari")

1. Potvrďte restart všech ovlivněných služeb v clusteru.

>[!NOTE]  
> I když se na indikátoru průběhu zobrazuje aktivováno, mezipaměť v/v není ve skutečnosti povolená, dokud nerestartujete ostatní ovlivněné služby.

## <a name="troubleshooting"></a>Řešení potíží
  
Po povolení vstupně-výstupních operací můžete získat chyby místa na disku při spouštění úloh Spark. K těmto chybám dochází, protože Spark používá k ukládání dat v průběhu operace přerozdělování také úložiště na místním disku. Spark může vyrazit z prostoru SSD, když je povolená vstupně-výstupní mezipaměť a prostor pro úložiště Spark se zmenší. Velikost místa využitého v/v mezipaměti je výchozím nastavením na polovinu celkového prostoru SSD. Využití místa na disku pro vstupně-výstupní mezipaměť je konfigurovatelné v Ambari. Pokud získáte chyby místa na disku, snižte velikost prostoru SSD používaného pro vstupně-výstupní mezipaměť a restartujte službu. Chcete-li změnit nastavení prostoru pro vstupně-výstupní operace, proveďte následující kroky:

1. V Apache Ambari vyberte na levé straně službu **HDFS** .

1. Vyberte karty **Konfigurace** a **Upřesnit** .

    ![Upravit pokročilou konfiguraci HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Upravit pokročilou konfiguraci HDFS")

1. Posuňte se dolů a rozbalte **vlastní oblast základní-lokalita** .

1. Vyhledejte vlastnost **Hadoop. cache. data. Full. PERCENTAGE**.

1. Změňte hodnotu v poli.

    ![Upravit procento plného využití mezipaměti v/] v (./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Upravit procento plného využití mezipaměti v/") v

1. V pravém horním rohu vyberte **Uložit** .

1. Vyberte **znovu** > restartovat**všechny ovlivněné**.

    ![Restartovat všechny ovlivněné](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Restartovat všechny ovlivněné")

1. Vyberte **Potvrdit restartování vše**.

Pokud to nefunguje, zakažte mezipaměť IO.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o mezipaměti v/v, včetně srovnávacích testů výkonu v tomto blogovém příspěvku: [Úlohy Apache Spark pomůžou zrychlit až v 9x s mezipamětí HDInsight v/v.](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
