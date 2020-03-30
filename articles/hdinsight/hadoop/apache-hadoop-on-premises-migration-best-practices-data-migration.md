---
title: 'Migrace dat: Místní Apache Hadoop do Azure HDInsight'
description: Seznamte se s osvědčenými postupy migrace dat pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665997"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight – osvědčené postupy pro migraci dat

Tento článek obsahuje doporučení pro migraci dat do Azure HDInsight. Je součástí řady, která poskytuje osvědčené postupy, které vám pomohou s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrace místních dat do Azure

Existují dvě hlavní možnosti migrace dat z místního prostředí azure:

* Přenos dat po síti pomocí TLS
    * Přes internet – data můžete přenášet do úložiště Azure prostřednictvím běžného připojení k internetu pomocí některého z několika nástrojů, jako jsou: Průzkumník úložiště Azure, AzCopy, Azure Powershell a Azure CLI. Další informace najdete [v tématu Přesun dat do a z Azure Storage](../../storage/common/storage-moving-data.md).

    * Express Route – ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datovými centry Microsoftu a infrastrukturou, která je ve vašich prostorách nebo v zařízení pro společné umístění. Připojení ExpressRoute neprocházejí přes veřejný Internet a nabízejí vyšší zabezpečení, spolehlivost a rychlosti s nižší latencí než typická připojení přes Internet. Další informace naleznete v [tématu Vytvoření a úprava okruhu ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Přenos dat datové schránky – Data Box Edge a Data Box Gateway jsou online produkty pro přenos dat, které fungují jako brány síťového úložiště pro správu dat mezi vaším webem a Azure. Data Box Edge, místní síťové zařízení, přenáší data do a z Azure a používá technologii edge s podporou umělé inteligence (AI) ke zpracování dat. Brána datové schránky je virtuální zařízení s možnostmi brány úložiště. Další informace najdete v [tématu Dokumentace datové schránky Azure – online přenos](https://docs.microsoft.com/azure/databox-online/).

* Data o dopravě offline

    Přenos dat datové schránky Data Box – zařízení Data Box, Data Box Disk a Data Box Heavy vám pomůžou přenášet do Azure velké množství dat, když síť nepřipadá v úvahu. Tato zařízení pro přenos dat offline jsou dodávána mezi vaší organizací a datovým centrem Azure. Používají šifrování AES, aby pomohli chránit vaše data při přenosu, a procházejí důkladným procesem sanitace po nahrání, aby odstranili vaše data ze zařízení. Další informace o zařízeních pro offline přenos datové schránky najdete v [tématu Dokumentace k datové schránce Azure – offline přenos](https://docs.microsoft.com/azure/databox/). Další informace o migraci clusterů Hadoop najdete [v tématu Migrace z místního úložiště HDFS do Úložiště Azure pomocí Azure Data Boxu.](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)

Následující tabulka obsahuje přibližnou dobu přenosu dat na základě objemu dat a šířky pásma sítě. Pokud se očekává, že migrace dat bude trvat déle než tři týdny, použijte datovou schránku.

|Datové množství|Šířka pásma sítě||||
|---|---|---|---|---|
|| **45 Mb/s (T3)**|**100 Mb/s**|**1 Gb/s**|**10 Gb/s**|
|1 TB|2 dny|1 den| 2 hodiny|14 minut|
|10 TB|22 dní|10 dní|1 den|2 hodiny|
|35 TB|76 dní|34 dní|3 dny|8 hodin|
|80 TB|173 dní|78 dní|8 dnů|19 hodin|
|100 TB|216 dní|97 dní|10 dní|1 den|
|200 TB|1 rok|194 dní|19 dní|2 dny|
|500 TB|3 roky|1 rok|49 dní|5 dní|
|1 PB|6 let|3 roky|97 dní|10 dní|
|2 PB|12 let|5 let|194 dní|19 dní|

Nástroje nativní pro Azure, jako je Apache Hadoop DistCp, Azure Data Factory a AzureCp, se můžou použít k přenosu dat v síti. Ke stejnému účelu lze použít i nástroj wandisco třetí strany. Apache Kafka Mirrormaker a Apache Sqoop se můžou používat pro průběžný přenos dat z místních do úložných systémů Azure.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Aspekty výkonu při použití Apache Hadoop DistCp

DistCp je projekt Apache, který používá úlohu MapReduce Map k přenosu dat, zpracování chyb a obnovení z těchto chyb. Přiřadí seznam zdrojových souborů ke každé úloze mapy. Úloha Mapovat pak zkopíruje všechny přiřazené soubory do cíle. Existuje několik technik může zlepšit výkon DistCp.

### <a name="increase-the-number-of-mappers"></a>Zvýšení počtu mapovačů

DistCp se pokusí vytvořit úlohy mapy tak, aby každý z nich zkopíruje zhruba stejný počet bajtů. Ve výchozím nastavení používají úlohy DistCp 20 mapovačů. Použití více mapovačů pro Distcp (s parametrem 'm' na příkazovém řádku) zvyšuje paralelismus během procesu přenosu dat a zkracovuje délku přenosu dat. Existují však dvě věci, které je třeba zvážit při zvyšování počtu mapperů:

* DistCp nejnižší rozlišovací schopnost je jeden soubor. Zadání počtu mapovačů, které jsou větší než počet zdrojových souborů, nepomůže a bude plýtvat dostupnými prostředky clusteru.

* Zvažte dostupné paměti příze v clusteru k určení počtu Mappers. Každá úloha mapy je spuštěna jako kontejner příze. Za předpokladu, že v clusteru neběží žádné jiné úlohy, počet mapovačů lze určit podle \* následujícího vzorce: m = (počet pracovních uzlů paměti YARN pro každý pracovní uzel) / Velikost kontejneru YARN. Pokud však paměť používají jiné aplikace, zvolte použít pouze část paměti YARN pro úlohy DistCp.

### <a name="use-more-than-one-distcp-job"></a>Použití více než jedné úlohy DistCp

Pokud je velikost datové sady, která má být přesunuta, větší než 1 TB, použijte více než jednu úlohu DistCp. Použití více než jedné úlohy omezuje dopad selhání. Pokud se některá úloha nezdaří, stačí restartovat tuto konkrétní úlohu, nikoli všechny úlohy.

### <a name="consider-splitting-files"></a>Zvažte rozdělení souborů

Pokud existuje malý počet velkých souborů, zvažte jejich rozdělení do bloků souborů 256 MB, abyste získali více potenciální souběžnosti s více mapovači.

### <a name="use-the-strategy-command-line-parameter"></a>Použití parametru příkazového řádku "strategie"

Zvažte `strategy = dynamic` použití parametru v příkazovém řádku. Výchozí hodnota parametru `strategy` `uniform size`je , v takovém případě každá mapa zkopíruje zhruba stejný počet bajtů. Při změně tohoto `dynamic`parametru na , výpis soubor je rozdělen do několika "chunk-files". Počet souborů bloku dat je násobkem počtu map. Každému úkolu mapy je přiřazen jeden z souborů bloku dat. Po zpracování všech cest v bloku jsou zpracovány, aktuální blok je odstraněn a je získán nový blok. Proces pokračuje, dokud nejsou k dispozici žádné další bloky. Tento "dynamický" přístup umožňuje rychlejší úlohy mapy spotřebovávat více cest než pomalejší, čímž se celkově urychlí úloha DistCp.

### <a name="increase-the-number-of-threads"></a>Zvýšení počtu vláken

Zjistěte, `-numListstatusThreads` zda zvýšení parametru zlepšuje výkon. Tento parametr určuje počet podprocesů, které mají být určeny pro výpis souborů budov, a 40 je maximální hodnota.

### <a name="use-the-output-committer-algorithm"></a>Použití algoritmu výstupního vývojáře

Zjistěte, zda `-Dmapreduce.fileoutputcommitter.algorithm.version=2` předání parametru zlepšuje výkon DistCp. Tento algoritmus výstupního vývojáře má optimalizace kolem zápisu výstupních souborů do cíle. Následující příkaz je příklad, který ukazuje použití různých parametrů:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrace metadat

### <a name="apache-hive"></a>Apache Úl

Metastore podregistru lze migrovat pomocí skriptů nebo pomocí replikace DB.

#### <a name="hive-metastore-migration-using-scripts"></a>Migrace metastore hive pomocí skriptů

1. Vygenerujte podregistr DDl z místního metaúložiště Hive. Tento krok lze provést pomocí [skriptu wrapper bash](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Upravte vygenerovanou adresu DDL a nahraďte adresu URL HDFS adresou WASB/ADLS/ABFS.
1. Spusťte aktualizovaný DDL v metastore z clusteru HDInsight.
1. Ujistěte se, že verze metastore Hive je kompatibilní mezi místním a cloudem.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migrace metaúložiště hive pomocí replikace DB

- Nastavte replikaci databáze mezi místními metastoredb Hive a METAStore DB HDInsight.
- Použijte "Hive MetaTool" nahradit HDFS url s WASB/ADLS/ABFS urls, například:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apačský ranger

- Exportujte místní zásady rangeru do souborů XML.
- Transformujte na místních specifických cestách založených na HDFS na WASB/ADLS pomocí nástroje, jako je XSLT.
- Importujte zásady do rangeru spuštěné na HDInsight.

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Doporučené postupy zabezpečení a devops pro místní migraci Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
