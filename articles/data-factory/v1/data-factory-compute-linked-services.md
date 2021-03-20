---
title: Výpočetní prostředí podporovaná ve verzi Azure Data Factory 1
description: Přečtěte si o výpočetních prostředích, která můžete použít v Azure Data Factorych kanálech (například Azure HDInsight) k transformaci nebo zpracování dat.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 07e13036a427ff7ff4f0cbbd2dfd6fbe272bf915
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377189"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Výpočetní prostředí podporovaná ve verzi Azure Data Factory 1
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [propojené služby COMPUTE v](../compute-linked-services.md)tématu.

Tento článek vysvětluje výpočetní prostředí, která můžete použít ke zpracování nebo transformaci dat. Poskytuje taky podrobnosti o různých konfiguracích (na vyžádání a vlastní), které Data Factory podporuje při konfiguraci propojených služeb, které propojují tato výpočetní prostředí s datovou továrnou Azure.

Následující tabulka uvádí seznam výpočetních prostředí, která jsou podporovaná Data Factory, a aktivity, které na nich můžou běžet. 

| Výpočetní prostředí                      | Aktivity                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [Dotnet](data-factory-use-custom-activities.md), [podregistr](data-factory-hive-activity.md), [prase](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasický)](#azure-machine-learning-studio-classic-linked-service) | [Aktivity studia (Classic): dávkové provádění a aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Aktivita Uložená procedura](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Verze HDInsight podporované v Data Factory
Azure HDInsight podporuje víc verzí clusterů Hadoop, které můžete nasadit kdykoli. Každá podporovaná verze vytvoří konkrétní verzi distribuce Hortonworks data Platform (HDP) a sadu komponent v distribuci. 

Microsoft aktualizuje seznam podporovaných verzí HDInsight s nejnovějšími komponentami a opravami ekosystému Hadoop. Podrobné informace najdete v tématu [podporované verze HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight verze 3,3 se systémem Linux byla vyřazena z 31. července 2017. Zákazníkům propojených služeb HDInsight na vyžádání Data Factory verze 1 byly uděleny do 15. prosince 2017 k testování a upgradu na novější verzi HDInsight. HDInsight se systémem Windows bude vyřazení od 31. července 2018.
>
> 

### <a name="after-the-retirement-date"></a>Po datu vyřazení 

Od 15. prosince 2017:

- Nemůžete už vytvářet clustery HDInsight verze 3,3 (nebo starší verze), které používají propojenou službu HDInsight na vyžádání v Data Factory verze 1. 
- Pokud vlastnosti [ **OsType** a **Version**](#azure-hdinsight-on-demand-linked-service) nejsou explicitně zadány v definici JSON pro Data Factory existující propojenou službu HDInsight verze 1 na vyžádání, bude výchozí hodnota změněna z **verze = 3.1, osType = Windows** na **Version = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType = Linux**).

Od 31. července 2018:

- Pomocí propojené služby HDInsight na vyžádání v Data Factory verze 1 už nemůžete vytvářet žádné verze clusterů HDInsight se systémem Windows. 

### <a name="recommended-actions"></a>Doporučené akce

- Aby bylo zajištěno, že budete moci použít nejnovější součásti a opravy ekosystému Hadoop, aktualizujte [vlastnosti **OsType** a **Version**](#azure-hdinsight-on-demand-linked-service) Data Factory ovlivněných služeb HDInsight na vyžádání verze 1 na vyžádání v novějších verzích HDInsight založených na systému Linux (HDInsight 3,6). 
- Do 15. prosince 2017 zkušebního Data Factory v podregistru, prase, MapReduce a streamování Hadoop verze 1, které odkazují na ovlivněnou propojenou službu. Ujistěte se, že jsou kompatibilní s novými **osType** a výchozími hodnotami **verze** (**verze = 3.6**, **OsType = Linux**) nebo explicitní verzí HDInsight a typem operačního systému, na který upgradujete. 
  Další informace o kompatibilitě najdete v tématu [migrace z clusteru HDInsight se systémem Windows do clusteru se systémem Linux](../../hdinsight/index.yml) a [Jaké jsou komponenty a verze systému Hadoop dostupné v HDInsight?](../../hdinsight/hdinsight-component-versioning.md). 
- Pokud chcete dál používat propojenou službu HDInsight na vyžádání Data Factory verze 1 k vytváření clusterů HDInsight se systémem Windows, explicitně nastavte **osType** na **Windows** do 15. prosince 2017. Doporučujeme migrovat clustery HDInsight se systémem Linux do 31. července 2018. 
- Pokud používáte propojenou službu HDInsight na vyžádání k provedení Data Factory vlastní aktivity verze 1, aktualizujte definici JSON vlastní aktivity DotNet, aby místo toho používala Azure Batch propojenou službu. Další informace najdete v tématu [použití vlastních aktivit v kanálu Data Factory](./data-factory-use-custom-activities.md). 

> [!Note]
> Pokud používáte své stávající propojené zařízení HDInsight clusteru v Data Factory verze 1 nebo v Azure Data Factory propojená služba HDInsight na vyžádání a na vyžádání, není nutná žádná akce. V těchto scénářích se už vynutila zásada podpory nejnovější verze clusterů HDInsight. 
>
> 


## <a name="on-demand-compute-environment"></a>Výpočetní prostředí na vyžádání
V konfiguraci na vyžádání Data Factory plně spravuje výpočetní prostředí. Data Factory automaticky vytvoří výpočetní prostředí před odesláním úlohy pro zpracování dat. Po dokončení úlohy Data Factory odebere výpočetní prostředí. 

Můžete vytvořit propojenou službu pro výpočetní prostředí na vyžádání. Použijte propojenou službu ke konfiguraci výpočetního prostředí a k řízení podrobných nastavení pro provádění úloh, správu clusterů a spouštěcí akce.

> [!NOTE]
> Konfigurace na vyžádání se v současné době podporuje jenom u clusterů HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Propojená služba Azure HDInsight na vyžádání
Data Factory může automaticky vytvořit cluster HDInsight na vyžádání založený na systému Windows nebo Linux pro zpracování dat. Cluster se vytvoří ve stejné oblasti jako účet úložiště, který je přidružený ke clusteru. K vytvoření clusteru použijte vlastnost **LINKEDSERVICENAME** JSON.

Všimněte si následujících *klíčových* bodů pro propojenou službu HDInsight na vyžádání:

* Cluster HDInsight na vyžádání se ve vašem předplatném Azure nezobrazí. Služba Data Factory spravuje cluster HDInsight na vyžádání vaším jménem.
* Protokoly pro úlohy, které jsou spuštěné na clusteru HDInsight na vyžádání, se zkopírují do účtu úložiště, který je přidružený ke clusteru HDInsight. Přístup k těmto protokolům získáte tak, že v Azure Portal přejdete do podokna **Podrobnosti o spuštění aktivit** . Další informace najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).
* Účtují se vám jenom čas, kdy je cluster HDInsight v provozu a spouští úlohy.

> [!IMPORTANT]
> Zřizování clusteru HDInsight na vyžádání obvykle trvá *20 minut* nebo déle.
>
> 

### <a name="example"></a>Příklad
Následující JSON definuje propojenou službu HDInsight na vyžádání v systému Linux. Při zpracování datového řezu Data Factory automaticky vytvoří cluster HDInsight se *systémem Linux* . 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Cluster HDInsight vytvoří *výchozí kontejner* ve službě Azure Blob Storage, kterou zadáte ve vlastnosti **linkedServiceName** JSON. Podle návrhu služba HDInsight při odstranění clusteru neodstraní tento kontejner. V propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat řez, pokud není existující živý cluster (**TimeToLive**). Po dokončení zpracování se cluster odstraní. 
>
> Po zpracování dalších řezů se v úložišti objektů BLOB zobrazuje spousta kontejnerů. Pokud nepotřebujete kontejnery pro úlohy odstraňování potíží, možná budete chtít kontejnery odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf<your Data Factory name>-<linked service name>-<date and time>`. K odstranění kontejnerů v úložišti objektů blob můžete použít nástroj, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/) .
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Povinné |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Nastavte vlastnost Type na **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Počet pracovních procesů a datových uzlů v clusteru. Cluster HDInsight se vytvoří s 2 hlavními uzly kromě počtu pracovních uzlů, které pro tuto vlastnost zadáte. Uzly mají velikost Standard_D3, která má 4 jádra. Cluster se čtyřmi pracovními uzly přijímá 24 jader (4 \* 4 = 16 jader pro pracovní uzly a 2 \* 4 = 8 jader pro hlavní uzly). Podrobnosti o Standard_D3 vrstvě najdete [v tématu vytváření clusterů Hadoop se systémem Linux ve službě HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| timeToLive                   | Povolený čas nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho zůstane aktivní cluster HDInsight na vyžádání, když je dokončený běh aktivity, pokud v clusteru nejsou žádné další aktivní úlohy.<br /><br />Pokud například spuštění aktivity trvá 6 minut a **TimeToLive** je nastaveno na 5 minut, zůstane cluster aktivní po dobu 5 minut po 6 minutách zpracování spuštění aktivity. Pokud se v okně 6 minut spustí jiný běh aktivity, zpracuje ho stejný cluster.<br /><br />Vytvoření clusteru HDInsight na vyžádání je náročná operace (může chvíli trvat). Toto nastavení použijte, pokud je to potřeba pro zlepšení výkonu datové továrny, a to tak, že znovu použijete cluster HDInsight na vyžádání.<br /><br />Pokud nastavíte hodnotu **TimeToLive** na hodnotu **0**, cluster se odstraní hned po dokončení spuštění aktivity. Pokud však nastavíte vysokou hodnotu, cluster může zůstat nečinný, což zbytečně vede k vysokým nákladům. Je důležité nastavit odpovídající hodnotu podle svých potřeb.<br /><br />Pokud je hodnota **TimeToLive** vhodně nastavená, může více kanálů sdílet instanci clusteru HDInsight na vyžádání. | Yes      |
| verze                      | Verze clusteru HDInsight. Povolené verze HDInsight najdete v tématu [podporované verze HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Pokud tato hodnota není zadaná, použije se [nejnovější výchozí verze HDI](../../hdinsight/hdinsight-component-versioning.md) . | No       |
| linkedServiceName            | Propojená služba Azure Storage, kterou má cluster na vyžádání použít k ukládání a zpracování dat. Cluster HDInsight se vytvoří ve stejné oblasti jako tento účet úložiště.<p>V současné době nemůžete vytvořit cluster HDInsight na vyžádání, který jako úložiště používá Azure Data Lake Store. Pokud chcete uložit výsledná data ze zpracování HDInsight v Data Lake Store, zkopírujte data z úložiště objektů blob do Data Lake Store pomocí aktivity kopírování. </p> | Yes      |
| additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight. Data Factory registruje účty úložiště vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight. Cluster HDInsight se vytvoří ve stejné oblasti jako účet úložiště, který je určený vlastností **linkedServiceName** . | No       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou **Linux** a **Windows**. Pokud tato hodnota není zadaná, použije se **Linux** .  <br /><br />Důrazně doporučujeme používat clustery HDInsight se systémem Linux. Datum vyřazení pro HDInsight ve Windows je 31. července 2018. | No       |
| hcatalogLinkedServiceName    | Název propojené služby Azure SQL, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí SQL Database jako metastore. | No       |

#### <a name="example-linkedservicenames-json"></a>Příklad: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Rozšířené vlastnosti
K podrobné konfiguraci clusteru HDInsight na vyžádání můžete zadat následující vlastnosti:

| Vlastnost               | Popis                              | Povinné |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje základní konfigurační parametry (core-site.xml) pro vytvoření clusteru HDInsight. | No       |
| hBaseConfiguration     | Určuje konfigurační parametry HBA (hbase-site.xml) pro cluster HDInsight. | No       |
| hdfsConfiguration      | Určuje konfigurační parametry HDFS (hdfs-site.xml) pro cluster HDInsight. | No       |
| hiveConfiguration      | Určuje parametry konfigurace podregistru (hive-site.xml) pro cluster HDInsight. | No       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro cluster HDInsight. | No       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro cluster HDInsight. | No       |
| stormConfiguration     | Určuje parametry konfigurace zaplavení (storm-site.xml) pro cluster HDInsight. | No       |
| yarnConfiguration      | Určuje konfigurační parametry PŘÍZe (yarn-site.xml) pro cluster HDInsight. | No       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad: konfigurace clusteru HDInsight na vyžádání s pokročilými vlastnostmi

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Velikosti uzlů
Chcete-li určit velikost hlav, dat a uzlů ZooKeeper, použijte následující vlastnosti: 

| Vlastnost          | Popis                              | Povinné |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Nastaví velikost hlavního uzlu. Výchozí hodnota je **Standard_D3**. Podrobnosti najdete v tématu [Určení velikosti uzlů](#specify-node-sizes). | No       |
| dataNodeSize      | Nastaví velikost datového uzlu. Výchozí hodnota je **Standard_D3**. | No       |
| zookeeperNodeSize | Nastaví velikost uzlu ZooKeeper. Výchozí hodnota je **Standard_D3**. | No       |

#### <a name="specify-node-sizes"></a>Zadat velikosti uzlů
Pro řetězcové hodnoty, které musíte zadat pro vlastnosti popsané v předchozí části, najdete informace v tématu [velikosti virtuálních počítačů](../../virtual-machines/sizes.md). Hodnoty musí odpovídat rutinám a rozhraním API, na která se odkazuje v [velikostech virtuálních počítačů](../../virtual-machines/sizes.md). Velká (výchozí) velikost uzlu dat má 7 GB paměti. To nemusí být pro váš scénář dostačující. 

Pokud chcete vytvořit hlavní uzly velikosti D4 a pracovní uzly, zadejte **Standard_D4** jako hodnotu pro vlastnosti **headNodeSize** a **dataNodeSize** : 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud jste pro tyto vlastnosti nastavili nesprávnou hodnotu, může se zobrazit následující zpráva:

  Nepovedlo se vytvořit cluster. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: ' PreClusterCreationValidationFailure '. 
  
Pokud se zobrazí tato zpráva, ujistěte se, že používáte rutinu a názvy rozhraní API z tabulky ve [velikostech virtuálních počítačů](../../virtual-machines/sizes.md).  

> [!NOTE]
> V současné době Data Factory nepodporuje clustery HDInsight, které jako primární úložiště používají Data Lake Store. Jako primární úložiště pro clustery HDInsight použijte Azure Storage. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Přineste si vlastní výpočetní prostředí
Existující prostředí COMPUTE můžete zaregistrovat jako propojenou službu v Data Factory. Spravujete prostředí Compute. Služba Data Factory používá výpočetní prostředí k provádění aktivit.

Tento typ konfigurace se podporuje pro následující výpočetní prostředí:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klasický)
* Azure Data Lake Analytics
* Azure SQL Database, analýza Azure synapse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Propojená služba Azure HDInsight
Můžete vytvořit propojenou službu HDInsight k registraci vlastního clusteru HDInsight s Data Factory.

### <a name="example"></a>Příklad

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Povinné |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Nastavte vlastnost Type na **HDInsight**. | Yes      |
| clusterUri        | Identifikátor URI clusteru HDInsight.        | Yes      |
| username          | Název uživatelského účtu, který se má použít pro připojení k existujícímu clusteru HDInsight. | Yes      |
| heslo          | Heslo pro uživatelský účet.   | Yes      |
| linkedServiceName | Název propojené služby úložiště, která odkazuje na úložiště objektů BLOB používané clusterem HDInsight. <p>V současné době nemůžete pro tuto vlastnost zadat propojenou službu Data Lake Store. Pokud má cluster HDInsight přístup k Data Lake Store, můžete získat přístup k datům v Data Lake Store z podregistru nebo skriptů pro vepřové rozhraní. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch
Můžete vytvořit propojenou službu Batch k registraci fondu služby Batch virtuálních počítačů do objektu pro vytváření dat. Vlastní aktivity Microsoft .NET můžete spustit buď pomocí služby Batch, nebo služby HDInsight.

Pokud s používáním služby Batch začínáte, provedete to takto:

* Seznamte se se [základy Azure Batch](../../azure-sql/database/sql-database-paas-overview.md).
* Přečtěte si informace o rutině [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) . Pomocí této rutiny můžete vytvořit účet Batch. Případně můžete vytvořit účet Batch pomocí [Azure Portal](../../batch/batch-account-create-portal.md). Podrobné informace o použití rutiny najdete v tématu [použití PowerShellu ke správě účtu Batch](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* Přečtěte si informace o rutině [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) . Pomocí této rutiny můžete vytvořit fond Batch.

### <a name="example"></a>Příklad

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Pro vlastnost názvu **účtu** přidejte **. \<region name\>** do názvu účtu Batch. Například:

```json
"accountName": "mybatchaccount.eastus"
```

Další možností je poskytnout koncový bod **batchUri** . Například:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Povinné |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Nastavte vlastnost Type na **AzureBatch**. | Yes      |
| accountName       | Název účtu Batch.         | Yes      |
| accessKey         | Přístupový klíč pro účet Batch.  | Yes      |
| poolName          | Název fondu virtuálních počítačů.    | Yes      |
| linkedServiceName | Název propojené služby úložiště, která je přidružená k této propojené službě Batch. Tato propojená služba se používá pro pracovní soubory, které jsou nutné ke spuštění aktivity a k ukládání protokolů spuštění aktivit. | Yes      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Propojená služba Azure Machine Learning Studio (Classic)
Můžete vytvořit propojenou službu Azure Machine Learning Studio (Classic), pomocí které zaregistrujete koncový bod dávkového vyhodnocování studia (Classic) do objektu pro vytváření dat.

### <a name="example"></a>Příklad

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Vlastnosti
| Vlastnost   | Popis                              | Povinné |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Nastavte vlastnost Type na **AzureML**. | Yes      |
| mlEndpoint | Adresa URL dávkového vyhodnocování                   | Yes      |
| apiKey     | Rozhraní API modelu publikovaného pracovního prostoru.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Můžete vytvořit propojenou službu Data Lake Analytics pro propojení Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka popisuje obecné vlastnosti, které se používají v definici JSON:

| Vlastnost                 | Popis                              | Povinné                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Nastavte vlastnost Type na **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Název Data Lake Analytics účtu  | Yes                                      |
| dataLakeAnalyticsUri | Identifikátor URI Data Lake Analytics.           | No                                       |
| subscriptionId       | ID předplatného Azure.                    | No<br /><br />(Pokud není zadaný, použije se předplatné Data Factory.) |
| resourceGroupName    | Název skupiny prostředků Azure.                | No<br /><br /> (Pokud není zadaný, použije se skupina prostředků Data Factory.) |

### <a name="authentication-options"></a>Možnosti ověřování
Pro propojenou službu Data Lake Analytics můžete vybrat mezi ověřováním pomocí instančního objektu nebo přihlašovacích údajů uživatele.

#### <a name="service-principal-authentication-recommended"></a>Ověřování instančního objektu (doporučeno)
Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Pak udělte službě Azure AD přístup k Data Lake Store. Podrobný postup najdete v tématu [ověřování služba-služba](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte ověřování instančního objektu zadáním následujících vlastností:

| Vlastnost                | Popis                              | Povinné |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID klienta aplikace.     | Yes      |
| servicePrincipalKey | Klíč aplikace           | Yes      |
| tenant              | Informace o tenantovi (název domény nebo ID tenanta), kde se nachází vaše aplikace. Chcete-li získat tyto informace, najeďte myší v pravém horním rohu Azure Portal. | Yes      |

**Příklad: ověřování instančního objektu**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Ověřování přihlašovacích údajů uživatele
Pro ověření přihlašovacích údajů uživatele pro Data Lake Analytics zadejte následující vlastnosti:

| Vlastnost          | Popis                              | Povinné |
| :---------------- | :--------------------------------------- | :------- |
| autorizace | V editoru Data Factory vyberte tlačítko **autorizovat** . Zadejte přihlašovací údaje, které přiřadí tuto vlastnost automaticky vygenerované autorizační adrese URL. | Yes      |
| sessionId     | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a dá se použít jenom jednou. Toto nastavení se generuje automaticky, když použijete Editor Data Factory. | Yes      |

**Příklad: ověření přihlašovacích údajů uživatele**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Vypršení platnosti tokenu
Autorizační kód, který jste vygenerovali výběrem tlačítka **autorizovat** , vyprší po nastaveném intervalu. 

V případě vypršení platnosti ověřovacího tokenu se může zobrazit následující chybová zpráva: 

  Chyba operace Credential: invalid_grant-AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: poskytnutý nebo odvolaný udělený přístup vypršel. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8D69-a776d2ffefd7 časové razítko: 2015-12-15 21:09:31Z

Následující tabulka uvádí vypršení platnosti podle typu uživatelského účtu: 

| Typ uživatele                                | Platnost vyprší po                            |
| :--------------------------------------- | :--------------------------------------- |
| Uživatelské účty, které *nespravuje služba* Azure AD (Hotmail, Live atd.) | 12 hodin.                                 |
| Uživatelské *účty, které spravuje Azure* AD | 14 dní po posledním spuštění řezu. <br /><br />90 dnů, pokud je řez založený na propojené službě založené na protokolu OAuth spouštěn nejméně jednou za 14 dní. |

Pokud chcete tuto chybu vyhnout, můžete ji znovu autorizovat tak, že po vypršení platnosti tokenu vyberete tlačítko **autorizovat** . Pak znovu nasaďte propojenou službu. Hodnoty pro **identifikátor SessionID** a vlastnosti **autorizace** můžete také generovat programově pomocí následujícího kódu:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Podrobnosti o třídách Data Factory, které jsou používány v tomto příkladu kódu, naleznete v tématu:
* [AzureDataLakeStoreLinkedService – třída](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [AzureDataLakeAnalyticsLinkedService – třída](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [AuthorizationSessionGetResponse – třída](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Přidejte odkaz na Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro třídu **WindowsFormsWebAuthenticationDialog** . 

## <a name="azure-sql-linked-service"></a>Propojená služba Azure SQL
Můžete vytvořit propojenou službu SQL a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z data Factoryho kanálu. Další informace najdete v tématu [konektor Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Propojená služba Azure synapse Analytics
Můžete vytvořit propojenou službu Azure synapse Analytics a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z data Factoryho kanálu. Další informace najdete v tématu [konektor Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Propojená služba SQL Server
Můžete vytvořit propojenou službu SQL Server a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu Data Factory. Další informace najdete v tématu [konektor SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).