---
title: Použití Data Lake Storage Gen1 se systémem Hadoop ve službě Azure HDInsight
description: Naučte se, jak zadávat dotazy na data z Azure Data Lake Storage Gen1 a ukládat výsledky analýzy.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945418"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Použití Data Lake Storage Gen1 s clustery Azure HDInsight

> [!Note]
> Nasaďte nové clustery HDInsight pomocí [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) pro lepší výkon a nové funkce.

Pokud chcete analyzovat data v clusteru HDInsight, můžete data uložit v [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)nebo [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Všechny možnosti úložiště umožňují bezpečně odstraňovat clustery HDInsight, které se používají pro výpočty, aniž by došlo ke ztrátě uživatelských dat.

V tomto článku se dozvíte, jak Data Lake Storage Gen1 pracuje s clustery HDInsight. Informace o tom, jak Azure Blob Storage funguje s clustery HDInsight, najdete v tématu [použití úložiště objektů BLOB v Azure s clustery Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Další informace o vytvoření clusteru HDInsight najdete v tématu věnovaném [vytváření Apache Hadoop clusterů ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> K Data Lake Storage Gen1 je vždy přistupovaná přes zabezpečený kanál, takže není k dispozici žádný `adls` název schématu systému souborů. Vždy používáte `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Dostupnost pro clustery HDInsight

Apache Hadoop podporuje pojem výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight zadejte jako výchozí systém souborů kontejner objektů BLOB v Azure Storage. Nebo se službou HDInsight 3,5 a novějšími verzemi můžete vybrat buď službu Azure Blob Storage, nebo Azure Data Lake Storage Gen1 jako výchozí systém souborů s několika výjimkami. Účet úložiště a clusteru musí být uloženy ve stejné oblasti.

Clustery HDInsight můžou Data Lake Storage Gen1 použít dvěma způsoby:

* Jako výchozí úložiště.
* Jako úložiště objektů BLOB v Azure jako výchozí úložiště jako další úložiště.

V současné době pouze některé typy a verze clusteru HDInsight podporují jako výchozí úložiště a další účty úložiště Data Lake Storage Gen1.

| Typ clusteru HDInsight | Data Lake Storage Gen1 jako výchozí úložiště | Data Lake Storage Gen1 jako další úložiště| Poznámky |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight verze 4,0 | No | No |ADLS Gen1 není v HDInsight 4,0 podporovaná. |
| HDInsight verze 3.6 | Yes | Yes | S výjimkou HBA|
| HDInsight verze 3.5 | Yes | Yes | S výjimkou HBA|
| HDInsight verze 3.4 | No | Yes | |
| HDInsight verze 3.3 | No | No | |
| HDInsight verze 3.2 | No | Yes | |
| Storm | | |Pomocí Data Lake Storage Gen1 můžete zapisovat data z topologie s více podmnožinami. Data Lake Storage Gen1 můžete použít také pro referenční data, která lze následně číst pomocí topologie zaplavení.|

> [!WARNING]  
> Clustery HDInsight nejsou podporované Azure Data Lake Storage Gen1

Použití Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon. Nebo možnost číst nebo zapisovat do úložiště objektů BLOB v Azure z clusteru.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Použít Data Lake Storage Gen1 jako výchozí úložiště

Když je HDInsight nasazený s Data Lake Storage Gen1 jako výchozí úložiště, soubory související s clusterem se ukládají v `adl://mydatalakestore/<cluster_root_path>/` , kde `<cluster_root_path>` je název složky, kterou vytvoříte v Data Lake Storage. Pokud pro každý cluster zadáte kořenovou cestu, můžete stejný účet Data Lake Storage použít pro více než jeden cluster. Takže máte nastavení, kde:

* Cluster1 může používat cestu `adl://mydatalakestore/cluster1storage`.
* Cluster2 může používat cestu `adl://mydatalakestore/cluster2storage`.

Všimněte si, že oba clustery používají stejný účet Data Lake Storage Gen1 **mydatalakestore**. Každý cluster má přístup ke svému vlastnímu kořenovému systému souborů v Data Lake Storage. Prostředí Azure Portal Deployment vás vyzve k použití názvu složky, jako je **/Clusters/ \<clustername>** pro kořenovou cestu.

Pokud chcete jako výchozí úložiště použít Data Lake Storage Gen1, musíte instančnímu objektu udělit přístup k následujícím cestám:

* Kořen účtu Data Lake Storage Gen1.  Například: adl://mydatalakestore/.
* Složka pro všechny složky clusteru.  Například: adl://mydatalakestore/clusters.
* Složka pro cluster.  Například: adl://mydatalakestore/clusters/cluster1storage.

Další informace o vytváření instančního objektu a udělení přístupu najdete v tématu Konfigurace přístupu Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahuje se certifikát z trezoru klíčů Azure pro použití při vytváření clusteru.

Pokud je certifikát pro instanční objekt uložený v Azure Key Vault, musíte certifikát převést na správný formát. Následující fragmenty kódu ukazují, jak provést převod.

Nejdřív Stáhněte certifikát z Key Vault a rozbalte `SecretValueText` .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Dále převeďte `SecretValueText` na certifikát.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Pak můžete použít `$identityCertificate` k nasazení nového clusteru jako v následujícím fragmentu kódu:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Použití Data Lake Storage Gen1 jako dalšího úložiště

Data Lake Storage Gen1 můžete použít také jako další úložiště pro cluster. V takových případech může být výchozí úložiště clusteru Azure Blob Storage nebo účet Azure Data Lake Storage Gen1. Pokud spouštíte úlohy HDInsight s daty uloženými v Azure Data Lake Storage Gen1 jako další úložiště, použijte plně kvalifikovanou cestu. Například:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

V adrese URL teď není **cluster_root_path** . To je proto, že v tomto případě Data Lake Storage není výchozí úložiště. Stačí pouze zadat cestu k souborům.

Pokud chcete použít Data Lake Storage Gen1 jako další úložiště, udělte instančnímu objektu přístup k cestám, kde jsou vaše soubory uložené.  Například:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Další informace o vytváření instančního objektu a udělení přístupu najdete v tématu Konfigurace přístupu Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Použít více než jeden Data Lake Storage Gen1 účet

Přidání účtu Data Lake Storage jako dalšího a přidání více účtů Data Lake Storage lze provést. Udělte clusteru HDInsight oprávnění k datům v jednom nebo více účtech Data Lake Storage. Další informace najdete v tématu Konfigurace přístupu Data Lake Storage Gen1 níže.

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurace přístupu Data Lake Storage Gen1

Pokud chcete nakonfigurovat přístup Azure Data Lake Storage Gen1 z vašeho clusteru HDInsight, musíte mít instanční objekt služby Azure Active Directory (Azure AD). Instanční objekt může vytvořit pouze správce Azure AD. Instanční objekt musí být vytvořený s certifikátem. Další informace najdete v tématu s [rychlým startem pro nastavení clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) a v části tématu věnované [vytváření instančních objektů s certifikátem podepsaným jeho držitelem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Pokud budete používat Azure Data Lake Storage Gen1 jako další úložiště pro cluster HDInsight, důrazně doporučujeme, abyste to provedli během vytváření clusteru, jak je popsáno v tomto článku. Přidání Azure Data Lake Storage Gen1 jako dalšího úložiště do stávajícího clusteru HDInsight není podporovaným scénářem.

Další informace o modelu řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům v Data Lake Storage z clusteru HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenu clusteru:

    ```
    adl:///<file path>
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založené na [připojení SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. V příkladech se používají všechna tři schémata identifikátoru URI. Nahraďte `DATALAKEACCOUNT` a `CLUSTERNAME` odpovídajícími hodnotami.

#### <a name="a-few-hdfs-commands"></a>Několik příkazů HDFS

1. Vytvoří soubor v místním úložišti.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopírovat data z místního úložiště do úložiště clusteru.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Vypíše obsah adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky podregistru

Pro ilustrativní účely se zobrazí tři umístění souborů. Pro skutečné provedení použijte jenom jednu z `LOCATION` položek.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identifikujte cestu k úložišti z Ambari.

Chcete-li určit úplnou cestu k nakonfigurovanému výchozímu úložišti , přejděte na  >  **Konfigurace** HDFS a zadejte `fs.defaultFS` do pole Filtr vstupu.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Vytváření clusterů HDInsight s přístupem k Data Lake Storage Gen1

Pomocí následujících odkazů najdete podrobné pokyny k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen1.

* [Pomocí portálu](./hdinsight-hadoop-provision-linux-clusters.md)
* [Použití PowerShellu (s Data Lake Storage Gen1 jako výchozí úložiště)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Použití PowerShellu (s Data Lake Storage Gen1 jako další úložiště)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Pomocí šablon Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aktualizace certifikátu HDInsight pro přístup k Data Lake Storage Gen1

Následující ukázkový kód PowerShellu přečte certifikát z místního souboru nebo Azure Key Vault a aktualizuje cluster HDInsight novým certifikátem pro přístup k Azure Data Lake Storage Gen1. Zadejte vlastní název clusteru HDInsight, název skupiny prostředků, ID předplatného, `app ID` místní cestu k certifikátu. Po zobrazení výzvy zadejte heslo.

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

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

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat HDFS kompatibilní Azure Data Lake Storage Gen1 se službou HDInsight. Toto úložiště umožňuje vytvářet přizpůsobitelná, dlouhodobá a dlouhodobá řešení pro získávání archivovaných dat. A využijte HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Rychlý start: Nastavení clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
* [Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1 s využitím Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití sdílených přístupových podpisů Azure Blob Storage k omezení přístupu k datům pomocí HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)