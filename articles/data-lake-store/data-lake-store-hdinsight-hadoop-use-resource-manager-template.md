---
title: Šablona – cluster HDInsight s Data Lake Storage Gen1
description: Pomocí Azure Resource Manager šablon můžete vytvářet a používat clustery Azure HDInsight s Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 2a0471055e4648944aa07d10fef67f5e7235a76b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856919"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Vytvoření clusteru HDInsight s Azure Data Lake Storage Gen1 pomocí šablony Azure Resource Manager
> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Naučte se, jak pomocí Azure PowerShell nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**.

U podporovaných typů clusterů se Data Lake Storage Gen1 dá použít jako výchozí úložiště nebo jako další účet úložiště. Když se Data Lake Storage Gen1 používá jako další úložiště, bude výchozí účet úložiště pro clustery stále úložiště objektů BLOB v Azure (WASB) a soubory související s clusterem (například protokoly atd.) se zapisují do výchozího úložiště, zatímco data, která chcete zpracovat, můžou být uložená v Data Lake Storage Gen1m účtu. Použití Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon ani možnost čtení a zápisu do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Použití Data Lake Storage Gen1 pro úložiště clusteru HDInsight

Tady jsou některé důležité informace pro používání služby HDInsight s Data Lake Storage Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1, protože výchozí úložiště je k dispozici pro HDInsight verze 3,5 a 3,6.

* Možnost vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1, protože další úložiště je k dispozici pro HDInsight verze 3,2, 3,4, 3,5 a 3,6.

V tomto článku zřídíme cluster Hadoop s Data Lake Storage Gen1 jako další úložiště. Pokyny k vytvoření clusteru Hadoop s Data Lake Storage Gen1 jako výchozího úložiště najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/).
* **Azure Active Directory instančního objektu**. Kroky v tomto kurzu poskytují pokyny k vytvoření instančního objektu ve službě Azure AD. Abyste ale mohli vytvořit instanční objekt, musíte být správcem služby Azure AD. Pokud jste správce Azure AD, můžete tento požadavek přeskočit a pokračovat v tomto kurzu.

    **Pokud nejste správce Azure AD**, nebudete moct provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce Azure AD nejdřív vytvořit instanční objekt, abyste mohli vytvořit cluster HDInsight s Data Lake Storage Gen1. Instanční objekt musí být také vytvořen pomocí certifikátu, jak je popsáno v tématu [Vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Vytvoření clusteru HDInsight s Data Lake Storage Gen1
Šablona Správce prostředků a požadavky pro použití šablony jsou dostupné na GitHubu v [nasazení clusteru HDInsight Linux s novými Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postupujte podle pokynů uvedených na tomto odkazu a vytvořte cluster HDInsight s Data Lake Storage Gen1 jako další úložiště.

Pokyny na výše uvedeném odkazu vyžadují PowerShell. Než začnete s těmito pokyny, ujistěte se, že jste se přihlásili ke svému účtu Azure. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragmenty kódu. Po zobrazení výzvy k přihlášení se ujistěte, že jste přihlášeni jako jeden ze správce nebo vlastníka předplatného:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Šablona nasadí tyto typy prostředků:

* [Microsoft. DataLakeStore/účty](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. HDInsight/clustery](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Nahrání ukázkových dat do Data Lake Storage Gen1
Šablona Správce prostředků vytvoří nový účet úložiště s Data Lake Storage Gen1 a přidruží ho ke clusteru HDInsight. Teď je potřeba nahrát některá ukázková data, která Data Lake Storage Gen1. Tato data budete potřebovat později v tomto kurzu, chcete-li spouštět úlohy z clusteru HDInsight, které přistupují k datům v účtu úložiště pomocí Data Lake Storage Gen1. Pokyny, jak nahrávat data, najdete v tématu [nahrání souboru do data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Nastavit příslušné seznamy ACL pro ukázková data
Abyste měli jistotu, že jsou ukázková data dostupná z clusteru HDInsight, musíte zajistit, aby aplikace služby Azure AD, která se používá k navázání identity mezi clusterem HDInsight a Data Lake Storage Gen1, měla přístup k souboru nebo složce, ke které se snažíte získat přístup. Uděláte to tak, že provedete následující kroky.

1. Vyhledejte název aplikace služby Azure AD, která je přidružená ke clusteru HDInsight a účtu úložiště s Data Lake Storage Gen1. Jedním ze způsobů, jak hledat název, je otevřít okno clusteru HDInsight, které jste vytvořili pomocí šablony Správce prostředků, kliknout na kartu **identita služby Azure AD** a vyhledat hodnotu **zobrazovaného názvu instančního objektu**.
2. Teď v souboru nebo složce, ke které chcete získat přístup z clusteru HDInsight, poskytněte přístup k této aplikaci Azure AD. Pokud chcete nastavit správné seznamy ACL pro soubor nebo složku v Data Lake Storage Gen1, přečtěte si téma [zabezpečení dat v Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spuštění testovacích úloh v clusteru HDInsight pro použití Data Lake Storage Gen1
Po nakonfigurování clusteru HDInsight můžete spustit testovací úlohy v clusteru, abyste otestovali, že cluster HDInsight má přístup k Data Lake Storage Gen1. Uděláte to tak, že spustíte ukázkovou úlohu podregistru, která vytvoří tabulku pomocí ukázkových dat, která jste předtím nahráli do svého účtu úložiště pomocí Data Lake Storage Gen1.

V této části provedete SSH do clusteru HDInsight Linux a spustíte ukázkový dotaz na podregistr. Pokud používáte klienta se systémem Windows, doporučujeme použít výstup, **který je možné**stáhnout z [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Další informace o používání výstupu najdete v tématu [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po připojení spusťte pomocí následujícího příkazu podregistr CLI:

   ```
   hive
   ```
2. Pomocí rozhraní příkazového řádku zadejte následující příkazy k vytvoření nové tabulky s názvem " **vozidla** " pomocí ukázkových dat v Data Lake Storage Gen1:

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
Jakmile nakonfigurujete cluster HDInsight pro použití Data Lake Storage Gen1, můžete k přístupu do Storu použít příkazy prostředí HDFS.

V této části provedete SSH do clusteru HDInsight Linux a spustíte příkazy HDFS. Pokud používáte klienta se systémem Windows, doporučujeme použít výstup, **který je možné**stáhnout z [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Další informace o používání výstupu najdete v tématu [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po připojení použijte následující příkaz HDFS systému souborů k vypsání souborů v účtu úložiště pomocí Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Mělo by se zobrazit seznam souborů, které jste dříve nahráli do Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Pomocí příkazu můžete také `hdfs dfs -put` Odeslat některé soubory do data Lake Storage Gen1 a potom použít `hdfs dfs -ls` k ověření, jestli se soubory úspěšně nahrály.


## <a name="next-steps"></a>Další kroky
* [Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
