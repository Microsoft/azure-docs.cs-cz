---
title: Použití šablon Azure k vytvoření HDInsightu pomocí Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Použití šablon Azure Resource Manageru k vytváření a používání clusterů HDInsight s Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161372"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Vytvoření clusteru HDInsight pomocí Azure Data Lake Storage Gen1 pomocí šablony Azure Resource Manager
> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí Azure PowerShellu nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**.

Pro podporované typy clusterů lze gen1 úložiště datového jezera použít jako výchozí úložiště nebo další účet úložiště. Při použití data Lake Storage Gen1 jako další úložiště, výchozí účet úložiště pro clustery bude stále Azure Storage Objekty BLOB (WASB) a soubory související s clusterem (například protokoly, atd.) jsou stále zapsány do výchozího úložiště, zatímco data, která chcete proces ukládat v účtu Data Lake Storage Gen1. Použití Data Lake Storage Gen1 jako další účet úložiště nemá vliv na výkon nebo schopnost číst a zapisovat do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Použití úložiště datového jezera Gen1 pro úložiště clusteru HDInsight

Tady jsou některé důležité aspekty používání HDInsightu s datovým lakem Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k úložišti Data Lake Storage Gen1 jako výchozí úložiště je k dispozici pro HDInsight verze 3.5 a 3.6.

* Možnost vytvářet clustery HDInsight s přístupem k úložišti Data Lake Storage Gen1, protože další úložiště je k dispozici pro hdinsight verze 3.2, 3.4, 3.5 a 3.6.

V tomto článku zřídíme cluster Hadoop s Datovým jezerem Storage Gen1 jako další úložiště. Pokyny k vytvoření clusteru Hadoop s datovým úložištěm Jezera Gen1 jako výchozího úložiště najdete [v tématu Vytvoření clusteru HDInsight s gen1 úložiště datových jezer pomocí portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Hlavní povinný ke službě Azure Active Directory**. Kroky v tomto kurzu poskytují pokyny, jak vytvořit instanční objekt ve službě Azure AD. Musíte však být správce azure ad, aby bylo možné vytvořit instanční objekt. Pokud jste správce Azure AD, můžete přeskočit tento předpoklad a pokračovat v kurzu.

    **Pokud nejste správce azure ad**, nebude možné provést kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce služby Azure AD nejprve vytvořit instanční objekt, než budete moct vytvořit cluster HDInsight s Gen1 úložiště datového jezera. Instanční objekt musí být také vytvořen pomocí certifikátu, jak je popsáno v na [vytvořit instanční objekt s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Vytvoření clusteru HDInsight s gen1 úložiště datového jezera
Šablona Správce prostředků a předpoklady pro použití šablony jsou k dispozici na GitHubu v [clusteru Deploy a HDInsight Linux s novým modelem Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Podle pokynů uvedených na tomto odkazu vytvořte cluster HDInsight s datovým lakem Gen1 jako další úložiště.

Pokyny na výše uvedeném odkazu vyžadují prostředí PowerShell. Než začnete s těmito pokyny, ujistěte se, že se přihlásíte ke svému účtu Azure. Na ploše otevřete nové okno Azure PowerShellu a zadejte následující úryvky. Po zobrazení výzvy k přihlášení se ujistěte, že se přihlásíte jako jeden ze správců/vlastníků předplatného:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Šablona nasazuje tyto typy prostředků:

* [Microsoft.DataLakeStore/účty](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clustery](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Nahrání ukázkových dat do úložiště datového jezera Gen1
Šablona Správce prostředků vytvoří nový účet Data Lake Storage Gen1 a přidruží ho k clusteru HDInsight. Nyní musíte nahrát některé ukázková data do data Lake Storage Gen1. Tato data budete potřebovat později v kurzu ke spuštění úloh z clusteru HDInsight, které přistupují k datům v účtu Data Lake Storage Gen1. Pokyny k nahrání dat najdete [v tématu Nahrání souboru do účtu Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Nastavení příslušných hodnot AC na ukázkových datech
Chcete-li se ujistit, že ukázková data, která nahrajete, jsou přístupná z clusteru HDInsight, musíte zajistit, aby aplikace Azure AD, která se používá k vytvoření identity mezi clusterem HDInsight a Gen1 úložiště datového jezera, má přístup k souboru nebo složce, ke které se pokoušíte získat přístup. Chcete-li to provést, proveďte následující kroky.

1. Najděte název aplikace Azure AD, která je přidružená ke clusteru HDInsight a účtu Data Lake Storage Gen1. Jedním ze způsobů, jak tento název vyhledat, je otevřít okno clusteru HDInsight, které jste vytvořili pomocí šablony Správce prostředků, kliknout na kartu **Identita služby AAD clusteru** a vyhledat hodnotu **zobrazovaného názvu Hlavní ho servisu**.
2. Teď poskytněte přístup k této aplikaci Azure AD na souboru nebo složce, ke které chcete získat přístup z clusteru HDInsight. Pokud chcete nastavit správné počet aklů na soubor/složku v Gen1 úložiště datového jezera, přečtěte si informace [o zabezpečení dat v souboru ACL Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spuštění testovacích úloh v clusteru HDInsight pro použití data lake storage gen1
Po konfiguraci clusteru HDInsight můžete spustit testovací úlohy v clusteru a otestovat, že cluster HDInsight má přístup k datovému úložišti datového jezera Gen1. Za tímto účelem spustíme ukázkovou úlohu Hive, která vytvoří tabulku pomocí ukázkových dat, která jste nahráli dříve do účtu Gen1 úložiště datového jezera.

V této části budete SSH do clusteru HDInsight Linux a spustit ukázkový dotaz Hive. Pokud používáte klienta systému Windows, doporučujeme použít **PuTTY**, kterou lze stáhnout z aplikace [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o používání PuTTY najdete [v tématu Použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po připojení spusťte příkaz příkaz příkazu Hive pomocí následujícího příkazu:

   ```
   hive
   ```
2. Pomocí příkazového příkazu zadejte následující příkazy k vytvoření nové tabulky s názvem **vozidla** pomocí ukázkových dat v gen1 úložiště datového jezera:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Přístup k datům Lake Storage Gen1 pomocí příkazů HDFS
Jakmile nakonfigurujete cluster HDInsight tak, aby používal Data Lake Storage Gen1, můžete pro přístup k úložišti použít příkazy prostředí HDFS.

V této části budete SSH do clusteru HDInsight Linux a spusťte příkazy HDFS. Pokud používáte klienta systému Windows, doporučujeme použít **PuTTY**, kterou lze stáhnout z aplikace [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o používání PuTTY najdete [v tématu Použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po připojení použijte následující příkaz souborového systému HDFS k zobrazení seznamu souborů v účtu Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

To by mělo seznam souboru, který jste nahráli dříve data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Pomocí příkazu `hdfs dfs -put` můžete také odeslat některé soubory do zařízení `hdfs dfs -ls` Data Lake Storage Gen1 a potom je použít k ověření, zda byly soubory úspěšně odeslány.


## <a name="next-steps"></a>Další kroky
* [Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Použití data lake storage gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
