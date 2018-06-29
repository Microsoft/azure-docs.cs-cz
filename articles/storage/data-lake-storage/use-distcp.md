---
title: Ke zkopírování dat do Azure Data Lake Storage Gen2 Preview použití Distcp | Microsoft Docs
description: Použití Distcp nástroj a zkopírujte data do a z Data Lake Storage Gen2 Preview
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 2a958ceb0b3a1db9d06d045a8161fa6cd3ef5aba
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059922"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Použití Distcp ke kopírování dat mezi objektů BLOB služby Azure Storage a Data Lake Storage Gen2 Preview

Pokud máte cluster služby HDInsight s přístupem k Azure Data Lake Storage Gen2 Preview, můžete použít nástroje pro ekosystém Hadoop jako Distcp ke zkopírování dat **do a z** úložišti clusteru HDInsight (WASB) do s podporou dat Gen2 úložiště Lake účet. Tento článek obsahuje pokyny, jak používat nástroj Distcp.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure s povolenou funkcí Azure Data Lake Storage (Preview)**. Pokyny o tom, jak vytvořit najdete v tématu [TODO](quickstart-create-account.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage. V tématu [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](use-hdi-cluster.md). Ujistěte se, že povolení vzdálené plochy pro cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití Distcp z clusteru služby HDInsight Linux

Cluster služby HDInsight se dodává s Distcp nástroj, který můžete použít ke zkopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali clusteru HDInsight do Azure Blob Storage a Azure Data Lake Storage společně použít, může být nástroj Distcp použité out-of-the-box ke kopírování dat mezi také. V této části se podíváme na to, jak používat nástroj Distcp.

1. Z plochy připojte se ke clusteru pomocí SSH. V tématu [připojení ke clusteru HDInsight se systémem Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z příkazového řádku SSH.

2. Ověřte, zda máte přístup Azure BLOB Storage (WASB). Spusťte následující příkaz:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Výstup by měl poskytovat seznam obsah v objektu blob úložiště.

3. Stejně tak ověřte, zda máte přístup k účtu Data Lake Storage z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/

    Výstup by měl poskytovat seznam souborů a složek v účtu Data Lake Storage.

4. Použití Distcp ke zkopírování dat z WASB na účet Data Lake Storage.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder

    Příkaz zkopíruje obsah **/příklad/data/gutenberg/** složku v úložišti objektů Blob k **/myfolder** v účtu Data Lake Storage.

5. Podobně použití Distcp ke zkopírování dat z účtu Data Lake Storage úložiště objektů Blob (WASB).

        hadoop distcp abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Store k **/příklad/data/gutenberg/** složky v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Faktory ovlivňující výkon při použití DistCp

Protože DistCp na nejnižší členitost je jeden soubor, nastavení maximální počet souběžných kopie je nejdůležitější parametr za účelem optimalizace pro Data Lake Storage. Počet souběžných kopií je řízena nastavením počet mappers ('m ') parametr na příkazovém řádku. Tento parametr určuje maximální počet mappers, které se používají ke zkopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder -m 100

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
