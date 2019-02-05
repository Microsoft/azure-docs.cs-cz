---
title: Výpočetní prostředí podporovaných službou Azure Data Factory | Dokumentace Microsoftu
description: Další informace o výpočetní prostředí, které můžete použít k transformaci nebo zpracování dat v Azure Data Factory kanálů (jako je například Azure HDInsight).
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: fea90d273d156eec3bf29f376e4cf6668c68170f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697513"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaných službou Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [propojené výpočetní služby v](../compute-linked-services.md).

Tento článek vysvětluje výpočetní prostředí, které můžete použít k zpracovávat a transformovat data. Také poskytuje podrobnosti o různých konfigurací (na vyžádání a přineste vlastní), že Data Factory podporuje při konfiguraci propojené služby, které je propojíte výpočetních prostředí do služby Azure data factory.

Následující tabulka obsahuje seznam výpočetní prostředí, které jsou podporovány v objektu pro vytváření dat a aktivity, které v nich dají spustit. 

| Výpočetní prostředí                      | Aktivity                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [systému SQL Server](#sql-server-linked-service) | [Aktivita Uložená procedura](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight verze podporované ve službě Data Factory
Azure HDInsight podporuje více verzích clusterů systému Hadoop, které můžete nasadit kdykoli. Všechny podporované verze vytvoří konkrétní verze dané distribuce Hortonworks Data Platform (HDP) a sada komponent v distribuci. 

Společnost Microsoft aktualizuje seznam podporovaných verzí HDInsight s nejnovější komponenty ekosystému Hadoop a opravy. Podrobné informace najdete v tématu [podporované HDInsight verze](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linuxovým systémem HDInsight verze 3.3 skončil 31. července 2017. Data Factory verze 1 HDInsight na vyžádání propojené služby, které zákazníci přidělených až do 15. prosince 2017, testovat a upgradujte na novější verzi HDInsight. HDInsight se systémem Windows se vyřadí z provozu 31. července 2018.
>
> 

### <a name="after-the-retirement-date"></a>Po datu vyřazení 

Po 15. prosince 2017:

- Že se už nevytvářejí Linuxovým systémem HDInsight verze 3.3 (nebo starší) clustery HDInsight na vyžádání pomocí propojenou službu ve službě Data Factory verze 1. 
- Pokud [ **osType** a **verze** vlastnosti](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nejsou explicitně zadané v definici JSON pro existující služby Data Factory verze 1 na vyžádání HDInsight propojené služby , výchozí hodnota se změní z **verze 3.1, osType = = Windows** k **verze =\<nejnovější verzi HDI výchozí\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType = Linux**.

Po 31. července 2018:

- Upgrade kterékoli verze clusterů HDInsight se systémem Windows už vytvoříte pomocí služby na vyžádání HDInsight, propojený ve službě Data Factory verze 1. 

### <a name="recommended-actions"></a>Doporučené akce 

- Aby bylo zajištěno, že můžete použít nejnovější komponenty ekosystému Hadoop a opravy, aktualizace [ **osType** a **verze** vlastnosti](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) ovlivněné služby Data Factory verze 1 na vyžádání Definice služby HDInsight propojený na novější verze Linuxovým systémem HDInsight (HDInsight 3.6). 
- Před 15. prosince 2017 se Data Factory verze 1 Hive, Pig, MapReduce a Hadoop streamování aktivit testu, které odkazují na ovlivněných propojenou službu. Ujistěte se, že jsou kompatibilní s novým **osType** a **verze** výchozí hodnoty (**verze = 3.6**, **osType = Linux**) nebo explicitní HDInsight verze a operační systém zadejte, že upgradujete na. 
  Další informace o kompatibilitě najdete v tématu [migrace z clusteru HDInsight se systémem Windows do clusteru se systémem Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) a [jaké jsou komponenty a verze, které jsou k dispozici s HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Chcete-li pokračovat v používání služby HDInsight, propojené služby Data Factory verze 1 na vyžádání k vytvoření clusterů HDInsight se systémem Windows, explicitně nastavit **osType** k **Windows** před 15. prosince 2017. Doporučujeme migrovat na clusterech HDInsight založených na Linuxu před 31. července 2018. 
- Pokud používáte ke spuštění služby Data Factory verze 1 služby na vyžádání HDInsight propojené DotNet vlastní aktivitu, aktualizujte definici JSON aktivity DotNet vlastní místo toho použití Azure Batch propojené služby. Další informace najdete v tématu [použití vlastních aktivit v kanálech Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Pokud používáte stávající, přineste vlastní cluster HDInsight zařízení do služby Data Factory verze 1 nebo přineste vlastní a na vyžádání HDInsight propojené služby v Azure Data Factory, nic se vyžaduje. V těchto případech je již vynuceno nejnovější zásady podpory verze clusterů HDInsight. 
>
> 


## <a name="on-demand-compute-environment"></a>Prostředí compute na vyžádání
V konfiguraci služby na vyžádání služby Data Factory plně spravuje výpočetní prostředí. Předtím, než je úloha odeslána pro zpracování dat, data Factory automaticky vytvoří prostředí compute. Po dokončení úlohy se odebere objekt pro vytváření dat výpočetní prostředí. 

Vytvoříte propojené služby pro výpočetní prostředky na vyžádání prostředí. Propojená služba použijte ke konfiguraci výpočetního prostředí a řídit granulární nastavení pro provádění úloh, správu clusteru a spuštění akce.

> [!NOTE]
> Konfigurace na vyžádání v současné době se podporuje jenom pro clustery HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight na vyžádání propojené služby
Data Factory můžete automaticky vytvořit na základě Windows nebo Linux na clusteru HDInsight na vyžádání pro zpracování dat. Vytvoření clusteru ve stejné oblasti jako účet úložiště, který je spojen s clusterem. Použít kód JSON **linkedServiceName** vlastnost k vytvoření clusteru.

Mějte na paměti následující *klíč* propojená služba HDInsight na vyžádání body:

* Cluster HDInsight na vyžádání se nezobrazí ve vašem předplatném Azure. Služba Data Factory slouží ke správě clusteru HDInsight na vyžádání za vás.
* V protokolech úloh, které jsou spuštěny v clusteru HDInsight na vyžádání se zkopírují do účtu úložiště, který je spojen s clusterem HDInsight. Chcete-li získat přístup k těmto protokolům na webu Azure Portal, přejděte **podrobnosti o spuštění aktivit** podokně. Další informace najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).
* Se účtují jenom za čas, který funguje HDInsight cluster a spuštěné úlohy.

> [!IMPORTANT]
> Obvykle trvá *20 minut* nebo více jak zřídit cluster HDInsight na vyžádání.
>
> 

### <a name="example"></a>Příklad:
Následující kód JSON určuje HDInsight propojené služby na vyžádání založené na Linuxu. Služba data Factory automaticky vytvoří *založených na Linuxu* clusteru HDInsight při zpracování datový řez. 

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
> Vytvoří HDInsight cluster *výchozí kontejner* ve službě Azure Blob storage, který zadáte v kódu JSON **linkedServiceName** vlastnost. Návrh HDInsight neprovede odstranění tohoto kontejneru odstranění clusteru. V HDInsight propojené služby na vyžádání se HDInsight cluster vytvoří pokaždé, když určitý řez je potřeba zpracovat, pokud neexistuje aktivní cluster (**timeToLive**). Po dokončení zpracování se cluster odstraní. 
>
> Po zpracování dalších řezů se v úložišti objektů Blob zobrazí spousta kontejnerů. Pokud nepotřebujete kontejnerů pro řešení potíží s úlohy, můžete k odstranění kontejnerů, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Můžete použít nástroje, jako je [Microsoft Storage Explorer](http://storageexplorer.com/) k odstranění kontejnerů v úložišti objektů Blob.
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Nastavit vlastnost typ **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet pracovních procesů a datových uzlů v clusteru. Vytvoření clusteru HDInsight s 2 hlavních uzlů se kromě počet pracovních uzlů, které zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, který má 4 jádra. 4 – pracovní uzel clusteru trvá 24 jader (4\*4 = 16 jader pro pracovní uzly a navíc 2\*4 = 8 jader pro hlavní uzly). Podrobnosti o úrovni Standard_D3 najdete v tématu [založených na Linuxu vytvoření Hadoop clusterů v HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ano      |
| timeToLive                   | Povolené prodlevy pro cluster HDInsight na vyžádání. Určuje, jak dlouho clusteru HDInsight na vyžádání zůstane aktivní po dokončení se spuštění aktivit, když v clusteru nejsou žádné aktivní úlohy.<br /><br />Například pokud aktivity spuštění trvá 6 minut a **timeToLive** nastavena na 5 minut, cluster zůstane aktivní po dobu 5 minut po 6 minut výpočetního spuštění aktivit. Pokud se spuštění další aktivity spouští v okně 6 minut, je zpracován programovacím modelem stejného clusteru.<br /><br />Vytváření clusteru HDInsight na vyžádání je náročná operace (to může nějakou dobu). Toto nastavení použijte ke zlepšení výkonu služby data factory pomocí opakovaného použití clusteru služby HDInsight na vyžádání podle potřeby.<br /><br />Pokud jste nastavili **timeToLive** hodnota, která se **0**, cluster odstraní co nejdříve po dokončení spuštění aktivity. Ale pokud nastavíte vysokou hodnotu, cluster může zůstat nečinná, zbytečně výsledkem vysoké náklady. Je důležité nastavit odpovídající hodnotu podle svých potřeb.<br /><br />Pokud **timeToLive** hodnota správně nastavená, více kanálů mohou sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| version                      | Verze clusteru HDInsight. Povolené verze HDInsight najdete v tématu [podporované HDInsight verze](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Pokud tuto hodnotu neurčíte, [nejnovější verzi HDI výchozí](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) se používá. | Ne       |
| linkedServiceName            | Propojená služba clusteru na vyžádání používané pro ukládání a zpracování dat Azure Storage. HDInsight cluster vytvoří ve stejné oblasti jako tento účet úložiště.<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, která používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledných dat z HDInsight zpracování v Data Lake Store, použijte aktivitu kopírování ke zkopírování dat z úložiště objektů Blob do Data Lake Store. </p> | Ano      |
| additionalLinkedServiceNames | Určuje další účty úložiště pro HDInsight propojenou službu. Data Factory zaregistruje všechny účty úložišť vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako HDInsight cluster. HDInsight cluster vytvoří ve stejné oblasti jako účet úložiště, která je zadána **linkedServiceName** vlastnost. | Ne       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou **Linux** a **Windows**. Pokud tuto hodnotu neurčíte, **Linux** se používá.  <br /><br />Důrazně doporučujeme používat clustery HDInsight založené na Linuxu. Datum vyřazení pro HDInsight ve Windows je 31. července 2018. | Ne       |
| hcatalogLinkedServiceName    | Název propojená služba Azure SQL, který odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří s využitím SQL database jako metastore. | Ne       |

#### <a name="example-linkedservicenames-json"></a>Příklad: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Upřesňující vlastnosti
Pro podrobnou konfiguraci clusteru HDInsight na vyžádání můžete zadat následující vlastnosti:

| Vlastnost               | Popis                              | Požaduje se |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje parametry konfigurace core (core-site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hBaseConfiguration     | Určuje parametry konfigurace HBase (hbase-site.xml) pro HDInsight cluster. | Ne       |
| hdfsConfiguration      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro HDInsight cluster. | Ne       |
| hiveConfiguration      | Určuje parametry konfigurace Hive (hive-site.xml) pro HDInsight cluster. | Ne       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro HDInsight cluster. | Ne       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro HDInsight cluster. | Ne       |
| stormConfiguration     | Určuje parametry konfigurace Storm (storm-site.xml) pro HDInsight cluster. | Ne       |
| yarnConfiguration      | Určuje parametry konfigurace YARN (yarn-site.xml) pro HDInsight cluster. | Ne       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad: Konfigurace clusteru HDInsight na vyžádání s upřesňující vlastnosti

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
Chcete-li určit velikost head, data a uzly ZooKeeper, použijte následující vlastnosti: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Nastaví velikost hlavního uzlu. Výchozí hodnota je **Standard_D3**. Podrobnosti najdete v tématu [určení velikosti uzlů](#specify-node-sizes). | Ne       |
| dataNodeSize      | Nastaví velikost datový uzel. Výchozí hodnota je **Standard_D3**. | Ne       |
| zookeeperNodeSize | Nastaví velikost uzlu ZooKeeper. Výchozí hodnota je **Standard_D3**. | Ne       |

#### <a name="specify-node-sizes"></a>Zadejte velikosti uzlů
Řetězcové hodnoty, které je nutné zadat pro vlastností popsaných v předchozí části, naleznete v tématu [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md). Hodnoty musí odpovídat rutiny a rozhraní API odkazovat v [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md). Velikost uzlu data velká (výchozí) má 7 GB paměti. To nemusí být dostatečné pro váš scénář. 

Pokud chcete vytvořit D4 velikost hlavní uzly a uzly pracovního procesu, zadejte **Standard_D4** hodnotu **vlastností headNodeSize** a **dataNodeSize** vlastnosti: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud jste nastavili nesprávnou hodnotu pro tyto vlastnosti, může se zobrazit následující zpráva:

  Nepovedlo se vytvořit cluster. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: "Chyba". Zpráva: "PreClusterCreationValidationFailure". 
  
Pokud se zobrazí tato zpráva, ujistěte se, že používáte rutiny a názvy rozhraní API z tabulky v [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Data Factory v současné době nepodporuje clusterů HDInsight, používající jako primární úložiště Data Lake Store. Pomocí služby Azure Storage jako primární úložiště pro clustery HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Přineste vlastní prostředí compute
Existující výpočetní prostředí můžete zaregistrovat jako propojenou službu ve službě Data Factory. Můžete spravovat výpočetní prostředí. Služba Data Factory využívá výpočetní prostředí ke spuštění aktivity.

Tento typ konfigurace platí pro následující výpočetních prostředích:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight, propojené služby
Můžete vytvořit služby HDInsight propojený, můžete registrovat vlastní cluster HDInsight s Data Factory.

### <a name="example"></a>Příklad:

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
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Nastavit vlastnost typ **HDInsight**. | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.        | Ano      |
| uživatelské jméno          | Název uživatelského účtu pro připojení do existujícího clusteru HDInsight. | Ano      |
| heslo          | Heslo pro uživatelský účet.   | Ano      |
| linkedServiceName | Název na propojenou službu storage, který odkazuje na objektu Blob úložiště používá HDInsight cluster. <p>V současné době není možné zadat že data Lake Store propojené služby pro tuto vlastnost. Pokud se HDInsight cluster má přístup k Data Lake Store, může přístup k datům v Data Lake Store pomocí skriptů Hive a Pig. </p> | Ano      |

## <a name="azure-batch-linked-service"></a>Služba Azure Batch propojené
Můžete vytvořit služby Batch propojené služby k zaregistrování fondu služby Batch virtuálních počítačů (VM) s datovou továrnou. Pomocí služby Batch nebo HDInsight, můžete spustit vlastní aktivity rozhraní Microsoft .NET.

Pokud jste novým uživatelem pomocí služby Batch:

* Další informace o [Základy služby Azure Batch](../../batch/batch-technical-overview.md).
* Další informace o [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) rutiny. Tuto rutinu použijte k vytvoření účtu Batch. Nebo můžete vytvořit účet Batch s využitím [webu Azure portal](../../batch/batch-account-create-portal.md). Podrobné informace o pomocí rutiny najdete v tématu [použití Powershellu ke správě účtu Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Další informace o [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) rutiny. Tuto rutinu použijte k vytvoření fondu služby Batch.

### <a name="example"></a>Příklad:

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

Pro **accountName** vlastnosti připojení **.\< Název oblasti\>**  na název vašeho účtu batch. Příklad:

```json
"accountName": "mybatchaccount.eastus"
```

Další možností je zadat **batchUri** koncového bodu. Příklad:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Nastavit vlastnost typ **AzureBatch**. | Ano      |
| accountName       | Název účtu Batch.         | Ano      |
| accessKey         | Přístupový klíč pro účet Batch.  | Ano      |
| poolName          | Název fondu virtuálních počítačů.    | Ano      |
| linkedServiceName | Název na propojenou službu storage, která je přidružená k této služby Batch propojenou službu. Tato propojená služba se používá pro pracovní soubory, které jsou požadovány ke spuštění aktivity a k ukládání protokolů spouštění aktivit. | Ano      |

## <a name="azure-machine-learning-linked-service"></a>Služba Azure Machine Learning propojený
Můžete vytvořit služby Machine Learning propojený zaregistrovat Machine Learning dávkového vyhodnocování koncový bod s datovou továrnou.

### <a name="example"></a>Příklad:

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
| Vlastnost   | Popis                              | Požaduje se |
| ---------- | ---------------------------------------- | -------- |
| Type       | Nastavit vlastnost typ **AzureML**. | Ano      |
| mlEndpoint | Adresu URL dávkového bodování.                   | Ano      |
| apiKey     | Rozhraní API publikované pracovního prostoru modelu.     | Ano      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics propojené služby
Můžete vytvořit služby Data Lake Analytics, která je propojené výpočetní služby Data Lake Analytics odkaz na službu Azure data factory. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka popisuje generické vlastnosti, které se používají v definici JSON:

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Nastavit vlastnost typ **AzureDataLakeAnalytics**. | Ano                                      |
| accountName          | Název účtu Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Identifikátor URI Data Lake Analytics.           | Ne                                       |
| subscriptionId       | ID předplatného Azure.                    | Ne<br /><br />(Pokud není zadán, odběru objekt pro vytváření dat se používá.) |
| resourceGroupName    | Název skupiny prostředků Azure.                | Ne<br /><br /> (Pokud není zadán, skupinu prostředků objekt pro vytváření dat se používá.) |

### <a name="authentication-options"></a>Možnosti ověřování
Pro službu Data Lake Analytics propojený můžete zvolit ověřování pomocí instančního objektu nebo přihlašovací údaje uživatele.

#### <a name="service-principal-authentication-recommended"></a>Ověřování instančních objektů (doporučeno)
Pokud chcete používat ověřování instančních objektů, zaregistrujte entity aplikací ve službě Azure Active Directory (Azure AD). Udělte přístup služby Azure AD k Data Lake Store. Podrobné pokyny najdete v článku [ověřování služba služba](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte ověřování instančních objektů zadáním následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID klienta vaší aplikace.     | Ano      |
| servicePrincipalKey | Klíč aplikace.           | Ano      |
| tenant              | Informaci o tenantovi (domény ID tenanta nebo název) ve kterém se nachází vaše aplikace. Pokud chcete získat tyto informace, umístěte ukazatel myši v pravém horním rohu webu Azure portal. | Ano      |

**Příklad: Ověřování instančních objektů**
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

#### <a name="user-credential-authentication"></a>Ověření přihlašovacích údajů uživatele
Pro ověření pověření uživatele pro Data Lake Analytics zadejte následující vlastnosti:

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| Autorizace | V editoru služby Data Factory, vyberte **Authorize** tlačítko. Zadejte pověření, které přiřadí tuto vlastnost adresa URL automaticky generované autorizace. | Ano      |
| ID relace     | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečný a může být použit pouze jednou. Toto nastavení není automaticky vygenerován při použití editoru služby Data Factory. | Ano      |

**Příklad: Ověření přihlašovacích údajů uživatele**
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
Autorizační kód, který jste vygenerovali tak, že vyberete **Authorize** tlačítko vyprší po nastaveném intervalu. 

Když vyprší platnost ověřovacího tokenu, může se zobrazit následující chybová zpráva: 

  Chyba operace přihlašovacích údajů: invalid_grant - AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS70008: Udělení přístupu zadaná vyprší nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 časové razítko: 2015-12-15 21:09:31Z

V následující tabulce jsou uvedeny vypršení platnosti podle typ uživatelského účtu: 

| Typ uživatele                                | Platnost vyprší po                            |
| :--------------------------------------- | :--------------------------------------- |
| Uživatelské účty, které jsou *není* spravovaný službou Azure AD (Hotmail, Live atd.) | 12 hodin.                                 |
| Uživatelské účty, které *jsou* spravovaný službou Azure AD | Spusťte 14 dnů po poslední řez. <br /><br />90 dnů, pokud určitý řez, který je založen na propojené služby na základě OAuth se spustí alespoň jednou za 14 dní. |

K zamezení nebo řešení této chyby, autorizovat tak, že vyberete **Authorize** tlačítko, když vyprší platnost tokenu. Potom znovu nasaďte propojenou službu. Hodnoty můžete vygenerovat také **sessionId** a **autorizace** vlastnosti prostřednictvím kódu programu pomocí následujícího kódu:

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

Podrobnosti o třídách objektu pro vytváření dat, které se používají v tomto příkladu kódu najdete v tématu:
* [Třída AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Třída AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Přidejte odkaz na Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro **WindowsFormsWebAuthenticationDialog** třídy. 

## <a name="azure-sql-linked-service"></a>Propojená služba Azure SQL
Můžete vytvořit propojené služby SQL a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) vyvolat uloženou proceduru z kanálu Data Factory. Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse propojené služby
Můžete vytvořit SQL Data Warehouse propojené služby a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) vyvolat uloženou proceduru z kanálu Data Factory. Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Propojené služby SQL serveru
Můžete vytvořit propojené služby SQL serveru a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) vyvolat uloženou proceduru z kanálu Data Factory. Další informace najdete v tématu [konektor SQL serveru](data-factory-sqlserver-connector.md#linked-service-properties).

