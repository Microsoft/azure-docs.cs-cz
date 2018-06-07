---
title: Vytvoření clusterů HDInsight s Data Lake Store jako výchozí úložiště pomocí prostředí PowerShell | Microsoft Docs
description: Pomocí prostředí Azure PowerShell k vytváření a používání clustery HDInsight s Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 71b6b507952793e34a0e0413d7d652640680dab7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625701"
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Vytvoření clusterů HDInsight s Data Lake Store jako výchozí úložiště pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Použití portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Další informace o použití prostředí Azure PowerShell ke konfiguraci Azure HDInsight clustery s Azure Data Lake Store, jako výchozí úložiště. Pokyny týkající se vytvoření clusteru HDInsight s Data Lake Store jako další úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Store jako další úložiště](data-lake-store-hdinsight-hadoop-use-powershell.md).

Zde jsou některé důležité informace týkající se používání HDInsight s Data Lake Store:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Store jako výchozí úložiště je k dispozici pro HDInsight verze 3.5 a 3.6.

* Možnost vytvořit HDInsight clustery s přístupem do Data Lake Store jako výchozí úložiště je *není k dispozici* u clusterů HDInsight Premium.

Ke konfiguraci HDInsight pro práci s Data Lake Store pomocí prostředí PowerShell, postupujte podle pokynů v následujících pět částech.

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, ujistěte se, že splňujete následující požadavky:

