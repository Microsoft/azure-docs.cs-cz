---
title: Přehled HDInsight 4,0 – Azure
description: Porovnání funkcí a omezení mezi HDInsight 3.6 a HDInsight 4.0 a doporučení k upgradu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 694acc0005e90d8299d46528f83ba68ea3fcf1c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931193"
---
# <a name="azure-hdinsight-40-overview"></a>Přehled Azure HDInsight 4,0

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky pro Apache Hadoop a Apache Spark. HDInsight 4,0 je cloudová distribuce komponent Apache Hadoop. Tento článek obsahuje informace o nejnovější verzi Azure HDInsight a pokyny k upgradu.

## <a name="whats-new-in-hdinsight-40"></a>Co je nového ve službě HDInsight 4,0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Analytické zpracování s nízkou latencí Apache Hive 3,0

Apache Hive Analytical Processing s nízkou latencí (LLAP) používá trvalé servery dotazů a ukládání do mezipaměti v paměti. Tento proces poskytuje rychlé výsledky dotazů SQL na data ve vzdáleném cloudovém úložišti. LLAP v podregistru používá sadu trvalých démonů, které spouštějí fragmenty dotazů na podregistry. Provádění dotazu s LLAP se podobá Hive bez LLAP, přičemž úlohy pracovních procesů neběží v kontejnerech, ale uvnitř procesů démonů LLAP.

Mezi výhody LLAP pro Hive patří:

* Možnost provádět hloubkové analýzy SQL bez obětování výkonu a přizpůsobivosti. Například složitá spojení, poddotazy, funkce oken, řazení, uživatelsky definované funkce a komplexní agregace.

* Interaktivní dotazy na data ve stejném úložišti, kde se data připravují, takže odpadá nutnost přesouvat data z úložiště do jiného modulu pro analytické zpracování.

* Ukládání výsledků dotazu do mezipaměti umožňuje znovu použít dříve počítané výsledky dotazu. Tato mezipaměť šetří čas a prostředky strávené spouštěním úloh clusteru vyžadovaných pro dotaz.

### <a name="hive-dynamic-materialized-views"></a>Dynamická materializovaná zobrazení Hive

Podregistr teď podporuje dynamická materializovaná zobrazení nebo předem vyhodnocení relevantních souhrnů. Zobrazení urychlují zpracování dotazů v datových skladech. Materializovaná zobrazení mohou být uložena nativně v Hive a bez problémů využívat akceleraci LLAP.

### <a name="hive-transactional-tables"></a>Transakční tabulky Hive

HDI 4,0 obsahuje Apache Hive 3. Podregistr 3 vyžaduje, aby byly v případě transakčních tabulek, které jsou ve skladu podregistru živé, shodné, konzistenci, izolaci a odolnost. Tabulky a tabulková data dodržující model ACID jsou používána a spravována produktem Hive. Data v tabulkách Create, načítat, Update a Delete (CRUD) musí být ve formátu optimalizovaného řádkového sloupce (ORC). Tabulky jen pro vložení podporují všechny formáty souborů. 

> [!Note]
> Podpora KYSELého a transakčního prostředí funguje pouze pro spravované tabulky a nikoli pro externí tabulky. Externí tabulky podregistru jsou navržené tak, aby externí strany mohly číst a zapisovat data tabulky, bez podregistru perfoming jakékoli změny podkladových dat. V případě tabulek pro KYSELINu může podregistr změnit podkladová data s komprimacemi a transakcemi.

Mezi výhody v tabulkách KYSELosti patří následující:

* V modelu ACID v2 došlo k navýšení výkonu jak u formátu úložiště, tak u prováděcího modulu.

* V modelu ACID je standardně povolená úplná podpora aktualizací dat.

* Vylepšené funkce modelu ACID umožňují aktualizaci a odstranění na úrovni řádků.

* Nedochází k žádnému plýtvání výkonem.

* Nevyžaduje se žádné rozdělení do kbelíků.

* Spark dokáže číst a zapisovat do tabulek Hive modelu ACID přes Hive Warehouse Connector.

Přečtěte si další informace o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark získává přes Hive Warehouse Connector aktualizovatelné tabulky a transakce ACID. Hive Warehouse Connector umožňuje registrovat transakční tabulky Hive jako externí tabulky ve Sparku a zpřístupnit tak plně transakční funkčnost. Předchozí verze podporovaly jen manipulaci s oddílem tabulky. Konektor pro datový sklad v podregistru podporuje také streamování datových snímků.  Tento proces streamuje a zapisuje do transakčních a streamických tabulek z Sparku.

