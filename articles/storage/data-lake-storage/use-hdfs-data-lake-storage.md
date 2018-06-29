---
title: Pomocí rozhraní příkazového řádku HDFS s Azure Data Lake Storage Gen2 Preview
description: Úvod do rozhraní příkazového řádku HDFS pro Data Lake Storage Gen2 Preview
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75fb07120c78c45d422ee5017eac0afcf0e80859
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060814"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Pomocí rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview umožňuje spravovat a přistupovat k datům, stejně jako s [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Jestli máte cluster služby HDInsight, připojit nebo spustit úlohu Apache Spark pomocí Azure Databricks k provedení analýzy na data uložená v Azure Data Lake Storage Gen2 slouží k načtení a zpracování načtená data rozhraní příkazového řádku (CLI). Zbývající části článku popisuje možnosti, můžete při [Azure Storage tým pracuje na přidání podpory pro Azure Storage Explorer a portálu Azure](https://azure.microsoft.com/roadmap/) -zábavy!

## <a name="hdfs-cli-with-hdinsight"></a>Rozhraní příkazového řádku HDFS s HDInsight

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. V tomto systému souborů je přístupný pomocí prostředí, který komunikuje přímo s HDFS a jiných systémů souborů, které podporuje Hadoop. Níže jsou často používané příkazy a odkazy na užitečné zdroje.

>[!IMPORTANT]
>Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturuje se fakturují za minutu, proto byste měli vždy odstranit cluster, když je již používán (Další informace jak [odstranit cluster](../../hdinsight/hdinsight-delete-cluster.md)). Data uložená v Azure Data Lake Storage Gen2 však trvá i po odstranění clusteru služby HDInsight.

Pokud chcete získat seznam souborů a složek:

    hdfs dfs -ls <args>
K vytvoření adresáře:

    hdfs dfs -mkdir [-p] <paths>
Chcete-li odstranit soubor nebo adresář:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Podívejme cluster systému HDInsight Hadoop v Linuxu nyní jako příklad. Pokud chcete používat rozhraní příkazového řádku HDFS, musíte nejprve vytvořit [vzdáleného přístupu ke službám](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) prostředí PowerShell ukázkový kód bude vypadat takto:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Připojovací řetězec lze nalézt na "SSH + clusteru přihlášení" části okna clusteru HDInsight na portálu Azure. SSH pověření byly zadány v době vytváření clusteru.

Další informace o HDFS rozhraní příkazového řádku najdete v tématu [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a [HDFS oprávnění průvodce](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI s Azure Databricks

Databricks poskytuje snadno použitelné rozhraní příkazového řádku postavená na Databricks REST API. Open-source projekt je hostované na [Githubu](https://github.com/databricks/databricks-cli). Níže jsou často používané příkazy.

Pokud chcete získat seznam souborů a složek:

    dbfs ls [-l]
K vytvoření adresáře:

    dbfs mkdirs
Pokud chcete odstranit soubor:

    dbfs rm [-r]

Dalším způsobem, jak interakci s Databricks jsou poznámkových bloků. Poznámkový blok se primární jazyk, je možné kombinovat jazyky zadáním jazyka % magic příkaz jazyka na začátku buňku. Konkrétně % dílet můžete spustit kód s prostředí v poznámkovém bloku podobně jako v příkladu HDInsight dříve v tomto článku.

Pokud chcete získat seznam souborů a složek:

    %sh ls <args>
K vytvoření adresáře:

    %sh mkdir [-p] <paths>
Chcete-li odstranit soubor nebo adresář:

    %sh rm [-skipTrash] URI [URI ...]

Po spuštění clusteru Spark v Azure Databricks, vytvoříte nový poznámkový blok. Ukázkový skript poznámkového bloku bude vypadat takto:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Další informace o Databricks rozhraní příkazového řádku najdete v tématu [oficiální dokumentaci](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Další informace o poznámkových bloků, najdete v článku [poznámkových bloků](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) části dokumentace.

## <a name="next-steps"></a>Další postup

- [Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Použít účet Azure Data Lake Storage Gen2 podporující v Azure Databricks](./quickstart-create-databricks-account.md) 