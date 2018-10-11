---
title: Přehled HDInsight 4.0 (Preview) – Azure
description: Porovnání funkcí a omezení mezi HDInsight 3.6 a HDInsight 4.0 a doporučení k upgradu
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 152a145601dcf4282ec0a3a3b6ebcf37bd11848b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992962"
---
# <a name="hdinsight-40-overview-preview"></a>Přehled HDInsight 4.0 (Preview)

Azure HDInsight je mezi podnikovými zákazníky jednou z nejoblíbenějších služeb pro analýzu Hadoop a Spark v Azure. HDInsight (HDI) 4.0 je cloudová distribuce komponent Hadoop z platformy [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html). Tento článek obsahuje informace o nejnovější verzi Azure HDInsight a pokyny k upgradu.

## <a name="whats-new-in-hdi-40"></a>Co je nového ve verzi HDI 4.0?

### <a name="hive-30-and-llap"></a>Hive 3.0 a LLAP

Díky analytickému zpracování s nízkou latencí (LLAP), které používá trvalé dotazovací servery a ukládání do mezipaměti v paměti, nabízí Hive rychlé výsledky dotazů SQL na data ve vzdáleném cloudovém úložišti. LLAP pro Hive využívá sadu trvalých procesů démonů, kteří provádějí fragmenty dotazů Hive. Provádění dotazu s LLAP se podobá Hive bez LLAP, přičemž úlohy pracovních procesů neběží v kontejnerech, ale uvnitř procesů démonů LLAP.

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

Už nemusíte mít obavy, když se nechtěně pokusíte o přístup k transakčním tabulkám Hive přímo ze Sparku, kdy docházelo k nekonzistentním výsledkům, duplicitním datům nebo poškození dat. V HDI 4.0 se tabulky Spark a tabulky Hive uchovávají v samostatných metastorech. K explicitní registraci transakčních tabulek Hive jako externích tabulek Sparku použijte Hive Data Warehouse Connector.

Přečtěte si další informace o [Apache Sparku](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="oozie"></a>Oozie

Apache Oozie 4.3.1 je součástí HDI 4.0 s následujícími změnami:

* Oozie už nespouští akce Hive. Rozhraní příkazového řádku Hive bylo odebráno a nahrazeno za BeeLine.

* Zahrnutím vylučovacího vzoru do souboru **job.properties** můžete z knihovny sdílení vyloučit nechtěné závislosti.

Přečtěte si další informace o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdi-40"></a>Jak upgradovat na HDI 4.0

Stejně jako u jakékoli hlavní verze je před implementací nejnovější verze do produkčního prostředí důležité důkladně otestovat komponenty. Pro zahájení upgradu máte k dispozici HDI 4.0, ale výchozí možností je HDI 3.6, aby nedošlo k nechtěným nehodám.

Neexistuje žádný podporovaný způsob upgradu z předchozích verzí HDI na HDI 4.0. Protože došlo ke změně datových formátů metastoru a objektů blob, není HDI 4.0 kompatibilní s předchozími verzemi. Je důležité, abyste nové prostředí HDI 4.0 udržovali oddělené od svého aktuálního produkčního prostředí. Pokud HDI 4.0 nasadíte do aktuálního prostředí, upgraduje se metastore, což nelze vrátit zpět.  

## <a name="limitations"></a>Omezení

* HDI 4.0 nepodporuje MapReduce. Použijte místo toho Tez. Přečtěte si další informace o [Apache Tez](https://tez.apache.org/).

* Zobrazení Hive už není v HDI 4.0 k dispozici. 

## <a name="next-steps"></a>Další kroky

* [Dokumentace k Azure HDInsight](index.yml)
* [Poznámky k verzi](hdinsight-release-notes.md)
