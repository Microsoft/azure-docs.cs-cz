---
title: Kopírování dat do Azure Data Lake Storage Gen2 Preview pomocí Distcp | Dokumentace Microsoftu
description: Pomocí Distcp nástroje pro kopírování dat do a z Data Lake Storage Gen2 ve verzi Preview
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522692"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Použití Distcp ke kopírování dat mezi objekty BLOB Azure Storage a verzi Preview služby Data Lake Storage Gen2

Pokud máte cluster HDInsight s přístupem k Azure Data Lake Storage Gen2 ve verzi Preview, můžete použít nástroje ekosystému Hadoopu, jako je [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ke kopírování dat **do a z** úložišti clusteru HDInsight (WASB) do Data Podporuje účet Lake Storage Gen2. Tento článek obsahuje pokyny, jak používat nástroj Distcp.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage s Azure Data Lake Storage (Preview) funkce povolena**. Pokyny k jeho vytvoření najdete v tématu [vytvoření účtu úložiště Azure Data Lake Storage Gen2 ve verzi Preview](quickstart-create-account.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](use-hdi-cluster.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití Distcp z clusteru HDInsight Linux

HDInsight cluster se dodává s Distcp nástroj, který slouží ke kopírování dat z různých zdrojů do clusteru služby HDInsight. Pokud nakonfigurujete cluster HDInsight používat Azure Blob Storage a Azure Data Lake Storage společně, může být nástroj Distcp používané out-of-the-box ke kopírování dat mezi také. V této části se podíváme na tom, jak používat nástroj Distcp.

1. Z plochy připojte se ke clusteru pomocí SSH. Zobrazit [připojení ke clusteru HDInsight se systémem Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z příkazového řádku SSH.

2. Ověřte, zda můžete přístup k objektům BLOB Azure Storage (WASB). Spusťte následující příkaz:

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Výstup by měl poskytovat seznam obsahu v objektu blob úložiště.

3. Stejně tak ověřte, zda můžete získat přístup k účtu Data Lake Storage z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Výstup by měl poskytovat seznam souborů a složek v účtu Data Lake Storage.

4. Použití Distcp ke kopírování dat z WASB do účtu Data Lake Storage.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Příkaz zkopíruje obsah **/příklad/data/gutenberg/** složky v úložišti objektů Blob do **/myfolder** v účtu Data Lake Storage.

5. Podobně použití Distcp ke kopírování dat z účtu Data Lake Storage do úložiště objektů Blob (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Store k **/příklad/data/gutenberg/** složky WASB.

## <a name="performance-considerations-while-using-distcp"></a>Faktory ovlivňující výkon při použití DistCp

Protože DistCp na nejnižší členitost je jeden soubor, nastavení maximálního počtu souběžných kopie je nejdůležitější parametr k optimalizaci pro Data Lake Storage. Počet souběžných kopií řídí nastavování počet mapovačů (**m**) parametr příkazového řádku. Tento parametr určuje maximální počet mapovačů, které se používají ke kopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak zjistím, počet mapovačů použít?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkové paměti YARN** -prvním krokem je určit, k dispozici pro cluster, kde spouštíte úlohy DistCp YARN paměti. Tyto informace jsou k dispozici na portálu Ambari přidružené ke clusteru. Přejděte na YARN a zobrazte kartu Konfigurace pro zobrazení paměti YARN. Chcete-li získat celkové paměti YARN, vynásobte paměti YARN na uzel s počtem uzlů, že máte ve vašem clusteru.

* **Krok 2: Vypočítat počet mapovačů** – hodnota **m** rovná podíl celkové paměti YARN děleného velikostí kontejneru YARN. Informace o velikosti kontejneru YARN je k dispozici na portálu Ambari. Přejděte na YARN a zobrazit na kartě konfigurace. V tomto okně je zobrazena velikost kontejneru YARN. Rovnice, můžete přejít na počet mapovačů (**m**) je

        m = (number of nodes * YARN memory for each node) / YARN container size

**Příklad**

Předpokládejme, že máte 4 D14v2s uzly v clusteru a pokoušíte se převést 10 TB dat z 10 různými složkami. Všechny složky obsahuje různé množství dat a velikostí souboru v rámci každé složky se liší.

* **Celková kapacita paměti YARN**: zjistíte, že paměti YARN 96 GB pro uzel D14 portál z Ambari. Ano v celkové paměti YARN u clusteru se čtyřmi uzly se: 

        YARN memory = 4 * 96GB = 384GB

* **Počet mapovačů**: portál z Ambari zjistíte, že velikost kontejneru YARN 3072 pro D14 uzlu clusteru. Ano počet mapovačů je:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Pokud jiné aplikace používají paměti, je možné použít pro DistCp pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Když je velká velikost datové sady, chcete-li přesunout (například > 1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více DistCp úloh. Pravděpodobně neexistuje žádný zvýšení výkonu, ale šíří navýšení kapacity úloh tak, že pokud některé z úloh selže, stačí restartovat tuto konkrétní úlohu, ne celý projekt.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mapovačů, které jsou podobné jako u velikosti za účelem optimalizace výkonu. Zvýšení počtu mapovačů nemusí vždy zvýšit výkon.

* DistCp je omezeno na pouze jednoho mapování na soubor. Proto by neměl mít více mapovačů, než jste soubory. Protože DistCp lze přiřadit pouze jeden Mapovač do souboru, to omezuje množství souběžnosti, který slouží ke kopírování velkých souborů.

* Pokud máte malé množství velkých souborů, byste je rozdělit do 256 MB bloky dat souborů poskytnout další potenciální souběžnosti. 
