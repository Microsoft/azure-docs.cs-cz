---
title: Použití rozhraní příkazového řádku HDFS s Azure Data Lake Storage Gen2
description: Pro Azure Data Lake Storage Gen2 použijte rozhraní příkazového řádku Hadoop systém souborů DFS (Distributed File System) (HDFS). Vytvoření kontejneru, získání seznamu souborů nebo adresářů a další.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d720aed44358dd314bc4226adb9ad517139cd18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836305"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Použití rozhraní příkazového řádku HDFS s Data Lake Storage Gen2

K datům v účtu úložiště můžete přistupovat a spravovat pomocí rozhraní příkazového řádku, stejně jako v [systém souborů DFS (Distributed File System) Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Tento článek popisuje několik příkladů, které vám pomůžou začít.

HDInsight poskytuje přístup k distribuovanému kontejneru, který je místně připojený k výpočetním uzlům. K tomuto kontejneru můžete přistupovat pomocí prostředí, které přímo spolupracuje se službou HDFS a dalšími systémy souborů, které podporuje Hadoop.

Další informace o HDFS CLI najdete v [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a v příručce k [oprávnění HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) .

>[!NOTE]
>Pokud místo HDInsight používáte Azure Databricks a chcete s daty pracovat pomocí rozhraní příkazového řádku, můžete k interakci se systémem souborů datacihly použít CLI datacihly. Viz [datacihly CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Použití rozhraní příkazového řádku HDFS s clusterem HDInsight Hadoop v systému Linux

Nejdřív navažte [vzdálený přístup ke službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) , ukázkový kód PowerShellu by vypadal takto:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Připojovací řetězec najdete v okně clusteru HDInsight v Azure Portal v části přihlášení SSH + cluster. Přihlašovací údaje SSH byly zadány v době vytváření clusteru.

>[!IMPORTANT]
>Fakturace clusteru HDInsight začíná po vytvoření clusteru a zastaví se, když se cluster odstraní. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Informace o tom, jak odstranit cluster, najdete v našem [článku v tématu](../../hdinsight/hdinsight-delete-cluster.md). Data uložená v účtu úložiště s povoleným Data Lake Storage Gen2 ale trvají i po odstranění clusteru HDInsight.

## <a name="create-a-container"></a>Vytvoření kontejneru

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* `<container-name>`Zástupný symbol nahraďte názvem, který chcete poskytnout kontejneru.

* `<storage-account-name>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.

## <a name="get-a-list-of-files-or-directories"></a>Získání seznamu souborů nebo adresářů

`hdfs dfs -ls <path>`

`<path>`Zástupný text nahraďte identifikátorem URI kontejneru nebo složky kontejneru.

Příklad: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Vytvoření adresáře

`hdfs dfs -mkdir [-p] <path>`

Nahraďte `<path>` zástupný symbol názvem kořenového kontejneru nebo složky ve vašem kontejneru.

Příklad: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Odstranění souboru nebo adresáře

`hdfs dfs -rm <path>`

`<path>`Zástupný text nahraďte identifikátorem URI souboru nebo složky, kterou chcete odstranit.

Příklad: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Zobrazit seznamy Access Control (seznamy ACL) souborů a adresářů

`hdfs dfs -getfacl [-R] <path>`

Příklad:

`hdfs dfs -getfacl -R /dir`

Viz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Nastavení seznamů ACL souborů a adresářů

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Příklad:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Viz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Změna vlastníků souborů

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Viz [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Změna přidružení skupin souborů

`hdfs dfs -chgrp [-R] <group> <URI>`

Viz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Změna oprávnění souborů

`hdfs dfs -chmod [-R] <mode> <URI>`

Viz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Úplný seznam příkazů můžete zobrazit na webu [Průvodce prostředím pro Apache Hadoop 2.4.1 systému souborů](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) .

## <a name="next-steps"></a>Další kroky

* [Použití účtu s možností Azure Data Lake Storage Gen2 v Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Další informace o seznamech řízení přístupu k souborům a adresářům](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
