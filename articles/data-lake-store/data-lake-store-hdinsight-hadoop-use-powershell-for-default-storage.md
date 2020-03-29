---
title: Vytvoření clusterů HDInsight s Azure Data Lake Storage Gen1 jako výchozím úložištěm pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Použití Azure PowerShellu k vytváření a používání clusterů HDInsight s Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161416"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Vytváření clusterů HDInsight s Azure Data Lake Storage Gen1 jako výchozím úložištěm pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Použití portálu Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použít Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Zjistěte, jak pomocí Azure PowerShellu nakonfigurovat clustery Azure HDInsight pomocí Azure Data Lake Storage Gen1 jako výchozího úložiště. Pokyny k vytvoření clusteru HDInsight s porty Data Lake Storage Gen1 jako další úložiště najdete v [tématu Vytvoření clusteru HDInsight s datovým jezerem Storage Gen1 jako další úložiště](data-lake-store-hdinsight-hadoop-use-powershell.md).

Tady jsou některé důležité aspekty používání HDInsightu s datovým lakem Gen1:

* Možnost vytvářet clustery HDInsight s přístupem k úložišti Data Lake Storage Gen1 jako výchozí úložiště je k dispozici pro HDInsight verze 3.5 a 3.6.

* Možnost vytvářet clustery HDInsight s přístupem k úložišti Data Lake Storage Gen1 jako výchozí úložiště *není k dispozici* pro clustery HDInsight Premium.

Chcete-li nakonfigurovat HDInsight pro práci s Datových jezer Úložiště Gen1 pomocí PowerShellu, postupujte podle pokynů v následujících pěti částech.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Než začnete tento kurz, ujistěte se, že splňujete následující požadavky:

