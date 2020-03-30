---
title: Kopírování dat do a z WASB do Azure Data Lake Storage Gen1 pomocí DistCp
description: Použití nástroje DistCp ke kopírování dat do objektů blob azure storage a z nich do Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638829"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Kopírování dat mezi objekty BLOB úložiště Azure a Gen1 úložiště datových úložišť Azure

> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Pokud máte cluster HDInsight s přístupem k Azure Data Lake Storage Gen1, můžete použít nástroje ekosystému Hadoop, jako je DistCp ke kopírování dat do a z úložiště clusteru HDInsight (WASB) do účtu Storage Data Lake Gen1. Tento článek ukazuje, jak používat nástroj DistCp.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete [v tématu Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Viz [Vytvoření clusteru HDInsight s gen1 úložiště datového jezera](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití distCp z clusteru HDInsight Linux

Cluster HDInsight je dodáván s nástrojem DistCp, který lze použít ke kopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali cluster HDInsight tak, aby používal data Lake Storage Gen1 jako další úložiště, můžete použít distCp out-of-the-box ke kopírování dat do a z účtu Gen1 úložiště datového jezera. V této části se podíváme na to, jak používat nástroj DistCp.

1. Z plochy se pomocí SSH připojte ke clusteru. Viz [Připojení k linuxovému clusteru HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Spusťte příkazy z výzvy SSH.

1. Ověřte, jestli máte přístup k objektům BLOB úložiště Azure (WASB). Spusťte následující příkaz:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Výstup obsahuje seznam obsahu v objektu blob úložiště.

1. Podobně ověřte, zda máte přístup k účtu Data Lake Storage Gen1 z clusteru. Spusťte následující příkaz:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Výstup obsahuje seznam souborů a složek v účtu Data Lake Storage Gen1.

1. Pomocí distCp zkopírujte data z WASB do účtu Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Příkaz zkopíruje obsah složky **/example/data/gutenberg/** v wasb do **/myfolder** v účtu Data Lake Storage Gen1.

1. Podobně použijte DistCp ke kopírování dat z účtu Data Lake Storage Gen1 do WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Storage Gen1 do složky **/example/data/gutenberg/** v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Důležité informace o výkonu při používání distCp

Vzhledem k tomu, že nejnižší rozlišovací schopnost nástroje DistCp je jeden soubor, je nastavení maximálního počtu simultánních kopií nejdůležitějším parametrem pro jeho optimalizaci podle data lake storage gen1. Počet simultánních kopií můžete řídit nastavením počtu mapovačů (m) na příkazovém řádku. Tento parametr určuje maximální počet mapovačů, které se používají ke kopírování dat. Výchozí hodnota je 20.

Příklad:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Jak zjistit počet mapovačů, které chcete použít

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určení celkové paměti YARN** – prvním krokem je určení paměti YARN, která je k dispozici pro cluster, ve kterém spustíte úlohu DistCp. Tyto informace jsou k dispozici na portálu Ambari přidruženém ke clusteru. Přejděte na YARN a zobrazte kartu **Configs,** abyste viděli paměť YARN. Chcete-li získat celkovou paměť YARN, vynásobte paměť YARN na uzel počtem uzlů, které máte v clusteru.

* **Krok 2: Vypočítejte počet mapovačů** - hodnota **m** se rovná kvocientu celkové paměti YARN vydělené velikostí kontejneru YARN. Informace o velikosti kontejneru YARN jsou také k dispozici na portálu Ambari. Přejděte na YARN a zobrazte kartu **Configs.** Velikost kontejneru YARN se zobrazí v tomto okně. Rovnice, která má dospět k počtu mapperů (**m**), je:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Příklad:

Předpokládejme, že máte v clusteru čtyři uzly D14v2s a chcete přenést 10 TB dat z 10 různých složek. Každá složka obsahuje různá množství dat a velikosti souborů v každé složce se liší.

* Celková paměť YARN - Z portálu Ambari zjistíte, že paměť YARN je 96 GB pro uzel D14. Takže celková paměť YARN pro cluster čtyř uzlů je:

   `YARN memory = 4 * 96GB = 384GB`

* Počet mapovačů – z portálu Ambari zjistíte, že velikost kontejneru YARN je 3072 pro uzel clusteru D14. Takže počet mapovačů je:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Pokud paměť používají jiné aplikace, můžete pro distCp použít pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Pokud je velikost datové sady, která má být přesunuta, velká (například > 1 TB) nebo pokud máte mnoho různých složek, zvažte použití více úloh DistCp. Pravděpodobně neexistuje žádný nárůst výkonu, ale rozloží úlohy tak, že pokud se některá úloha nezdaří, je třeba restartovat pouze tuto konkrétní úlohu namísto celé úlohy.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mapovače, které mají podobnou velikost pro optimalizaci výkonu. Zvýšení počtu mapovačů nemusí vždy zvýšit výkon.

* DistCp je omezena pouze na jeden mapovač na soubor. Proto byste neměli mít více mapovačů, než máte soubory. Vzhledem k tomu, že DistCp může přiřadit pouze jeden mapovač k souboru, omezuje to množství souběžnosti, které lze použít ke kopírování velkých souborů.

* Pokud máte malý počet velkých souborů, rozdělte je na bloky souborů o velikosti 256 MB, abyste měli k tomu větší potenciální souběžnost.

* Pokud kopírujete z účtu úložiště objektů Blob Azure, může být úloha kopírování omezena na straně úložiště objektů blob. Tím se sníží výkon úlohy kopírování. Další informace o limitech úložiště objektů blob Azure najdete v tématu Limity úložiště Azure na [limitech předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Viz také

* [Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
