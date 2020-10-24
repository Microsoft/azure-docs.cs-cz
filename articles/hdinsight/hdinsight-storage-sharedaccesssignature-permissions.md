---
title: Omezení přístupu pomocí sdílených přístupových podpisů – Azure HDInsight
description: Naučte se používat sdílené přístupové podpisy k omezení přístupu HDInsight k datům uloženým v úložišti objektů BLOB v Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: e00f7b97b923443cef3b97e0cdeda009ad5c9b03
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490862"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Použití sdílených přístupových podpisů Azure Blob Storage k omezení přístupu k datům v HDInsight

Služba HDInsight má úplný přístup k datům v účtech služby Azure Blob Storage přidružených ke clusteru. Pomocí sdílených přístupových podpisů v kontejneru objektů blob můžete omezit přístup k datům. Sdílené přístupové podpisy (SAS) jsou funkcemi účtů úložiště Azure Blob, které vám umožňují omezit přístup k datům. Například poskytnutí přístupu k datům jen pro čtení.

> [!IMPORTANT]  
> Pro řešení využívající Apache Ranger zvažte použití služby HDInsight připojené k doméně. Další informace najdete v tématu [Konfigurace dokumentu HDInsight připojeného k doméně](./domain-joined/apache-domain-joined-configure.md) .

> [!WARNING]  
> HDInsight musí mít úplný přístup k výchozímu úložišti pro cluster.

