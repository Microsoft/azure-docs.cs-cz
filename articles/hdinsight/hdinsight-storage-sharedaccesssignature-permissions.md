---
title: Omezení přístupu pomocí sdílených přístupových podpisů – Azure HDInsight
description: Další informace o použití sdílených přístupových podpisů omezit HDInsight přístup k datům uloženým v objektech BLOB Azure storage.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409561"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Omezení přístupu k datům v HDInsight pomocí Azure Storage sdílených přístupových podpisů

HDInsight má úplný přístup k datům v Azure Storage účty přidružené ke clusteru. Sdílené přístupové podpisy v kontejneru objektů blob můžete použít k omezení přístupu k datům. Sdílených přístupových podpisů (SAS) jsou funkce účty úložiště Azure, která vám umožní omezit přístup k datům. Například poskytuje přístup jen pro čtení k datům.

> [!IMPORTANT]  
> Pro řešení, které využívá Apache Ranger zvažte použití HDInsight připojených k doméně. Další informace najdete v tématu [konfigurace HDInsight připojených k doméně](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]  
> HDInsight musí mít úplný přístup k výchozím úložištěm clusteru.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Existující [kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Pokud používáte PowerShell, budete potřebovat [Az modulu](https://docs.microsoft.com/powershell/azure/overview).

* Pokud chtějí používat rozhraní příkazového řádku Azure a je zatím ho ještě nenainstalovali, naleznete v tématu [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Pokud používáte [Python](https://www.python.org/downloads/), verze 2.7 nebo novější.

* Pokud používáte C#, Visual Studio musí být ve verzi 2013 nebo novější.

* [Schéma identifikátoru URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) pro váš účet úložiště. To může být `wasb://` pro službu Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by `wasbs://` nebo `abfss://`, respektive najdete [zabezpečený přenos](../storage/common/storage-require-secure-transfer.md).

* Existující cluster HDInsight sdíleného přístupového podpisu pro přidání. Pokud ne, můžete použít Azure PowerShell k vytvoření clusteru a přidejte sdílený přístupový podpis při vytváření clusteru.

* V příkladu souborů z [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Toto úložiště obsahuje následující položky:

  * Projekt sady Visual Studio, můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití se službou HDInsight
  * Skript v jazyce Python, můžete vytvořit kontejner úložiště, uložené zásady a SAS pro použití se službou HDInsight
  * Skript prostředí PowerShell, který můžete vytvořit HDInsight cluster a nakonfigurujte ho na používání sdíleného přístupového podpisu. Použít aktualizovanou verzi níže.
  * Ukázkový soubor: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Existují dvě formy sdílené přístupové podpisy:

* Ad hoc: Čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadané v identifikátoru URI SAS.

* Uložené zásady přístupu: Uložené zásady přístupu je definován na kontejner prostředku, jako je například kontejner objektů blob. Zásadu lze použít ke správě omezení pro jeden nebo více sdílených přístupových podpisů. Když přiřadíte SAS uložené zásady přístupu, dědí SAS omezení – čas zahájení, čas vypršení platnosti a oprávnění – definice zásady přístupu.

Rozdíl mezi dvě různými formami je důležité pro jeden klíč scénář: odvolání. SAS je adresa URL, takže každý, kdo získává sdíleného přístupového podpisu, použít bez ohledu na to, který je požadovaný začneme. Pokud SAS je publikována veřejně, je možné použít kdokoli na světě. SAS, která se distribuuje je platné, dokud jednu ze čtyř akcí se stane:

1. Je dosaženo času vypršení platnosti zadané v protokolu SAS.

2. Je dosaženo času vypršení platnosti zadané v zásadách přístupu uložené odkazuje SAS. Čas vypršení platnosti tím dosáhnout způsobit, že následující scénáře:

    * Časový interval uplynul.
    * Zásady přístupu uložené upraví tak mají čas vypršení platnosti v minulosti. Změna čas vypršení platnosti je jeden způsob odvolání SAS.

3. Zásady přístupu uložené odkazuje sdíleného přístupového podpisu je odstranit, což je jiný způsob odvolání SAS. Pokud znovu vytvoříte zásady přístupu se stejným názvem, všechny tokeny SAS pro předchozí zásady jsou platné (pokud nebyl předán čas vypršení platnosti na sdíleného přístupového podpisu). Pokud máte v úmyslu odvolání SAS, je nutné použít jiný název, pokud znovu vytvoříte zásady přístupu s času vypršení platnosti v budoucnu.

4. Klíč účtu, který byl použit k vytvoření sdíleného přístupového podpisu se znova vygeneroval. Obnovuje se klíč způsobí, že všechny aplikace, které používají předchozí klíč vč. ověřování. Aktualizujte všechny komponenty na nový klíč.

> [!IMPORTANT]  
> Identifikátor URI sdíleného přístupového podpisu je přidružená ke klíči účet použitý k vytvoření podpisu a přidruženého uložené zásady přístupu (pokud existuje). Pokud není zadána žádná uložené zásady přístupu, chcete-li změnit klíč účtu je jediný způsob, jak odebrat sdílený přístupový podpis.

Doporučujeme vždy používat uložené zásady přístupu. Při použití uložené zásady, můžete odvolat podpisy nebo prodloužit datum vypršení platnosti podle potřeby. Kroky v tomto dokumentu využívají uložené zásady přístupu k vygenerování SAS.

Další informace o sdílených přístupových podpisů najdete v tématu [vysvětlení modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Vytvoření uložené zásady a SAS

Uložte tokenu SAS, který je vytvořen na konci každé metody. Token, který bude vypadat nějak takto:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Nahraďte `RESOURCEGROUP`, `STORAGEACCOUNT`, a `STORAGECONTAINER` příslušnými hodnotami pro váš existující kontejner úložiště. Změňte adresář na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` nebo upravujete `-File` parametr obsahovat absolutní cestu k `Set-AzStorageblobcontent`. Zadejte následující příkaz Powershellu:

```PowerShell
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

Použití proměnných v této části je založená na prostředí Windows. Malé odchylky bude potřebovat jiné prostředí nebo prostředí bash.

1. Nahraďte `STORAGEACCOUNT`, a `STORAGECONTAINER` příslušnými hodnotami pro váš existující kontejner úložiště.

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

2. Nastavte načtený primární klíč do proměnné pro pozdější použití. Nahraďte `PRIMARYKEY` pomocí načtené hodnoty v předchozím kroku a potom zadejte následující příkaz:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Změňte adresář na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` nebo upravujete `--file` parametr obsahovat absolutní cestu k `az storage blob upload`. Spusťte zbývající příkazy:

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

Otevřít `SASToken.py` soubor a nahradit `storage_account_name`, `storage_account_key`, a `storage_container_name` příslušnými hodnotami pro existující kontejner úložiště a pak spusťte skript.

Možná budete muset provést `pip install --upgrade azure-storage` Pokud se zobrazí chybová zpráva `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Pomocí C#

1. Otevřete řešení v sadě Visual Studio.

2. V Průzkumníku řešení klikněte pravým tlačítkem myši na **SASExample** projektu a vyberte **vlastnosti**.

3. Vyberte **nastavení** a přidejte hodnoty pro následující položky:

   * StorageConnectionString: Připojovací řetězec pro účet úložiště, který chcete vytvořit SAS pro a uložené zásady. Formát by měl být `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` kde `myaccount` je název vašeho účtu úložiště a `mykey` je klíč pro účet úložiště.

   * ContainerName: Kontejner v účtu úložiště, který chcete omezit přístup k datům.

   * SASPolicyName: Název, který se má použít pro uložené zásady k vytvoření.

   * FileToUpload: Cesta k souboru, který se nahraje do kontejneru.

4. Spusťte projekt. Uložte zásadu token SAS, název účtu úložiště a název kontejneru. Když přidružíte účet úložiště pro HDInsight cluster se používají tyto hodnoty.

## <a name="use-the-sas-with-hdinsight"></a>Použití SAS s HDInsight

Při vytváření clusteru HDInsight, musíte zadat účet primárního úložiště a volitelně můžete zadat další účty úložiště. Obě tyto metody pro přidání úložiště vyžadují plný přístup k účtům úložiště a kontejnerů, které se používají.

Pokud chcete omezit přístup ke kontejneru pomocí sdíleného přístupového podpisu, přidejte vlastní položku a **základního webu** konfiguraci clusteru. Položky můžete přidat při vytváření clusteru pomocí Powershellu nebo po vytvoření clusteru pomocí nástroje Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Vytvořit cluster, který používá sdíleného přístupového podpisu

Nahraďte `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, a `TOKEN` příslušnými hodnotami. Zadejte příkazy prostředí PowerShell:

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

$config = $config | Add-AzHDInsightConfigValues `
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
> Po zobrazení výzvy pro HTTP/s nebo SSH uživatelské jméno a heslo, musí se zadat heslo, které splňují následující kritéria:
>
> * Musí být minimálně 10 znaků.
> * Musí obsahovat alespoň jednu číslici.
> * Musí obsahovat alespoň jeden jiný než alfanumerický znak.
> * Musí obsahovat aspoň jedno velké nebo malé písmeno.

To chvíli trvat, než tento skript k dokončení, obvykle přibližně 15 minut. Po dokončení skriptu bez chyb, cluster se vytvořil.

### <a name="use-the-sas-with-an-existing-cluster"></a>Použití SAS s existujícím clusteru

Pokud máte existující cluster, můžete přidat SAS **základního webu** konfigurace pomocí následujících kroků:

1. Otevřete webové uživatelské rozhraní Ambari pro váš cluster. Adresa pro tuto stránku `https://YOURCLUSTERNAME.azurehdinsight.net`. Po zobrazení výzvy ověřování ke clusteru pomocí jméno správce (správce) a heslo, které jste použili při vytváření clusteru.

2. V levé části webové uživatelské rozhraní Ambari, vyberte **HDFS** a pak vyberte **Configs** kartu uprostřed stránky.

3. Vyberte **Upřesnit** kartu a potom vyhledejte **základního webu vlastní** oddílu.

4. Rozbalte **základního webu vlastní** část a pak přejděte k ukončení a vyberte **přidat vlastnost...**  odkaz. Použijte následující hodnoty **klíč** a **hodnotu** pole:

   * **Klíč**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Hodnota**: SAS vrátil pomocí jedné z metod dříve spuštěn.

     Nahraďte `CONTAINERNAME` s kontejnerem název budete používat s C# nebo přidružení zabezpečení aplikace. Nahraďte `STORAGEACCOUNTNAME` názvem účtu úložiště můžete použít.

5. Klikněte na tlačítko **přidat** tlačítko Uložit tento klíč a hodnotu a pak klikněte na tlačítko **Uložit** tlačítko Uložit změny konfigurace. Po zobrazení výzvy, přidejte popis změny ("Přidání přístup k úložišti SAS" příklad) a potom klikněte na **Uložit**.

    Klikněte na tlačítko **OK** kdy byly změny dokončeny.

   > [!IMPORTANT]  
   > Změna se projeví až po restartování několik služeb.

6. Webové uživatelské rozhraní Ambari, vyberte **HDFS** ze seznamu na levé straně a pak vyberte **restartujte všechny ovlivněné** z **akce služby** rozevírací seznam na pravé straně. Po zobrazení výzvy vyberte __potvrďte restartování všech__.

    Tento postup opakujte pro MapReduce2 a YARN.

7. Po restartování služby, vyberte každé z nich a zakázat režim údržby z **akce služby** rozevírací seznam.

## <a name="test-restricted-access"></a>Test s omezením pomocí specifikátoru přístupu

Pomocí následujících kroků ověřte, ke kterým můžete v účtu úložiště SAS pouze pro čtení a seznam položek.

1. Připojte se ke clusteru. Nahraďte `CLUSTERNAME` s názvem vašeho clusteru a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Pro vypsání obsahu kontejneru, použijte následující příkaz z příkazového řádku:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Nahraďte `SASCONTAINER` s názvem kontejneru, který vytvořili pro účet úložiště SAS. Nahraďte `SASACCOUNTNAME` s názvem účet úložiště používané pro SAS.

    Seznam obsahuje soubor, který nahráli při vytvoření kontejneru a SAS.

3. Použijte následující příkaz k ověření, že si můžete přečíst obsah souboru. Nahradit `SASCONTAINER` a `SASACCOUNTNAME` stejně jako v předchozím kroku. Nahraďte `sample.log` s názvem souboru zobrazeného v předchozím příkazem:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Tento příkaz vypíše obsah souboru.

4. Stáhněte soubor do místního systému souborů použijte následující příkaz:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Tento příkaz stáhne soubor do místního souboru s názvem **testfile.txt**.

5. Použijte následující příkaz k nahrání místního souboru do nového souboru s názvem **testupload.txt** na úložiště SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zobrazí zpráva podobná následující text:

        put: java.io.IOException

    K této chybě dochází, protože je čtení + seznam pouze umístění úložiště. Umístění dat na výchozí úložiště pro cluster, který se může zapisovat použijte následující příkaz:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tentokrát by měl úspěšně dokončit operaci.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak přidat úložiště omezený přístup ke svému clusteru HDInsight, přečtěte si další způsoby, jak pracovat s daty ve vašem clusteru:

* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

