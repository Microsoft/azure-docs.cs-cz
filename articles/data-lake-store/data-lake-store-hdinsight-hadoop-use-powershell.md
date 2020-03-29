---
title: 'PowerShell: Cluster Azure HDInsight s Azure Data Lake Storage Gen1 jako doplňkovým úložištěm | Dokumenty společnosti Microsoft'
description: Zjistěte, jak pomocí Azure PowerShellu nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 jako další úložiště.
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970142"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Použití Azure PowerShellu k vytvoření clusteru HDInsight s Azure Data Lake Storage Gen1 (jako další úložiště)

> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí Azure PowerShellu nakonfigurovat cluster HDInsight s Azure Data Lake Storage Gen1 **jako další úložiště**. Pokyny k vytvoření clusteru HDInsight s virtuálním úložištěm Data Lake Storage 1 jako výchozím úložištěm najdete v [tématu Vytvoření clusteru HDInsight s gen1 úložiště datového jezera jako výchozího úložiště](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Pokud budete používat Data Lake Storage Gen1 jako další úložiště pro cluster HDInsight, důrazně doporučujeme provést při vytváření clusteru, jak je popsáno v tomto článku. Přidání Data Lake Storage Gen1 jako dalšího úložiště do existujícího clusteru HDInsight je složitý proces a náchylný k chybám.
>

Pro podporované typy clusterů lze gen1 úložiště datového jezera použít jako výchozí úložiště nebo další účet úložiště. Při použití data Lake Storage Gen1 jako další úložiště, výchozí účet úložiště pro clustery bude stále Azure Storage Objekty BLOB (WASB) a soubory související s clusterem (například protokoly, atd.) jsou stále zapsány do výchozího úložiště, zatímco data, která chcete proces ukládat v účtu Data Lake Storage Gen1. Použití Data Lake Storage Gen1 jako další účet úložiště nemá vliv na výkon nebo schopnost číst a zapisovat do úložiště z clusteru.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Použití úložiště datového jezera Gen1 pro úložiště clusteru HDInsight

Tady jsou některé důležité aspekty používání HDInsightu s datovým lakem Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k úložišti Data Lake Storage Gen1, protože další úložiště je k dispozici pro hdinsight verze 3.2, 3.4, 3.5 a 3.6.

Konfigurace HDInsight pro práci s Data Lake Storage Gen1 pomocí PowerShellu zahrnuje následující kroky:

* Vytvoření účtu Data Lake Storage Gen1
* Nastavení ověřování pro přístup k úložišti Data Lake Storage Gen1 na základě rolí
* Vytvoření clusteru HDInsight s ověřováním na Data Lake Storage Gen1
* Spuštění testovací úlohy v clusteru

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Sada Windows SDK**. Můžete si ji nainstalovat [odtud](https://dev.windows.com/en-us/downloads). Tento certifikát slouží k vytvoření certifikátu zabezpečení.
* **Hlavní povinný ke službě Azure Active Directory**. Kroky v tomto kurzu poskytují pokyny, jak vytvořit instanční objekt ve službě Azure AD. Musíte však být správce azure ad, aby bylo možné vytvořit instanční objekt. Pokud jste správce Azure AD, můžete přeskočit tento předpoklad a pokračovat v kurzu.

    **Pokud nejste správce azure ad**, nebude možné provést kroky potřebné k vytvoření instančního objektu. V takovém případě musí správce služby Azure AD nejprve vytvořit instanční objekt, než budete moct vytvořit cluster HDInsight s Gen1 úložiště datového jezera. Instanční objekt musí být také vytvořen pomocí certifikátu, jak je popsáno v na [vytvořit instanční objekt s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Chcete-li vytvořit účet Data Lake Storage Gen1, postupujte takto.

1. Na ploše otevřete nové okno Azure PowerShellu a zadejte následující fragment. Po zobrazení výzvy k přihlášení se ujistěte, že se přihlásíte jako jeden ze správců/vlastníků předplatného:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Pokud se zobrazí chyba `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` podobná při registraci zprostředkovatele prostředků Data Lake Storage Gen1, je možné, že vaše předplatné není na seznamu povolených pro Data Lake Storage Gen1. Podle následujících [pokynů](data-lake-store-get-started-portal.md)nezapomeňte povolit předplatné Azure pro úložiště datových jezer Gen1 .
   >
   >
2. Účet Gen1 úložiště datového jezera je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měl by se zobrazit výstup podobný tomuto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvořte účet Data Lake Storage Gen1. Zadaný název účtu musí obsahovat pouze malá písmena a čísla.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

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

5. Nahrajte některá ukázková data do data Lake Storage Gen1. Použijeme to později v tomto článku k ověření, že data jsou přístupná z clusteru HDInsight. Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup k úložišti Data Lake Storage Gen1 na základě rolí

Každé předplatné Azure je přidružené ke službě Azure Active Directory. Uživatelé a služby, které přistupují k prostředkům předplatného pomocí portálu Azure nebo rozhraní API Azure Resource Manager, se musí nejprve ověřit pomocí tohoto nástroje Azure Active Directory. Přístup se uděluje předplatným a službám Azure přiřazením příslušné role na prostředek Azure.  U služeb objekt zabezpečení služby identifikuje službu ve službě Azure Active Directory (AAD). Tato část ukazuje, jak udělit aplikační služby, jako je HDInsight, přístup k prostředku Azure (účet Data Lake Storage Gen1, který jste vytvořili dříve) vytvořením instančního objektu pro aplikaci a přiřazením rolí k němu prostřednictvím Azure PowerShellu.

Chcete-li nastavit ověřování služby Active Directory pro program Gen1 úložiště datového jezera, je nutné provést následující úkoly.

* Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
* Vytvoření aplikace ve službě Azure Active Directory a instančního objektu

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Než budete pokračovat v krocích v této části, ujistěte se, že máte nainstalovanou sadku [Windows SDK.](https://dev.windows.com/en-us/downloads) Musíte také vytvořit adresář, například **C:\mycertdir**, kde bude vytvořen certifikát.

1. Z okna Prostředí PowerShell přejděte do umístění, kde jste `C:\Program Files (x86)\Windows Kits\10\bin\x86` nainstalovali sadu Windows SDK (obvykle, a pomocí nástroje [MakeCert][makecert] vytvořte certifikát podepsaný svým držitelem a soukromý klíč. Použijte následující příkazy.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Budete vyzváni k zadání hesla soukromého klíče. Po úspěšném spuštění příkazu byste měli vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikátu, který jste zadali.
2. Pomocí nástroje [Pvk2Pfx][pvk2pfx] převeďte soubory .pvk a CER, které makecert vytvořil, na soubor .pfx. Spusťte následující příkaz.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte heslo soukromého klíče, které jste zadali dříve. Hodnota, kterou zadáte pro parametr **-po,** je heslo, které je přidruženo k souboru .pfx. Po úspěšném dokončení příkazu byste měli také vidět CertFile.pfx v adresáři certifikátu, který jste zadali.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Vytvoření služby Azure Active Directory a instančního objektu

V této části provedete kroky k vytvoření instančního objektu pro aplikaci služby Azure Active Directory, přiřazení role instančnímu objektu a ověření jako instančního objektu poskytnutím certifikátu. Spusťte následující příkazy a vytvořte aplikaci ve službě Azure Active Directory.

1. Vložte následující rutiny do okna konzoly PowerShellu. Ujistěte se, že hodnota, kterou zadáte pro vlastnost **-DisplayName,** je jedinečná. Také hodnoty pro **-HomePage** a **-IdentiferUris** jsou zástupné hodnoty a nejsou ověřeny.

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
2. Vytvořte instanční objekt pomocí ID aplikace.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udělte primárnímu servisnímu objektu přístup ke složce Data Lake Storage Gen1 a souboru, ke kterému budete mít přístup z clusteru HDInsight. Tento fragment poskytuje přístup ke kořenovému adresáři účtu Data Lake Storage Gen1 (kde jste zkopírovali ukázkový datový soubor) a k samotnému souboru.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru HDInsight Linux s datem Lake Storage Gen1 jako dalším úložištěm

V této části vytvoříme cluster HDInsight Hadoop Linux s Datovým lakem Storage Gen1 jako dalším úložištěm. Pro tuto verzi musí být cluster HDInsight a účet Gen1 úložiště datového jezera ve stejném umístění.

1. Začněte s načtením ID klienta předplatného. To budeš potřebovat později.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Pro tuto verzi pro cluster Hadoop, Data Lake Storage Gen1 lze použít pouze jako další úložiště pro cluster. Výchozí úložiště bude stále objekty BLOB úložiště Azure (WASB). Takže nejprve vytvoříme účet úložiště a kontejnery úložiště potřebné pro cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Vytvořte cluster HDInsight. Použijte následující rutiny.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Po úspěšném dokončení rutiny by se měl zobrazit výstup s podrobnostmi o clusteru.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Spuštění testovacích úloh v clusteru HDInsight k použití účtu Data Lake Storage Gen1
Po konfiguraci clusteru HDInsight můžete spustit testovací úlohy v clusteru a otestovat, že cluster HDInsight má přístup k datovému úložišti datového jezera Gen1. Za tímto účelem spustíme ukázkovou úlohu Hive, která vytvoří tabulku pomocí ukázkových dat, která jste nahráli dříve do účtu Gen1 úložiště datového jezera.

V této části budete SSH do hdinsight linuxového clusteru, který jste vytvořili, a spustíte ukázkový dotaz Hive.

* Pokud používáte klienta Systému Windows pro SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte linuxového klienta pro SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na HDInsightu z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po připojení spusťte příkaz příkaz příkazu Hive pomocí následujícího příkazu:

        hive
2. Pomocí příkazového příkazu zadejte následující příkazy k vytvoření nové tabulky s názvem **vozidla** pomocí ukázkových dat v gen1 úložiště datového jezera:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Přístup k datům Lake Storage Gen1 pomocí příkazů HDFS
Jakmile nakonfigurujete cluster HDInsight tak, aby používal Data Lake Storage Gen1, můžete pro přístup k úložišti použít příkazy prostředí HDFS.

V této části budete SSH do HDInsight Linux clusteru jste vytvořili a spustit příkazy HDFS.

* Pokud používáte klienta Systému Windows pro SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte linuxového klienta pro SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na HDInsightu z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po připojení použijte následující příkaz souborového systému HDFS k zobrazení seznamu souborů v účtu Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

To by mělo seznam souboru, který jste nahráli dříve data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Pomocí příkazu `hdfs dfs -put` můžete také odeslat některé soubory do zařízení `hdfs dfs -ls` Data Lake Storage Gen1 a potom je použít k ověření, zda byly soubory úspěšně odeslány.

## <a name="see-also"></a>Viz také
* [Použití data lake storage gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: Vytvoření clusteru HDInsight pro použití data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
