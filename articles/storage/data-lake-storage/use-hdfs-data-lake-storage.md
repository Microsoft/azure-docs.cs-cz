---
title: Použití HDFS CLI s Azure Data Lake Storage Gen2 ve verzi Preview
description: Úvod do rozhraní HDFS příkazového řádku pro verzi Preview služby Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c5f11cbb12b727f5f308d7a71c51706fa8ec373f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277082"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Pomocí rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Ať už máte cluster HDInsight připojený nebo spuštění úlohy Apache Spark pomocí Azure Databricks provádět analýzu na datech uložených v účtu služby Azure Storage, můžete použít rozhraní příkazového řádku (CLI) pro načítání a manipulaci s načtená data.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS rozhraní příkazového řádku s HDInsight

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. V tomto systému souborů je přístupný pomocí prostředí, který komunikuje přímo s HDFS a jiných systémů souborů, které podporuje Hadoop. Níže jsou uvedeny často používané příkazy a odkazy na užitečné zdroje informací.

>[!IMPORTANT]
>Účtování clusteru HDInsight spustí po vytvoření clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Zjistěte, jak odstranit cluster, najdete v našich [článek na téma](../../hdinsight/hdinsight-delete-cluster.md). Data uložená v účtu úložiště pomocí služby Data Lake Storage Gen2 povolené však přetrvává i po odstranění clusteru služby HDInsight.

Pokud chcete získat seznam souborů či adresářů:

    hdfs dfs -ls <args>
K vytvoření adresáře:

    hdfs dfs -mkdir [-p] <paths>
Pokud chcete odstranit soubor nebo adresář:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Nyní Pojďme se cluster HDInsight Hadoop na Linuxu jako příklad. Pokud chcete používat rozhraní příkazového řádku HDFS, musíte nejprve vytvořit [vzdálený přístup ke službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ukázkový kód Powershellu vypadá takto:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Připojovací řetězec najdete tady "SSH + clusteru přihlášení" okna clusteru HDInsight na webu Azure portal. Přihlašovací údaje SSH byly zadány v době vytváření clusteru.

Další informace o rozhraní příkazového řádku HDFS najdete v článku [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a [HDFS oprávnění průvodce](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI s Azure Databricks

Databricks poskytuje snadno použitelné rozhraní příkazového řádku postavené na rozhraní REST API Databricks. Open source projektu hostována službou [Githubu](https://github.com/databricks/databricks-cli). Níže jsou uvedeny často používané příkazy.

Pokud chcete získat seznam souborů či adresářů:

    dbfs ls [-l]
K vytvoření adresáře:

    dbfs mkdirs
Pokud chcete odstranit soubor:

    dbfs rm [-r]

Jiný způsob interakce s Databricks jsou poznámkové bloky. Zatímco Poznámkový blok má primární jazyk, můžete kombinovat jazyky zadáním jazyk % magický příkaz jazyka na začátku buňky. Konkrétně % TV umožňuje spouštět kód prostředí v poznámkovém bloku podobně jako v příkladu HDInsight dříve v tomto článku.

Pokud chcete získat seznam souborů či adresářů:

    %sh ls <args>
K vytvoření adresáře:

    %sh mkdir [-p] <paths>
Pokud chcete odstranit soubor nebo adresář:

    %sh rm [-skipTrash] URI [URI ...]

Po spuštění clusteru Spark v Azure Databricks vytvoříte Poznámkový blok nový. Ukázkový skript Poznámkový blok bude vypadat takto:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Další informace o rozhraní příkazového řádku Databricks, najdete v článku [oficiální dokumentaci](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Další informace o poznámkových bloků, najdete v článku [poznámkových bloků](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) část dokumentace.

## <a name="next-steps"></a>Další postup

- [Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Používat účet Azure Data Lake Storage Gen2 podporující v Azure Databricks](./quickstart-create-databricks-account.md) 