* **Předplatné Azure**: přejděte na [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Prostředí Azure PowerShell 1.0 nebo vyšší**: najdete v části [postup instalace a konfigurace prostředí PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Chcete-li nainstalovat sadu Windows SDK, přejděte na [stáhne a nástroje pro Windows 10](https://dev.windows.com/downloads). Sada SDK se používá k vytvoření certifikát zabezpečení.
* **Objekt služby Azure Active Directory**: Tento kurz popisuje, jak vytvořit objekt služby v Azure Active Directory (Azure AD). Pokud chcete vytvořit objekt služby, ale musí být správce Azure AD. Pokud jste správce, můžete přeskočit tento požadavek a pokračujte v tomto kurzu.

    >[!NOTE]
    >Pouze v případě, že jste správce Azure AD, vytvořte službu objektu zabezpečení. Správce služby Azure AD musí vytvořte službu objektu zabezpečení před vytvořením clusteru HDInsight s Data Lake Store. Objekt služby musí být vytvořeny pomocí certifikátu, jak je popsáno v [vytvořit objekt služby pomocí certifikátu](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Vytvoření účtu Data Lake Store

Pokud chcete vytvořit účet Data Lake Store, postupujte takto:

1. Z plochy otevřete okno prostředí PowerShell a potom zadejte níže zobrazené fragmenty kódu. Když se zobrazí výzva k přihlášení, přihlaste se jako správci předplatného nebo vlastníky. 

        # Sign in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Pokud registrace zprostředkovatele prostředků Data Lake Store a zobrazí se chyba podobná `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, vaše předplatné nemusí být seznam povolených adres pro Data Lake Store. Aktivujte předplatné Azure pro verzi public preview Data Lake Store, postupujte podle pokynů v [Začínáme s Azure Data Lake Store pomocí portálu Azure](data-lake-store-get-started-portal.md).
    >

2. Účet Data Lake Store je přidruženo ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měli byste vidět výstup takto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvoření účtu Data Lake Store. Název účtu, který zadáte, musí obsahovat jenom malá písmena a číslice.

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

4. Pomocí Data Lake Store jako výchozí úložiště, musíte určit kořenovou cestu, do které soubory specifických pro cluster se zkopírují při vytváření clusteru. Chcete-li vytvořit kořenovou cestu, která je **/clustery/hdiadlcluster** v tomto fragmentu kódu pomocí následující rutiny:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Nastavení ověřování pro přístup na základě rolí k Data Lake Store
Každé předplatné služby Azure souvisí s entitou, Azure AD. Uživatelů a služeb, která přistupují k prostředkům předplatné pomocí portálu Azure nebo rozhraní API služby Azure Resource Manager, musí nejprve ověřit pomocí služby Azure AD. Udělením přístupu k předplatných Azure a služby přiřazením příslušné role na prostředek služby Azure. Objekt služby pro služby, identifikuje služby ve službě Azure AD.

Tato část ukazuje postup udělení služby aplikace, jako je například HDInsight, přístup k prostředku Azure (účet Data Lake Store, který jste vytvořili dříve). To uděláte tak, že vytvoříte hlavní aplikace a přiřazení rolí k němu pomocí prostředí PowerShell služby.

Nastavení ověřování služby Active Directory pro Azure Data Lake, proveďte kroky v následujících dvou částech.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Zajistěte, aby byla [Windows SDK](https://dev.windows.com/en-us/downloads) nainstalovat před provedením kroků v této části. Musíte také vytvořit adresář, jako například *C:\mycertdir*, kde můžete vytvořit certifikát.

1. V okně PowerShell přejděte do umístění, kam jste nainstalovali Windows SDK (obvykle *C:\Program Files (x86) \Windows Kits\10\bin\x86*) a použít [MakeCert] [ makecert] nástroj vytvořit certifikát podepsaný svým držitelem a privátní klíč. Použijte následující příkazy:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Zobrazí se výzva k zadání heslo soukromého klíče. Po provedení příkazu je úspěšně, měli byste vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikát, který jste zadali.
2. Použití [Pvk2Pfx] [ pvk2pfx] nástroj pro převod pvk a .cer soubory, které vytvořili MakeCert do souboru .pfx. Spusťte následující příkaz:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte heslo soukromého klíče, který jste dřív zadali. Hodnota zadaná **-SP** parametr je heslo, které je přidružené k souboru .pfx. Po příkazu byla úspěšně dokončena, měli byste taky vidět **CertFile.pfx** v adresáři certifikát, který jste zadali.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Vytvoření Azure AD a instanční objekt
V této části vytvořit objekt služby pro aplikaci Azure AD, přiřazení role objekt služby a ověření jako objekt služby tím, že poskytuje certifikát. Pokud chcete vytvořit aplikaci ve službě Azure AD, spusťte následující příkazy:

1. Vložte následující rutiny v okně konzoly prostředí PowerShell. Ujistěte se, že hodnota zadáváte pro **– DisplayName** vlastnost je jedinečný. Hodnoty pro **– Domovská stránka** a **- IdentiferUris** jsou zástupné hodnoty a nejsou ověřené.

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
3. Udělte přístup k hlavní službě Data Lake Store kořenové a všechny složky v kořenové cestě, která jste zadali dříve. Pomocí následující rutiny:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Vytvoření clusteru služby HDInsight Linux s Data Lake Store jako výchozí úložiště

V této části vytvoříte clusteru služby HDInsight Hadoop Linux s Data Lake Store jako výchozí úložiště. Pro tuto verzi clusteru HDInsight a Data Lake Store musí být ve stejném umístění.

1. Načtení ID předplatného klienta a uloží jej pro pozdější použití.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Vytvoření clusteru HDInsight pomocí následující rutiny:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
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

    Po úspěšném dokončení rutinu byste měli vidět výstup, který uvádí podrobnosti o clusteru.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Spuštění testu úloh na clusteru HDInsight pomocí Data Lake Store
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit testovací úlohy na něm zajistit, že Data Lake Store můžete získat přístup. Uděláte to tak, spusťte ukázkové úlohy Hive a vytvořte tabulku, která používá ukázková data, která je již k dispozici v Data Lake Store v  *<cluster root>/example/data/sample.log*.

V této části vytvoříte připojení Secure Shell (SSH) do clusteru HDInsight Linux, který jste vytvořili, a spusťte ukázkový dotaz Hive.

* Pokud používáte klienta se systémem Windows k vytvoření připojení SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linux vytvoření připojení SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po provedení připojení, spusťte rozhraní příkazového řádku (CLI) Hive pomocí následujícího příkazu:

        hive
2. Pomocí rozhraní příkazového řádku zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Měli byste vidět výstup dotazu v konzole SSH.

    >[!NOTE]
    >Cesta k vzorovými daty v předchozím příkazu CREATE TABLE je `adl:///example/data/`, kde `adl:///` je kořenový cluster. Následující příklad kořenu clusteru, který je uveden v tomto kurzu příkaz je `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Můžete buď použít kratší alternativní nebo zadejte úplnou cestu k kořenovém clusteru.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Přístup k Data Lake Store pomocí příkazů HDFS
Po dokončení konfigurace clusteru HDInsight pomocí Data Lake Store, můžete přístup k obchodu s příkazy prostředí Hadoop Distributed File System (HDFS).

V této části provedete připojení SSH do clusteru HDInsight Linux, který jste vytvořili a pak spusťte příkazy HDFS.

* Pokud používáte klienta se systémem Windows k vytvoření připojení SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linux vytvoření připojení SSH do clusteru, přečtěte si téma [použití SSH se systémem Linux Hadoop v HDInsight ze systému Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po provedení připojení, seznam souborů v Data Lake Store pomocí následujícího příkazu systému souborů HDFS.

    hdfs dfs -ls adl:///

Můžete také `hdfs dfs -put` příkazu do Data Lake Store nahrát některé soubory a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrál.

## <a name="see-also"></a>Další informace najdete v tématech
* [Použití Data Lake Store s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál Azure: vytvoření clusteru HDInsight používat Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
