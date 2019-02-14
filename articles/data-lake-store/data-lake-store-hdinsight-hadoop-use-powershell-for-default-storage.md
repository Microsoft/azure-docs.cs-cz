---
title: Vytvoření clusterů HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště pomocí prostředí PowerShell | Dokumentace Microsoftu
description: Pomocí prostředí Azure PowerShell k vytvoření a použití clusterů HDInsight s Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 84bed7031307316545cc8e468196c6b12cde7bb7
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237057"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Vytvoření clusterů HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Použití portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Resource Manageru](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Zjistěte, jak pomocí Azure Powershellu ke konfiguraci clustery Azure HDInsight s Azure Data Lake Storage Gen1, jako výchozí úložiště. Pokyny týkající se vytvoření clusteru služby HDInsight s Data Lake Storage Gen1 jako další úložiště najdete v tématu [vytvoření clusteru HDInsight s Data Lake Storage Gen1 jako další úložiště](data-lake-store-hdinsight-hadoop-use-powershell.md).

Tady jsou některé důležité informace týkající se použití HDInsight s Data Lake Storage Gen1:

* Možnost k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1 jako výchozím úložištěm je dostupná pro HDInsight verze 3.5 a 3.6.

* Možnost vytvořit HDInsight clustery s přístupem k Data Lake Storage Gen1 jako výchozí úložiště je *není k dispozici* u clusterů HDInsight Premium.

Konfigurace HDInsight pro práci s Data Lake Storage Gen1 pomocí prostředí PowerShell, postupujte podle pokynů v následujících pět oddíly.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Než začnete tento kurz, ujistěte se, že splňujete následující požadavky:

