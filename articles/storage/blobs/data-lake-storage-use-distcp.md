---
title: Kopírování dat do Azure Data Lake Storage Gen2 pomocí DistCp | Microsoft Docs
description: Kopírování dat do a z Azure Data Lake Storage Gen2 pomocí nástroje Apache Hadoop Distributed Copy (DistCp).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912480"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen2

Pomocí [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) můžete kopírovat data mezi účtem úložiště pro obecné účely v2 a účtem úložiště pro obecné účely v2 s povoleným hierarchickým oborem názvů. Tento článek poskytuje pokyny pro použití nástroje DistCp.

DistCp poskytuje nejrůznější parametry příkazového řádku a důrazně doporučujeme, abyste si tento článek přečetli, abyste mohli optimalizovat jeho využití. Tento článek ukazuje základní funkce a zaměřuje se na její použití ke kopírování dat na účet s povoleným hierarchickým oborem názvů.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Existující účet Azure Storage bez povolených možností Data Lake Storage Gen2 (hierarchický obor názvů).
* Azure Storage účet s povolenými možnostmi Data Lake Storage Gen2 (hierarchický obor názvů). Pokyny, jak ho vytvořit, najdete v tématu [Vytvoření účtu Azure Storage](../common/storage-account-create.md) .
* Kontejner, který byl vytvořen v účtu úložiště s povoleným hierarchickým oborem názvů.
* Cluster Azure HDInsight s přístupem k účtu úložiště s povolenou funkcí hierarchického oboru názvů. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití DistCp z clusteru HDInsight Linux

Cluster An HDInsight se dodává s nástrojem DistCp, který se dá použít ke kopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali cluster HDInsight na používání Azure Blob Storage a Azure Data Lake Storage společně, můžete k kopírování dat mezi i použít nástroj DistCp. V této části se podíváme na použití nástroje DistCp.

1. Vytvořte relaci SSH ke clusteru HDI. Viz [připojení ke clusteru HDInsight se systémem Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ověřte, jestli máte přístup k vašemu stávajícímu účtu pro obecné účely v2 (bez povoleného hierarchického oboru názvů).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   Výstup by měl poskytnout seznam obsahu v kontejneru.

3. Podobně ověřte, zda máte přístup k účtu úložiště s povoleným hierarchickým oborem názvů z clusteru. Spusťte následující příkaz:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    Výstup by měl poskytnout seznam souborů nebo složek v účtu úložiště Data Lake.

4. Pomocí DistCp můžete kopírovat data z WASB na účet Data Lake Storage.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    Příkaz zkopíruje obsah složky **/example/data/Gutenberg/** v úložišti objektů blob do **/myFolder** účtu Data Lake Storage.

5. Podobně použijte DistCp ke zkopírování dat z Data Lake Storage účtu do Blob Storage (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    Příkaz zkopíruje obsah **/myFolder** v účtu Data Lake Store do složky **/example/data/Gutenberg/** v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Požadavky na výkon při použití DistCp

Vzhledem k tomu, že je nejnižší členitost DistCp jediným souborem, je nastavení maximálního počtu souběžných kopií nejdůležitějším parametrem pro jejich optimalizaci proti Data Lake Storage. Počet souběžných kopií se rovná parametru počet mapovačů (**m**) na příkazovém řádku. Tento parametr určuje maximální počet mapovačů, které se použijí ke kopírování dat. Výchozí hodnota je 20.

**Příklad**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Návody určit počet mapovačů, které se mají použít?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: určení celkové dostupné paměti pro výchozí frontu přízových aplikací** – prvním krokem je určení paměti dostupné pro výchozí frontu příz aplikace. Tyto informace jsou k dispozici na portálu Ambari přidruženém ke clusteru. Přejděte na PŘÍZe a zobrazte kartu Konfigurace, abyste viděli paměť PŘÍZe dostupnou pro výchozí frontu aplikací. Toto je celková dostupná paměť pro vaši úlohu DistCp (která je ve skutečnosti úlohou MapReduce).

* **Krok 2: výpočet počtu mapovačů** – hodnota **m** se rovná PODÍLu celkové paměti příze DĚLENé velikostí kontejneru příze. Informace o velikosti kontejneru PŘÍZe jsou k dispozici také na portálu Ambari. Přejděte na PŘÍZe a zobrazte kartu konfigurace. V tomto okně se zobrazí velikost kontejneru PŘÍZe. Rovnice pro doručení do počtu mapovačů (**m**) je

    m = (počet uzlů × paměť PŘÍZe pro každý uzel)/velikost kontejneru PŘÍZe

**Příklad**

Předpokládejme, že máte cluster s 4x D14v2s a snažíte se přenést 10 TB dat z 10 různých složek. Každá složka obsahuje různé objemy dat a velikosti souborů v jednotlivých složkách se liší.

* **Celková paměť příze**: z portálu Ambari zjistíte, že paměť příze je 96 GB pro uzel D14. Proto je celková paměť PŘÍZe pro cluster se čtyřmi uzly: 

    Paměť PŘÍZ = 4 * 96GB = 384GB

* **Počet mapovačů**: na portálu Ambari zjistíte, že velikost kontejneru příze je 3 072 MB pro uzel clusteru D14. Proto je počet mapovačů:

    m = (4 uzly * 96GB)/3072MB = mapovače 128

Pokud jiné aplikace používají paměť, můžete zvolit, že chcete použít jenom část paměti PŘÍZe clusteru pro DistCp.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Když velikost datové sady, která se má přesunout, je velká (například >1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více úloh DistCp. Je možné, že nedojde k nárůstu výkonu, ale rozšíří úlohy tak, aby v případě, že dojde k chybě jakékoli úlohy, stačí restartovat pouze tuto konkrétní úlohu, nikoli celou úlohu.

### <a name="limitations"></a>Omezení

* DistCp se snaží vytvořit mapovače, které mají pro optimalizaci výkonu podobnou velikost. Zvýšení počtu mapovačů nemusí vždy zvyšovat výkon.

* DistCp je omezené jenom na jedno mapování na soubor. Proto byste neměli mít větší mapovače než soubory. Vzhledem k tomu, že DistCp může přiřazovat pouze jedno Mapovač k souboru, omezuje to velikost souběžnosti, kterou lze použít ke kopírování velkých souborů.

* Pokud máte malý počet velkých souborů, pak je byste měli rozdělit na bloky dat souborů 256 MB, abyste měli k dispozici více potenciálních souběžnosti.