## <a name="prerequisites"></a>Předpoklady

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Existující [kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Pokud používáte PowerShell, budete potřebovat [AZ Module](https://docs.microsoft.com/powershell/azure/).

* Pokud chcete použít rozhraní příkazového řádku Azure a ještě jste ho nenainstalovali, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

* Pokud používáte [Python](https://www.python.org/downloads/), verze 2,7 nebo vyšší.

* Pokud používáte jazyk C#, Visual Studio musí být verze 2013 nebo vyšší.

* Schéma identifikátoru URI pro váš účet úložiště. Toto schéma bude `wasb://` pro službu Azure Blob Storage `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro úložiště objektů BLOB v Azure povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` .

* Existující cluster HDInsight, do kterého se má přidat sdílený přístupový podpis V takovém případě můžete použít Azure PowerShell k vytvoření clusteru a přidání sdíleného přístupového podpisu během vytváření clusteru.

* Ukázkové soubory z [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Toto úložiště obsahuje následující položky:

  * Projekt sady Visual Studio, který může vytvořit kontejner úložiště, uloženou zásadu a SAS pro použití se službou HDInsight
  * Skript Pythonu, který může vytvořit kontejner úložiště, uloženou zásadu a SAS pro použití se službou HDInsight
  * PowerShellový skript, který může vytvořit cluster HDInsight a nakonfigurovat ho tak, aby používal SAS. Níže je využívána aktualizovaná verze.
  * Vzorový soubor: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Existují dvě formy podpisů sdíleného přístupu:

* `Ad hoc`: Čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadané na identifikátoru URI SAS.

* `Stored access policy`: Uložené zásady přístupu jsou definované v kontejneru prostředků, jako je například kontejner objektů BLOB. Zásady se dají použít ke správě omezení pro jeden nebo víc podpisů sdíleného přístupu. Při přidružení SAS k uložené zásadě přístupu SAS zdědí omezení – čas spuštění, čas vypršení platnosti, a oprávnění definovaná pro zásady uloženého přístupu.

Rozdíl mezi těmito dvěma formami je důležitý pro jeden klíčový scénář: odvolání. SAS je adresa URL, takže ji může použít kdokoli, kdo získá SAS. Nezáleží na tom, který z nich požadoval začátek. Pokud se SAS zveřejňuje veřejně, může ho použít kdokoli na světě. Distribuované přidružení zabezpečení je platné, dokud neproběhne jedna ze čtyř věcí:

1. Bylo dosaženo času vypršení platnosti zadaného v rámci SAS.

2. Dosáhlo se doby vypršení platnosti zadané v zásadách uloženého přístupu, na kterou se odkazuje pomocí SAS. Následující scénáře způsobují vypršení doby platnosti:

    * Uplynul časový interval.
    * Zásady uloženého přístupu se změnily tak, aby v minulosti měly čas vypršení platnosti. Změna doby vypršení platnosti je jedním ze způsobů, jak SAS odvolat.

3. Uložené zásady přístupu, na které se odkazuje pomocí SAS, se odstraní, což je jiný způsob, jak SAS odvolat. Pokud znovu vytvoříte zásady uloženého přístupu se stejným názvem, všechny tokeny SAS pro předchozí zásady jsou platné (Pokud čas vypršení platnosti SAS neuplynul). Pokud máte v úmyslu odvolat SAS, nezapomeňte použít jiný název, pokud znovu vytvoříte zásady přístupu s časem vypršení platnosti v budoucnosti.

4. Klíč účtu, který se použil k vytvoření SAS, se znovu vygeneruje. Při opětovném generování klíče dojde k selhání ověřování u všech aplikací, které používají předchozí klíč. Aktualizuje všechny součásti na nový klíč.

> [!IMPORTANT]  
> Identifikátor URI sdíleného přístupového podpisu je přidružený k klíči účtu, který se používá k vytvoření podpisu, a k přidružené zásadě uloženého přístupu (pokud existuje). Pokud neurčíte žádné uložené zásady přístupu, stačí pro odvolání sdíleného přístupového podpisu změnit klíč účtu.

Doporučujeme vždy používat uložené zásady přístupu. Pokud používáte uložené zásady, můžete buď odvolat signatury nebo podle potřeby prodloužení platnosti data vypršení platnosti. Kroky v tomto dokumentu používají uložené zásady přístupu k vygenerování SAS.

Další informace o sdílených přístupových podpisech najdete v tématu [Principy modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Vytvoření uložené zásady a SAS

Uložte token SAS vytvořený na konci každé metody. Token bude vypadat podobně jako následující výstup:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

`RESOURCEGROUP`Hodnoty, `STORAGEACCOUNT` a nahraďte `STORAGECONTAINER` odpovídajícími hodnotami pro existující kontejner úložiště. Změňte adresář na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` nebo upravte `-File` parametr tak, aby obsahoval absolutní cestu pro `Set-AzStorageblobcontent` . Zadejte následující příkaz prostředí PowerShell:

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

Použití proměnných v této části vychází z prostředí systému Windows. Pro bash nebo jiná prostředí se budou potřebovat mírné variace.

1. Nahraďte `STORAGEACCOUNT` a `STORAGECONTAINER` příslušnými hodnotami pro existující kontejner úložiště.

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

2. Nastavte načtený primární klíč na proměnnou pro pozdější použití. Nahraďte `PRIMARYKEY` načtenou hodnotou v předchozím kroku a potom zadejte následující příkaz:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Změňte adresář na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` nebo upravte `--file` parametr tak, aby obsahoval absolutní cestu pro `az storage blob upload` . Spusťte zbývající příkazy:

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

Otevřete `SASToken.py` soubor, nahraďte `storage_account_name` , `storage_account_key` a `storage_container_name` Zadejte odpovídající hodnoty pro existující kontejner úložiště a potom spusťte skript.

Pokud se zobrazí chybová zpráva, může být nutné provést akci `pip install --upgrade azure-storage` `ImportError: No module named azure.storage` .

### <a name="using-c"></a>Pomocí jazyka C\#

1. Otevřete řešení v sadě Visual Studio.

2. V Průzkumník řešení klikněte pravým tlačítkem na projekt **SASExample** a vyberte **vlastnosti**.

3. Vyberte **Nastavení** a přidejte hodnoty pro následující položky:

    |Položka |Popis |
    |---|---|
    |StorageConnectionString|Připojovací řetězec pro účet úložiště, pro který chcete vytvořit uloženou zásadu a SAS pro. Formát by měl být `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` ve formátu, kde `myaccount` je název vašeho účtu úložiště a `mykey` je klíč pro účet úložiště.|
    |ContainerName|Kontejner v účtu úložiště, ke kterému chcete omezit přístup|
    |SASPolicyName|Název, který se má použít pro vytvoření uložené zásady.|
    |FileToUpload|Cesta k souboru, který je odeslán do kontejneru.|

4. Spusťte projekt. Uložte token zásad SAS, název účtu úložiště a název kontejneru. Tyto hodnoty se používají při přidružení účtu úložiště k vašemu clusteru HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Použití SAS se službou HDInsight

Při vytváření clusteru HDInsight musíte zadat primární účet úložiště. Můžete také zadat další účty úložiště. Obě tyto metody přidání úložiště vyžadují úplný přístup k účtům úložiště a kontejnerům, které se používají.

Omezte přístup k kontejneru pomocí sdíleného přístupového podpisu. Přidejte vlastní položku do konfigurace **základního serveru** pro cluster. Záznam můžete přidat během vytváření clusteru pomocí prostředí PowerShell nebo po vytvoření clusteru pomocí Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Vytvoření clusteru, který používá SAS

Nahraďte,,, `CLUSTERNAME` `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` , `STORAGEACCOUNT` a `TOKEN` odpovídajícími hodnotami. Zadejte příkazy prostředí PowerShell:

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
> Po zobrazení výzvy k zadání uživatelského jména a hesla protokolu HTTP/s nebo SSH musíte zadat heslo, které splňuje následující kritéria:
>
> * Délka musí být aspoň 10 znaků.
> * Musí obsahovat alespoň jednu číslici.
> * Musí obsahovat alespoň jeden jiný než alfanumerický znak.
> * Musí obsahovat aspoň jedno velké nebo malé písmeno.

Dokončení tohoto skriptu trvá, obvykle přibližně po dobu 15 minut. Po dokončení skriptu bez jakýchkoli chyb byl cluster vytvořen.

### <a name="use-the-sas-with-an-existing-cluster"></a>Použití SAS s existujícím clusterem

Pokud máte existující cluster, můžete přidat SAS do konfigurace **základního webu** pomocí následujících kroků:

1. Otevřete webové uživatelské rozhraní Ambari pro váš cluster. Adresa této stránky je `https://YOURCLUSTERNAME.azurehdinsight.net` . Po zobrazení výzvy proveďte ověření v clusteru pomocí jména správce (správce) a hesla, které jste použili při vytváření clusteru.

1. Přejděte na stránku **HDFS**  >  **Konfigurace**  >  **Upřesnit**  >  **vlastní jádro-site**.

1. Rozbalte část **vlastní základ pro základní lokalitu** , přejděte na konec a a pak vyberte **Přidat vlastnost...**. Pro **klíč** a **hodnotu**použijte následující hodnoty:

    * **Klíč**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Hodnota**: SAS vrácená jednou z výše provedených metod.

    Nahraďte `CONTAINERNAME` názvem kontejneru, který jste použili v aplikaci C# nebo SAS. Nahraďte `STORAGEACCOUNTNAME` názvem účtu úložiště, který jste použili.

    Vyberte **Přidat** a uložte tento klíč a hodnotu.

1. Kliknutím na tlačítko **Uložit** uložte změny konfigurace. Po zobrazení výzvy přidejte popis změny (například "Přidání přístupu k úložišti SAS") a pak vyberte **Uložit**.

    Po dokončení změn vyberte **OK** .

   > [!IMPORTANT]  
   > Změny se projeví až po restartování několika služeb.

1. Zobrazí se rozevírací seznam pro **restartování** . V rozevíracím seznamu vyberte **restartovat vše** a pak __potvrďte restart vše__.

    Tento postup opakujte pro **MapReduce2** a **příze**.

1. Po restartování služby vyberte jednotlivé položky a v rozevíracím seznamu **Akce služby** zakažte režim údržby.

## <a name="test-restricted-access"></a>Test omezeného přístupu

Pomocí následujících kroků ověříte, že můžete číst a vypisovat položky v účtu úložiště SAS.

1. Připojte se ke clusteru. Nahraďte `CLUSTERNAME` názvem vašeho clusteru a zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Chcete-li zobrazit obsah kontejneru, použijte následující příkaz z příkazového řádku:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Nahraďte `SASCONTAINER` názvem kontejneru vytvořeným pro účet úložiště SAS. Nahraďte `SASACCOUNTNAME` názvem účtu úložiště použitým pro SAS.

    Seznam obsahuje soubor nahraný při vytvoření kontejneru a SAS.

3. Pomocí následujícího příkazu ověřte, zda můžete číst obsah souboru. Nahraďte `SASCONTAINER` a `SASACCOUNTNAME` jako v předchozím kroku. Nahraďte `sample.log` názvem souboru zobrazeným v předchozím příkazu:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Tento příkaz vypíše obsah souboru.

4. K stažení souboru do místního systému souborů použijte následující příkaz:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Tento příkaz stáhne soubor do místního souboru s názvem **testfile.txt**.

5. Pomocí následujícího příkazu nahrajte místní soubor do nového souboru s názvem **testupload.txt** v úložišti SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zobrazí se zpráva podobná následujícímu textu:

    ```output
    put: java.io.IOException
    ```

    K této chybě dochází, protože umístění úložiště je pouze pro čtení a seznam. Pomocí následujícího příkazu umístěte data do výchozího úložiště pro cluster, do kterého lze zapisovat:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tentokrát by se operace měla dokončit úspěšně.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak do clusteru HDInsight přidat úložiště s omezeným přístupem, se naučíte, jak pracovat s daty ve vašem clusteru:

* [Použití SSH s HDInsightem](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Autorizace uživatelů pro zobrazení Apache Ambari](hdinsight-authorize-users-to-ambari.md)
