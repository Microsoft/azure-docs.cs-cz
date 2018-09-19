---
title: 'Prostředí PowerShell: Clusteru Azure HDInsight s Azure Data Lake Storage Gen1 jako doplňkové úložiště | Dokumentace Microsoftu'
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: fe742156ccff4325ab6b9f9fc9619093784ccc03
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129790"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Pomocí prostředí Azure PowerShell k vytvoření clusteru služby HDInsight s Azure Data Lake Storage Gen1 (jako další úložiště)

> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Resource Manageru](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí Azure Powershellu ke konfiguraci clusteru služby HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**. Pokyny o tom, jak vytvořit HDInsight cluster s Data Lake Storage Gen1 jako výchozí úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako výchozím úložištěm](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Pokud se chystáte použít Data Lake Storage Gen1 jako další úložiště pro HDInsight cluster, důrazně doporučujeme, abyste to udělali při vytváření clusteru, jak je popsáno v tomto článku. Přidání Data Lake Storage Gen1 jako další úložiště k existující HDInsight je cluster složitý proces a náchylná k chybám.
>

Pro typy podporovaných clusterů Data Lake Storage Gen1 může sloužit jako výchozí úložiště nebo dalšího účtu úložiště. Pokud Data Lake Storage Gen1 slouží jako další úložiště, výchozí účet úložiště pro clustery bude stále objekty BLOB Azure Storage (WASB) a soubory související s clusterem (jako jsou protokoly atd.) jsou stále zapsány do výchozího úložiště dat, který chcete proces mohou být uloženy v účtu Data Lake Storage Gen1. Pomocí Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon ani schopnost čtení a zápis do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Pro úložiště clusteru HDInsight pomocí Data Lake Storage Gen1

Tady jsou některé důležité informace týkající se použití HDInsight s Data Lake Storage Gen1:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1 jako další úložiště je k dispozici pro HDInsight verze 3.2, 3.4, 3.5 a 3.6.

Konfigurace HDInsight pro práci s Data Lake Storage Gen1 pomocí prostředí PowerShell zahrnuje následující kroky:

* Vytvoření účtu Data Lake Storage Gen1
* Nastavení ověřování pro přístup na základě rolí k Data Lake Storage Gen1
* Vytvoření clusteru HDInsight při ověřování vůči službě Data Lake Storage Gen1
* Na tomto clusteru spustí testovací úlohy

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Můžete si ho nainstalovat [odtud](https://dev.windows.com/en-us/downloads). Můžete použít pro vytvoření certifikátu zabezpečení.
* **Azure Active Directory Service Principal**. Kroky v tomto kurzu poskytují pokyny o tom, jak vytvořit instanční objekt ve službě Azure AD. Však musí být správce Azure AD mohli vytvořit instanční objekt. Pokud jste správce Azure AD, můžete tuto požadovanou součást přeskočit a pokračovat v tomto kurzu.

    **Pokud si nejste správce Azure AD**, nebudete moci provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce Azure AD nejprve vytvořit instanční objekt služby, než vytvoříte HDInsight cluster s Data Lake Storage Gen1. Navíc instanční objekt musí být vytvořen pomocí certifikátu, jak je popsáno v [vytvoření instančního objektu s certifikátem](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Postupujte podle těchto kroků a vytvořte účet Data Lake Storage Gen1.

1. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragment kódu. Po zobrazení výzvy k přihlášení, ujistěte se, že se že přihlásíte jako správce/vlastníka předplatného:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Pokud se zobrazí zpráva podobná `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` při registraci poskytovatele prostředků Data Lake Storage Gen1, je možné, že vaše předplatné není na seznamu povolených pro Data Lake Storage Gen1. Ujistěte se, že vaše předplatné Azure pro Data Lake Storage Gen1 povolíte pomocí těchto [pokyny](data-lake-store-get-started-portal.md).
   >
   >
2. Účet Data Lake Storage Gen1 souvisí s skupiny prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měli byste vidět výstup podobný tomuto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvoření účtu Data Lake Storage Gen1. Název účtu, který zadáte musí obsahovat jenom malá písmena a číslice.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Zobrazený výstup by měl vypadat asi takto:

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

5. Nahrání ukázkových dat do Data Lake Storage Gen1. Použijeme dále v tomto článku ověřit, že data jsou přístupná z clusteru HDInsight. Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup na základě rolí k Data Lake Storage Gen1

Každé předplatné služby Azure souvisí s Azure Active Directory. Uživatelům a službám přístup k prostředkům předplatného pomocí webu Azure portal nebo rozhraní API Azure Resource Manageru, musí nejdřív ověřit pomocí služby Azure Active Directory. Udělením přístupu k předplatným Azure a služby přiřazením příslušné role v prostředku Azure.  Pro služby identifikuje instančního objektu služby v Azure Active Directory (AAD). Tato část ukazuje, jak udělit aplikační služba, jako je HDInsight, přístup k prostředku Azure (účet Data Lake Storage Gen1 jste vytvořili dříve) tak, že vytvoření instančního objektu pro aplikaci a přiřazení rolí, která prostřednictvím Azure Powershellu.

Pokud chcete nastavit ověřování služby Active Directory pro Data Lake Storage Gen1, musí provádět následující úlohy.

* Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
* Vytvoření aplikace v Azure Active Directory a instančního objektu

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Ujistěte se, že máte [sady Windows SDK](https://dev.windows.com/en-us/downloads) nainstalované před provedením kroků v této části. Musíte mít také vytvořený adresář, jako například **C:\mycertdir**, kde se vytvoří certifikát.

1. V okně Powershellu přejděte do umístění, kam jste nainstalovali sadu Windows SDK (obvykle `C:\Program Files (x86)\Windows Kits\10\bin\x86` a použít [MakeCert] [ makecert] nástroj k vytvoření certifikátu podepsaného svým držitelem a privátní klíč. Pomocí následujících příkazů.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Jste vyzváni k zadání hesla privátního klíče. Jakmile se příkaz úspěšně provede, měli byste vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikát, který jste zadali.
2. Použití [Pvk2Pfx] [ pvk2pfx] nástroj pro převod souborů .pvk a .cer, vytvořené pomocí nástroje MakeCert do souboru .pfx. Spusťte následující příkaz.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte heslo soukromého klíče jste zadali dříve. Hodnota zadaná **-No** parametr je heslo, které je přidružený k souboru .pfx. Po úspěšném dokončení příkazu, měli byste vidět CertFile.pfx v adresáři certifikát, který jste zadali.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Vytvoření aplikace Azure Active Directory a instančního objektu

V této části provedete postup vytvořit instanční objekt pro aplikaci Azure Active Directory, přiřazení role pro instanční objekt a ověřit jako instanční objekt služby tím, že poskytuje certifikát. Spusťte následující příkazy k vytvoření aplikace v Azure Active Directory.

1. Vložte následující rutiny v okně konzoly Powershellu. Ujistěte se, že hodnota zadaná **- DisplayName** vlastnost je jedinečný. Navíc hodnoty **– Domovská stránka** a **- IdentiferUris** zástupné hodnoty jsou a nejsou ověřeny.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Vytvoření instančního objektu pomocí ID aplikace.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udělení přístupu instančního objektu služby Data Lake Storage Gen1 složky a souboru, ke kterému bude přístup z clusteru HDInsight. Následující fragment poskytuje přístup ke kořenové složce účtu Data Lake Storage Gen1 (kde jste zkopírovali soubor ukázkových dat) a samotný soubor.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru HDInsight s Linuxem s Data Lake Storage Gen1 jako další úložiště

V této části vytvoříme cluster HDInsight Hadoop Linux s Data Lake Storage Gen1 jako další úložiště. Pro tuto verzi clusteru HDInsight a účet Data Lake Storage Gen1 musí být ve stejném umístění.

1. Začněte s načítání ID předplatného tenanta. Budete potřebovat, který později.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. Pro tuto verzi pro Hadoop cluster, Data Lake Storage Gen1 jde použít jenom jako další úložiště pro cluster. Výchozí úložiště bude nadále objekty BLOB služby Azure storage (WASB). Proto nejdřív vytvoříme účet úložiště a kontejnery úložiště potřebné pro cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Vytvoření clusteru HDInsight. Pomocí následující rutiny.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Po úspěšném dokončení rutina byste měli vidět výstup výpisu podrobnosti o clusteru.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Spouštění testů úloh na clusteru HDInsight pro použití účtu Data Lake Storage Gen1
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit testovací úlohy na clusteru pro testování, aby mohli získat přístup ke clusteru HDInsight Gen1 úložiště Data Lake. Uděláte to tak, provedeme ukázkové úlohy Hive, který vytvoří tabulku pomocí ukázkových dat, který jste dříve nahráli do svého účtu Data Lake Storage Gen1.

V této části se SSH do clusteru HDInsight Linux můžete vytvořit a spustit ukázkový dotaz Hive.

* Pokud používáte Windows klient SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte Linux klienta SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Jakmile budete připojeni, pomocí následujícího příkazu spusťte rozhraní příkazového řádku Hive:

        hive
2. Pomocí rozhraní příkazového řádku zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Zobrazený výstup by měl vypadat přibližně takto:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Přístup k Data Lake Storage Gen1 pomocí příkazů HDFS
Po konfiguraci clusteru HDInsight pro použití služby Data Lake Storage Gen1, můžete použít příkazy prostředí HDFS pro přístup k úložišti.

V této části se SSH do clusteru HDInsight Linux vytvořili a spustili příkazů HDFS.

* Pokud používáte Windows klient SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte Linux klienta SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Jakmile budete připojeni, použijte následující příkaz systému souborů HDFS zobrazte seznam souborů v účtu Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Tento soubor, který jste dříve nahráli do Data Lake Storage Gen1 zveřejnit.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Můžete také použít `hdfs dfs -put` příkazu některé soubory nahrát do Data Lake Storage Gen1 a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrány.

## <a name="see-also"></a>Viz také
* [Pomocí Data Lake Storage Gen1 s využitím clusterů Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
