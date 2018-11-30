---
title: Migrace s místními clustery systému Apache Hadoop do Azure HDInsight – osvědčené postupy migrace dat
description: Přečtěte si osvědčené postupy migrace dat pro migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 492087f7eeca8628ac6ac9a9e42f355a9356f1ce
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584702"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrace s místními clustery systému Apache Hadoop do Azure HDInsight – osvědčené postupy migrace dat

Tento článek obsahuje doporučení pro migraci dat do Azure HDInsight. To je součástí série, která poskytuje osvědčené postupy pro pomoc s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="migrate-data-from-on-premises-to-azure"></a>Migrace místních dat do Azure

Existují dvě hlavní možnosti, jak migrovat data z místního prostředí Azure:

1.  Přenos dat přes síť pomocí protokolu TLS
    1. Přes internet – můžete přenést data do úložiště Azure přes regulární připojení k Internetu pomocí některého z několik nástrojů, jako: Průzkumník služby Azure Storage, AzCopy, Azure Powershellu a rozhraní příkazového řádku Azure.  Zobrazit [přesun dat do a z Azure Storage](../../storage/common/storage-moving-data.md) Další informace.
    2. Expressroute – ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datacentry Microsoftu a infrastrukturou ve vlastních prostorách nebo v společně umístěného zařízení. Připojení ExpressRoute nemáte se přenášejí prostřednictvím veřejného Internetu a nabízí vyšší zabezpečení, spolehlivost a rychlost s nižší latencí než Typická připojení přes Internet. Další informace najdete v tématu [vytvoření a úprava okruhu ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Přenosy dat online pole dat – okraj pole Data a brána pole dat jsou online data převodu produkty, které fungují jako sítě úložiště brány pro správu dat mezi vaší lokalitou a Azure. Data Box Edge je místní síťové zařízení, které přenáší data do a z Azure a při zpracování dat využívá hraniční výpočetní prostředky s podporou umělé inteligence (AI). Data Box Gateway je virtuální zařízení s funkcemi brány úložiště. Další informace najdete v tématu [Azure dokumentaci služby Data Box – Online přenos](https://docs.microsoft.com/azure/databox-online/).
1.  Přesouvání dat do offline režimu
    1. Import / Export service – fyzické disky může odesílat do Azure a nahraje za vás. Další informace najdete v tématu [co je služba Azure Import/Export?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
    1. Data pole přenosy dat offline – zařízení Data Box Data Box Disk, a zařízení Data Box náročné umožňují přenos velkých objemů dat do Azure, pokud síť není možné. Tato zařízení pro offline přenos dat se převážejí mezi vaší organizací a datacentrem Azure. Přenášená data pomáhají chránit s využitím šifrování AES a po nahrání provádějí proces důkladné sanitizace, který odstraní vaše data ze zařízení. Další informace najdete v tématu [Azure dokumentaci služby Data Box – do offline režimu přenosu](https://docs.microsoft.com/azure/databox/).

Následující tabulka má dobu přenosu přibližné dat na základě dat svazku a síťové šířky pásma. Pokud se očekává, že migrace dat trvat více než tři týdny, použijte Data box.

|**Množství dat**|**Šířka pásma sítě**|||
|---|---|---|---|
|| **45 MB/s (T3)**|**100 MB/s**|**1 GB/s**|**10 GB/s**
|1 TB|2 dny|1 den| 2 hodiny|14 minut|
|10 TB|22 dnů|10 dnů|1 den|2 hodiny|
|35 TB|76 dnů|34 dnů|3 dny|8 hodin|
|80 TB|173 dnů|78 dnů|8 dní|19 hodin|
|100 TB|216 dnů|97 dnů|10 dnů|1 den|
|200 TB|1 rok|194 dnů|19 dnů|2 dny|
|500 TB|3 roky|1 rok|49 dní|5 dní|
|1 PB|6 let|3 roky|97 dnů|10 dnů|
|2 PB|12 let|5 let.|194 dnů|19 dnů|

Nástroje pro nativní pro Azure, jako jsou DistCp, Azure Data Factory a AzureCp, je možné posílat data přes síť. Nástroj třetí strany WANDisco lze použít také ke stejnému účelu. Nástroje Mirrormaker Kafka a Sqoop slouží pro přenosy probíhající dat z místního systémů služby Azure storage.

## <a name="performance-considerations-when-using-apache-distcp"></a>Požadavky na výkon při použití Apache DistCp

DistCp je projekt Apache, který používá k přenosu dat, zpracování chyb a zotavení z těchto chyb úlohu MapReduce mapy. Každý úkol mapy přiřazuje seznam zdrojových souborů. Úloha mapy všechny své přiřazené soubory pak zkopíruje do cíle. Existuje několik postupů může zlepšit výkon DistCp.

### <a name="increase-the-number-of-mappers"></a>Zvýšit počet Mapovačů

DistCp se pokusí vytvořit mapu úlohy tak, aby každý z nich zkopíruje přibližně stejný počet bajtů. Ve výchozím nastavení používají 20 mapovačů DistCp úlohy. Pomocí více Mapovačů Distcp (s na mě "parametr příkazového řádku) během procesu přenosu dat se zvyšuje paralelismu a snižuje délce přenosu dat. Nicméně existují dvě věci k uvážení při zvýšení počtu Mapovačů:

1. DistCp na nejnižší členitost je jeden soubor. Určení počtu Mapovačů větší než počet zdrojových souborů nepomůže a bude plýtvání prostředky clusteru k dispozici.
1. Vezměte v úvahu dostupnou paměť Yarn clusteru k určení počtu Mapovačů. Každý úkol mapování se spustí jako kontejneru Yarn. Za předpokladu, že v clusteru běží bez náročných úloh, můžete určit počet Mapovačů tento vzorec: m = (počet uzlů pracovního procesu \* paměti YARN pro každý pracovní uzel) / YARN velikost kontejneru. Ale pokud jiné aplikace používají paměti, vyberte pouze použít část paměti YARN pro úlohy DistCp.

### <a name="use-more-than-one-distcp-job"></a>Použití více než jednu úlohu DistCp

Pokud velikost datové sady, přesunout je větší než 1 TB, použijte více než jednu úlohu DistCp. Pomocí více než jedné úlohy se omezuje dopad selhání. Pokud některé z úloh selže, stačí restartovat tuto konkrétní úlohu, ne všechny úlohy.

### <a name="consider-splitting-files"></a>Zvažte rozdělení souborů

Pokud existují malé množství velkých souborů, zvažte jejich rozdělení do souboru 256 MB bloků dat, chcete-li získat další potenciální souběžnosti ovládacím prvkem více Mapovačů.

### <a name="use-the-strategy-command-line-parameter"></a>Použijte parametr příkazového řádku 'strategií.

Zvažte použití `strategy = dynamic` parametrů v příkazovém řádku. Výchozí hodnota `strategy` parametr `uniform size`, v takovém případě zkopíruje každý mapy přibližně stejný počet bajtů. Pokud tento parametr se změní na `dynamic`, soubor výpisu je rozdělený do několika "bloků dat souborů". Počet bloků dat souborů, které je násobkem počet mapy. Každý úkol mapa má přiřazenou jednu bloků dat souborů. Po zpracování všech cest v bloku dat, se odstraní aktuální blok dat a získat nový blok. Proces pokračuje, dokud nejsou k dispozici žádné další bloky. Tento přístup "dynamické" umožňuje rychlejší mapy úkoly k využívání více cest než ty pomalejší, tedy urychlení celkové DistCp úlohy.

### <a name="increase-the-number-of-threads"></a>Zvýšení počtu vláken

Pokud zvýšení `-numListstatusThreads` parametr zvyšuje výkon. Tento parametr řídí počet vláken pro vytvoření souboru výpisu a maximální hodnota je 40.

### <a name="use-the-output-committer-algorithm"></a>Použít algoritmus potvrzovatelem výstupu

Pokud předávání parametru `-Dmapreduce.fileoutputcommitter.algorithm.version=2` zlepšuje výkon DistCp. Tento výstup potvrzovatelem algoritmus využívající dlaždice má optimalizace kolem zápis výstupní soubory do cílového umístění. Následující příkaz je příklad, který ukazuje použití různé parametry:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrace metadat

### <a name="hive"></a>Hive

Hive metastore je možné migrovat pomocí skriptů nebo pomocí replikace databáze.

#### <a name="hive-metastore-migration-using-scripts"></a>Migrace metastore Hive pomocí skriptů

1. Generovat Hive DDLs z metastore Hive v místním prostředí. Tento krok lze provést pomocí [bash skript obálky]. (https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)
1. Upravte vygenerovaný DDL nahraďte adresu url HDFS WASB nebo ADLS/ABFS adresy URL
1. Aktualizované DDL spouštět metastore z clusteru HDInsight
1. Ujistěte se, že verze metastore Hive je kompatibilní mezi místním prostředím a cloudem

#### <a name="hive-metastore-migration-using-db-replication"></a>Migrace metastore Hive pomocí replikace databáze

- Nastavení replikace databáze mezi metastore Hive místní databáze a HDInsight metastore DB
- Nahraďte adresu url HDFS WASB nebo ADLS/ABFS adresy URL, třeba pomocí "Hive MetaTool":

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="ranger"></a>Ranger

- Export místní zásady Ranger pro soubory xml.
- Transformace v místním prostředí konkrétním systémem HDFS cesty k WASB nebo ADLS pomocí některého nástroje, například XSLT.
- Importujte zásady k Ranger systémem HDInsight.

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Osvědčené postupy pro Azure HDInsight Hadoop migrace místní zabezpečení a DevOps](apache-hadoop-on-premises-migration-best-practices-security-devops.md)