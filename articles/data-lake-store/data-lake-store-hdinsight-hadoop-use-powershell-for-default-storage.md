---
title: PowerShell – cluster HDInsight s Data Lake Storage Gen1 – Azure
description: Pomocí Azure PowerShell můžete pomocí Azure Data Lake Storage Gen1 vytvářet a používat clustery Azure HDInsight.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606772"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Vytváření clusterů HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Použití Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použít Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Naučte se, jak pomocí Azure PowerShell nakonfigurovat clustery Azure HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště. Pokyny k vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako další úložiště najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako další úložiště](data-lake-store-hdinsight-hadoop-use-powershell.md).

Tady jsou některé důležité informace pro používání služby HDInsight s Data Lake Storage Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1 výchozím úložištěm, které je k dispozici pro HDInsight verze 3,5 a 3,6.

* Možnost vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1 jako výchozí úložiště *nejsou k dispozici* pro HDInsight Premium clustery.

Pokud chcete nakonfigurovat HDInsight pro práci s Data Lake Storage Gen1 pomocí prostředí PowerShell, postupujte podle pokynů v následujících pěti částech.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:

* **Předplatné Azure**: přejít k [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 nebo vyšší**: Přečtěte si [článek Jak nainstalovat a nakonfigurovat PowerShell](/powershell/azure/).
* **Sada Windows Software Development Kit (SDK)**: Chcete-li nainstalovat Windows SDK, navštivte stránku [soubory ke stažení a nástroje pro Windows 10](https://dev.windows.com/downloads). Sada SDK slouží k vytvoření certifikátu zabezpečení.
* **Azure Active Directory instančního objektu**: v tomto kurzu se dozvíte, jak vytvořit instanční objekt ve službě Azure Active Directory (Azure AD). Pokud ale chcete vytvořit instanční objekt, musíte být správce Azure AD. Pokud jste správce, můžete tento požadavek přeskočit a pokračovat v tomto kurzu.

    >[!NOTE]
    >Instanční objekt můžete vytvořit jenom v případě, že jste správce Azure AD. Správce Azure AD musí vytvořit instanční objekt, aby bylo možné vytvořit cluster HDInsight s Data Lake Storage Gen1. Instanční objekt musí být vytvořen s certifikátem, jak je popsáno v tématu [Vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Vytvoření účtu Azure Data Lake Storage Gen1

Pokud chcete vytvořit účet Data Lake Storage Gen1, udělejte toto:

1. Z plochy otevřete okno prostředí PowerShell a potom zadejte následující fragmenty kódu. Až se zobrazí výzva k přihlášení, přihlaste se jako jeden z správců nebo vlastníků předplatného. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Pokud zaregistrujete poskytovatele prostředků Data Lake Storage Gen1 a obdržíte chybu podobnou této `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` : vaše předplatné nemusí být schválené pro data Lake Storage Gen1. Pokud chcete povolit předplatné Azure pro Data Lake Storage Gen1, postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md).
    >

2. Účet Data Lake Storage Gen1 je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků.

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
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Použití Data Lake Storage Gen1 jako výchozího úložiště vyžaduje, abyste zadali kořenovou cestu, do které se zkopírují soubory specifické pro konkrétní cluster při vytváření clusteru. Chcete-li vytvořit kořenovou cestu, která je **/Clusters/hdiadlcluster** ve fragmentu, použijte následující rutiny:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup na základě rolí na Data Lake Storage Gen1
Každé předplatné Azure je přidruženo k entitě Azure AD. Uživatelé a služby, které přistupují k prostředkům předplatného pomocí Azure Portal nebo rozhraní Azure Resource Manager API, se musí nejdřív ověřit pomocí Azure AD. Přístup k předplatným a službám Azure se uděluje tím, že jim přiřadíte příslušnou roli na prostředku Azure. Pro služby identifikuje instanční objekt službu ve službě Azure AD.

Tato část ukazuje, jak udělit aplikační službu, jako je například HDInsight, přístup k prostředku Azure (účet Data Lake Storage Gen1, který jste vytvořili dříve). Provedete to tak, že vytvoříte instanční objekt pro aplikaci a přiřadíte k němu role přes PowerShell.

Chcete-li nastavit ověřování služby Active Directory pro Data Lake Storage Gen1, proveďte úkoly v následujících dvou částech.

### <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
Před pokračováním v postupu v této části se ujistěte, že máte nainstalovanou [Windows SDK](https://dev.windows.com/en-us/downloads) . Musíte také vytvořit adresář, například *C:\mycertdir*, kde vytvoříte certifikát.

1. V okně PowerShellu přejdete do umístění, kam jste nainstalovali Windows SDK (obvykle *C:\Program Files (x86) \Windows Kits\10\bin\x86*), a pomocí nástroje [Makecert][makecert] vytvořit certifikát podepsaný svým držitelem a soukromý klíč. Použijte následující příkazy:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Zobrazí se výzva k zadání hesla privátního klíče. Po úspěšném provedení příkazu byste měli v adresáři s certifikátem, který jste zadali, zobrazit **Soubor_certifikátu. cer** a **myKey. PVK** .
2. Pomocí nástroje [Pvk2Pfx][pvk2pfx] Převeďte soubory. PVK a. CER, které byly vytvořeny pomocí příkazu Makecert vytvořené na soubor. pfx. Spusťte následující příkaz:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Po zobrazení výzvy zadejte heslo privátního klíče, které jste zadali dříve. Hodnota, kterou zadáte pro parametr **-No** , je heslo, které je přidruženo k souboru. pfx. Po úspěšném dokončení příkazu byste měli také v adresáři s certifikátem, který jste zadali, zobrazit soubor **Soubor_certifikátu. pfx** .

### <a name="create-an-azure-ad-and-a-service-principal"></a>Vytvoření služby Azure AD a instančního objektu
V této části vytvoříte instanční objekt pro aplikaci Azure AD, přiřadíte roli k instančnímu objektu a ověříte ho jako instanční objekt tím, že poskytnete certifikát. Pokud chcete vytvořit aplikaci ve službě Azure AD, spusťte následující příkazy:

1. V okně konzoly PowerShellu vložte následující rutiny. Ujistěte se, že hodnota, kterou zadáte pro vlastnost **-DisplayName** , je jedinečná. Hodnoty pro **-domovskou stránku** a **-IdentiferUris** jsou zástupné hodnoty a nejsou ověřeny.

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

2. Vytvořte instanční objekt pomocí ID aplikace.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Udělte instančnímu objektu přístup k kořenovému adresáři Data Lake Storage Gen1 a všem složkám v kořenové cestě, kterou jste zadali dříve. Použijte následující rutiny:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Vytvoření clusteru HDInsight Linux s Data Lake Storage Gen1 jako výchozí úložiště

V této části vytvoříte cluster HDInsight Hadoop Linux s Data Lake Storage Gen1 jako výchozí úložiště. V této verzi musí být cluster HDInsight a Data Lake Storage Gen1 ve stejném umístění.

1. Načtěte ID tenanta předplatného a uložte ho pro pozdější použití.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Vytvořte cluster HDInsight pomocí následujících rutin:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Po úspěšném dokončení rutiny by se měl zobrazit výstup, který obsahuje seznam podrobností o clusteru.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spuštění testovacích úloh v clusteru HDInsight pro použití Data Lake Storage Gen1
Po nakonfigurování clusteru HDInsight můžete na něm spustit testovací úlohy, aby se zajistilo, že bude mít přístup k Data Lake Storage Gen1. Uděláte to tak, že spustíte ukázkovou úlohu podregistru a vytvoříte tabulku, která používá ukázková data, která jsou už dostupná v Data Lake Storage Gen1 na adrese *\<cluster root> /example/data/Sample.log*.

V této části provedete připojení k Secure Shell (SSH) do clusteru HDInsight Linux, který jste vytvořili, a potom spustíte ukázkový dotaz na podregistr.

* Pokud k vytvoření připojení SSH do clusteru používáte klienta se systémem Windows, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud k vytvoření připojení SSH do clusteru používáte klienta se systémem Linux, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po navázání připojení spusťte rozhraní příkazového řádku (CLI) podregistru pomocí následujícího příkazu:

    ```powershell
    hive
    ```

2. Pomocí rozhraní příkazového řádku zadejte následující příkazy k vytvoření nové tabulky s názvem " **vozidla** " pomocí ukázkových dat v Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

V konzole SSH by se měl zobrazit výstup dotazu.

>[!NOTE]
>Cesta k ukázkovým datům v předchozím příkazu CREATE TABLE je `adl:///example/data/` , kde `adl:///` je kořenový adresář clusteru. V rámci příkladu kořenového adresáře clusteru uvedeného v tomto kurzu je příkaz `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Můžete buď použít kratší alternativu, nebo zadat úplnou cestu k kořenovému adresáři clusteru.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Přístup k Data Lake Storage Gen1 pomocí příkazů HDFS
Po nakonfigurování clusteru HDInsight pro použití Data Lake Storage Gen1 můžete k přístupu do Storu použít příkazy prostředí Hadoop systém souborů DFS (Distributed File System) (HDFS).

V této části provedete připojení SSH ke clusteru HDInsight Linux, který jste vytvořili, a potom spustíte příkazy HDFS.

* Pokud k vytvoření připojení SSH do clusteru používáte klienta se systémem Windows, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud k vytvoření připojení SSH do clusteru používáte klienta se systémem Linux, přečtěte si téma [Použití SSH se systémem Linux Hadoop ve službě HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po vytvoření připojení seznam souborů v Data Lake Storage Gen1 pomocí následujícího příkazu HDFS systému souborů.

```azurepowershell
hdfs dfs -ls adl:///
```

Pomocí příkazu můžete také `hdfs dfs -put` Odeslat některé soubory do data Lake Storage Gen1 a potom použít `hdfs dfs -ls` k ověření, jestli se soubory úspěšně nahrály.

## <a name="see-also"></a>Viz také
* [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure Portal: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx