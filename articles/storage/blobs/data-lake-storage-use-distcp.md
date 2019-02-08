---
title: Kopírování dat do Azure Data Lake Storage Gen2 pomocí DistCp | Dokumentace Microsoftu
description: Pomocí DistCp nástroje pro kopírování dat do a z Data Lake Storage Gen2
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 8328ea5afb66bbecdafbb06dcbf6700194d62f9e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864056"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Použití DistCp ke kopírování dat mezi objekty BLOB Azure Storage a Azure Data Lake Storage Gen2

Můžete použít [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ke kopírování dat mezi účet úložiště pro obecné účely V2 a účet úložiště pro obecné účely V2 s hierarchického oboru názvů povolené. Tento článek obsahuje pokyny, jak používat nástroj DistCp.

DistCp poskytuje celou řadu parametrů příkazového řádku a důrazně doporučujeme, abyste v tomto článku k optimalizaci využití ho. Tento článek popisuje základní funkce při zaměření na jeho použití pro kopírování dat do účtu hierarchického oboru názvů povolené.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Existující účet úložiště Azure bez Data Lake Storage Gen2 funkcích (hierarchického oboru názvů), které přináší**.
* **Účet služby Azure Storage s povolenou funkcí Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v tématu [vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Systém souborů** , který byl vytvořen v účtu úložiště s hierarchického oboru názvů povolené.
* **Azure HDInsight cluster** s přístupem k účtu úložiště pomocí Data Lake Storage Gen2 povolena. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](data-lake-storage-use-hdi-cluster.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití DistCp z clusteru HDInsight Linux

HDInsight cluster se dodává s DistCp nástroj, který slouží ke kopírování dat z různých zdrojů do clusteru služby HDInsight. Pokud nakonfigurujete cluster HDInsight používat Azure Blob Storage a Azure Data Lake Storage společně, může být nástroj DistCp používané out-of-the-box ke kopírování dat mezi také. V této části se podíváme na tom, jak používat nástroj DistCp.

1. Vytvořte relaci SSH k vašemu clusteru HDI. Zobrazit [připojení ke clusteru HDInsight se systémem Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ověřte, zda máte přístup vaší existující obecné účely V2 účtu (bez povolené hierarchického oboru názvů).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Výstup by měl poskytovat seznam obsah v kontejneru.

3. Stejně tak ověřte, zda účtu úložiště můžou přistupovat pomocí hierarchického oboru názvů povolené z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Výstup by měl poskytovat seznam souborů a složek v účtu Data Lake Storage.

4. Použití DistCp ke kopírování dat z WASB do účtu Data Lake Storage.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Příkaz zkopíruje obsah **/příklad/data/gutenberg/** složky v úložišti objektů Blob do **/myfolder** v účtu Data Lake Storage.

5. Podobně použití DistCp ke kopírování dat z účtu Data Lake Storage do úložiště objektů Blob (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Store k **/příklad/data/gutenberg/** složky WASB.

## <a name="performance-considerations-while-using-distcp"></a>Faktory ovlivňující výkon při použití DistCp

Protože DistCp na nejnižší členitost je jeden soubor, nastavení maximálního počtu souběžných kopie je nejdůležitější parametr k optimalizaci pro Data Lake Storage. Počet souběžných kopie je rovna počtu mapovačů (**m**) parametr příkazového řádku. Tento parametr určuje maximální počet mapovačů, které se používají ke kopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak zjistím, počet mapovačů použít?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určit celkové paměti k dispozici do fronty aplikace YARN "default"** -prvním krokem je stanovit paměť k dispozici do fronty aplikace YARN "default". Tyto informace jsou k dispozici na portálu Ambari přidružené ke clusteru. Přejděte na YARN a zobrazit na kartě Konfigurace zobrazíte dostupné do fronty "default" aplikace YARN paměti. Toto je do celkově dostupné paměti pro úlohu DistCp (která je ve skutečnosti úlohu MapReduce).

* **Krok 2: Vypočítat počet mapovačů** – hodnota **m** rovná podíl celkové paměti YARN děleného velikostí kontejneru YARN. Informace o velikosti kontejneru YARN je k dispozici na portálu Ambari. Přejděte na YARN a zobrazit na kartě konfigurace. V tomto okně je zobrazena velikost kontejneru YARN. Rovnice, můžete přejít na počet mapovačů (**m**) je

        m = (number of nodes * YARN memory for each node) / YARN container size

**Příklad**

Předpokládejme, že máte 4 x D14v2s cluster a pokoušíte se převést 10 TB dat z 10 různými složkami. Všechny složky obsahuje různé množství dat a velikostí souboru v rámci každé složky se liší.

* **Celková kapacita paměti YARN**: Z portálu Ambari zjistíte, že paměti YARN 96 GB pro D14 uzel. Ano v celkové paměti YARN u clusteru se čtyřmi uzly se: 

        YARN memory = 4 * 96GB = 384GB

* **Počet mapovačů**: Z portálu Ambari zjistíte, že velikost kontejneru YARN 3,072 MB pro D14 uzlu clusteru. Ano počet mapovačů je:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Pokud jiné aplikace používají paměti, je možné použít pro DistCp pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Když je velká velikost datové sady, chcete-li přesunout (například > 1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více DistCp úloh. Pravděpodobně neexistuje žádný zvýšení výkonu, ale šíří navýšení kapacity úloh tak, že pokud některé z úloh selže, stačí restartovat tuto konkrétní úlohu, ne celý projekt.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mapovačů, které jsou podobné jako u velikosti za účelem optimalizace výkonu. Zvýšení počtu mapovačů nemusí vždy zvýšit výkon.

* DistCp je omezeno na pouze jednoho mapování na soubor. Proto by neměl mít více mapovačů, než jste soubory. Protože DistCp lze přiřadit pouze jeden Mapovač do souboru, to omezuje množství souběžnosti, který slouží ke kopírování velkých souborů.

* Pokud máte malé množství velkých souborů, byste je rozdělit do 256 MB bloky dat souborů poskytnout další potenciální souběžnosti.
