---
title: Přehled HDInsight 4.0 – Azure
description: Porovnání funkcí a omezení mezi HDInsight 3.6 a HDInsight 4.0 a doporučení k upgradu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 0463e3297bbb2fda50adfeefaa89f0a7a3ef8b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72901527"
---
# <a name="azure-hdinsight-40-overview"></a>Přehled Azure HDInsight 4.0

Azure HDInsight je jednou z nejoblíbenějších služeb mezi podnikovými zákazníky pro analýzy Apache Hadoop a Apache Spark s otevřeným zdrojovým kódem v Azure. HDInsight 4.0 je cloudová distribuce komponent Apache Hadoop. Tento článek obsahuje informace o nejnovější verzi Azure HDInsight a pokyny k upgradu.

## <a name="whats-new-in-hdinsight-40"></a>Co je nového v HDInsight 4.0?

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 a LLAP

Apache Hive analytické zpracování s nízkou latencí (LLAP) používá servery s trvalými dotazy a ukládání do mezipaměti v paměti k poskytování rychlých výsledků dotazů SQL na data ve vzdáleném cloudovém úložišti. LLAP pro Hive využívá sadu trvalých procesů démonů, kteří provádějí fragmenty dotazů Hive. Provádění dotazu s LLAP se podobá Hive bez LLAP, přičemž úlohy pracovních procesů neběží v kontejnerech, ale uvnitř procesů démonů LLAP.

Mezi výhody LLAP pro Hive patří:

* Schopnost podrobné analýzy SQL, jako jsou komplexní spojení, poddotazy, funkce časových oken, řazení, uživatelem definované funkce a komplexní agregace, bez obětování výkonu a škálovatelnosti.

* Interaktivní dotazy na data ve stejném úložišti, kde se data připravují, takže odpadá nutnost přesouvat data z úložiště do jiného modulu pro analytické zpracování.

* Ukládání výsledků dotazu do mezipaměti umožňuje opětovné použití dříve vypočítaných výsledků dotazů, což šetří čas a prostředky potřebné ke spuštění clusterových úloh požadovaných pro dotaz.

### <a name="hive-dynamic-materialized-views"></a>Dynamická materializovaná zobrazení Hive

Hive teď podporuje dynamická materializovaná zobrazení, neboli předběžný výpočet relevantních souhrnů, sloužící k urychlení zpracování dotazů v datových skladech. Materializovaná zobrazení mohou být uložena nativně v Hive a bez problémů využívat akceleraci LLAP.

### <a name="hive-transactional-tables"></a>Transakční tabulky Hive

HDI 4.0 obsahuje Apache Hive 3, který pro transakční tabulky, které se nacházejí ve skladu Hive, vyžaduje dodržování nedělitelnosti, konzistence, izolace a stálosti (ACID). Tabulky a tabulková data dodržující model ACID jsou používána a spravována produktem Hive. Data v tabulkách pro vytváření, načítání, aktualizaci a odstraňování (CRUD) musí být ve formátu souboru ORC (Optimized Row Column), ale tabulky určené jen pro vkládání podporují všechny formáty souborů.

* V modelu ACID v2 došlo k navýšení výkonu jak u formátu úložiště, tak u prováděcího modulu.

* V modelu ACID je standardně povolená úplná podpora aktualizací dat.

* Vylepšené funkce modelu ACID umožňují aktualizaci a odstranění na úrovni řádků.

* Nedochází k žádnému plýtvání výkonem.

* Nevyžaduje se žádné rozdělení do kbelíků.

* Spark dokáže číst a zapisovat do tabulek Hive modelu ACID přes Hive Warehouse Connector.

Přečtěte si další informace o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark získává přes Hive Warehouse Connector aktualizovatelné tabulky a transakce ACID. Hive Warehouse Connector umožňuje registrovat transakční tabulky Hive jako externí tabulky ve Sparku a zpřístupnit tak plně transakční funkčnost. Předchozí verze podporovaly jen manipulaci s oddílem tabulky. Hive Warehouse Connector podporuje také streamovací datové rámce pro streamování čtení a zápisů do transakčních a streamovacích tabulek Hive ze Sparku.

Exekutory Spark se dokážou připojit přímo k procesům démonů Hive LLAP a načítat a aktualizovat data transakčním způsobem, takže Hive má nad daty pořád kontrolu.

Apache Spark v HDInsight 4.0 podporuje následující scénáře:

* Spuštění tréninku modelu strojového učení na stejné transakční tabulce použité k vykazování
* Bezpečné přidávání sloupců ze Spark ML do tabulky Hive pomocí transakcí ACID
* Spuštění streamovací úlohy Spark v kanálu změn ze streamovací tabulky Hive
* Vytváření souborů ORC přímo z úlohy strukturovaného streamu Sparku

Už nemusíte mít obavy, když se nechtěně pokusíte o přístup k transakčním tabulkám Hive přímo ze Sparku, kdy docházelo k nekonzistentním výsledkům, duplicitním datům nebo poškození dat. V HDInsight 4.0 jsou tabulky Spark a Hive uloženy v samostatných Metastores. K explicitní registraci transakčních tabulek Hive jako externích tabulek Sparku použijte Hive Data Warehouse Connector.

Přečtěte si další informace o [Apache Sparku](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apač Oozie

Apache Oozie 4.3.1 je součástí HDI 4.0 s následujícími změnami:

* Oozie už nespouští akce Hive. Rozhraní příkazového řádku Hive bylo odebráno a nahrazeno za BeeLine.

* Zahrnutím vylučovacího vzoru do souboru **job.properties** můžete z knihovny sdílení vyloučit nechtěné závislosti.

Přečtěte si další informace o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Jak upgradovat na HDInsight 4.0

Stejně jako u jakékoli hlavní verze je před implementací nejnovější verze do produkčního prostředí důležité důkladně otestovat komponenty. HDInsight 4.0 je k dispozici pro zahájení procesu upgradu, ale HDInsight 3.6 je výchozí možností, jak zabránit náhodným nehodám.

Neexistuje žádná podporovaná cesta upgradu z předchozích verzí HDInsightu na HDInsight 4.0. Vzhledem k tomu, že metastore a blob datové formáty se změnily, HDInsight 4.0 není kompatibilní s předchozími verzemi. Je důležité, abyste udrželi nové prostředí HDInsight 4.0 oddělené od aktuálního produkčního prostředí. Pokud nasadíte HDInsight 4.0 do aktuálního prostředí, vaše Metastore bude upgradován a nelze jej vrátit zpět.  

## <a name="limitations"></a>Omezení

* HDInsight 4.0 nepodporuje MapReduce pro Apache Hive. Místo toho použijte Apache Tez. Přečtěte si další informace o [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 nepodporuje Apache Storm.
* Zobrazení hive již není v hdinsightu 4.0 k dispozici.
* Překladač prostředí v Apache Zeppelin není podporován v clusterech Spark a Interactive Query.
* V clusteru Spark-LLAP není možné *zakázat* LLAP. LLAP můžete jenom vypnout.
* Azure Data Lake Storage Gen2 nemůže v clusteru Spark ukládat poznámkové bloky Jupyter.

## <a name="next-steps"></a>Další kroky

* [Dokumentace ke službě HDInsight](index.yml)
* [Poznámky](hdinsight-release-notes.md)
