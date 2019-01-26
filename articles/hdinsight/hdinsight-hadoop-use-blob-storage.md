---
title: Dotazy na data z úložiště Azure kompatibilního se systémem HDFS – Azure HDInsight
description: Zjistěte, jak zadávat dotazy na data z Azure storage a Azure Data Lake Storage k ukládání výsledků analýzy.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: 05793afc5de8b6c8285c1f700929e4f110bdfc8d
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912549"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití úložiště Azure s clustery Azure HDInsight

Pokud chcete analyzovat data v clusteru HDInsight, můžete můžete ukládat buď v [služby Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[2. generace úložiště Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md), nebo obojí. Obě možnosti ukládání umožňují bezpečné odstranění clusterů HDInsight, které se používají pro výpočty, aniž by se ztratila uživatelská data.

Apache Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight, můžete zadat kontejner objektů blob ve službě Azure Storage jako výchozí systém souborů nebo u služby HDInsight 3.6, můžete vybrat služby Azure Storage nebo Azure Data Lake Storage Gen 1 / Azure Data Lake Storage Gen 2 jako výchozí soubory systém s několika výjimkami. Možnosti použití Data Lake Storage Gen 1 jako výchozí i propojené úložiště, najdete v části [dostupnost pro HDInsight cluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak služba Azure Storage pracuje s clustery HDInsight. Informace o tom, jak služba Data Lake Storage Gen 1 pracuje s clustery HDInsight, naleznete v tématu [použití služby Azure Data Lake Storage s Azure HDInsight clustery](hdinsight-hadoop-use-data-lake-store.md). Další informace o vytvoření clusteru HDInsight najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage je robustní řešení úložiště pro obecné účely, které se jednoduše integruje se službou HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní systému souborů Hadoop DFS (HDFS) může celá sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

> [!WARNING]  
> Při vytváření účtu služby Azure Storage je k dispozici několik možností. Následující tabulka poskytuje informace o podporovaných možnostech se službou HDInsight:

| Typ účtu úložiště | Podporované služby | Úrovně výkonu podporované | Podporované přístupu |
|----------------------|--------------------|-----------------------------|------------------------|
| Pro obecné účely V2   | Objekt blob               | Standard                    | Horká, studená, archivní *    |
| Pro obecné účely V1   | Objekt blob               | Standard                    | neuvedeno                    |
| Blob Storage         | Objekt blob               | Standard                    | Horká, studená, archivní *    |

Nedoporučujeme používat výchozí kontejner objektů blob pro ukládání firemních dat. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Mějte na paměti, že výchozí kontejner obsahuje protokoly aplikace a systémový protokol. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho kontejneru objektů blob jako výchozího systému souborů pro několik clusterů se nepodporuje.
 
 > [!NOTE]  
 > Archivní úroveň přístupu je offline vrstvy, který má několik latence načtení hodinu a nedoporučuje se používat pro použití s HDInsight. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier">archivní úroveň přístupu</a>.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight
Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá službu Azure Storage:

![Clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných i nestrukturovaných dat do služby Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Služba HDInsight navíc umožňuje přístup k datům uloženým ve službě Azure Storage. Syntaxe je:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Při použití účtu Azure Storage s clustery HDInsight je potřeba zvážit tyto aspekty.

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou přidružené ke clusteru během vytváření, máte plný přístup k objektům BLOB v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources">Správa přístupu ke kontejnerům a objektům blob</a>.
  > 
  > 
* **Privátní kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** Objekty BLOB v kontejnerech nelze získat přístup, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. To se vysvětluje dále v tomhle článku.

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Výchozím chováním služby HDInsight je používání účtů úložiště, které jsou definovány v souboru core-site.xml. Je toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Apache Hive, MapReduce, streamování Apache Hadoop a Apache Pig, může obsahovat popis účtů úložiště a spojených metadat. (To aktuálně funguje pro Pig s účty úložiště, ale ne pro metadata.) Více informací najdete v části [Použití clusteru HDInsight s alternativními účty úložiště a metaúložišti](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů blob ukládají data jako páry klíč/hodnota a neexistuje žádná hierarchie adresářů. V názvu klíče se dá použít lomítko (/), aby název klíče připomínal  cestu k souboru. Klíč k objektu blob může být například *input/log1.txt*. Žádný skutečný *vstupní* adresář neexistuje, ale vzhledem k lomítku v názvu klíče tento název připomíná zobrazení cesty k souboru.

## <a id="benefits"></a>Výhody služby Azure Storage
Předpokládaná výkonová náročnost společně umístěných výpočetních clusterů a prostředků úložiště je zmírněna tím, že výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť umožňuje efektivní přístup výpočetních uzlů k datům ve službě Azure Storage.

S ukládáním dat ve službě Azure Storage namísto HDFS je spojeno několik výhod:

* **Opakované použití dat a sdílení:** Data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure Storage jsou přístupná prostřednictvím rozhraní API HDFS nebo prostřednictvím [rozhraní REST API služby Blob Storage][blob-storage-restAPI]. Proto s větším počtem aplikací (včetně jiných clusterů HDInsight) a nástrojů  se dají vytvářet a využívat data.
* **Archivace dat:** Ukládání dat ve službě Azure storage umožňuje clusterů HDInsight, které jsou používány pro výpočty bezpečně odstranit, aniž by se ztratila uživatelská data.
* **Náklady na úložiště dat:** Pro dlouhodobé ukládání dat v systému souborů DFS je proto dražší než ukládání dat ve službě Azure storage, protože náklady na výpočetní cluster jsou vyšší než náklady na úložiště Azure. Navíc se data nemusí nahrávat znovu pro každou generaci výpočetních clusterů, náklady na nahrávání dat jsou tak nižší.
* **Elastické horizontální navýšení kapacity:** I když HDFS poskytuje systém horizontálním navýšením kapacity souborů, škála se určuje podle počtu uzlů, které vytvoříte pro váš cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky k dispozici ve službě Azure Storage.
* **Geografická replikace:** Úložiště Azure může být geograficky replikovaný. I když to přináší geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může vést k dalším nákladům. Doporučujeme proto geografickou replikaci dobře zvážit a zvolit jen v případě, že hodnota dat je vyšší než náklady na celou operaci.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage ve skutečnosti uložit nechcete. V takovém případě můžete zvolit k uložení dat do místní HDFS. Ve skutečnosti služba HDInsight používá DFS pro některé z těchto mezilehlých výsledků v úlohách Hive a jiných procesech.

> [!NOTE]  
> Většina příkazů HDFS (například <b>ls</b>, <b>copyFromLocal</b> a <b>mkdir</b>) bude i nadále fungovat podle očekávání. Jenom příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), jako je například <b>fschk</b> a <b>dfsadmin</b>, se budou chovat ve službě Azure Storage odlišně.


## <a name="create-blob-containers"></a>Vytvoření kontejnerů objektů Blob
K použití objektů blob je třeba nejprve vytvořit [Účet služby Azure Storage][azure-storage-create]. V rámci tohoto procesu zadáte oblast Azure, ve které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze serveru SQL metaúložiště Hive a databáze serveru SQL metaúložiště Apache Oozie musí být také umístěny ve stejné oblasti.

Bez ohledu na svoje umístění patří každý objekt blob, který vytvoříte, do kontejneru v účtu úložiště Azure. Tento kontejner může být existující objekt blob, který se vytvořil mimo HDInsight, nebo to může být kontejner, který se vytvořil pro cluster služby HDInsight.

Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí kontejner objektu Blob nesdílejte s více clustery služby HDInsight. Může dojít k poškození historie úlohy. Doporučujeme použít jiný kontejner pro každý cluster a umístit sdílená data na propojený účet úložiště, zadaný v nasazení všech příslušných clusterů, nikoli na výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Tvorba clusterů HDInsight][hdinsight-creation]. Nicméně, po odstranění původního clusteru HDInsight můžete znovu použít výchozí kontejner úložiště. Pro clustery HBase můžete zachovat schéma a data tabulky HBase vytvořením nového clusteru HBase pomocí výchozího kontejneru objektů blob, který je používán odstraněným clusterem HBase.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Při vytváření clusteru HDInsight z portálu máte možnost (jak je vidět níže) zadat podrobnosti účtu úložiště. Můžete také určit, zda chcete ještě účet úložiště přidružené ke clusteru a pokud ano, zvolit z úložiště Data Lake nebo jiné služby Azure Storage blob jako další úložiště.

![Zdroj dat pro vytvoření hadoopu HDInsight](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.


### <a name="use-azure-powershell"></a>Použití Azure Powershell
Pokud jste [nainstalovali a nakonfigurovali Azure PowerShell][powershell-install], můžete použít následující z příkazového řádku Azure PowerShell k vytvoření účtu úložiště a kontejneru:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-classic-cli"></a>Použití Azure Classic CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Pokud máte [instalaci a konfiguraci Azure Classic CLI](../cli-install-nodejs.md), slouží následující příkaz k účtu úložiště a kontejner.

```cli
azure storage account create <storageaccountname> --type LRS
```

> [!NOTE]  
> Parametr `--type` určuje, jak bude účet úložiště replikován. Další informace najdete v tématu [Replikace Azure Storage](../storage/storage-redundancy.md). Nepoužívejte ZRS, protože nepodporuje objekt blob stránky, soubor, tabulku ani frontu.

Budete vyzváni k zadání geografické oblasti, ve které se vytvoří účet úložiště. Účet úložiště byste měli vytvořit ve stejné oblasti, kterou chcete použít k vytvoření clusteru služby HDInsight.

Po vytvoření účtu úložiště použijte následující příkaz k načtení klíčů účtu úložiště:

```cli
azure storage account keys list <storageaccountname>
```

Když chcete vytvořit kontejner, použijte následující příkaz:

```cli
azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="address-files-in-azure-storage"></a>Adresování souborů ve službě Azure Storage
Schéma identifikátoru URI pro přístup k souborům ve službě Azure Storage ze služby HDInsight je:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schéma identifikátoru URI poskytuje nezašifrovaný přístup (s předponou *wasb:*) a zašifrovaný přístup SSL (s *wasbs*). Doporučujeme používat *wasbs* kdykoli je to možné, i v případě přístupu k datům, umístěným uvnitř stejné oblasti v Azure.

&lt;BlobStorageContainerName&gt; označuje název kontejneru objektů blob ve službě Azure Storage.
&lt;StorageAccountName&gt; identifikuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

Pokud nebyl zadán &lt;BlobStorageContainerName&gt; ani &lt;StorageAccountName&gt;, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například soubor *hadoop-mapreduce-examples.jar*, který se dodává s clustery HDInsight, lze odkazovat pomocí jedné z následujících akcí:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> V clusterech HDInsight verze 2.1 a 1.6. je název souboru <i>hadoop-examples.jar</i>

&lt;Cesta&gt; je název cesty HDFS souboru nebo adresáře. Vzhledem k tomu, že kontejnery ve službě Azure Storage jsou jednoduše úložiště párů klíč-hodnota, neexistuje žádný opravdový hierarchický systém souborů. Lomítko ( / ) uvnitř klíče objektu blob se považuje za oddělovač adresářů. Například název objektu blob pro *hadoop-mapreduce-examples.jar* je:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Při práci s objekty blob mimo HDInsight většina nástrojů nerozpozná formát WASB a místo toho očekávají základní formát cesty, jako je například `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs"></a>Přístup k objektům blob

### <a name="access-blobs-using-azure-powershell"></a> Použití Azure Powershellu

> [!NOTE]

> Příkazy v této části jsou ukázkami základních příkladů použití prostředí PowerShell pro přístup k datům, uloženým v objektech blob. Obsáhlejší a plnohodnotný příklad, přizpůsobený pro práci s HDInsight, najdete v části [Nástroje HDInsight](https://github.com/Blackmist/hdinsight-tools).

Pomocí následujícího příkazu můžete zobrazit seznam rutin týkajících se objektu blob:

```powershell 
Get-Command *blob*
```

![Seznam rutin prostředí PowerShell týkajících se objektu blob.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Nahrání souborů

Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

#### <a name="download-files"></a>Stažení souborů

Následující skript stáhne objekt blob bloku do aktuální složky. Před spuštěním skriptu změňte adresář na složku, ke které máte oprávnění k zápisu.

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

# Use Add-AzureAccount if you haven't connected to your Azure subscription
Connect-AzureRmAccount 
Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

Write-Host "Create a context object ... " -ForegroundColor Green
$storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

Write-Host "List the downloaded file ..." -ForegroundColor Green
cat "./$blob"
```

Při poskytnutí názvu skupiny prostředků a názvu clusteru můžete použít následující kód:

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$clusterName = "<HDInsightClusterName>"
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
$defaultStorageContainer = $cluster.DefaultStorageContainer
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force
```

#### <a name="delete-files"></a>Odstranění souborů

```powershell
Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob
```

#### <a name="list-files"></a>Zobrazení souborů

```powershell
Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"
```

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Spuštění dotazů Hive pomocí nedefinovaného účtu úložiště

Tento příklad ukazuje, jak zobrazit obsah složky z účtu úložiště, které není definováno během procesu vytváření.

```powershell
$clusterName = "<HDInsightClusterName>"

$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

Use-AzureRmHDInsightCluster $clusterName

$defines = @{}
$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
```

### <a name="use-azure-classic-cli"></a>Použití Azure Classic CLI

Pomocí následujícího příkazu můžete zobrazit seznam příkazů týkajících se objektu blob:

```cli
azure storage blob
```

**Příklad použití rozhraní příkazového řádku Azure Classic k nahrání souboru**

```cli
azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Příklad použití rozhraní příkazového řádku Azure Classic pro stažení souboru**

```cli
azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Příklad použití rozhraní příkazového řádku Azure Classic k odstranění souboru**

```cli
azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Příklad použití rozhraní příkazového řádku Azure Classic pro vytvoření seznamu souborů**

```cli
azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight][hdinsight-get-started]
* [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Apache Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Apache Pig s HDInsight][hdinsight-use-pig]
* [Použití sdílených přístupových podpisů služby Azure Storage k omezení přístupu k datům pomocí HDInsight][hdinsight-use-sas]
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)

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
