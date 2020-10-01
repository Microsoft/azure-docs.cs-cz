---
title: PowerShell – HDInsight s Data Lake Storage Gen1 – doplněk úložiště – Azure
description: Naučte se, jak pomocí Azure PowerShell nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 jako další úložiště.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 69cfe529f3d2b0f956bfbe9d002a5a220b37e19f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620000"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Použití Azure PowerShell k vytvoření clusteru HDInsight s Azure Data Lake Storage Gen1 (jako další úložiště)

> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Naučte se, jak pomocí Azure PowerShell nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**. Pokyny, jak vytvořit cluster HDInsight s Data Lake Storage Gen1 jako výchozí úložiště, najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako výchozí úložiště](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Pokud budete používat Data Lake Storage Gen1 jako další úložiště pro cluster HDInsight, důrazně doporučujeme, abyste to provedli během vytváření clusteru, jak je popsáno v tomto článku. Přidání Data Lake Storage Gen1 jako dalšího úložiště do stávajícího clusteru HDInsight je složitý proces a náchylné k chybám.
>

U podporovaných typů clusterů se Data Lake Storage Gen1 dá použít jako výchozí úložiště nebo další účet úložiště. Pokud se Data Lake Storage Gen1 používá jako další úložiště, bude se výchozí účet úložiště pro clustery pořád naAzure Storage objekty BLOB (WASB) a soubory související s clusterem (například protokoly atd.) se zapisují do výchozího úložiště, zatímco data, která chcete zpracovat, můžou být uložená v účtu Data Lake Storage Gen1. Použití Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon ani možnost čtení a zápisu do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Použití Data Lake Storage Gen1 pro úložiště clusteru HDInsight

Tady jsou některé důležité informace pro používání služby HDInsight s Data Lake Storage Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1, protože další úložiště je k dispozici pro HDInsight verze 3,2, 3,4, 3,5 a 3,6.

Konfigurace HDInsight pro práci s Data Lake Storage Gen1 pomocí prostředí PowerShell zahrnuje následující kroky:

* Vytvoření účtu Data Lake Storage Gen1
* Nastavení ověřování pro přístup na základě rolí na Data Lake Storage Gen1
* Vytvoření clusteru HDInsight s ověřováním pro Data Lake Storage Gen1
* Spuštění testovací úlohy na clusteru

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/).
* **Windows SDK**. Můžete si ji nainstalovat [odtud](https://dev.windows.com/en-us/downloads). Tento postup slouží k vytvoření certifikátu zabezpečení.
* **Azure Active Directory instančního objektu**. Kroky v tomto kurzu poskytují pokyny k vytvoření instančního objektu ve službě Azure AD. Abyste ale mohli vytvořit instanční objekt, musíte být správcem služby Azure AD. Pokud jste správce Azure AD, můžete tento požadavek přeskočit a pokračovat v tomto kurzu.

    **Pokud nejste správce Azure AD**, nebudete moct provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce Azure AD nejdřív vytvořit instanční objekt, abyste mohli vytvořit cluster HDInsight s Data Lake Storage Gen1. Instanční objekt musí být také vytvořen pomocí certifikátu, jak je popsáno v tématu [Vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Pomocí těchto kroků vytvořte účet Data Lake Storage Gen1.

1. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragment kódu. Po zobrazení výzvy k přihlášení se ujistěte, že jste se přihlásili jako jeden ze správce nebo vlastníka předplatného:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Pokud se zobrazí chybová zpráva podobná `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` při registraci Data Lake Storage Gen1 poskytovatele prostředků, je možné, že vaše předplatné není schválené pro data Lake Storage Gen1. Ujistěte se, že jste povolili předplatné Azure pro Data Lake Storage Gen1 pomocí následujících [pokynů](data-lake-store-get-started-portal.md).
   >
   >
2. Účet Data Lake Storage Gen1 je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Měl by se zobrazit výstup podobný tomuto:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Vytvořte účet Data Lake Storage Gen1. Název účtu, který zadáte, musí obsahovat jenom malá písmena a číslice.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Zobrazený výstup by měl vypadat asi takto:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Nahrajte ukázková data do Data Lake Storage Gen1. Později v tomto článku použijeme k ověření, že data jsou přístupná z clusteru HDInsight. Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup na základě rolí na Data Lake Storage Gen1

Každé předplatné Azure je přidruženo k Azure Active Directory. Uživatelé a služby, kteří přistupují k prostředkům předplatného pomocí Azure Portal nebo rozhraní Azure Resource Manager API, se musí nejdřív ověřit pomocí tohoto Azure Active Directory. Přístup k předplatným a službám Azure se uděluje tím, že jim přiřadíte příslušnou roli na prostředku Azure.  Pro služby identifikuje instanční objekt službu v Azure Active Directory (AAD). Tato část ukazuje, jak udělit aplikační službu, jako je HDInsight, přístup k prostředku Azure (Data Lake Storage Gen1 účet, který jste vytvořili dříve) vytvořením instančního objektu pro aplikaci a přiřazením rolí k této službě prostřednictvím Azure PowerShell.

Chcete-li nastavit ověřování služby Active Directory pro Data Lake Storage Gen1, je nutné provést následující úlohy.

* Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
* Vytvoření aplikace v Azure Active Directory a instančního objektu

### <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

Před pokračováním v postupu v této části se ujistěte, že máte nainstalovanou [Windows SDK](https://dev.windows.com/en-us/downloads) . Musíte také vytvořit adresář, například **C:\mycertdir**, kde se certifikát vytvoří.

1. V okně PowerShellu přejděte do umístění, kam jste nainstalovali Windows SDK (obvykle, `C:\Program Files (x86)\Windows Kits\10\bin\x86` a pomocí nástroje [Makecert][makecert] vytvořte certifikát podepsaný svým držitelem a soukromý klíč. Použijte následující příkazy.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Zobrazí se výzva k zadání hesla privátního klíče. Po úspěšném provedení příkazu byste měli v adresáři certifikátu, který jste zadali, zobrazit **Soubor_certifikátu. cer** a **myKey. PVK** .
2. Pomocí nástroje [Pvk2Pfx][pvk2pfx] Převeďte soubory. PVK a. CER, které byly vytvořeny pomocí příkazu Makecert vytvořené na soubor. pfx. Spusťte následující příkaz.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Po zobrazení výzvy zadejte heslo privátního klíče, které jste zadali dříve. Hodnota, kterou zadáte pro parametr **-No** , je heslo, které je přidruženo k souboru. pfx. Po úspěšném dokončení příkazu byste měli taky v zadaném adresáři s certifikátem zobrazit soubor Soubor_certifikátu. pfx.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Vytvoření Azure Active Directory a instančního objektu

V této části provedete kroky k vytvoření instančního objektu pro Azure Active Directory aplikaci, přiřazení role k instančnímu objektu a ověření jako instančního objektu poskytnutím certifikátu. Spuštěním následujících příkazů vytvořte aplikaci v Azure Active Directory.

1. V okně konzoly PowerShellu vložte následující rutiny. Ujistěte se, že hodnota, kterou zadáte pro vlastnost **-DisplayName** , je jedinečná. Hodnoty pro **-domovskou stránku** a **-IdentiferUris** jsou také zástupné hodnoty a nejsou ověřeny.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Vytvořte instanční objekt s použitím ID aplikace.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Udělte instančnímu objektu přístup ke složce Data Lake Storage Gen1 a k souboru, ke kterému budete přistupovat z clusteru HDInsight. Následující fragment kódu poskytuje přístup ke kořenovému adresáři Data Lake Storage Gen1 účtu (kam jste zkopírovali soubor ukázkových dat) a samotnému souboru.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru HDInsight Linux s Data Lake Storage Gen1 jako další úložiště

V této části vytvoříme cluster HDInsight Hadoop Linux s Data Lake Storage Gen1 jako další úložiště. V této verzi musí být cluster HDInsight a účet Data Lake Storage Gen1 ve stejném umístění.

1. Začněte s načtením ID tenanta předplatného. Budete je potřebovat později.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. V této verzi můžete pro cluster Hadoop použít Data Lake Storage Gen1 jenom jako dodatečné úložiště pro cluster. Výchozím úložištěm budou i objekty blob služby Azure Storage (WASB). Nejprve vytvoříme účet úložiště a kontejnery úložiště vyžadované pro cluster.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. Vytvořte cluster HDInsight. Použijte následující rutiny.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    Po úspěšném dokončení rutiny by se měl zobrazit výstup s výpisem podrobností o clusteru.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Spuštění testovacích úloh v clusteru HDInsight pro použití účtu Data Lake Storage Gen1
Po nakonfigurování clusteru HDInsight můžete spustit testovací úlohy v clusteru, abyste otestovali, že cluster HDInsight má přístup k Data Lake Storage Gen1. Uděláte to tak, že spustíte ukázkovou úlohu podregistru, která vytvoří tabulku pomocí ukázkových dat, která jste předtím nahráli do svého účtu Data Lake Storage Gen1.

V této části provedete SSH do clusteru HDInsight Linux, který jste vytvořili, a spustíte ukázkový dotaz na podregistr.

* Pokud ke službě SSH v clusteru používáte klienta Windows, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud ke službě SSH v clusteru používáte klienta se systémem Linux, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) .

1. Po připojení spusťte pomocí následujícího příkazu podregistr CLI:

    ```azurepowershell
    hive
    ```

2. Pomocí rozhraní příkazového řádku zadejte následující příkazy k vytvoření nové tabulky s názvem " **vozidla** " pomocí ukázkových dat v Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ```output
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

V této části provedete SSH do clusteru HDInsight Linux, který jste vytvořili, a spustíte příkazy HDFS.

* Pokud ke službě SSH v clusteru používáte klienta Windows, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud ke službě SSH v clusteru používáte klienta se systémem Linux, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) .

Po připojení použijte následující příkaz HDFS systému souborů k vypsání souborů v Data Lake Storage Gen1m účtu.

```azurepowershell
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Mělo by se zobrazit seznam souborů, které jste dříve nahráli do Data Lake Storage Gen1.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Pomocí příkazu můžete také `hdfs dfs -put` Odeslat některé soubory do data Lake Storage Gen1 a potom použít `hdfs dfs -ls` k ověření, jestli se soubory úspěšně nahrály.

## <a name="see-also"></a>Viz také
* [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
