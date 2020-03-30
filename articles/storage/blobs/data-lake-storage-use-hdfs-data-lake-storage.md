---
title: Použití rozhraní PŘÍKAZOVÉHO PŘÍKAZU HDFS s Azure Data Lake Storage Gen2
description: Úvod do rozhraní SEkat ELHdFS pro úložiště datových jezer Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992218"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Použití rozhraní SE konstruum HDFS s datovým úložištěm jezera Gen2

K datům v účtu úložiště můžete přistupovat a spravovat je pomocí rozhraní příkazového řádku stejně jako u [hadoopského distribuovaného souborového systému (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Tento článek obsahuje některé příklady, které vám pomohou začít.

HDInsight poskytuje přístup k distribuovanému kontejneru, který je místně připojený k výpočetním uzlům. K tomuto kontejneru můžete přistupovat pomocí prostředí, které přímo spolupracuje s HDFS a dalšími systémy souborů, které Hadoop podporuje.

Další informace o rozhraní API HDFS naleznete v [oficiální dokumentaci](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) a [v průvodci oprávněními HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Pokud používáte Azure Databricks místo HDInsight a chcete pracovat s daty pomocí rozhraní příkazového řádku, můžete použít Databricks CLI pro interakci se souborovým systémem Databricks. Viz [Datový chotovce Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Použití rozhraní SE konfekčního rozhraní HDFS s clusterem HDInsight Hadoop na Linuxu

Nejprve zavést [vzdálený přístup ke službám](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Pokud vyberete [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ukázkový kód Prostředí PowerShell bude vypadat takto:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Připojovací řetězec najdete v části "Přihlášení SSH + Cluster" v okně clusteru HDInsight na webu Azure Portal. Pověření SSH byla zadána v době vytvoření clusteru.

>[!IMPORTANT]
>Fakturace clusteru HDInsight se spustí po vytvoření clusteru a zastaví se po odstranění clusteru. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Informace o odstranění clusteru naleznete v našem [článku na téma](../../hdinsight/hdinsight-delete-cluster.md). Data uložená v účtu úložiště s povolenou službou Data Lake Storage Gen2 však zůstanou zachována i po odstranění clusteru HDInsight.

## <a name="create-a-container"></a>Vytvoření kontejneru

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Nahraďte `<container-name>` zástupný symbol názvem, který chcete dát kontejneru.

* Nahraďte `<storage-account-name>` zástupný symbol názvem svého účtu úložiště.

## <a name="get-a-list-of-files-or-directories"></a>Získání seznamu souborů nebo adresářů

    hdfs dfs -ls <path>

Nahraďte `<path>` zástupný symbol identifikátorem URI kontejneru nebo složky kontejneru.

Příklad: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Vytvoření adresáře

    hdfs dfs -mkdir [-p] <path>

`<path>` Nahraďte zástupný symbol názvem kořenového kontejneru nebo složky v kontejneru.

Příklad: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Odstranění souboru nebo adresáře

    hdfs dfs -rm <path>

Nahraďte `<path>` zástupný symbol identifikátorem URI souboru nebo složky, které chcete odstranit.

Příklad: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Zobrazení seznamů řízení přístupu (Seznamů Řízení přístupu) souborů a adresářů

    hdfs dfs -getfacl [-R] <path>

Příklad:

`hdfs dfs -getfacl -R /dir`

Viz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Nastavení seznamů AC souborů a adresářů

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Příklad:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Viz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Změna vlastníka souborů

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Viz [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Změnit přidružení skupiny souborů

    hdfs dfs -chgrp [-R] <group> <URI>

Viz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Změna oprávnění souborů

    hdfs dfs -chmod [-R] <mode> <URI>

Viz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Můžete si prohlédnout kompletní seznam příkazů na [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webových stránkách.

## <a name="next-steps"></a>Další kroky

* [Použití účtu s podporou Azure Data Lake Storage Gen2 v Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Informace o seznamech řízení přístupu v souborech a adresářích](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
