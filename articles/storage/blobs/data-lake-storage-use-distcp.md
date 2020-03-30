---
title: Kopírování dat do Azure Data Lake Storage Gen2 pomocí DistCp| Dokumenty společnosti Microsoft
description: Kopírování dat do a z úložiště datového jezera Gen2 pomocí nástroje DistCp
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255532"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Kopírování dat mezi objekty BLOB úložiště Azure a Gen2 úložiště datových úložišť Azure

[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) můžete použít ke kopírování dat mezi účtem úložiště pro obecné účely V2 a účtem úložiště pro obecné účely V2 s povoleným hierarchickým oborem názvů. Tento článek obsahuje pokyny k použití nástroje DistCp.

DistCp poskytuje různé parametry příkazového řádku a důrazně doporučujeme, abyste si přečetli tento článek, abyste optimalizovali jeho používání. Tento článek ukazuje základní funkce a zároveň se zaměřuje na jeho použití pro kopírování dat do hierarchického účtu s povoleným oborem názvů.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Existující účet Azure Storage bez povolených funkcí Data Lake Storage Gen2 (hierarchický obor názvů).**
* **Účet Azure Storage s povolenou funkcí Data Lake Storage Gen2**. Pokyny k vytvoření najdete [v tématu Vytvoření účtu úložiště Azure Data Lake Storage Gen2.](data-lake-storage-quickstart-create-account.md)
* **Souborový systém,** který byl vytvořen v účtu úložiště s povoleným hierarchickým oborem názvů.
* **Cluster Azure HDInsight** s přístupem k účtu úložiště s povoleným úložištěm Data Lake Storage Gen2. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Použití distCp z clusteru HDInsight Linux

Cluster HDInsight je dodáván s nástrojem DistCp, který lze použít ke kopírování dat z různých zdrojů do clusteru HDInsight. Pokud jste nakonfigurovali cluster HDInsight tak, aby používal Azure Blob Storage a Azure Data Lake Storage společně, nástroj DistCp lze použít ioutavací okno ke kopírování dat mezi nimi. V této části se podíváme na to, jak používat nástroj DistCp.

1. Vytvořte relaci SSH do clusteru HDI. Viz [Připojení k linuxovému clusteru HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ověřte, zda máte přístup ke svému stávajícímu účtu V2 pro obecné účely (bez povoleného hierarchického oboru názvů).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Výstup by měl poskytnout seznam obsahu v kontejneru.

3. Podobně ověřte, zda máte přístup k účtu úložiště s hierarchickým oborem názvů povoleným z clusteru. Spusťte následující příkaz:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Výstup by měl poskytnout seznam souborů nebo složek v účtu úložiště datového jezera.

4. Pomocí příkazu DistCp zkopírujte data z wasb do účtu úložiště datového jezera.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Příkaz zkopíruje obsah složky **/example/data/gutenberg/** v úložišti objektů Blob do **/myfolder** v účtu úložiště datového jezera.

5. Podobně použijte DistCp ke kopírování dat z účtu úložiště datového jezera do úložiště objektů blob (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Příkaz zkopíruje obsah **/myfolder** v účtu Data Lake Store do složky **/example/data/gutenberg/** v WASB.

## <a name="performance-considerations-while-using-distcp"></a>Důležité informace o výkonu při používání distCp

Vzhledem k tomu, že nejnižší rozlišovací schopnost distCp je jeden soubor, nastavení maximálního počtu souběžných kopií je nejdůležitějším parametrem pro jeho optimalizaci podle úložiště datového jezera. Počet simultánních kopií se rovná počtu mapperů (**m**) parametru na příkazovém řádku. Tento parametr určuje maximální počet mapovačů, které se používají ke kopírování dat. Výchozí hodnota je 20.

**Příklad**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak zjistím počet mapovačů, které chcete použít?

Tady je několik rad, kterými se můžete řídit.

* **Krok 1: Určete celkovou paměť, která je k dispozici pro "výchozí" frontu aplikací YARN** – prvním krokem je určení paměti dostupné pro "výchozí" frontu aplikací YARN. Tyto informace jsou k dispozici na portálu Ambari přidruženém ke clusteru. Přejděte na YARN a zobrazte kartu Configs, abyste viděli paměť YARN, která je k dispozici pro "výchozí" frontu aplikací. Toto je celková dostupná paměť pro úlohu DistCp (což je ve skutečnosti úloha MapReduce).

* **Krok 2: Vypočítejte počet mapovačů** - hodnota **m** se rovná kvocientu celkové paměti YARN vydělené velikostí kontejneru YARN. Informace o velikosti kontejneru YARN jsou k dispozici také na portálu Ambari. Přejděte na YARN a zobrazte kartu Configs. Velikost kontejneru YARN se zobrazí v tomto okně. Rovnice, která má dospět k počtu mapperů (**m),** je

        m = (number of nodes * YARN memory for each node) / YARN container size

**Příklad**

Předpokládejme, že máte cluster 4x D14v2s a pokoušíte se přenést 10 TB dat z 10 různých složek. Každá složka obsahuje různá množství dat a velikosti souborů v každé složce se liší.

* **Celková paměť YARN**: Z portálu Ambari zjistíte, že paměť YARN je 96 GB pro uzel D14. Takže celková paměť YARN pro cluster čtyř uzlů je: 

        YARN memory = 4 * 96GB = 384GB

* **Počet mapovačů**: Z portálu Ambari zjistíte, že velikost kontejneru YARN je 3 072 MB pro uzel clusteru D14. Takže počet mapovačů je:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Pokud paměť používají jiné aplikace, můžete pro distCp použít pouze část paměti YARN vašeho clusteru.

### <a name="copying-large-datasets"></a>Kopírování velkých datových sad

Pokud je velikost datové sady, která má být přesunuta, velká (například >1 TB) nebo pokud máte mnoho různých složek, měli byste zvážit použití více úloh DistCp. Pravděpodobně neexistuje žádný nárůst výkonu, ale rozloží úlohy tak, aby v případě selhání některé úlohy bylo nutné restartovat tuto konkrétní úlohu, nikoli celou úlohu.

### <a name="limitations"></a>Omezení

* DistCp se pokusí vytvořit mapovače, které mají podobnou velikost pro optimalizaci výkonu. Zvýšení počtu mapovačů nemusí vždy zvýšit výkon.

* DistCp je omezena pouze na jeden mapovač na soubor. Proto byste neměli mít více mapovačů, než máte soubory. Vzhledem k tomu, že DistCp můžete přiřadit pouze jeden mapovač k souboru, to omezuje množství souběžnosti, které lze použít ke kopírování velkých souborů.

* Pokud máte malý počet velkých souborů, měli byste je rozdělit na bloky souborů o velikosti 256 MB, abyste měli větší potenciální souběžnost.