* **Předplatné Azure**: Přejděte na [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Prostředí Azure PowerShell 1.0 nebo vyšší**: Zobrazit [instalace a konfigurace Powershellu](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Chcete-li nainstalovat sadu Windows SDK, přejděte na [soubory ke stažení a nástroje pro Windows 10](https://dev.windows.com/downloads). Sada SDK se používá k vytvoření certifikátu zabezpečení.
* **Instanční objekt Azure Active Directory**: Tento kurz popisuje, jak vytvořit instanční objekt v Azure Active Directory (Azure AD). K vytvoření instančního objektu, ale musí být správce Azure AD. Pokud jste správce, můžete tuto požadovanou součást přeskočit a pokračovat v tomto kurzu.

    >[!NOTE]
    >Služby můžete vytvořit instanční objekt, pouze v případě, že jste správce Azure AD. Správce Azure AD musíte vytvořit instanční objekt před vytvořením clusteru služby HDInsight s Data Lake Storage Gen1. Instanční objekt musí být vytvořeny pomocí certifikátu, jak je popsáno v [vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

Vytvoření účtu Data Lake Storage Gen1, postupujte takto:

1. Z plochy otevřete okno Powershellu a pak zadejte níže zobrazené fragmenty kódu. Po zobrazení výzvy k přihlášení, přihlaste se jako správci předplatného nebo vlastníci. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Pokud se registrace poskytovatele prostředků Data Lake Storage Gen1 a zobrazí se chyba podobná `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, vaše předplatné nemusí být uvedeny v seznamu povolených pro Data Lake Storage Gen1. Aby vaše předplatné Azure pro Data Lake Storage Gen1, postupujte podle pokynů v [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md).
    >

2. Účet Data Lake Storage Gen1 je přidružené ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Měli byste vidět výstup podobný tomuto:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Vytvoření účtu Data Lake Storage Gen1. Název účtu, který zadáte, musí obsahovat jenom malá písmena a číslice.

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

4. Pomocí Data Lake Storage Gen1 jako výchozím úložištěm, musíte určit kořenovou cestu, ke které se zkopírují soubory specifických pro cluster při vytváření clusteru. Chcete-li vytvořit kořenovou cestu, která je **/clustery/hdiadlcluster** v tomto fragmentu kódu pomocí následující rutiny:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Nastavení ověřování pro přístup na základě rolí k Data Lake Storage Gen1
Každé předplatné služby Azure souvisí s převodem entity služby Azure AD. S Azure AD musí nejdřív ověřit uživatele a služby, které přístup k prostředkům předplatného s využitím webu Azure portal nebo rozhraní API Azure Resource Manageru. Udělením přístupu k předplatným Azure a služby přiřazením příslušné role v prostředku Azure. Pro služby identifikuje instančního objektu služby ve službě Azure AD.

Tato část ukazuje, jak udělit aplikační služba, jako je HDInsight, přístup k prostředku Azure (účet Data Lake Storage Gen1, který jste vytvořili dříve). To uděláte tak, že vytvoříte službu objektu zabezpečení pro aplikace a přiřazení rolí k němu přes PowerShell.

Pokud chcete nastavit ověřování služby Active Directory pro Data Lake Storage Gen1, proveďte úlohy v následujících dvou částech.

### <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
Ujistěte se, že máte [sady Windows SDK](https://dev.windows.com/en-us/downloads) nainstalované před provedením kroků v této části. Musíte mít také vytvořený adresář, jako například *C:\mycertdir*, kde můžete vytvořit certifikát.

1. V okně Powershellu přejděte do umístění, kam jste nainstalovali sadu Windows SDK (obvykle *C:\Program Files (x86) \Windows Kits\10\bin\x86*) a použít [MakeCert] [ makecert] Nástroj k vytvoření certifikátu podepsaného svým držitelem a privátní klíč. Pomocí následujících příkazů:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Jste vyzváni k zadání hesla privátního klíče. Po úspěšném spuštění příkazu byste měli vidět **CertFile.cer** a **mykey.pvk** v adresáři certifikát, který jste zadali.
2. Použití [Pvk2Pfx] [ pvk2pfx] nástroj pro převod souborů .pvk a .cer, vytvořené pomocí nástroje MakeCert do souboru .pfx. Spusťte následující příkaz:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Po zobrazení výzvy zadejte heslo soukromého klíče, který jste zadali dříve. Hodnota zadaná **-No** parametr je heslo, který je spojen se souborem .pfx. Po příkazu byla úspěšně dokončena, měli byste taky vidět **CertFile.pfx** v adresáři certifikát, který jste zadali.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Vytvoření Azure AD a instanční objekt služby
V této části se vytvořit instanční objekt pro aplikaci Azure AD, přiřadit roli instančnímu objektu služby a ověřit jako instanční objekt služby tím, že poskytuje certifikát. Vytvoření aplikace ve službě Azure AD, spusťte následující příkazy:

1. Vložte následující rutiny v okně konzoly Powershellu. Ujistěte se, že hodnotu určenou pro **- DisplayName** vlastnost je jedinečný. Hodnoty pro **– Domovská stránka** a **- IdentiferUris** zástupné hodnoty jsou a nejsou ověřeny.

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
2. Vytvoření instančního objektu pomocí ID aplikace.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Udělení přístupu instančního objektu služby na kořenovém adresáři Data Lake Storage Gen1 a všechny složky v kořenové cestě, která jste zadali dříve. Pomocí následujících rutin:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Vytvoření clusteru HDInsight s Linuxem s Data Lake Storage Gen1 jako výchozí úložiště

V této části vytvoříte cluster HDInsight Hadoop Linux s Data Lake Storage Gen1 jako výchozí úložiště. Pro tuto verzi clusteru HDInsight a Data Lake Storage Gen1 musí být ve stejném umístění.

1. Načtení ID tenanta předplatného a uložte ho pro pozdější použití.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Vytvoření clusteru HDInsight pomocí následující rutiny:

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

    Po úspěšném dokončení rutina byste měli vidět výstup, který obsahuje podrobnosti o clusteru.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Spouštění testů úloh na clusteru HDInsight pro použití služby Data Lake Storage Gen1
Po dokončení konfigurace clusteru služby HDInsight, můžete spustit na ní zajistit, aby měl přístup k Data Lake Storage Gen1 testovací úlohy. Uděláte to tak, spuštění ukázkové úlohy Hive vytvořit tabulku, která se používá ukázková data, která je již k dispozici v Data Lake Storage Gen1 na  *<cluster root>/example/data/sample.log*.

V této části vytvoříte připojení Secure Shell (SSH) do clusteru HDInsight Linux, kterou jste vytvořili a potom spusťte ukázkový dotaz Hive.

* Vytvořte připojení SSH do clusteru pomocí klienta Windows, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Vytvořte připojení SSH do clusteru pomocí klienta systému Linux, najdete v článku [použití SSH se systémem Linux Hadoop v HDInsight z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po provedení připojení pomocí následujícího příkazu spusťte Hive rozhraní příkazového řádku (CLI):

        hive
2. Pomocí rozhraní příkazového řádku zadejte následující příkazy, čímž umožňuje vytvořit novou tabulku s názvem **vozidel** pomocí ukázkových dat v Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    V konzole pro SSH byste měli vidět výstup dotazu.

    >[!NOTE]
    >Cesta k vzorovými daty v předchozím příkazu CREATE TABLE je `adl:///example/data/`, kde `adl:///` je ke kořenu clusteru. Následující ukázka ke kořenu clusteru, který je zadán v tomto kurzu má příkaz tuto podobu `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Můžete buď použít kratší alternativa nebo zadat úplnou cestu ke kořenu clusteru.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Přístup k Data Lake Storage Gen1 pomocí příkazů HDFS
Po dokončení konfigurace clusteru HDInsight pro použití služby Data Lake Storage Gen1, můžete použít příkazy prostředí souboru systému HDFS (Hadoop Distributed) pro přístup k úložišti.

V této části vytvořte připojení SSH do clusteru HDInsight Linux, kterou jste vytvořili, a potom spustíte příkazy HDFS.

* Vytvořte připojení SSH do clusteru pomocí klienta Windows, naleznete v tématu [použití SSH se systémem Linux Hadoop v HDInsight z Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Vytvořte připojení SSH do clusteru pomocí klienta systému Linux, najdete v článku [použití SSH se systémem Linux Hadoop v HDInsight z Linuxu](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po provedení připojení, seznam souborů v Data Lake Storage Gen1 pomocí následujícího příkazu systému souboru HDFS.

    hdfs dfs -ls adl:///

Můžete také použít `hdfs dfs -put` příkazu některé soubory nahrát do Data Lake Storage Gen1 a pak použijte `hdfs dfs -ls` ověřit, zda soubory byly úspěšně nahrány.

## <a name="see-also"></a>Další informace najdete v tématech
* [Pomocí Data Lake Storage Gen1 s využitím clusterů Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure portal: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
