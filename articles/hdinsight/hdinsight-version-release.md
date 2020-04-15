---
title: Přehled HDInsight 4.0 – Azure
description: Porovnání funkcí a omezení mezi HDInsight 3.6 a HDInsight 4.0 a doporučení k upgradu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383036"
---
# <a name="azure-hdinsight-40-overview"></a>Přehled Azure HDInsight 4.0

Azure HDInsight je jednou z nejoblíbenějších služeb mezi podnikovými zákazníky pro Apache Hadoop a Apache Spark. HDInsight 4.0 je cloudová distribuce komponent Apache Hadoop. Tento článek obsahuje informace o nejnovější verzi Azure HDInsight a pokyny k upgradu.

## <a name="whats-new-in-hdinsight-40"></a>Co je nového v HDInsight 4.0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 a analytické zpracování s nízkou latencí

Apache Hive analytické zpracování s nízkou latencí (LLAP) používá servery s trvalými dotazy a ukládání do mezipaměti v paměti. Tento proces poskytuje rychlé výsledky dotazů SQL na data ve vzdáleném cloudovém úložišti. Hive LLAP používá sadu trvalých daemonů, které spouštějí fragmenty dotazů Hive. Provádění dotazu s LLAP se podobá Hive bez LLAP, přičemž úlohy pracovních procesů neběží v kontejnerech, ale uvnitř procesů démonů LLAP.

Mezi výhody LLAP pro Hive patří:

* Schopnost provést hloubkovou analýzu SQL bez obětování výkonu a přizpůsobivosti. Například komplexní spojení, poddotazy, funkce okna, řazení, uživatelem definované funkce a komplexní agregace.

* Interaktivní dotazy na data ve stejném úložišti, kde se data připravují, takže odpadá nutnost přesouvat data z úložiště do jiného modulu pro analytické zpracování.

* Výsledky dotazu ukládání do mezipaměti umožňuje znovu použít dříve vypočítané výsledky dotazu. Tato mezipaměť šetří čas a prostředky strávené spuštěním úloh clusteru požadovaných pro dotaz.

### <a name="hive-dynamic-materialized-views"></a>Dynamická materializovaná zobrazení Hive

Hive nyní podporuje dynamické zhmotněné pohledy nebo předvýpočty relevantních souhrnů. Zobrazení urychlují zpracování dotazů v datových skladech. Materializovaná zobrazení mohou být uložena nativně v Hive a bez problémů využívat akceleraci LLAP.

### <a name="hive-transactional-tables"></a>Transakční tabulky Hive

HDI 4.0 obsahuje Apache Hive 3. Hive 3 vyžaduje nedělitost, konzistence, izolace a trvanlivost dodržování předpisů pro transakční tabulky, které žijí ve skladu Hive. Tabulky a tabulková data dodržující model ACID jsou používána a spravována produktem Hive. Data v tabulkách vytvoření, načtení, aktualizace a odstranění (CRUD) musí být ve formátu souboru Optimalizovaný sloupec řádku (ORC). Tabulky pouze pro vložení podporují všechny formáty souborů.

* V modelu ACID v2 došlo k navýšení výkonu jak u formátu úložiště, tak u prováděcího modulu.

* V modelu ACID je standardně povolená úplná podpora aktualizací dat.

* Vylepšené funkce modelu ACID umožňují aktualizaci a odstranění na úrovni řádků.

* Nedochází k žádnému plýtvání výkonem.

* Nevyžaduje se žádné rozdělení do kbelíků.

* Spark dokáže číst a zapisovat do tabulek Hive modelu ACID přes Hive Warehouse Connector.

Přečtěte si další informace o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark získává přes Hive Warehouse Connector aktualizovatelné tabulky a transakce ACID. Hive Warehouse Connector umožňuje registrovat transakční tabulky Hive jako externí tabulky ve Sparku a zpřístupnit tak plně transakční funkčnost. Předchozí verze podporovaly jen manipulaci s oddílem tabulky. Hive Warehouse Connector také podporuje datové rámce datových proudů.  Tento proces streamuje čtení a zápisy do transakčních a streamovaných tabulek Hive ze Sparku.

Exekutory Spark se dokážou připojit přímo k procesům démonů Hive LLAP a načítat a aktualizovat data transakčním způsobem, takže Hive má nad daty pořád kontrolu.

Apache Spark v HDInsight 4.0 podporuje následující scénáře:

* Spuštění tréninku modelu strojového učení na stejné transakční tabulce použité k vykazování
* Bezpečné přidávání sloupců ze Spark ML do tabulky Hive pomocí transakcí ACID
* Spuštění streamovací úlohy Spark v kanálu změn ze streamovací tabulky Hive
* Vytváření souborů ORC přímo z úlohy strukturovaného streamu Sparku

Už se nemusíte obávat náhodného pokusu o přístup k transakčním tabulkám Hive přímo ze Sparku. Výsledkem jsou nekonzistentní výsledky, duplicitní data nebo poškození dat. V HDInsight 4.0 jsou tabulky Spark a Hive uloženy v samostatných Metastores. K explicitní registraci transakčních tabulek Hive jako externích tabulek Sparku použijte Hive Data Warehouse Connector.

Přečtěte si další informace o [Apache Sparku](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apač Oozie

Apache Oozie 4.3.1 je součástí HDI 4.0 s následujícími změnami:

* Oozie už nespouští akce Hive. Rozhraní příkazového řádku Hive bylo odebráno a nahrazeno za BeeLine.

* Zahrnutím vylučovacího vzoru do souboru **job.properties** můžete z knihovny sdílení vyloučit nechtěné závislosti.

Přečtěte si další informace o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Jak upgradovat na HDInsight 4.0

Před implementací nejnovější verze v provozním prostředí důkladně otestujte součásti. HdInsight 4.0 je k dispozici pro zahájení procesu upgradu. HDInsight 3.6 je výchozí možnost, aby se zabránilo náhodnému neštěstí.

Neexistuje žádná podporovaná cesta upgradu z předchozích verzí HDInsightu na HDInsight 4.0. Vzhledem k tomu, že metastore a blob datové formáty se změnily, 4.0 není kompatibilní s předchozími verzemi. Je důležité, abyste své nové prostředí HDInsight 4.0 oddělili od aktuálního produkčního prostředí. Pokud nasadíte HDInsight 4.0 do aktuálního prostředí, vaše Metastore bude trvale upgradován.  

## <a name="limitations"></a>Omezení

* HDInsight 4.0 nepodporuje MapReduce pro Apache Hive. Místo toho použijte Apache Tez. Přečtěte si další informace o [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 nepodporuje Apache Storm.
* Zobrazení hive již není v hdinsightu 4.0 k dispozici.
* Překladač prostředí v Apache Zeppelin není podporován v clusterech Spark a Interactive Query.
* V clusteru Spark-LLAP není možné *zakázat* LLAP. Llap můžete vypnout pouze.
* Azure Data Lake Storage Gen2 nemůže uložit poznámkové bloky Jupyter v clusteru Spark.

## <a name="next-steps"></a>Další kroky

* [Dokumentace ke službě HDInsight](index.yml)
* [Poznámky](hdinsight-release-notes.md)