* **Předplatné Azure:** Přejděte na [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**: Přečtěte [si, jak nainstalovat a nakonfigurovat PowerShell](/powershell/azure/overview).
* **Sada Windows Software Development Kit (SDK):** Chcete-li nainstalovat sadu Windows SDK, přejděte ke [stažení a nástrojům pro Systém Windows 10](https://dev.windows.com/downloads). Sada SDK se používá k vytvoření certifikátu zabezpečení.
* **Hlavní povinný služby Azure Active Directory**: Tento kurz popisuje, jak vytvořit instanční objekt ve službě Azure Active Directory (Azure AD). Chcete-li však vytvořit instanční objekt, musíte být správcem služby Azure AD. Pokud jste správce, můžete tento předpoklad přeskočit a pokračovat v kurzu.

    >[!NOTE]
    >Instanční objekt můžete vytvořit pouze v případě, že jste správce azure ad. Váš správce Služby Azure AD musí vytvořit instanční objekt, než budete moct vytvořit cluster HDInsight s Date Lake Storage Gen1. Instanční objekt musí být vytvořen s certifikátem, jak je popsáno v [části Vytvořit instanční objekt s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

Chcete-li vytvořit účet Data Lake Storage Gen1, postupujte takto:

1. Na ploše otevřete okno PowerShellu a zadejte výstřižky níže. Až se zobrazí výzva k přihlášení, přihlaste se jako jeden ze správců nebo vlastníků předplatného. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Pokud zaregistrujete zprostředkovatele prostředků Data Lake Storage `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`Gen1 a zobrazí se chyba podobná , vaše předplatné nemusí být na seznamu povolených pro Data Lake Storage Gen1. Pokud chcete povolit předplatné Azure pro Data Lake Storage Gen1, postupujte podle pokynů v [části Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md).
    >

2. Účet Gen1 úložiště datového jezera je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měl by se zobrazit výstup podobný tomuto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvořte účet Data Lake Storage Gen1. Zadaný název účtu musí obsahovat pouze malá písmena a čísla.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Použití data lake storage gen1 jako výchozí úložiště vyžaduje zadání kořenové cesty, do které se zkopírují soubory specifické pro cluster během vytváření clusteru. Chcete-li vytvořit kořenovou cestu, která je **/clusters/hdiadlcluster** ve výstřižku, použijte následující rutiny:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup k úložišti Data Lake Storage Gen1 na základě rolí
Každé předplatné Azure je přidružené k entitě Azure AD. Uživatelé a služby, které přistupují k prostředkům předplatného pomocí portálu Azure nebo rozhraní API Azure Resource Manager, se musí nejprve ověřit pomocí Služby Azure AD. Přístup se uděluje předplatným a službám Azure přiřazením příslušné role na prostředek Azure. Pro služby instanční objekt identifikuje službu ve službě Azure AD.

Tato část ukazuje, jak udělit aplikační služby, jako je například HDInsight, přístup k prostředku Azure (účet Data Lake Storage Gen1, který jste vytvořili dříve). Uděláte to vytvořením instančního objektu pro aplikaci a přiřazením rolí k němu prostřednictvím prostředí PowerShell.

Chcete-li nastavit ověřování služby Active Directory pro program Data Lake Storage Gen1, proveďte úkoly v následujících dvou částech.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Než budete pokračovat v krocích v této části, ujistěte se, že máte nainstalovanou sadku [Windows SDK.](https://dev.windows.com/en-us/downloads) Musíte také vytvořit adresář, například *C:\mycertdir*, kde vytvoříte certifikát.

1. V okně prostředí PowerShell přejděte do umístění, kde jste nainstalovali sadu Windows SDK (obvykle *C:\Program Files (x86)\Windows Kit\10\bin\x86*) a pomocí nástroje [MakeCert][makecert] vytvořte certifikát podepsaný svým držitelem a soukromý klíč. Použijte následující příkazy:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Budete vyzváni k zadání hesla soukromého klíče. Po úspěšném spuštění příkazu byste měli vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikátu, který jste zadali.
2. Pomocí nástroje [Pvk2Pfx][pvk2pfx] převeďte soubory .pvk a CER, které makecert vytvořil, na soubor .pfx. Spusťte následující příkaz:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte heslo soukromého klíče, které jste zadali dříve. Hodnota, kterou zadáte pro parametr **-po,** je heslo přidružené k souboru .pfx. Po úspěšném dokončení příkazu byste měli také vidět **soubor CertFile.pfx** v adresáři certifikátu, který jste zadali.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Vytvoření azure a instančního objektu
V této části vytvoříte instanční objekt pro aplikaci Azure AD, přiřadíte roli instančnímu objektu a ověříte jako instanční objekt poskytnutím certifikátu. Pokud chcete vytvořit aplikaci ve službě Azure AD, spusťte následující příkazy:

1. Vložte následující rutiny do okna konzoly PowerShellu. Ujistěte se, že hodnota, kterou zadáte pro vlastnost **-DisplayName,** je jedinečná. Hodnoty pro **-HomePage** a **-IdentiferUris** jsou zástupné hodnoty a nejsou ověřeny.

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
3. Udělte instančnímu objektu přístup ke kořenovému adresáři Data Lake Storage Gen1 a všem složkám v kořenové cestě, které jste zadali dříve. Použijte následující rutiny:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Vytvoření clusteru HDInsight Linux s datem Lake Storage Gen1 jako výchozím úložištěm

V této části vytvoříte cluster HDInsight Hadoop Linux s datovým lakem Gen1 jako výchozím úložištěm. Pro tuto verzi musí být cluster HDInsight a Data Lake Storage Gen1 ve stejném umístění.

1. Načtěte ID klienta předplatného a uložte ho pro pozdější použití.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Vytvořte cluster HDInsight pomocí následujících rutin:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
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

    Po úspěšném dokončení rutiny by se měl zobrazit výstup, který obsahuje podrobnosti o clusteru.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spuštění testovacích úloh v clusteru HDInsight pro použití data lake storage gen1
Po konfiguraci clusteru HDInsight můžete spustit testovací úlohy na něm, abyste zajistili, že má přístup k datovému úložišti Data Lake Storage Gen1. Chcete-li tak učinit, spusťte ukázkovou úlohu Hive a vytvořte tabulku, která používá ukázková data, která jsou již k dispozici v souboru Data Lake Storage Gen1 v * \<kořenovém> clusteru/example/data/sample.log*.

V této části vytvoříte připojení zabezpečeného prostředí (SSH) do clusteru HDInsight Linux, který jste vytvořili, a pak spustíte ukázkový dotaz Hive.

* Pokud používáte klienta systému Windows k vytvoření připojení SSH do clusteru, přečtěte si informace [o použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linuxu k vytvoření připojení SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na HDInsightu z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po nastoání připojení spusťte rozhraní příkazového řádku Hive (CLI) pomocí následujícího příkazu:

        hive
2. Pomocí příkazového příkazu k zadání následujících příkazů vytvořte novou tabulku s názvem **vozidla** pomocí ukázkových dat v gen1 úložiště datového jezera:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Výstup dotazu by se měl zobrazit v konzole SSH.

    >[!NOTE]
    >Cesta k ukázkovým datům v předchozím `adl:///example/data/`příkazu `adl:///` CREATE TABLE je , kde je kořen clusteru. Následující příklad kořene clusteru, který je zadán v `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`tomto kurzu, příkaz je . Můžete použít kratší alternativu nebo poskytnout úplnou cestu ke kořenovému adresáři clusteru.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Přístup k datům Lake Storage Gen1 pomocí příkazů HDFS
Po nakonfigurování clusteru HDInsight pro použití data lake storage gen1, můžete použít Hadoop Distributed File System (HDFS) shell příkazy pro přístup k úložišti.

V této části vytvoříte připojení SSH do clusteru HDInsight Linux, který jste vytvořili, a pak spustíte příkazy HDFS.

* Pokud používáte klienta systému Windows k vytvoření připojení SSH do clusteru, přečtěte si informace [o použití SSH s Hadoopem založeným na Linuxu na webu HDInsight ze systému Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Pokud používáte klienta Linuxu k vytvoření připojení SSH do clusteru, přečtěte [si informace o použití SSH s Hadoopem založeným na Linuxu na HDInsightu z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po nastoání připojení uveďte soubory v programu Data Lake Storage Gen1 pomocí následujícího příkazu systému souborů HDFS.

    hdfs dfs -ls adl:///

Pomocí příkazu `hdfs dfs -put` můžete také odeslat některé soubory do zařízení `hdfs dfs -ls` Data Lake Storage Gen1 a potom je použít k ověření, zda byly soubory úspěšně odeslány.

## <a name="see-also"></a>Viz také
* [Použití data lake storage gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál Azure: Vytvoření clusteru HDInsight pro použití data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
