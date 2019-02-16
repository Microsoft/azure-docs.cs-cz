---
title: Použití Data Lake Storage Gen1 se systémem Hadoop v Azure HDInsight
description: Zjistěte, jak k dotazování dat ze služby Azure Data Lake Storage Gen1 a k ukládání výsledků analýzy.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 9dabf3fd37c591525625969ac0af1873477b84d6
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311410"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Pomocí Data Lake Storage Gen1 s využitím clusterů Azure HDInsight

> [!Note] 
> Nasazení nových clusterů HDInsight pomocí [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) pro vylepšení výkonu a nové funkce.

Pokud chcete analyzovat data v clusteru HDInsight, můžete můžete ukládat buď v [služby Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) nebo [2. generace úložiště Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md). Všechny možnosti ukládání umožňují bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

V tomto článku se dozvíte, jak služba Data Lake Storage Gen1 pracuje s clustery HDInsight. Informace o tom, jak s clustery HDInsight pracuje služba Azure Storage, najdete v tématu [Použití služby Azure Storage s clustery Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Další informace o vytvoření clusteru HDInsight najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 se vždy přistupuje prostřednictvím zabezpečeného kanálu, takže není žádná `adls` název schématu systému souborů. Vždy používáte `adl`.


## <a name="availability-for-hdinsight-clusters"></a>Dostupnosti pro clustery HDInsight

Apache Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight, můžete ve službě Azure Storage jako výchozí systém souborů zadat kontejner objektů blob, nebo s HDInsight 3.5 a novějších verzí, můžete vybrat služby Azure Storage nebo Azure Data Lake Storage Gen1 jako výchozí systém souborů několik výjimek. 

Clustery HDInsight můžete použít Data Lake Storage Gen1 dvěma způsoby:

* Jako výchozí úložiště.
* Jako další úložiště, přičemž Azure Storage Blob je výchozí úložiště.

V současné době pouze některé HDInsight clusterů podporu typy a verze pomocí Data Lake Storage Gen1 jako výchozího úložiště a dalších účtů úložiště:

| Typ clusteru HDInsight | Data Lake Storage Gen1 jako výchozí úložiště | Data Lake Storage Gen1 jako další úložiště| Poznámky |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight verze 3.6 | Ano | Ano | S výjimkou HBase|
| HDInsight verze 3.5 | Ano | Ano | S výjimkou HBase|
| HDInsight verze 3.4 | Ne | Ano | |
| HDInsight verze 3.3 | Ne | Ne | |
| HDInsight verze 3.2 | Ne | Ano | |
| Storm | | |Data Lake Storage Gen1 můžete použít k zápisu dat z topologie Storm. Data Lake Storage můžete také použít pro referenční data, která pak může číst topologie Storm.|

> [!WARNING]  
> HDInsight HBase se nepodporuje s Azure Data Lake Storage Gen1

Pomocí Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon ani schopnost číst nebo zapisovat do služby Azure storage z clusteru.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Použít jako výchozího úložiště Data Lake Storage Gen1

Při nasazení HDInsight s Data Lake Storage Gen1 jako výchozím úložištěm, soubory související s clusterem ukládají v následujícím umístění:

    adl://mydatalakestore/<cluster_root_path>/

kde `<cluster_root_path>` je název složky, kterou vytvoříte ve službě Data Lake Storage. Tak, že zadáte kořenovou cestu pro každý cluster, můžete použít stejný účet Data Lake Storage pro více než jeden cluster. Takže máte nastavení, kde:

* Cluster1 může používat cestu `adl://mydatalakestore/cluster1storage`.
* Cluster2 může používat cestu `adl://mydatalakestore/cluster2storage`.

Všimněte si, že oba clustery používají stejný účet Data Lake Storage Gen1 **mydatalakestore**. Každý cluster má přístup k vlastnímu kořenovému systému souborů ve službě Data Lake Storage. Prostředí nasazení na webu Azure Portal vás zvláště vyzývá, abyste pro kořenovou cestu používali název složky ve formátu například **/clustery/\<název_clusteru>**.

Aby bylo možné používat jako výchozího úložiště Data Lake Storage Gen1, je nutné udělit přístup k hlavní službě k následujícím cestám:

- Kořenový adresář účtu Data Lake Storage Gen1.  Například: adl://mydatalakestore/.
- Složka pro všechny složky clusteru.  Například: adl://mydatalakestore/clusters.
- Složka pro cluster.  Například: adl://mydatalakestore/clusters/cluster1storage.

Další informace o vytvoření instančního objektu a udělení přístupu služby, najdete v části Konfigurace Data Lake Storage přístup.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahování certifikátu z Azure Key Vaultu pro použití při vytváření clusteru

Pokud chcete nastavit jako výchozí úložiště pro nový cluster Azure Data Lake Storage Gen1 a certifikát pro váš objekt služby je uložený ve službě Azure Key Vault, existuje pár další kroky potřebné k převedení certifikát správný formát. Následující fragmenty kódu ukazují, jak k provedení převodu.

Nejprve stáhněte certifikát ze služby Key Vault a extrahování `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

V dalším kroku převést `SecretValueText` k certifikátu.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Můžete použít `$identityCertificate` nasadit nový cluster, stejně jako v následujícím fragmentu kódu:

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Použití Data Lake Storage Gen1 jako další úložiště

Data Lake Storage Gen1 můžete použít jako další úložiště pro cluster také. V takových případech výchozím úložištěm clusteru může být účet Data Lake Storage nebo Azure Storage Blob. Pokud spouštíte úlohy HDInsight s daty uloženými ve službě Data Lake Storage jako další úložiště, musíte použít plně kvalifikovanou cestu k souborům. Příklad:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Všimněte si, že teď v adrese URL není **cluster_root_path**. Důvodem je skutečnost, že Data Lake Storage není výchozím úložištěm v tomto případě všechno, co je potřeba je zadat cestu k souborům.

Aby bylo možné použít Data Lake Storage Gen1 jako další úložiště, stačí k udělení přístupu instančního objektu služby k cestám, kde jsou uloženy soubory.  Příklad:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Další informace o vytvoření instančního objektu a udělení přístupu služby, najdete v části Konfigurace Data Lake Storage přístup.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Použití více účtů Data Lake Storage

Přidání účtu Data Lake Storage jako další a přidávání více než jeden Data Lake Storage účty se provést tak, že udělíte oprávnění ke clusteru HDInsight s daty v jedné nebo více účtů Data Lake Storage. Přístup k úložišti Data Lake najdete v tématu Konfigurace.

## <a name="configure-data-lake-storage-access"></a>Konfigurace přístupu v Data Lake Storage

Konfigurace přístupu k Data Lake Storage z vašeho clusteru HDInsight, musíte mít službu instančního objektu Azure Active directory (Azure AD). Instanční objekt může vytvořit pouze správce Azure AD. Instanční objekt musí být vytvořený s certifikátem. Další informace najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), a [vytvoření instančního objektu s certifikátem samoobslužné podepsaným](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Pokud se chystáte použít Azure Data Lake Storage Gen1 jako další úložiště pro HDInsight cluster, důrazně doporučujeme, abyste to udělali při vytváření clusteru, jak je popsáno v tomto článku. Přidání Azure Data Lake Storage Gen1 jako další úložiště do existujícího clusteru HDInsight není podporovaný scénář.
>

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům ve službě Data Lake Storage z clusteru služby HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Pomocí zkráceného formátu cesty**. S tímto přístupem nahradíte část cesty až ke kořenu clusteru za adl:///. Takže ve výše uvedeném příkladu můžete nahradit `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` za `adl:///`.

        adl:///<file path>

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup. Například pokud je úplná cesta k souboru následující:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Ke stejnému souboru sample.log můžete přistupovat pomocí této relativní cesty:

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1

Podrobné pokyny o tom, jak vytvářet clustery HDInsight s přístupem k Data Lake Storage Gen1 pomocí následujících odkazů.

* [Pomocí portálu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Pomocí Powershellu (se službou Data Lake Storage Gen1 jako výchozím úložištěm)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Pomocí Powershellu (se službou Data Lake Storage Gen1 jako další úložiště)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Pomocí šablon Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aktualizovat certifikát HDInsight pro přístup k Data Lake Storage Gen1

Následující příklad kódu Powershellu načte certifikát z místního souboru nebo služby Azure Key Vault a aktualizuje váš cluster HDInsight pomocí nového certifikátu pro přístup k Azure Data Lake Storage Gen1. Zadejte vlastní název clusteru HDInsight, název skupiny prostředků, ID předplatného, ID aplikace, místní cesta k certifikátu. Zadejte heslo po zobrazení výzvy.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat HDFS kompatibilní s Azure Data Lake Gen1 Storage s HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Rychlé zprovoznění: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1 pomocí Azure Powershellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Apache Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Apache Pig s HDInsight][hdinsight-use-pig]
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
