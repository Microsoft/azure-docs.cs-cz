---
title: Pomocí šablony Azure můžete vytvořit HDInsight a Data Lake Store | Microsoft Docs
description: Pomocí šablony Azure Resource Manager můžete vytvořit a použít clustery HDInsight s Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1a5651e9e1b8c46e8151e44308de44245aa2ac35
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Vytvoření clusteru HDInsight s Data Lake Store pomocí šablony Azure Resource Manageru
> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Další informace o použití prostředí Azure PowerShell ke konfiguraci clusteru HDInsight s Azure Data Lake Store, **jako další úložiště**.

Pro typy podporované clusteru Data Lake Store použít jako výchozí úložiště nebo účtu další úložiště. Když se Data Lake Store se použije jako další úložiště, výchozí účet úložiště pro clustery budou mít pořád Azure úložiště objektů BLOB (WASB) a soubory související s clusteru (například protokoly atd.) se stále zapisují do výchozí úložiště, při data, která chcete zpracovat mohou být uloženy v účtu Data Lake Store. Pomocí Data Lake Store jako další úložiště účet nemá negativní vliv na výkon nebo možnost čtení/zápisu do úložiště z clusteru.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Pro úložiště clusteru HDInsight pomocí Data Lake Store

Zde jsou některé důležité informace týkající se používání HDInsight s Data Lake Store:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Store, jako je k dispozici pro HDInsight verze 3.5 a 3.6 výchozí úložiště.

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Store, jako je k dispozici pro HDInsight verze 3.2, 3,4, 3.5 a 3.6 Další úložiště.

V tomto článku jsme zřízení clusteru Hadoop s Data Lake Store jako další úložiště. Pokyny o tom, jak vytvořit Hadoop cluster s Data Lake Store jako výchozí úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Store pomocí portálu Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory instanční objekt**. Kroky v tomto kurzu poskytují pokyny o tom, jak vytvořit objekt služby ve službě Azure AD. Ale musí být správce Azure AD mohli vytvořit objekt služby. Pokud jste správce Azure AD, můžete přeskočit tento požadavek a pokračujte v tomto kurzu.

    **Pokud si nejste správce Azure AD**, nebudete moci provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musíte vám správce Azure AD nejdřív vytvořit objekt služby, před vytvořením clusteru HDInsight s Data Lake Store. Navíc instanční objekt musí být vytvořen pomocí certifikátu, jak je popsáno v [vytvořit objekt služby pomocí certifikátu](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Vytvoření clusteru HDInsight s Azure Data Lake Store
Šablony Resource Manageru a požadavků na používání šablony jsou dostupné na webu GitHub na [nasadit cluster HDInsight Linux s novou Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postupujte podle pokynů uvedených v tento odkaz pro vytvoření clusteru HDInsight s Azure Data Lake Store jako další úložiště.

Postupujte podle pokynů v odkazu zmíněné vyžadují prostředí PowerShell. Než začnete pomocí těchto pokynů, zkontrolujte, zda že se přihlásit k účtu Azure. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragmenty kódu. Po zobrazení výzvy k přihlášení se nezapomeňte přihlásit jako jeden ze správců / vlastník předplatného:

```
# Log in to your Azure account
Connect-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Nahrát ukázková data do Azure Data Lake Store
Šablony Resource Manageru vytvoří nový účet Data Lake Store a přidruží ji s clusterem HDInsight. Teď musíte nahrát ukázková data do Data Lake Store. Tato data budete potřebovat později v tomto kurzu ke spuštění úloh z clusteru služby HDInsight, který přístup k datům v Data Lake Store. Pokyny, jak odesílat data, najdete v části [nahrání souboru do Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Nastavit příslušné seznamy ACL na ukázková data
Pokud chcete mít jistotu, že je dostupný z clusteru HDInsight, který nahrajete ukázková data, musíte zajistit, aby aplikace Azure AD, která se používá k vytvoření identity mezi HDInsight cluster a Data Lake Store má přístup k souboru nebo složky, které se pokoušíte získat přístup. Chcete-li to provést, proveďte následující kroky.

1. Najděte název aplikace Azure AD, který je přidružen HDInsight cluster a Data Lake Store. Jeden způsob hledání pro název je otevřete okno clusteru HDInsight, který jste vytvořili pomocí šablony Resource Manageru, klikněte **identita AAD clusteru** kartě a vyhledejte hodnotu **zobrazovaný název objektu služby**.
2. Nyní poskytují přístup k této aplikaci Azure AD na soubor nebo složku, kterou chcete získat přístup z clusteru HDInsight. Správné seznamy řízení přístupu na soubor nebo složku, v Data Lake Store, naleznete v tématu [zabezpečení dat v Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Spuštění testu úloh na clusteru HDInsight pomocí Data Lake Store
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit testovací úlohy v clusteru k testování clusteru HDInsight můžete získat přístup k Data Lake Store. To pokud chcete udělat, budeme se spustí úlohy Hive vzorku, který vytvoří tabulku pomocí ukázkových dat, který jste dříve nahráli do Data Lake Store.

V této části se SSH do služby clusteru HDInsight Linux a spustit ukázkový dotaz Hive. Pokud používáte klienta se systémem Windows, doporučujeme používat **PuTTY**, který si můžete stáhnout z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o použití klienta PuTTY, najdete v části [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po připojení, spusťte rozhraní příkazového řádku Hive pomocí následujícího příkazu:

   ```
   hive
   ```
2. Pomocí rozhraní příkazového řádku, zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Store:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Přístup k Data Lake Store pomocí příkazů HDFS
Po konfiguraci clusteru HDInsight pomocí Data Lake Store, můžete pro přístup do obchodu příkazů HDFS prostředí.

V této části se SSH do HDInsight Linux clusteru a spusťte příkazy HDFS. Pokud používáte klienta se systémem Windows, doporučujeme používat **PuTTY**, který si můžete stáhnout z [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Další informace o použití klienta PuTTY, najdete v části [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po připojení, použijte následující příkaz systému souborů HDFS zobrazte seznam souborů v Data Lake Store.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

To by měl seznam soubor, který jste dříve nahráli do Data Lake Store.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Můžete také `hdfs dfs -put` příkazu do Data Lake Store nahrát některé soubory a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrál.


## <a name="next-steps"></a>Další postup
* [Kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Použití Data Lake Store s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
