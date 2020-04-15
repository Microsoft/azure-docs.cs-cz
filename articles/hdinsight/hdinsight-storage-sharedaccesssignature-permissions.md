---
title: Omezení přístupu pomocí sdílených přístupových podpisů – Azure HDInsight
description: Zjistěte, jak pomocí sdílených přístupových podpisů omezit přístup HDInsight u dat uložených v objektech BLOB úložiště Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313938"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům ve službě HDInsight

HDInsight má úplný přístup k datům v účtech Azure Storage přidružených ke clusteru. Pomocí sdílených přístupových podpisů v kontejneru objektů blob můžete omezit přístup k datům. Sdílené přístupové podpisy (SAS) jsou funkce účtů úložiště Azure, která umožňuje omezit přístup k datům. Například poskytování přístupu jen pro čtení k datům.

> [!IMPORTANT]  
> Pro řešení pomocí Apache Ranger, zvažte použití domény-připojil HDInsight. Další informace naleznete v dokumentu [Konfigurace hdinsightu přilehlém k doméně.](./domain-joined/apache-domain-joined-configure.md)

> [!WARNING]  
> HDInsight musí mít úplný přístup k výchozímu úložišti pro cluster.

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Existující [kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Pokud používáte Prostředí PowerShell, budete potřebovat [modul Az](https://docs.microsoft.com/powershell/azure/overview).

* Pokud chcete používat Azure CLI a ještě jste ho [nenainstalovali, přečtěte si informace o instalaci příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Pokud používáte [Python](https://www.python.org/downloads/), verze 2.7 nebo vyšší.

* Pokud používáte C#, Visual Studio musí být verze 2013 nebo vyšší.

* Schéma [URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) pro váš účet úložiště. Toto schéma `wasb://` by bylo `abfs://` pro Azure Storage, `adl://` pro Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI bude `wasbs://`. Viz také [bezpečný přenos](../storage/common/storage-require-secure-transfer.md).

* Existující cluster HDInsight, do který chcete přidat sdílený přístupový podpis. Pokud ne, můžete pomocí Azure PowerShellu vytvořit cluster a přidat sdílený přístupový podpis během vytváření clusteru.

* Ukázkové soubory [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)z . Toto úložiště obsahuje následující položky:

  * Projekt Visual Studia, který může vytvořit kontejner úložiště, uložené zásady a SAS pro použití s HDInsight
  * Skript Pythonu, který může vytvořit kontejner úložiště, uložené zásady a SAS pro použití s HDInsight
  * Skript prostředí PowerShell, který může vytvořit cluster HDInsight a nakonfigurovat jej tak, aby používal SAS. Aktualizovaná verze se používá níže.
  * Ukázkový soubor:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Sdílené přístupové podpisy mají dvě formy:

* `Ad hoc`: Čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou zadány na identifikátoru URI SAS.

* `Stored access policy`: Uložené zásady přístupu je definována na kontejneru prostředků, jako je například kontejner objektů blob. Zásadu lze použít ke správě omezení pro jeden nebo více sdílených přístupových podpisů. Když přidružíte SAS k uložené zásady přístupu, SAS zdědí omezení – čas zahájení, čas vypršení platnosti a oprávnění – definované pro zásady uloženého přístupu.

Rozdíl mezi těmito dvěma formami je důležitý pro jeden klíčový scénář: odvolání. SAS je adresa URL, takže ji může používat každý, kdo získá SAS. Nezáleží na tom, kdo o to požádal. Pokud je SAS publikovánveřejně, může jej používat kdokoli na světě. SAS, který je distribuován je platný, dokud se stane jedna ze čtyř věcí:

1. Je dosaženo doby vypršení platnosti zadané na SAS.

2. Je dosažena doba vypršení platnosti zadaná v zásadách uloženého přístupu, na kterou odkazuje SAS. Následující scénáře způsobit vypršení platnosti, které mají být dosaženy:

    * Časový interval uplynul.
    * Zásady uloženého přístupu jsou upraveny tak, aby měly v minulosti čas vypršení platnosti. Změna doby vypršení platnosti je jedním ze způsobů, jak odvolat SAS.

3. Uložené zásady přístupu odkazuje SAS je odstraněn, což je další způsob, jak odvolat SAS. Pokud znovu vytvoříte uložené zásady přístupu se stejným názvem, všechny tokeny SAS pro předchozí zásady jsou platné (pokud neuplynula doba vypršení platnosti na SAS). Pokud máte v úmyslu odvolat SAS, nezapomeňte použít jiný název, pokud znovu vytvořit zásady přístupu s časvypršení min v budoucnu.

4. Klíč účtu, který byl použit k vytvoření SAS je obnovena. Obnovení klíče způsobí, že všechny aplikace, které používají předchozí klíč k selhání ověřování. Aktualizujte všechny součásti na nový klíč.

> [!IMPORTANT]  
> Identifikátor URI sdíleného přístupového podpisu je přidružen ke klíči účtu použitému k vytvoření podpisu a přidruženým zásadám uloženého přístupu (pokud existuje). Pokud není zadána žádná uložená zásada přístupu, jediným způsobem, jak odvolat sdílený přístupový podpis, je změna klíče účtu.

Doporučujeme vždy používat uložené zásady přístupu. Při použití uložených zásad můžete buď odvolat podpisy, nebo podle potřeby prodloužit datum vypršení platnosti. Kroky v tomto dokumentu používají uložené zásady přístupu ke generování SAS.

Další informace o sdílených přístupových podpisech naleznete [v tématu Principy modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Vytvoření uložené zásady a SAS

Uložte token SAS, který je vytvořen na konci každé metody. Token bude vypadat podobně jako následující výstup:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Nahraďte `RESOURCEGROUP`, `STORAGEACCOUNT`a `STORAGECONTAINER` příslušné hodnoty pro stávající kontejner úložiště. Změňte `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` adresář na `-File` parametr nebo jej `Set-AzStorageblobcontent`změňte tak, aby obsahoval absolutní cestu pro program . Zadejte následující příkaz PowerShellu:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Použití Azure CLI

Použití proměnných v této části je založeno na prostředí systému Windows. Mírné odchylky budou potřebné pro bash nebo jiných prostředích.

1. `STORAGEACCOUNT`Nahraďte `STORAGECONTAINER` a odpovídajícími hodnotami pro stávající kontejner úložiště.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Nastavte načtený primární klíč na proměnnou pro pozdější použití. Nahraďte `PRIMARYKEY` načtenou hodnotou v předchozím kroku a zadejte následující příkaz:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Změňte `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` adresář na `--file` parametr nebo jej `az storage blob upload`změňte tak, aby obsahoval absolutní cestu pro program . Proveďte zbývající příkazy:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Pomocí Pythonu

Otevřete `SASToken.py` soubor `storage_account_name`a `storage_account_key`nahraďte , a `storage_container_name` s příslušnými hodnotami pro existující kontejner úložiště a spusťte skript.

Pokud se zobrazí `pip install --upgrade azure-storage` chybová zpráva `ImportError: No module named azure.storage`, bude pravděpodobně nutné provést spuštění .

### <a name="using-c"></a>Použití C\#

1. Otevřete řešení v sadě Visual Studio.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **SASExample** a vyberte **vlastnosti**.

3. Vyberte **Nastavení** a přidejte hodnoty pro následující položky:

    |Položka |Popis |
    |---|---|
    |StorageConnectionString|Připojovací řetězec pro účet úložiště, který chcete vytvořit uloženou zásadu a SAS pro. Formát by `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` měl `myaccount` být tam, kde `mykey` je název vašeho účtu úložiště a je klíčem pro účet úložiště.|
    |ContainerName|Kontejner v účtu úložiště, ke kterému chcete omezit přístup.|
    |Název zásad SAS|Název, který chcete použít pro uložené zásady k vytvoření.|
    |SouborToUpload|Cesta k souboru, který je odeslán do kontejneru.|

4. Spusťte projekt. Uložte token zásad SAS, název účtu úložiště a název kontejneru. Tyto hodnoty se používají při připojování účtu úložiště k clusteru HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Použití SAS s HDInsight

Při vytváření clusteru HDInsight je nutné zadat účet primárního úložiště. Můžete také zadat další účty úložiště. Obě tyto metody přidání úložiště vyžadují úplný přístup k účtům úložiště a kontejnerům, které se používají.

K omezení přístupu ke kontejneru použijte sdílený přístupový podpis. Přidejte vlastní položku do konfigurace **hlavní lokality** pro cluster. Položku můžete přidat během vytváření clusteru pomocí prostředí PowerShell nebo po vytvoření clusteru pomocí ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Vytvoření clusteru, který používá SAS

`CLUSTERNAME`Nahraďte `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER`, `STORAGEACCOUNT` `RESOURCEGROUP`, `TOKEN` , , a s příslušnými hodnotami. Zadejte příkazy Prostředí PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Po zobrazení výzvy k zadání uživatelského jména a hesla http/s nebo ssh je nutné zadat heslo, které splňuje následující kritéria:
>
> * Musí mít délku alespoň 10 znaků.
> * Musí obsahovat alespoň jednu číslici.
> * Musí obsahovat alespoň jeden nealfanumerický znak.
> * Musí obsahovat alespoň jedno velké nebo malé písmeno.

Chvíli trvá, než tento skript dokončit, obvykle kolem 15 minut. Po dokončení skriptu bez chyb byl vytvořen cluster.

### <a name="use-the-sas-with-an-existing-cluster"></a>Použití SAS s existujícím clusterem

Pokud máte existující cluster, můžete přidat SAS do konfigurace **základní lokality** pomocí následujících kroků:

1. Otevřete webové uživatelské uživatelské nastavení Ambari pro váš cluster. Adresa této stránky `https://YOURCLUSTERNAME.azurehdinsight.net`je . Po zobrazení výzvy se ověřte v clusteru pomocí názvu správce (správce) a hesla, které jste použili při vytváření clusteru.

1. Přejděte na **rozhraní HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Rozbalte oddíl **Vlastní jádro webu,** přejděte na konec a pak vyberte **Přidat vlastnost...**. Pro **klíč** a **hodnotu**použijte následující hodnoty :

    * **Klíč**:`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Hodnota**: SAS vrácena jednou z metod dříve provedených.

    Nahraďte `CONTAINERNAME` název kontejneru, který jste použili s aplikací C# nebo SAS. Nahraďte `STORAGEACCOUNTNAME` název účtu úložiště, který jste použili.

    Chcete-li uložit tento klíč a hodnotu, vyberte **přidat.**

1. Chcete-li uložit změny konfigurace, vyberte tlačítko **Uložit.** Po zobrazení výzvy přidejte popis změny (například přidání přístupu k úložišti SAS) a pak vyberte **Uložit**.

    Po dokončení změn vyberte **ok.**

   > [!IMPORTANT]  
   > Než se změna projeví, je nutné restartovat několik služeb.

1. Zobrazí se rozevírací seznam **Restart.** V rozevíracím seznamu vyberte **Restartovat všechny ovlivněné** položky a __potom potvrdit možnost Restartovat vše__.

    Tento postup opakujte pro **MapReduce2** a **YARN**.

1. Po restartování služeb vyberte každou z nich a zakažte režim údržby z rozevíracího souboru **Akce služby.**

## <a name="test-restricted-access"></a>Omezený přístup k testu

Pomocí následujících kroků ověřte, zda můžete číst a vypsat pouze položky v účtu úložiště SAS.

1. Připojte se ke clusteru. Nahraďte `CLUSTERNAME` název clusteru a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Chcete-li zobrazit obsah kontejneru, použijte následující příkaz z výzvy:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Nahraďte `SASCONTAINER` název kontejneru vytvořeného pro účet úložiště SAS. Nahraďte `SASACCOUNTNAME` se názvem účtu úložiště, který se používá pro SAS.

    Seznam obsahuje soubor nahraný při vytvoření kontejneru a SAS.

3. Pomocí následujícího příkazu ověřte, zda můžete číst obsah souboru. `SASCONTAINER` Nahraďte `SASACCOUNTNAME` a jako v předchozím kroku. Nahraďte `sample.log` název souboru zobrazeným v předchozím příkazu:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Tento příkaz obsahuje seznam obsahu souboru.

4. Ke stažení souboru do místního systému souborů použijte následující příkaz:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Tento příkaz stáhne soubor do místního souboru s názvem **testfile.txt**.

5. Pomocí následujícího příkazu nahrajte místní soubor do nového souboru s názvem **testupload.txt** v úložišti SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zobrazí se zpráva podobná následujícímu textu:

        put: java.io.IOException

    K této chybě dochází, protože umístění úložiště je číst +seznam pouze. Pomocí následujícího příkazu můžete data umístit do výchozího úložiště clusteru, které je zapisovatelné:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tentokrát by měla být operace úspěšně dokončena.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili přidat úložiště s omezeným přístupem do clusteru HDInsight, naučte se další způsoby práce s daty v clusteru:

* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)