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
ms.openlocfilehash: c59331c772e140fccfefb89eef086a35837171e1
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576971"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Pomocí rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Ať už máte cluster HDInsight připojený nebo spuštění úlohy Apache Spark pomocí Azure Databricks provádět analýzy s daty uloženými v Azure Data Lake Storage Gen2 můžete použít rozhraní příkazového řádku (CLI) pro načítání a manipulaci s načtená data. Zbytek tohoto článku popisuje možnosti mít současně přitom [tým služby Azure Storage pracuje na přidání podpory pro Průzkumníka služby Azure Storage a Azure portal](https://azure.microsoft.com/roadmap/).

## <a name="hdfs-cli-with-hdinsight"></a>HDFS rozhraní příkazového řádku s HDInsight

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. V tomto systému souborů je přístupný pomocí prostředí, který komunikuje přímo s HDFS a jiných systémů souborů, které podporuje Hadoop. Níže jsou uvedeny často používané příkazy a odkazy na užitečné zdroje informací.

>[!IMPORTANT]
>Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturuje se fakturují za minutu, takže cluster měli odstranit vždy když se už používá (zjistěte, jak [odstranění clusteru](../../hdinsight/hdinsight-delete-cluster.md)). Data uložená v Azure Data Lake Storage Gen2 však přetrvává i po odstranění clusteru služby HDInsight.

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