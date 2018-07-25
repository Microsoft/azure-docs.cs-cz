---
title: Použití Data Lake Store se systémem Hadoop v Azure HDInsight
description: Zjistěte, jak zadávat dotazy na data ze služby Azure Data Lake Store a ukládat výsledky analýzy.
services: hdinsight,storage
tags: azure-portal
author: mumian
ms.author: jgao
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 8f81e04c1b80173868f068957b6ca7da6bfe19c1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222885"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Použití služby Data Lake Store s clustery Azure HDInsight

Pokud chcete analyzovat data v clusteru HDInsight, můžete je ukládat ve službě [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) nebo v obou. Obě možnosti ukládání umožňují bezpečné odstranění clusterů HDInsight, které se používají pro výpočty, aniž by se ztratila uživatelská data.

V tomto článku se dozvíte, jak služba Data Lake Store pracuje s clustery HDInsight. Informace o tom, jak s clustery HDInsight pracuje služba Azure Storage, najdete v tématu [Použití služby Azure Storage s clustery Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Další informace o vytvoření clusteru HDInsight najdete v tématu [Vytváření clusterů Hadoop ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> Ke službě Data Lake Store se vždy přistupuje prostřednictvím zabezpečeného kanálu, takže se nepoužívá název schématu systému souborů `adls`. Vždy používáte `adl`.
> 
> 

## <a name="availability-for-hdinsight-clusters"></a>Dostupnosti pro clustery HDInsight

Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete jako výchozí systém souborů zadat kontejner objektů blob ve službě Azure Storage. U služby HDInsight 3.5 a novější můžete až na některé výjimky vybrat jako výchozí systém souborů službu Azure Storage nebo Azure Data Lake Store. 

Clustery HDInsight můžou službu Data Lake Store využívat dvěma způsoby:

* Jako výchozí úložiště.
* Jako další úložiště, přičemž Azure Storage Blob je výchozí úložiště.

V současné době podporují použití služby Data Lake Store jako výchozího úložiště a dalších účtů úložiště pouze některé typy a verze clusterů HDInsight:

| Typ clusteru HDInsight | Data Lake Store jako výchozí úložiště | Data Lake Store jako další úložiště| Poznámky |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight verze 3.6 | Ano | Ano | |
| HDInsight verze 3.5 | Ano | Ano | S výjimkou HBase|
| HDInsight verze 3.4 | Ne | Ano | |
| HDInsight verze 3.3 | Ne | Ne | |
| HDInsight verze 3.2 | Ne | Ano | |
| Storm | | |Data Lake Store můžete použít k zápisu dat z topologie Storm. Data Lake Store můžete také použít pro referenční data, která pak může číst topologie Storm.|

Použití služby Data Lake Store jako dalšího účtu úložiště nemá vliv na výkon ani schopnost číst ze služby Azure Storage nebo do ní zapisovat z clusteru.


## <a name="use-data-lake-store-as-default-storage"></a>Použití služby Data Lake Store jako výchozího úložiště

Když je služba HDInsight nasazená se službou Data Lake Store jako výchozím úložištěm, soubory související s clusterem se ukládají do služby Data Lake Store v tomto umístění:

    adl://mydatalakestore/<cluster_root_path>/

kde `<cluster_root_path>` je název složky, kterou vytvoříte ve službě Data Lake Store. Pokud pro každý cluster zadáte kořenovou cestu, můžete stejný účet Data Lake Store použít pro více než jeden cluster. Takže máte nastavení, kde:

* Cluster1 může používat cestu `adl://mydatalakestore/cluster1storage`.
* Cluster2 může používat cestu `adl://mydatalakestore/cluster2storage`.

Všimněte si, že oba clustery používají stejný účet Data Lake Store **mydatalakestore**. Každý cluster má přístup k vlastnímu kořenovému systému souborů ve službě Data Lake Store. Prostředí nasazení na webu Azure Portal vás zvláště vyzývá, abyste pro kořenovou cestu používali název složky ve formátu například **/clustery/\<název_clusteru>**.

Pokud chcete službu Data Lake Store použít jako výchozí úložiště, musíte instančnímu objektu udělit přístup k následujícím cestám:

- Kořenový adresář účtu Data Lake Store.  Například: adl://mydatalakestore/.
- Složka pro všechny složky clusteru.  Například: adl://mydatalakestore/clusters.
- Složka pro cluster.  Například: adl://mydatalakestore/clusters/cluster1storage.

Další informace o vytvoření instančního objektu a udělení přístupu najdete v části [Konfigurace přístupu ke službě Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Použití služby Data Lake Store jako dalšího úložiště

Službu Data Lake Store můžete také použít jako další úložiště. Výchozím úložištěm clusteru v takových případech může být účet Data Lake Store nebo Azure Storage Blob. Pokud spouštíte úlohy HDInsight s daty uloženými ve službě Data Lake Store jako dalším úložišti, musíte použít plně kvalifikovanou cestu k souborům. Příklad:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Všimněte si, že teď v adrese URL není **cluster_root_path**. Je to proto, že služba Data Lake Store v tomto případě není výchozím úložištěm, takže stačí zadat pouze cestu k souborům.

Pokud chcete použít službu Data Lake Store jako další úložiště, stačí instančnímu objektu udělit přístup pouze k cestám, kde jsou vaše soubory uloženy.  Příklad:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Další informace o vytvoření instančního objektu a udělení přístupu najdete v části [Konfigurace přístupu ke službě Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Použití více účtů Data Lake Store

Přidání účtu Data Lake Store jako dalšího úložiště a přidání více účtů Data Lake Store můžete provést tak, že clusteru HDInsight udělíte oprávnění k datům v jednom nebo několika účtech Data Lake Store. Viz [Konfigurace přístupu ke službě Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurace přístupu ke službě Data Lake Store

Pokud chcete nakonfigurovat přístup ke službě Data Lake Store z clusteru HDInsight, musíte mít instanční objekt Azure Active Directory (Azure AD). Instanční objekt může vytvořit pouze správce Azure AD. Instanční objekt musí být vytvořený s certifikátem. Další informace najdete v tématu s [rychlým startem pro nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) a v části tématu věnované [vytváření instančních objektů s certifikátem podepsaným jeho držitelem](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Pokud plánujete službu Azure Data Lake Store používat jako další úložiště pro cluster HDInsight, důrazně doporučujeme to provést během vytváření clusteru, jak je popsáno v tomto článku. Přidání služby Azure Data Lake Store jako dalšího úložiště do existujícího clusteru HDInsight se nepodporuje.
>

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům ve službě Data Lake Store z clusteru HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Pomocí zkráceného formátu cesty**. S tímto přístupem nahradíte část cesty až ke kořenu clusteru za adl:///. Takže ve výše uvedeném příkladu můžete nahradit `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` za `adl:///`.

        adl:///<file path>

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup. Například pokud je úplná cesta k souboru následující:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Ke stejnému souboru sample.log můžete přistupovat pomocí této relativní cesty:

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Vytváření clusterů HDInsight s přístupem ke službě Data Lake Store

Na následujících odkazech najdete podrobné pokyny k vytvoření clusterů HDInsight s přístupem ke službě Data Lake Store.

* [Pomocí portálu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Pomocí PowerShellu (se službou Data Lake Store jako výchozím úložištěm)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Pomocí PowerShellu (se službou Data Lake Store jako dalším úložištěm)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Pomocí šablon Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Aktualizovat certifikát HDInsight pro přístup k Data Lake Store

Následující příklad kódu Powershellu načte do místního souboru certifikátu a aktualizuje svůj cluster HDInsight pomocí nového certifikátu pro přístup k Azure Data Lake Store. Zadejte vlastní název clusteru HDInsight, název skupiny prostředků, ID předplatného, ID aplikace, místní cesta k certifikátu. Zadejte heslo po zobrazení výzvy.

```powershell-interactive
$clusterName = 'MyCluster'
$resourceGroupName = 'MyResourceGroup'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId
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
V tomto článku jste zjistili, jak používat HDFS kompatibilní službu Azure Data Lake Store se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Rychlý start: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Vytvoření clusteru HDInsight pro použití služby Data Lake Store pomocí Azure PowerShellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
