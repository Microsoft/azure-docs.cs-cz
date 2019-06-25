---
title: Použití HDFS CLI s Azure Data Lake Storage Gen2
description: Úvod do rozhraní HDFS příkazového řádku pro Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 24123278ff353860ff2af59f4fd77645dfc189e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938860"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Pomocí rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

Můžete přístup a spravovat data ve vašem účtu úložiště pomocí rozhraní příkazového řádku, stejně jako při použití [souboru systému HDFS (Hadoop Distributed)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Tento článek obsahuje příklady, které vám pomůžou začít.

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. V tomto systému souborů můžete přistupovat pomocí prostředí, který komunikuje přímo s HDFS a jiných systémů souborů, které podporuje Hadoop.

Další informace o rozhraní příkazového řádku HDFS najdete v článku [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a [Průvodce oprávnění HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Pokud používáte Azure Databricks HDInsight, a chcete pracovat s daty pomocí rozhraní příkazového řádku, můžete použít rozhraní příkazového řádku Databricks k interakci s systému souborů Databricks. Zobrazit [rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Použití HDFS rozhraní příkazového řádku s clusterem HDInsight Hadoop v Linuxu

Nejprve navázat [vzdálený přístup ke službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ukázkový kód Powershellu vypadá takto:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Připojovací řetězec najdete tady "SSH + clusteru přihlášení" okna clusteru HDInsight na webu Azure portal. Přihlašovací údaje SSH byly zadány v době vytváření clusteru.

>[!IMPORTANT]
>Účtování clusteru HDInsight spustí po vytvoření clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Zjistěte, jak odstranit cluster, najdete v našich [článek na téma](../../hdinsight/hdinsight-delete-cluster.md). Data uložená v účtu úložiště pomocí služby Data Lake Storage Gen2 povolené však přetrvává i po odstranění clusteru služby HDInsight.

## <a name="create-a-file-system"></a>Vytvořit systém souborů

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Nahradit `<file-system-name>` zástupným názvem, který chcete udělit systému souborů.

* Nahradit `<storage-account-name>` zástupný symbol s názvem účtu úložiště.

## <a name="get-a-list-of-files-or-directories"></a>Získat seznam souborů či adresářů

    hdfs dfs -ls <path>

Nahradit `<path>` zástupný symbol s identifikátorem URI systému souborů nebo složku systému souborů.

Příklad: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Vytvoření adresáře

    hdfs dfs -mkdir [-p] <path>

Nahradit `<path>` zástupný symbol systému názvu kořenového souboru nebo složky v rámci systému souborů.

Příklad: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Odstranit soubor nebo adresář

    hdfs dfs -rm <path>

Nahradit `<path>` zástupný symbol s identifikátorem URI souboru nebo složky, který chcete odstranit.

Příklad: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Zobrazí seznamy řízení přístupu (ACL) souborů a adresářů

    hdfs dfs -getfacl [-R] <path>

Příklad:

`hdfs dfs -getfacl -R /dir`

Zobrazit [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Nastavení seznamů ACL souborů a adresářů

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Příklad:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobrazit [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Změnit vlastníka soubory

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zobrazit [změnit vlastníka](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Změnit přidružení skupiny souborů

    hdfs dfs -chgrp [-R] <group> <URI>

Zobrazit [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Změnit oprávnění pro soubory

    hdfs dfs -chmod [-R] <mode> <URI>

Zobrazit [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Můžete zobrazit úplný seznam příkazů na [průvodce prostředí systému souborů Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webu.

## <a name="next-steps"></a>Další postup

* [Používat účet Azure Data Lake Storage Gen2 podporující v Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Další informace o seznamy řízení přístupu na soubory a adresáře](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
