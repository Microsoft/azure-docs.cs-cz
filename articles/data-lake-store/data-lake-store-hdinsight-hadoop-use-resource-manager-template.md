---
title: Vytvoření HDInsight pomocí Azure Data Lake Storage Gen1 pomocí šablony Azure | Dokumentace Microsoftu
description: Použití šablon Azure Resource Manageru k vytvoření a použití clusterů HDInsight s Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: e08e7ae0a97b206a692dd47e91c4c94847cb9833
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235168"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen1 pomocí šablony Azure Resource Manageru
> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Resource Manageru](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí Azure Powershellu ke konfiguraci clusteru služby HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**.

Pro typy podporovaných clusteru je možné Data Lake Storage Gen1 jako výchozím úložištěm nebo dalšího účtu úložiště. Pokud Data Lake Storage Gen1 slouží jako další úložiště, výchozí účet úložiště pro clustery bude stále objekty BLOB Azure Storage (WASB) a soubory související s clusterem (jako jsou protokoly atd.) jsou stále zapsány do výchozího úložiště dat, který chcete proces mohou být uloženy v účtu Data Lake Storage Gen1. Pomocí Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon ani schopnost čtení a zápis do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Pro úložiště clusteru HDInsight pomocí Data Lake Storage Gen1

Tady jsou některé důležité informace týkající se použití HDInsight s Data Lake Storage Gen1:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1 jako výchozí úložiště je k dispozici pro HDInsight verze 3.5 a 3.6.

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1 jako další úložiště je k dispozici pro HDInsight verze 3.2, 3.4, 3.5 a 3.6.

V tomto článku jsme zřízení clusteru Hadoop s Data Lake Storage Gen1 jako další úložiště. Pokyny o tom, jak vytvořit Hadoop cluster s Data Lake Storage Gen1 jako výchozí úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory Service Principal**. Kroky v tomto kurzu poskytují pokyny o tom, jak vytvořit instanční objekt ve službě Azure AD. Však musí být správce Azure AD mohli vytvořit instanční objekt. Pokud jste správce Azure AD, můžete tuto požadovanou součást přeskočit a pokračovat v tomto kurzu.

    **Pokud si nejste správce Azure AD**, nebudete moci provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce Azure AD nejprve vytvořit instanční objekt služby, než vytvoříte HDInsight cluster s Data Lake Storage Gen1. Navíc instanční objekt musí být vytvořen pomocí certifikátu, jak je popsáno v [vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Vytvoření clusteru HDInsight s Data Lake Storage Gen1
Šablony Resource Manageru a předpoklady pro použití šablony, jsou k dispozici na Githubu v [nasazení clusteru HDInsight s Linuxem pomocí nového Gen1 úložiště Data Lake](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postupujte podle pokynů uvedených v tomto odkazu pro vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako další úložiště.

Postupujte podle pokynů v odkazu uvedeného výše vyžadují prostředí PowerShell. Než začnete s využitím těchto pokynů, ujistěte se, že se že přihlásíte ke svému účtu Azure. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragmenty kódu. Po zobrazení výzvy k přihlášení, ujistěte se, že se že přihlásíte jako jeden ze správců/vlastník předplatného:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Šablona nasadí tyto typy prostředků:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Nahrání ukázkových dat do Data Lake Storage Gen1
Šablony Resource Manageru se vytvoří nový účet Data Lake Storage Gen1 a přidruží ji s clusterem HDInsight. Nyní musíte nahrát ukázková data do Data Lake Storage Gen1. Tato data budete potřebovat v pozdější části kurzu ke spouštění úloh z clusteru HDInsight, přístup k datům v účtu Data Lake Storage Gen1. Pokyny, jak nahrát data, najdete v části [nahrání souboru do účtu Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Nastavit příslušné seznamy ACL v ukázkových dat
Pokud chcete mít jistotu, že je přístupný z clusteru HDInsight, který nahrajete ukázková data, musíte zajistit, aby aplikace Azure AD, který se používá k navázání identity mezi clusterem HDInsight a Data Lake Storage Gen1 má přístup k soubor nebo složku, kterou se pokoušíte přístup. Chcete-li to provést, postupujte následovně.

1. Vyhledání názvu aplikace Azure AD, který je přidružený HDInsight cluster a účet Data Lake Storage Gen1. Jedním ze způsobů k vyhledání názvu je a otevřete tak okno clusteru HDInsight, který jste vytvořili pomocí šablony Resource Manageru, klikněte **Identity AAD clusteru** kartu a vyhledejte hodnotu **zobrazovaný název objektu služby**.
2. Nyní poskytuje přístup k této aplikaci Azure AD na soubor nebo složku, kterou chcete získat přístup z clusteru HDInsight. Nastavím správné seznamy ACL na soubor nebo složku v Data Lake Storage Gen1, najdete v článku [zabezpečení dat v Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spouštění testů úloh na clusteru HDInsight pro použití služby Data Lake Storage Gen1
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit testovací úlohy na clusteru pro testování, aby mohli získat přístup ke clusteru HDInsight Gen1 úložiště Data Lake. Uděláte to tak, provedeme ukázkové úlohy Hive, který vytvoří tabulku pomocí ukázkových dat, který jste dříve nahráli do svého účtu Data Lake Storage Gen1.

V této části se SSH do clusteru HDInsight s Linuxem a spustit ukázkový dotaz Hive. Pokud používáte klienta Windows, doporučujeme použít **PuTTY**, který si můžete stáhnout z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o použití klienta PuTTY, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight z Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Jakmile budete připojeni, pomocí následujícího příkazu spusťte rozhraní příkazového řádku Hive:

   ```
   hive
   ```
2. Pomocí rozhraní příkazového řádku zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Zobrazený výstup by měl vypadat přibližně takto:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Přístup k Data Lake Storage Gen1 pomocí příkazů HDFS
Po konfiguraci clusteru HDInsight pro použití služby Data Lake Storage Gen1, můžete použít příkazy prostředí HDFS pro přístup k úložišti.

V této části budete do HDInsight Linux SSH clusteru a spuštění příkazů HDFS. Pokud používáte klienta Windows, doporučujeme použít **PuTTY**, který si můžete stáhnout z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o použití klienta PuTTY, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight z Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Jakmile budete připojeni, použijte následující příkaz systému souborů HDFS zobrazte seznam souborů v účtu Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Tento soubor, který jste dříve nahráli do Data Lake Storage Gen1 zveřejnit.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Můžete také použít `hdfs dfs -put` příkazu některé soubory nahrát do Data Lake Storage Gen1 a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrány.


## <a name="next-steps"></a>Další postup
* [Kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Pomocí Data Lake Storage Gen1 s využitím clusterů Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
