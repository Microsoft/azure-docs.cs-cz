---
title: Zkopírování dat do a z WASB do Data Lake Store pomocí Distcp | Microsoft Docs
description: Použití Distcp nástroj ke zkopírování dat do a z úložiště objektů BLOB Azure do Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: c682ff56f4850638d3cad6e327c1237a0492c4d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Použití Distcp ke kopírování dat mezi objekty blob Azure Storage a službou Data Lake Store
> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Pokud máte cluster služby HDInsight s přístupem do Data Lake Store, můžete použít nástroje pro ekosystém Hadoop jako Distcp ke zkopírování dat **do a z** úložišti clusteru HDInsight (WASB) do účtu Data Lake Store. Tento článek obsahuje pokyny, jak používat nástroj Distcp.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že povolení vzdálené plochy pro cluster.

## <a name="do-you-learn-fast-with-videos"></a>Pomáhají vám při učení videa?
[Přehrát toto video](https://mix.office.com/watch/1liuojvdx6sie) o tom, jak kopírovat data mezi objektů BLOB služby Azure Storage a Data Lake Store pomocí DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití Distcp z clusteru služby HDInsight Linux

Cluster služby HDInsight se dodává s Distcp nástroj, který můžete použít ke zkopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali clusteru HDInsight pomocí Data Lake Store jako další úložiště, může být nástroj Distcp použité out-of-the-box ke zkopírování dat do a z účtu Data Lake Store taky. V této části se podíváme na to, jak používat nástroj Distcp.

1. Z plochy připojte se ke clusteru pomocí SSH. V tématu [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z příkazového řádku SSH.

2. Ověřte, zda máte přístup Azure BLOB Storage (WASB). Spusťte následující příkaz:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Výstup by měl poskytovat seznam obsah v objektu blob úložiště.

3. Stejně tak ověřte, zda účet Data Lake Store můžete přistupovat z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Výstup by měl poskytovat seznam souborů a složek v účtu Data Lake Store.

4. Použití Distcp ke zkopírování dat z WASB do účtu Data Lake Store.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Příkaz zkopíruje obsah **/příklad/data/gutenberg/** složky v WASB k **/myfolder** v účtu Data Lake Store.

5. Podobně použití Distcp ke zkopírování dat z účtu Data Lake Store k WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Store k **/příklad/data/gutenberg/** složky v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Faktory ovlivňující výkon při použití DistCp

Protože DistCp na nejnižší členitost je jeden soubor, nastavení maximální počet souběžných kopie je nejdůležitější parametr za účelem optimalizace pro Data Lake Store. Počet souběžných kopií je řízena nastavením počet mappers ('m ') parametr na příkazovém řádku. Tento parametr určuje maximální počet mappers, které se používají ke zkopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak určit počet mappers používat?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkové paměti YARN** -prvním krokem je určit, k dispozici pro cluster, kde spouštíte úlohu DistCp YARN paměti. Tyto informace jsou k dispozici na portálu Ambari, který je přidružen ke clusteru. Přejděte do YARN a zobrazit kartu konfigurací zobrazíte paměti YARN. Chcete-li získat celkovou velikost paměti YARN, vynásobte YARN paměti na jeden uzel s počet uzlů, že máte v clusteru.

* **Krok 2: Vypočítat počet mappers** – hodnota **m** rovná podíl celkové paměti YARN děleného velikostí YARN kontejneru. Informace o velikosti kontejneru YARN je k dispozici na portálu Ambari. Přejděte do YARN a zobrazit kartu konfigurací. Velikost YARN kontejner se zobrazí v tomto okně. Rovnice přijaty ve počet mappers (**m**) je

        m = (number of nodes * YARN memory for each node) / YARN container size

**Příklad**

Předpokládejme, že máte 4 D14v2s uzly v clusteru a chcete přenést 10 TB dat z 10 jiné složky. Všechny složky obsahuje různých objemy dat a velikosti souborů v rámci každé složky se liší.

* Celkový počet YARN paměť – portál z Ambari, že zjistíte, že je paměť YARN 96 GB pro uzel D14. Ano je celková paměť YARN pro cluster se čtyřmi uzly: 

        YARN memory = 4 * 96GB = 384GB

* Počet mappers - portál z Ambari zjistíte, že velikost YARN kontejneru 3072 pro uzel clusteru s podporou D14. Ano počet mappers je:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Pokud jiné aplikace používají paměť, je možné použít pro DistCp pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování rozsáhlých datových sad

Pokud je velikost datové sady se přesune velké (například > 1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více DistCp úloh. Je pravděpodobné žádné výkonnější, ale šíří se úlohy tak, že pokud se všechny úlohy nezdaří, potřebujete jenom restartujte tuto konkrétní úlohu spíš než celý projekt.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mappers, které jsou podobné ve velikosti za účelem optimalizace výkonu. Zvýšením počtu mappers nemusí vždy zvýšit výkon.

* DistCp je omezený na jedinou mapper na soubor. Proto by neměl mít další mappers, než kolik máte soubory. Vzhledem k tomu, že DistCp lze přiřadit pouze jeden mapper do souboru, toto omezení velikosti souběžnosti, který slouží ke kopírování velkých souborů.

* Pokud máte malý počet velkých souborů, by je rozdělit na 256 MB bloky dat souborů tak, abyste získali další potenciální souběžnosti. 
 
* Pokud kopírujete z účtu úložiště objektů Blob Azure, může vaše úlohu kopírování omezeny na straně úložiště objektů blob. To snižuje výkon kopírování úlohy. Další informace o omezení Azure Blob Storage najdete v tématu omezení Azure Storage v [předplatného Azure a omezení služby](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
