---
title: 'Migrace dat: místní Apache Hadoop do Azure HDInsight'
description: Naučte se osvědčené postupy migrace dat pro migraci místních clusterů Hadoop do Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939794"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrace místních Apache Hadoopových clusterů do Azure HDInsight – osvědčené postupy pro migraci dat

Tento článek obsahuje doporučení pro migraci dat do Azure HDInsight. Je součástí série, která poskytuje osvědčené postupy, které vám pomůžou s migrací místních Apache Hadoop systémů do Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrace místních dat do Azure

Existují dvě hlavní možnosti migrace dat z místního prostředí do prostředí Azure:

* Přenos dat přes síť pomocí protokolu TLS
    * Přes Internet – data můžete přenést do služby Azure Storage prostřednictvím běžného internetového připojení pomocí některého z několika nástrojů, jako je: Průzkumník služby Azure Storage, AzCopy, Azure PowerShell a Azure CLI. Další informace najdete v tématu [přesun dat do a z Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md).

    * Express Route-ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datovými centry Microsoftu a infrastrukturou ve vašich prostorách nebo v zařízení se systémem. Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí vyšší úroveň zabezpečení, spolehlivosti a rychlosti s nižší latencí než typická připojení přes Internet. Další informace najdete v tématu [Vytvoření a úprava okruhu ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box online přenosu dat – Data Box Edge a Data Box Gateway jsou online produkty pro přenos dat, které fungují jako brány síťového úložiště pro správu dat mezi vaší lokalitou a Azure. Data Box Edge, místní síťové zařízení, přenáší data do a z Azure a využívá hraniční výpočetní funkce s povoleným umělou logikou (AI) pro zpracování dat. Data Box Gateway je virtuální zařízení s funkcemi brány úložiště. Další informace najdete v tématu [Azure Data box dokumentaci – online přenos](../../databox-online/index.yml).

* Přenos dat do režimu offline

    Data Box offline přenos dat – Data Box, Data Box Disk a Data Box Heavy zařízení vám pomůžou přenášet velké objemy dat do Azure, když síť není možnost. Tato zařízení offline pro přenos dat se dodávají mezi vaší organizací a datacenterm Azure. Používají šifrování AES k ochraně vašich dat při přenosu a prošly důkladným procesem pro úpravu po nahrání, který umožňuje odstranit vaše data ze zařízení. Další informace o Data Box offline přenosových zařízeních najdete v části [Azure Data box dokumentace – offline přenos](../../databox/index.yml). Další informace o migraci clusterů Hadoop najdete v tématu [použití Azure Data box k migraci z místního úložiště HDFS do Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Následující tabulka má přibližnou dobu trvání přenosu dat na základě objemu dat a šířky pásma sítě. Pokud se očekává, že migrace dat trvá déle než tři týdny, použijte data box.

|Množství dat | Šířka pásma sítě<br>z<br>**45 MB/s (T3)**|Šířka pásma sítě<br>z<br>**100 Mb/s**|Šířka pásma sítě<br>z<br>**1 Gb/s**|Šířka pásma sítě<br>z<br>**10 Gb/s**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 dny|1 den| 2 hodiny|14 minut|
|10 TB|22 dní|10 dní|1 den|2 hodiny|
|35 TB|76 dní|34 dní|3 dny|8 hodin|
|80 TB|173 dní|78 dní|8 dnů|19 hodin|
|100 TB|216 dní|97 dní|10 dní|1 den|
|200 TB|1 rok|194 dní|19 dní|2 dny|
|500 TB|3 roky|1 rok|49 dní|5 dní|
|1 PB|6 let|3 roky|97 dní|10 dní|
|2 PB|12 let|5 let|194 dní|19 dní|

Nástroje, které jsou nativní pro Azure, například Apache Hadoop DistCp, Azure Data Factory a AzureCp, se dají použít k přenosu dat přes síť. Nástroj WANDisco třetí strany se dá použít i pro stejný účel. Apache Kafka nástroje MirrorMaker a Apache Sqoop je možné použít pro průběžné přenosy dat z místního prostředí do systémů úložiště Azure.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Požadavky na výkon při použití Apache Hadoop DistCp

DistCp je projekt Apache, který používá úlohu mapování MapReduce k přenosu dat, zpracování chyb a zotavení z těchto chyb. Přiřadí seznam zdrojových souborů ke každé úloze mapy. Úloha mapy pak zkopíruje všechny přiřazené soubory do cílového umístění. Existuje několik postupů, které mohou zlepšit výkon DistCp.

### <a name="increase-the-number-of-mappers"></a>Zvýšení počtu mapovačů

DistCp se pokusí vytvořit úlohy mapování, aby každá z nich přibližně stejný počet bajtů měla. Ve výchozím nastavení používají úlohy DistCp 20 mapovačů. Použití většího počtu mapovačů pro Distcp (s parametrem ' m ' na příkazovém řádku) zvyšuje paralelismus během procesu přenosu dat a snižuje délku přenosu dat. Při zvyšování počtu mapovačů je ale potřeba vzít v úvahu dvě věci:

* Nejnižší členitost DistCp je jeden soubor. Zadání počtu mapovačů s více než počtem zdrojových souborů nepomůže a bude mít za odpad dostupné prostředky clusteru.

* Vezměte v úvahu dostupnou paměť příze v clusteru, abyste zjistili počet mapovačů. Každá úloha mapy se spustí jako kontejner příze. Za předpokladu, že v clusteru nejsou spuštěny žádné jiné náročné úlohy, lze počet mapovačů určit pomocí následujícího vzorce: m = (počet pracovních uzlů \* příze paměti pro každý pracovní uzel)/velikost kontejneru příz. Pokud však jiná aplikace používá paměť, pak zvolte možnost použít pouze část paměti PŘÍZe pro úlohy DistCp.

### <a name="use-more-than-one-distcp-job"></a>Použití víc než jedné úlohy DistCp

Pokud je velikost datové sady, která má být přesunuta, větší než 1 TB, použijte více než jednu úlohu DistCp. Používání více než jedné úlohy omezuje dopad selhání. Pokud dojde k chybě některé úlohy, stačí pouze restartovat konkrétní úlohu, nikoli všechny úlohy.

### <a name="consider-splitting-files"></a>Zvažte rozdělení souborů

Pokud existuje malý počet velkých souborů, zvažte jejich rozdělení na bloky dat souborů 256 MB, aby se získalo více potenciálních souběžnosti s více mapovači.

### <a name="use-the-strategy-command-line-parameter"></a>Použijte parametr příkazového řádku strategie.

Zvažte použití `strategy = dynamic` parametru v příkazovém řádku. Výchozí hodnota `strategy` parametru je `uniform size` , v takovém případě Každá mapa kopíruje přibližně stejný počet bajtů. Když se tento parametr změní na `dynamic` , soubor výpisu se rozdělí do několika "bloků souborů". Počet souborů bloku je násobek počtu map. Každé úloze mapování se přiřadí jeden ze souborů bloku. Po zpracování všech cest v bloku dat se odstraní aktuální blok a získá se nový blok. Proces pokračuje, dokud nebudou k dispozici žádné další bloky. Tento "dynamický" přístup umožňuje rychlejší mapování – úlohy pro využívání více cest než pomalejších, čímž se celková rychlost DistCp úlohy.

### <a name="increase-the-number-of-threads"></a>Zvýšení počtu vláken

Podívejte se, jestli zvýšením `-numListstatusThreads` parametru zlepšíte výkon. Tento parametr řídí počet vláken, která se mají použít pro sestavování seznamu souborů, a 40 je maximální hodnota.

### <a name="use-the-output-committer-algorithm"></a>Použití algoritmu výstupního potvrzení

Podívejte se, jestli předání parametru `-Dmapreduce.fileoutputcommitter.algorithm.version=2` vylepšuje DistCp výkon. Tento algoritmus vypisování výstupu obsahuje optimalizace pro zápis výstupních souborů do cíle. Následující příkaz je příklad, který ukazuje použití různých parametrů:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrace metadat

### <a name="apache-hive"></a>Apache Hive

Podregistr metastore lze migrovat buď pomocí skriptů, nebo pomocí replikace databáze.

#### <a name="hive-metastore-migration-using-scripts"></a>metastore Hive migrace pomocí skriptů

1. Vygenerujte podregistr DDLs z místního metastore Hive. Tento krok se dá udělat pomocí [skriptu bash obálky](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Upravte vygenerovanou položku DDL tak, aby nahradila adresu URL HDFS pomocí adres URL WASB/ADLS/ABFS.
1. Spusťte aktualizovaný skript DDL v metastore z clusteru HDInsight.
1. Ujistěte se, že je verze metastore Hive kompatibilní mezi místním prostředím a cloudem.

#### <a name="hive-metastore-migration-using-db-replication"></a>metastore Hive migrace pomocí replikace databáze

- Nastavte replikaci databáze mezi místními metastore Hive DB a HDInsight metastore DB.
- Pomocí příkazu "podregistr MetaTool" nahraďte adresu URL HDFS adresami URL WASB/ADLS/ABFS, například:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportujte zásady místních Ranger do souborů XML.
- Transformujte místní cesty založené na HDFS na WASB/ADLS pomocí nástroje jako XSLT.
- Importujte zásady na Ranger běžící na HDInsight.

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Osvědčené postupy zabezpečení a DevOps migrace z místního prostředí do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)