Exekutory Spark se dokážou připojit přímo k procesům démonů Hive LLAP a načítat a aktualizovat data transakčním způsobem, takže Hive má nad daty pořád kontrolu.

Apache Spark v HDInsight 4.0 podporuje následující scénáře:

* Spuštění tréninku modelu strojového učení na stejné transakční tabulce použité k vykazování
* Bezpečné přidávání sloupců ze Spark ML do tabulky Hive pomocí transakcí ACID
* Spuštění streamovací úlohy Spark v kanálu změn ze streamovací tabulky Hive
* Vytváření souborů ORC přímo z úlohy strukturovaného streamu Sparku

Už se nemusíte starat o náhodný pokus o přístup k transakčním tabulkám podregistru přímo ze Sparku. Výsledkem jsou nekonzistentní výsledky, duplicitní data nebo poškození dat. V HDInsight 4,0 jsou tabulky Spark a tabulky podregistru uloženy v samostatných Metaúložiště. K explicitní registraci transakčních tabulek Hive jako externích tabulek Sparku použijte Hive Data Warehouse Connector.

Přečtěte si další informace o [Apache Sparku](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 je součástí HDI 4.0 s následujícími změnami:

* Oozie už nespouští akce Hive. Rozhraní příkazového řádku Hive bylo odebráno a nahrazeno za BeeLine.

* Zahrnutím vylučovacího vzoru do souboru **job.properties** můžete z knihovny sdílení vyloučit nechtěné závislosti.

Přečtěte si další informace o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Postup upgradu na HDInsight 4,0

Před implementací nejnovější verze v produkčním prostředí důkladně otestujte své komponenty. K dispozici je HDInsight 4,0, abyste mohli zahájit proces upgradu. HDInsight 3,6 je výchozí možnost, která zabraňuje nechtěnému mishaps.

Neexistuje žádná podporovaná cesta upgradu z předchozích verzí HDInsight do HDInsight 4,0. Vzhledem k tomu, že se změnilo metastore a formáty dat objektů blob, 4,0 není kompatibilní s předchozími verzemi. Je důležité, abyste zachovali nové prostředí HDInsight 4,0, které je oddělené od aktuálního produkčního prostředí. Pokud nasadíte HDInsight 4,0 do vašeho aktuálního prostředí, vaše metastore se trvale upgradují.  

## <a name="limitations"></a>Omezení

* HDInsight 4,0 nepodporuje MapReduce pro Apache Hive. Místo toho použijte Apache Tez. Přečtěte si další informace o [Apache Tez](https://tez.apache.org/).
* HDInsight 4,0 nepodporuje Apache Storm.
* HDInsight 4,0 nepodporuje typ clusteru ML Services.
* Zobrazení podregistru je dostupné jenom u clusterů HDInsight 4,0 s číslem verze rovným nebo větším než 4,1. Toto číslo verze je dostupné ve verzích Ambari admin->.
* Překladač prostředí v Apache Zeppelin se nepodporuje v clusterech Spark a interaktivních dotazech.
* V clusteru Spark-LLAP není možné *zakázat* LLAP. LLAP můžete jenom vypnout.
* Azure Data Lake Storage Gen2 v clusteru Spark nemůžou ukládat poznámkové bloky Jupyter.
* Apache prasete se ve výchozím nastavení spouští ve tez, ale můžete ho změnit na MapReduce.
* Integrace Spark SQL Ranger pro zabezpečení řádků a sloupců je zastaralá.
* Spark 2,4 a Kafka 2,1 jsou dostupné v HDInsight 4,0, takže Spark 2,3 a Kafka 1,1 se už nepodporují. V HDInsight 4,0 doporučujeme používat Spark 2,4 & Kafka 2,1 a novější.

## <a name="next-steps"></a>Další kroky

* [Průvodce migrací pro HBA](./hbase/apache-hbase-migrate-new-version.md)
* [Průvodce migrací z registru](./interactive-query/apache-hive-migrate-workloads.md)
* [Průvodce migrací Kafka](./kafka/migrate-versions.md)
* [Příručka k migraci Spark](./spark/migrate-versions.md)
* [Dokumentace ke službě Azure HDInsight](index.yml)
* [Zpráva k vydání verze](hdinsight-release-notes.md)
