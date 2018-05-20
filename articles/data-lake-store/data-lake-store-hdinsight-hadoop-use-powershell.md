---
title: 'Prostředí PowerShell: Clusteru Azure HDInsight s Data Lake Store jako přídavná služba storage | Microsoft Docs'
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 4c08dac95a2d2b52f1a1d28f6933b94ad4db10b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Pomocí prostředí Azure PowerShell k vytvoření clusteru HDInsight s Data Lake Store (jako další úložiště)

> [!div class="op_single_selector"]
> * [Pomocí portálu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Další informace o použití prostředí Azure PowerShell ke konfiguraci clusteru HDInsight s Azure Data Lake Store, **jako další úložiště**. Pokyny k vytvoření clusteru HDInsight s Azure Data Lake Store jako výchozí úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Store jako výchozí úložiště](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Pokud plánujete službu Azure Data Lake Store používat jako další úložiště pro cluster HDInsight, důrazně doporučujeme to provést během vytváření clusteru, jak je popsáno v tomto článku. Přidání služby Azure Data Lake Store jako dalšího úložiště do existujícího clusteru HDInsight je složitý proces, při kterém může docházet k chybám.
>

Pro typy podporované clusteru slouží jako výchozí úložiště nebo účtu další úložiště Data Lake Store. Když se Data Lake Store se použije jako další úložiště, výchozí účet úložiště pro clustery budou mít pořád Azure úložiště objektů BLOB (WASB) a soubory související s clusteru (například protokoly atd.) se stále zapisují do výchozí úložiště, při data, která chcete zpracovat mohou být uloženy v účtu Data Lake Store. Pomocí Data Lake Store jako další úložiště účet nemá negativní vliv na výkon nebo možnost čtení/zápisu do úložiště z clusteru.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Pro úložiště clusteru HDInsight pomocí Data Lake Store

Zde jsou některé důležité informace týkající se používání HDInsight s Data Lake Store:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Store, jako je k dispozici pro HDInsight verze 3.2, 3,4, 3.5 a 3.6 Další úložiště.

Konfigurace HDInsight pro práci s Data Lake Store pomocí prostředí PowerShell zahrnuje následující kroky:

* Vytvoření Azure Data Lake Store
* Nastavení ověřování pro přístup na základě rolí k Data Lake Store
* Vytvoření clusteru HDInsight pomocí ověřování do Data Lake Store
* V clusteru spustit testovací úlohy

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Můžete si ho nainstalovat [odtud](https://dev.windows.com/en-us/downloads). To použijete k vytvoření certifikátu zabezpečení.
* **Azure Active Directory instanční objekt**. Kroky v tomto kurzu poskytují pokyny o tom, jak vytvořit objekt služby ve službě Azure AD. Ale musí být správce Azure AD mohli vytvořit objekt služby. Pokud jste správce Azure AD, můžete přeskočit tento požadavek a pokračujte v tomto kurzu.

    **Pokud si nejste správce Azure AD**, nebudete moci provádět kroky potřebné k vytvoření instančního objektu. V takovém případě musíte vám správce Azure AD nejdřív vytvořit objekt služby, před vytvořením clusteru HDInsight s Data Lake Store. Navíc instanční objekt musí být vytvořen pomocí certifikátu, jak je popsáno v [vytvořit objekt služby pomocí certifikátu](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Vytvoření Azure Data Lake Store
Postupujte podle těchto kroků můžete vytvořit Data Lake Store.

1. Z plochy otevřete nové okno Azure PowerShell a zadejte následující fragment kódu. Po zobrazení výzvy k přihlášení, nezapomeňte že přihlásit jako jeden správce nebo vlastníka předplatného:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Pokud se zobrazí zpráva podobná `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` při registraci zprostředkovatele prostředků Data Lake Store, je možné, že vaše předplatné není povolený pro Azure Data Lake Store. Ujistěte se, aktivujte předplatné Azure pro verzi public preview služby Data Lake Store pomocí následujících tyto [pokyny](data-lake-store-get-started-portal.md).
   >
   >
2. Účet Azure Data Lake Store je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měli byste vidět výstup takto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvořte účet Azure Data Lake Store. Název účtu, který zadáte musí obsahovat jenom malá písmena a číslice.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

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

5. Nahrajte ukázková data do Azure Data Lake. Použijeme ho později v tomto článku k ověření, že data jsou přístupná z clusteru služby HDInsight. Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Nastavení ověřování pro přístup na základě rolí k Data Lake Store

Každé předplatné služby Azure souvisí s Azure Active Directory. Uživatelů a služeb, která přistupují k prostředkům odběru pomocí portálu Azure nebo rozhraní API služby Azure Resource Manager, musí nejprve ověřit pomocí této služby Azure Active Directory. Udělením přístupu k předplatných Azure a služby přiřazením příslušné role na prostředek služby Azure.  Objekt služby pro služby, identifikuje službu v Azure Active Directory (AAD). Tato část ukazuje postup udělení aplikační služba, stejně jako HDInsight, přístup k prostředku Azure (účet Azure Data Lake Store jste vytvořili dříve) vytvořením objekt služby pro aplikaci a přiřazení rolí k, pomocí prostředí Azure PowerShell.

Nastavení ověřování služby Active Directory pro Azure Data Lake, musíte provést následující úlohy.

* Vytvořit certifikát podepsaný svým držitelem
* Vytvoření aplikace v Azure Active Directory a objektu služby

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem

Zajistěte, aby byla [Windows SDK](https://dev.windows.com/en-us/downloads) nainstalovat před provedením kroků v této části. Musíte také vytvořit adresář, jako například **C:\mycertdir**, kde bude certifikát vytvořen.

1. V okně PowerShell přejděte do umístění, kam jste nainstalovali Windows SDK (obvykle `C:\Program Files (x86)\Windows Kits\10\bin\x86` a použít [MakeCert] [ makecert] nástroj vytvořit certifikát podepsaný svým držitelem a privátní klíč. Použijte následující příkazy.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zobrazí se výzva k zadání heslo soukromého klíče. Až se příkaz úspěšně provede, měli byste vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikát, který jste zadali.
2. Použití [Pvk2Pfx] [ pvk2pfx] nástroj pro převod pvk a .cer soubory, které vytvořili MakeCert do souboru .pfx. Spusťte následující příkaz.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte dříve zadaný heslo soukromého klíče. Hodnota zadaná **-SP** parametr je heslo, které souvisí s soubor .pfx. Po úspěšném dokončení příkazu, měli byste taky vidět CertFile.pfx v adresáři certifikát, který jste zadali.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Vytvoření služby Azure Active Directory a objektu služby

V této části provedete kroky k vytvoření služby hlavní pro aplikaci Azure Active Directory, přiřaďte roli instanční objekt a ověřit jako objekt služby tím, že poskytuje certifikát. Spusťte následující příkazy k vytvoření aplikace v Azure Active Directory.

1. Vložte následující rutiny v okně konzoly prostředí PowerShell. Ujistěte se, hodnota zadaná **– DisplayName** vlastnost je jedinečný. Navíc hodnoty **– Domovská stránka** a **- IdentiferUris** jsou zástupné hodnoty a nejsou ověřené.

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
2. Vytvořit objekt služby pomocí ID aplikace.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udělte přístup k hlavní službě Data Lake Store složku a soubor, který bude využívat z clusteru HDInsight. Následující fragment poskytuje přístup ke kořenovému adresáři účtu Data Lake Store (které jste zkopírovali ukázkový datový soubor) a v samotném souboru.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Vytvoření clusteru služby HDInsight Linux s Data Lake Store jako další úložiště

V této části vytvoříme cluster služby HDInsight Hadoop Linux s Data Lake Store jako další úložiště. Pro tuto verzi clusteru HDInsight a Data Lake Store musí být ve stejném umístění.

1. Začněte s načítání ID předplatného klienta. Budete potřebovat, který později.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. Pro tuto verzi pro Hadoop cluster, Data Lake Store se použít jenom jako další úložiště pro cluster. Výchozí úložiště budou mít pořád úložiště Azure BLOB (WASB). Proto nejdřív vytvoříme účet úložiště a kontejnery úložiště potřebné pro cluster.

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

    Po úspěšném dokončení rutiny, měli byste vidět výstup výpis podrobnosti o clusteru.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Spuštění testu úloh na clusteru HDInsight pomocí Data Lake Store
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit testovací úlohy v clusteru k testování clusteru HDInsight můžete získat přístup k Data Lake Store. To pokud chcete udělat, budeme se spustí úlohy Hive vzorku, který vytvoří tabulku pomocí ukázkových dat, který jste dříve nahráli do Data Lake Store.

V této části se SSH do clusteru HDInsight Linux můžete vytvořit a spustit ukázkový dotaz Hive.

* Pokud používáte klienta Windows do SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linux tak, aby SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po připojení, spusťte rozhraní příkazového řádku Hive pomocí následujícího příkazu:

        hive
2. Pomocí rozhraní příkazového řádku, zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Store:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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

## <a name="access-data-lake-store-using-hdfs-commands"></a>Přístup k Data Lake Store pomocí příkazů HDFS
Po konfiguraci clusteru HDInsight pomocí Data Lake Store, můžete pro přístup do obchodu příkazů HDFS prostředí.

V této části se SSH do clusteru HDInsight Linux můžete vytvořit a spustit příkazy HDFS.

* Pokud používáte klienta Windows do SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linux tak, aby SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po připojení, použijte následující příkaz systému souborů HDFS zobrazte seznam souborů v Data Lake Store.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

To by měl seznam soubor, který jste dříve nahráli do Data Lake Store.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Můžete také `hdfs dfs -put` příkazu do Data Lake Store nahrát některé soubory a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrál.

## <a name="see-also"></a>Viz také
* [Použití Data Lake Store s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portálu: Vytvoření clusteru HDInsight do Data Lake Store pomocí](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
