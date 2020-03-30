---
title: Použití data lake storage Gen1 s Hadoopem v Azure HDInsight
description: Zjistěte, jak se dotazovat na data z Azure Data Lake Storage Gen1 a ukládat výsledky analýzy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251091"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Použití data lake storage gen1 s clustery Azure HDInsight

> [!Note]
> Nasazujte nové clustery HDInsight pomocí [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) pro lepší výkon a nové funkce.

Chcete-li analyzovat data v clusteru HDInsight, můžete je uložit buď do [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)nebo Azure Data Lake Storage Gen [2](../storage/blobs/data-lake-storage-introduction.md). Všechny možnosti úložiště umožňují bezpečně odstranit clustery HDInsight, které se používají pro výpočty bez ztráty uživatelských dat.

V tomto článku se dozvíte, jak data Lake Storage Gen1 funguje s clustery HDInsight. Informace o tom, jak s clustery HDInsight pracuje služba Azure Storage, najdete v tématu [Použití služby Azure Storage s clustery Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Další informace o vytvoření clusteru HDInsight najdete [v tématu Vytvoření clusterů Apache Hadoop v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 je vždy přístupné prostřednictvím `adls` zabezpečeného kanálu, takže neexistuje žádný název schématu souborového systému. Vždy používáte `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Dostupnost pro clustery HDInsight

Apache Hadoop podporuje představu o výchozím souborovém systému. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight můžete zadat kontejner objektů blob ve službě Azure Jako výchozí souborový systém nebo s HDInsight 3.5 a novějšími verzemi můžete jako výchozí systém souborů s výchozím souborovým systémem s a. několik výjimek. Všimněte si, že cluster a účet úložiště musí být hostované ve stejné oblasti.

Clustery HDInsight můžou používat Data Lake Storage Gen1 dvěma způsoby:

* Jako výchozí úložiště.
* Jako další úložiště, přičemž Azure Storage Blob je výchozí úložiště.

Od této chvíle podporují použití data Lake Storage Gen1 jako výchozího úložiště a dalších účtů úložiště pouze některé typy nebo verze clusteru HDInsight:

| Typ clusteru HDInsight | Úložiště datových jezer Gen1 jako výchozí úložiště | Úložiště datového jezera Gen1 jako další úložiště| Poznámky |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight verze 4.0 | Ne | Ne |ADLS Gen1 není podporován s HDInsight 4.0 |
| HDInsight verze 3.6 | Ano | Ano | S výjimkou HBase|
| HDInsight verze 3.5 | Ano | Ano | S výjimkou HBase|
| HDInsight verze 3.4 | Ne | Ano | |
| HDInsight verze 3.3 | Ne | Ne | |
| HDInsight verze 3.2 | Ne | Ano | |
| Storm | | |You can use Data Lake Storage Gen1 to write data from a Storm topology. Úložiště datových jezer můžete také použít pro referenční data, která pak lze číst topologii Storm.|

> [!WARNING]  
> HDInsight HBase není podporován s Azure Data Lake Storage Gen1

Použití Data Lake Storage Gen1 jako dalšího účtu úložiště nemá vliv na výkon nebo možnost čtení nebo zápisu do úložiště Azure z clusteru.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Použití úložiště datového jezera Gen1 jako výchozího úložiště

Když je HDInsight nasazen s Datovým lakem Gen1 jako výchozí úložiště, soubory související s clusterem jsou uloženy v `adl://mydatalakestore/<cluster_root_path>/`aplikaci , kde `<cluster_root_path>` je název složky, kterou vytvoříte v úložišti Data Lake. Zadáním kořenové cesty pro každý cluster můžete použít stejný účet úložiště datového jezera pro více než jeden cluster. Takže máte nastavení, kde:

* Cluster1 může používat cestu `adl://mydatalakestore/cluster1storage`.
* Cluster2 může používat cestu `adl://mydatalakestore/cluster2storage`.

Všimněte si, že oba clustery používají stejný účet Data Lake Storage Gen1 **mydatalakestore**. Každý cluster má přístup ke svému vlastnímu kořenovému souborovému systému v úložišti Data Lake Storage. Prostředí nasazení na webu Azure Portal vás zvláště vyzývá, abyste pro kořenovou cestu používali název složky ve formátu například **/clustery/\<název_clusteru>**.

Chcete-li mít možnost používat Data Lake Storage Gen1 jako výchozí úložiště, musíte udělit instanční přístup k následujícím cestám:

* Kořenový adresář účtu Data Lake Storage Gen1.  Například: adl://mydatalakestore/.
* Složka pro všechny složky clusteru.  Například: adl://mydatalakestore/clusters.
* Složka pro cluster.  Například: adl://mydatalakestore/clusters/cluster1storage.

Další informace o vytvoření instančního objektu a udělení přístupu naleznete v tématu Konfigurace přístupu k úložišti datového jezera.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahování certifikátu z Azure Keyvault pro použití při vytváření clusteru

Pokud chcete nastavit Azure Data Lake Storage Gen1 jako výchozí úložiště pro nový cluster a certifikát pro váš instanční objekt je uložen v Azure Key Vault, existuje několik dalších kroků potřebných k převodu certifikátu do správného formátu. Následující fragmenty kódu ukazují, jak provést převod.

Nejprve stáhněte certifikát z trezoru klíčů a extrahujte . `SecretValueText`

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Dále převeďte na `SecretValueText` certifikát.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Potom můžete použít `$identityCertificate` k nasazení nového clusteru jako v následujícím fragmentu:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Použití úložiště datového jezera Gen1 jako dalšího úložiště

Úložiště datového jezera Gen1 můžete použít také jako další úložiště pro cluster. V takových případech může být výchozí úložiště clusteru buď objekt blob úložiště Azure, nebo účet úložiště datového jezera. Pokud používáte úlohy HDInsight proti datům uloženým v úložišti Data Lake Jako další úložiště, musíte použít plně kvalifikovanou cestu k souborům. Například:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Všimněte si, že teď v adrese URL není **cluster_root_path**. Je to proto, že úložiště datového jezera není v tomto případě výchozím úložištěm, takže vše, co musíte udělat, je poskytnout cestu k souborům.

Chcete-li mít možnost použít Data Lake Storage Gen1 jako další úložiště, stačí udělit instanční přístup k cesty, kde jsou uloženy soubory.  Například:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Další informace o vytvoření instančního objektu a udělení přístupu naleznete v tématu Konfigurace přístupu k úložišti datového jezera.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Použití více než jednoho úložiště datových jezer

Přidání účtu úložiště datového jezera jako další a přidání více než jeden data lake storage účty se provádí tím, že clusteru HDInsight oprávnění k datům v jedné nebo další účty Úložiště datového jezera. Viz Konfigurace přístupu k úložišti datového jezera.

## <a name="configure-data-lake-storage-access"></a>Konfigurace přístupu k úložišti datového jezera

Chcete-li nakonfigurovat přístup k úložišti datového jezera z clusteru HDInsight, musíte mít instanční objekt služby Azure Active directory (Azure AD). Instanční objekt může vytvořit pouze správce Azure AD. Instanční objekt musí být vytvořený s certifikátem. Další informace najdete v tématu s [rychlým startem pro nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) a v části tématu věnované [vytváření instančních objektů s certifikátem podepsaným jeho držitelem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Pokud budete používat Azure Data Lake Storage Gen1 jako další úložiště pro hdinsight clusteru, důrazně doporučujeme provést při vytváření clusteru, jak je popsáno v tomto článku. Přidání Azure Data Lake Storage Gen1 jako další úložiště do existujícího clusteru HDInsight není podporovaný scénář.

Další informace o základech modelu řízení přístupu pro Data Lake Storage Gen1 najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak získat přístup k souborům v úložišti Data Lake Storage z clusteru HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenovému adresáři clusteru:

    ```
    adl:///<file path>
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založeny na [ssh připojení](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. Příklady používají všechna tři schémata URI. Nahraďte `DATALAKEACCOUNT` a `CLUSTERNAME` s příslušnými hodnotami.

#### <a name="a-few-hdfs-commands"></a>Několik příkazů hdfs

1. Vytvořte jednoduchý soubor v místním úložišti.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v clusterovém úložišti.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Zkopírujte data z místního úložiště do clusterového úložiště.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Seznam obsahu adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky Podregistru

Pro ilustrativní účely jsou zobrazena tři umístění souborů. Pro skutečné spuštění použijte pouze `LOCATION` jednu z položek.

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

## <a name="identify-storage-path-from-ambari"></a>Identifikace cesty úložiště z Ambari

Chcete-li identifikovat úplnou cestu k nakonfigurovanému výchozímu úložišti, přejděte na**konfigurace** **HDFS** > a zadejte `fs.defaultFS` do vstupního pole filtru.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Vytváření clusterů HDInsight s přístupem k úložišti Data Lake Storage Gen1

Pomocí následujících odkazů naleznete podrobné pokyny k vytvoření clusterů HDInsight s přístupem k úložišti Data Lake Storage Gen1.

* [Pomocí portálu](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Použití PowerShellu (s výchozím úložištěm Úložiště datových jezer Gen1)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Použití PowerShellu (s úložištěm datového jezera Gen1 jako dalšíúložiště)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Pomocí šablon Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Aktualizace certifikátu HDInsight pro přístup k Data Lake Storage Gen1

Následující příklad kódu PowerShellu přečte certifikát z místního souboru nebo trezoru klíčů Azure a aktualizuje cluster HDInsight novým certifikátem pro přístup k Azure Data Lake Storage Gen1. Zadejte svůj vlastní název clusteru HDInsight, název skupiny prostředků, ID předplatného, ID aplikace, místní cestu k certifikátu. Po zobrazení výzvy zadejte heslo.

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

V tomto článku jste se dozvěděli, jak používat HDFS kompatibilní Azure Data Lake Storage Gen1 s HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rychlý start: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Vytvoření clusteru HDInsight pro použití data lake storage gen1 pomocí Azure PowerShellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Kurz: Extrahování, transformace a načítání dat pomocí interaktivního dotazu v Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
