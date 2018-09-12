---
title: Kopírování dat do a z WASB do Azure Data Lake Storage Gen1 pomocí Distcp | Dokumentace Microsoftu
description: Pomocí Distcp nástroje pro kopírování dat do a z úložiště objektů BLOB Azure do Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 9740de34fe7cf7d06af1803cc6d77d7e89bbb73f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391517"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Použití Distcp ke kopírování dat mezi objekty BLOB Azure Storage a Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Pokud máte cluster HDInsight s přístupem k Azure Data Lake Storage Gen1, můžete použít nástroje ekosystému Hadoop jako Distcp ke kopírování dat **do a z** úložišti clusteru HDInsight (WASB) do účtu Azure Data Lake Storage Gen1. Tento článek obsahuje pokyny, jak používat nástroj Distcp.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.

## <a name="do-you-learn-fast-with-videos"></a>Pomáhají vám při učení videa?
[V tomto videu](https://mix.office.com/watch/1liuojvdx6sie) o tom, jak kopírovat data mezi objekty BLOB Azure Storage a Data Lake Storage Gen1 pomocí DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití Distcp z clusteru HDInsight Linux

HDInsight cluster se dodává s Distcp nástroj, který slouží ke kopírování dat z různých zdrojů do clusteru služby HDInsight. Pokud jste nakonfigurovali clusteru HDInsight pro použití služby Data Lake Storage Gen1 jako další úložiště, může být nástroj Distcp používané out-of-the-box ke kopírování dat do a z účtu Data Lake Storage Gen1 také. V této části se podíváme na tom, jak používat nástroj Distcp.

1. Z plochy připojte se ke clusteru pomocí SSH. Zobrazit [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z příkazového řádku SSH.

2. Ověřte, zda můžete přístup k objektům BLOB Azure Storage (WASB). Spusťte následující příkaz:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Výstup by měl poskytovat seznam obsahu v objektu blob úložiště.

3. Stejně tak ověřte, zda máte přístup účtu Data Lake Storage Gen1 z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    Výstup by měl poskytovat seznam souborů a složek v účtu Data Lake Storage Gen1.

4. Použití Distcp ke kopírování dat do účtu Data Lake Storage Gen1 z WASB.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Příkaz zkopíruje obsah **/příklad/data/gutenberg/** složky WASB k **/myfolder** v účtu Data Lake Storage Gen1.

5. Podobně použití Distcp ke kopírování dat z účtu Data Lake Storage Gen1 do WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** účtu Data Lake Storage Gen1 do **/příklad/data/gutenberg/** složky WASB.

## <a name="performance-considerations-while-using-distcp"></a>Faktory ovlivňující výkon při použití DistCp

Protože DistCp na nejnižší členitost je jeden soubor, nastavení maximálního počtu souběžných kopie je nejdůležitější parametr k optimalizaci proti Gen1 úložiště Data Lake. Počet souběžných kopií řídí nastavování počet mapovačů (am ") parametr příkazového řádku. Tento parametr určuje maximální počet mapovačů, které se používají ke kopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak zjistím, počet mapovačů použít?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkové paměti YARN** -prvním krokem je určit, k dispozici pro cluster, kde spouštíte úlohy DistCp YARN paměti. Tyto informace jsou k dispozici na portálu Ambari přidružené ke clusteru. Přejděte na YARN a zobrazte kartu Konfigurace pro zobrazení paměti YARN. Chcete-li získat celkové paměti YARN, vynásobte paměti YARN na uzel s počtem uzlů, že máte ve vašem clusteru.

* **Krok 2: Vypočítat počet mapovačů** – hodnota **m** rovná podíl celkové paměti YARN děleného velikostí kontejneru YARN. Informace o velikosti kontejneru YARN je k dispozici na portálu Ambari. Přejděte na YARN a zobrazit na kartě konfigurace. V tomto okně je zobrazena velikost kontejneru YARN. Rovnice, můžete přejít na počet mapovačů (**m**) je

        m = (number of nodes * YARN memory for each node) / YARN container size

**Příklad**

Předpokládejme, že máte 4 D14v2s uzly v clusteru a pokoušíte se převést 10 TB dat z 10 různými složkami. Všechny složky obsahuje různé množství dat a velikostí souboru v rámci každé složky se liší.

* Celkem paměti YARN – portál z Ambari že zjistíte, že paměti YARN 96 GB pro D14 uzel. Ano v celkové paměti YARN u clusteru se čtyřmi uzly se: 

        YARN memory = 4 * 96GB = 384GB

* Počet mapovačů – portál z Ambari zjistíte, že velikost kontejneru YARN 3072 pro D14 uzlu clusteru. Ano počet mapovačů je:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Pokud jiné aplikace používají paměti, je možné použít pro DistCp pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Když je velká velikost datové sady, chcete-li přesunout (například > 1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více DistCp úloh. Pravděpodobně neexistuje žádný zvýšení výkonu, ale šíří navýšení kapacity úloh tak, že pokud některé z úloh selže, stačí restartovat tuto konkrétní úlohu, ne celý projekt.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mapovačů, které jsou podobné jako u velikosti za účelem optimalizace výkonu. Zvýšení počtu mapovačů nemusí vždy zvýšit výkon.

* DistCp je omezeno na pouze jednoho mapování na soubor. Proto by neměl mít více mapovačů, než jste soubory. Protože DistCp lze přiřadit pouze jeden Mapovač do souboru, to omezuje množství souběžnosti, který slouží ke kopírování velkých souborů.

* Pokud máte malé množství velkých souborů, byste je rozdělit do 256 MB bloky dat souborů poskytnout další potenciální souběžnosti. 
 
* Pokud kopírujete z účtu služby Azure Blob Storage, může kopírovat projekt omezeny na straně úložiště objektů blob. To snižuje výkon vaší úlohy kopírování. Další informace o omezeních úložiště objektů Blob v Azure najdete v tématu limity služby Azure Storage na [předplatného Azure a omezení služeb](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
