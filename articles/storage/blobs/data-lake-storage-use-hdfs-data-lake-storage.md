---
title: Použití HDFS CLI s Azure Data Lake Storage Gen2 ve verzi Preview
description: Úvod do rozhraní HDFS příkazového řádku pro verzi Preview služby Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2691346433b3cc29b22446152ea797cefb02a380
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241266"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Pomocí rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview umožňuje spravovat a přistupovat k datům, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Ať už máte cluster HDInsight připojený nebo spuštění úlohy Apache Spark pomocí Azure Databricks provádět analýzu na datech uložených v účtu služby Azure Storage, můžete použít rozhraní příkazového řádku (CLI) pro načítání a manipulaci s načtená data.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS rozhraní příkazového řádku s HDInsight

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. V tomto systému souborů je přístupný pomocí prostředí, který komunikuje přímo s HDFS a jiných systémů souborů, které podporuje Hadoop. Níže jsou uvedeny často používané příkazy a odkazy na užitečné zdroje informací.

>[!IMPORTANT]
>Účtování clusteru HDInsight spustí po vytvoření clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Zjistěte, jak odstranit cluster, najdete v našich [článek na téma](../../hdinsight/hdinsight-delete-cluster.md). Data uložená v účtu úložiště pomocí služby Data Lake Storage Gen2 povolené však přetrvává i po odstranění clusteru služby HDInsight.

### <a name="get-a-list-of-files-or-directories"></a>Získat seznam souborů či adresářů

    hdfs dfs -ls <args>

### <a name="create-a-directory"></a>Vytvoření adresáře

    hdfs dfs -mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Odstranit soubor nebo adresář

    hdfs dfs -rm [-skipTrash] URI [URI ...]

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Použití HDFS rozhraní příkazového řádku s clusterem HDInsight Hadoop v Linuxu

Nejprve navázat [vzdálený přístup ke službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ukázkový kód Powershellu vypadá takto:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Připojovací řetězec najdete tady "SSH + clusteru přihlášení" okna clusteru HDInsight na webu Azure portal. Přihlašovací údaje SSH byly zadány v době vytváření clusteru.

Další informace o rozhraní příkazového řádku HDFS najdete v článku [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a [HDFS oprávnění průvodce](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Další informace týkající se seznamů ACL ve službě Databricks, najdete v článku [tajné kódy rozhraní příkazového řádku](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli). 

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI s Azure Databricks

Databricks poskytuje snadno použitelné rozhraní příkazového řádku postavené na rozhraní REST API Databricks. Open source projektu hostována službou [Githubu](https://github.com/databricks/databricks-cli). Níže jsou uvedeny často používané příkazy.

### <a name="get-a-list-of-files-or-directories"></a>Získat seznam souborů či adresářů

    dbfs ls [-l]

### <a name="create-a-directory"></a>Vytvoření adresáře

    dbfs mkdirs

### <a name="delete-a-file"></a>Odstranění souboru

    dbfs rm [-r]

Jiný způsob interakce s Databricks jsou poznámkové bloky. Zatímco Poznámkový blok má primární jazyk, můžete kombinovat jazyky zadáním jazyk % magický příkaz jazyka na začátku buňky. Konkrétně % TV umožňuje spouštět kód prostředí v poznámkovém bloku podobně jako v příkladu HDInsight dříve v tomto článku.

### <a name="get-a-list-of-files-or-directories"></a>Získat seznam souborů či adresářů

    %sh ls <args>

### <a name="create-a-directory"></a>Vytvoření adresáře

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Odstranit soubor nebo adresář

    %sh rm [-skipTrash] URI [URI ...]

Po spuštění clusteru Spark v Azure Databricks vytvoříte Poznámkový blok nový. Ukázkový skript Poznámkový blok bude vypadat takto:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Další informace o rozhraní příkazového řádku Databricks, najdete v článku [oficiální dokumentaci](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Další informace o poznámkových bloků, najdete v článku [poznámkových bloků](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) část dokumentace.

## <a name="set-file-and-directory-level-permissions"></a>Nastavte oprávnění na úrovni souborů a adresářů

Nastavení a získání přístupových oprávnění na úrovni souborů a adresářů. Tady je několik příkazů, které vám pomůžou začít. 

Další informace o souboru a adresáře úrovně oprávnění pro systém souborů Azure Data Lake Gen2 najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Zobrazí seznamy řízení přístupu (ACL) souborů a adresářů

    hdfs dfs -getfacl [-R] <path>

Příklad:

`hdfs dfs -getfacl -R /dir`

Zobrazit [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Nastavení seznamů ACL souborů a adresářů

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Příklad:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobrazit [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Změnit vlastníka soubory

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zobrazit [změnit vlastníka](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Změnit přidružení skupiny souborů

    hdfs dfs -chgrp [-R] <group> <URI>

Zobrazit [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Změnit oprávnění pro soubory

    hdfs dfs -chmod [-R] <mode> <URI>

Zobrazit [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Můžete zobrazit úplný seznam příkazů na [průvodce prostředí systému souborů Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webu.

## <a name="next-steps"></a>Další postup

[Používat účet Azure Data Lake Storage Gen2 podporující v Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 
