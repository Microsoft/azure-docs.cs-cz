---
title: Kopírování dat do a z WASB do Azure Data Lake Storage Gen1 pomocí DistCp
description: Pomocí nástroje DistCp můžete kopírovat data do Azure Storage objektů BLOB a z nich Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515682"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Použití DistCp ke kopírování dat mezi objekty blob Azure Storage a Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Pokud máte cluster HDInsight s přístupem k Azure Data Lake Storage Gen1, můžete použít nástroje pro ekosystém Hadoop, jako je DistCp, ke kopírování dat do úložiště clusteru HDInsight (WASB) do účtu Data Lake Storage Gen1. V tomto článku se dozvíte, jak používat nástroj DistCp.

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Další informace najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití DistCp z clusteru HDInsight Linux

Cluster An HDInsight se dodává s nástrojem DistCp, který se dá použít ke kopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali cluster HDInsight tak, aby používal Data Lake Storage Gen1 jako další úložiště, můžete k kopírování dat do a z účtu Data Lake Storage Gen1 použít DistCp. V této části se podíváme na to, jak používat nástroj DistCp.

1. Z plochy se připojte ke clusteru pomocí SSH. Viz [připojení ke clusteru HDInsight se systémem Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z příkazového řádku SSH.

1. Ověřte, zda máte přístup k objektům blob Azure Storage (WASB). Spusťte následující příkaz:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Výstup poskytuje seznam obsahu v objektu BLOB úložiště.

1. Podobně ověřte, zda máte přístup k účtu Data Lake Storage Gen1 z clusteru. Spusťte následující příkaz:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Výstup poskytuje seznam souborů a složek v účtu Data Lake Storage Gen1.

1. Pomocí DistCp můžete kopírovat data z WASB na účet Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Příkaz zkopíruje obsah složky **/example/data/Gutenberg/** v WASB na **/myfolder** v účtu Data Lake Storage Gen1.

1. Podobně použijte DistCp ke zkopírování dat z Data Lake Storage Gen1 účtu do WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Příkaz zkopíruje obsah **/myFolder** v účtu Data Lake Storage Gen1 do složky **/example/data/Gutenberg/** v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Požadavky na výkon při použití DistCp

Vzhledem k tomu, že je nejnižší členitost nástroje DistCp jediným souborem, je nastavení maximálního počtu souběžných kopií nejdůležitějším parametrem pro jejich optimalizaci proti Data Lake Storage Gen1. Počet souběžných kopií můžete řídit nastavením parametru počet mapovačů ('s) na příkazovém řádku. Tento parametr určuje maximální počet mapovačů, které se použijí ke kopírování dat. Výchozí hodnota je 20.

Příklad:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Jak určit počet mapovačů k použití

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: určení celkové paměti příze** – prvním krokem je určení paměti příze dostupné clusteru, na kterém spouštíte úlohu DistCp. Tyto informace jsou k dispozici na portálu Ambari přidruženém ke clusteru. Přejděte na PŘÍZe a zobrazte kartu **Konfigurace** , aby se zobrazila paměť příze. Pro získání celkové paměti PŘÍZe vynásobte paměť PŘÍZe na uzel počtem uzlů, které máte ve vašem clusteru.

* **Krok 2: výpočet počtu mapovačů** – hodnota **m** se rovná PODÍLu celkové paměti příze DĚLENé velikostí kontejneru příze. Informace o velikosti kontejneru PŘÍZe jsou také k dispozici na portálu Ambari. Přejděte na PŘÍZe a zobrazte kartu **Konfigurace** . V tomto okně se zobrazí velikost kontejneru PŘÍZe. Rovnice pro doručení do počtu mapovačů (**m**) je:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Příklad:

Pojďme předpokládat, že máte čtyři uzly D14v2s v clusteru a chcete přenést 10 TB dat z 10 různých složek. Každá složka obsahuje různé objemy dat a velikosti souborů v jednotlivých složkách se liší.

* Celková paměť PŘÍZe – z portálu Ambari zjistíte, že paměť PŘÍZe je 96 GB pro uzel D14. Proto je celková paměť PŘÍZe pro cluster se čtyřmi uzly:

   `YARN memory = 4 * 96GB = 384GB`

* Počet mapovačů – na portálu Ambari zjistíte, že velikost kontejneru PŘÍZe je 3072 pro uzel clusteru D14. Proto je počet mapovačů:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Pokud jiné aplikace používají paměť, můžete se rozhodnout, že budete používat jenom část paměti PŘÍZe clusteru pro DistCp.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Když velikost datové sady, která se má přesunout, je velká (například > 1 TB) nebo pokud máte mnoho různých složek, zvažte použití několika úloh DistCp. Pravděpodobně není k dispozici žádný výkon, ale rozšíří úlohy tak, aby v případě, že dojde k chybě jakékoli úlohy, je nutné restartovat pouze tuto konkrétní úlohu místo celé úlohy.

### <a name="limitations"></a>Omezení

* DistCp se snaží vytvořit mapovače, které mají pro optimalizaci výkonu podobnou velikost. Zvýšení počtu mapovačů nemusí vždy zvyšovat výkon.

* DistCp je omezené jenom na jedno mapování na soubor. Proto byste neměli mít větší mapovače než soubory. Vzhledem k tomu, že DistCp může přiřazovat pouze jedno mapovači souboru, omezuje množství souběžnosti, které lze použít ke kopírování velkých souborů.

* Pokud máte malý počet velkých souborů, rozdělte je do bloků 256 MB souborů a poskytněte vám tak více potenciálních souběžnosti.

* Pokud kopírujete z účtu služby Azure Blob Storage, může být vaše úloha kopírování omezena na straně úložiště objektů BLOB. Tím se snižuje výkon úlohy kopírování. Další informace o limitech úložiště objektů BLOB v Azure najdete v tématu omezení Azure Storagech v rámci [předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Viz také

* [Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
