---
title: Výpočetní prostředí podporovaných službou Azure Data Factory | Microsoft Docs
description: Další informace o výpočetní prostředí, které můžete použít v Azure Data Factory kanály (například Azure HDInsight) k datům transformace nebo proces.
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
ms.openlocfilehash: 51a0f43587b9d34a3693eb4a2927d10c71bd95d1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621747"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaných službou Azure Data Factory
> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [výpočetní propojené služby v verze 2](../compute-linked-services.md).

Tento článek vysvětluje výpočetní prostředí, které můžete použít k datům procesu nebo transformace. Také poskytuje podrobnosti o různých konfiguracích (na vyžádání a přineste vlastní), podporuje objekt pro vytváření dat při konfiguraci propojené služby, které odkazují tyto výpočetní prostředí s objektem pro vytváření dat Azure.

Následující tabulka obsahuje seznam výpočetní prostředí, které jsou podporovány v objektu pro vytváření dat a aktivity, které můžete spustit na nich. 

| Výpočetní prostředí                      | Aktivity                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [systému SQL Server](#sql-server-linked-service) | [Aktivita Uložená procedura](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight verze podporované v datové továrně
Azure HDInsight podporuje několik verzí clusteru Hadoop, které můžete nasadit kdykoli. Všechny podporované verze vytvoří na konkrétní verzi rozdělení Hortonworks Data Platform (HDP) a sadu součástí v distribuci. 

Microsoft aktualizuje seznam podporovaných verzí HDInsight s nejnovější komponenty ekosystém Hadoop a opravy. Podrobné informace najdete v tématu [podporované HDInsight verze](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight se systémem Linux verze 3.3 vyřazenou 31 července 2017. Objekt pro vytváření dat verze 1 HDInsight na vyžádání propojené služby, které zákazníci byly zadány až 15 prosinec 2017, testování a provést upgrade na novější verzi HDInsight. HDInsight se systémem Windows vyřadí 31 července 2018.
>
> 

### <a name="after-the-retirement-date"></a>Po datu vyřazení 

Po 15. prosince 2017:

- Můžete vytvořit už HDInsight se systémem Linux verze 3.3 (nebo starší verze) clustery HDInsight na vyžádání pomocí propojená služba v datové továrně verze 1. 
- Pokud [ **osType** a **verze** vlastnosti](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nejsou explicitně zadané v definici JSON pro existující objekt pro vytváření dat verze 1 na vyžádání HDInsight propojené služby , výchozí hodnota je změněn z **verze 3.1, osType = = Windows** k **verze =\<nejnovější verze HDI výchozí\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType = Linux**.

Po 31. července 2018:

- Již nemohou vytvářet všechny verze clustery HDInsight se systémem Windows pomocí na vyžádání propojené služby HDInsight v datové továrně verze 1. 

### <a name="recommended-actions"></a>Doporučené akce 

- Aby se zajistilo, že můžete použít nejnovější součástí ekosystému Hadoop a opravy, aktualizace [ **osType** a **verze** vlastnosti](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) z ovlivněné služby Data Factory verze 1 na vyžádání Definice služby HDInsight propojený na novější verze HDInsight se systémem Linux (HDInsight 3.6). 
- Před 15 prosince 2017 testovací Data Factory verze 1 Hive, Pig, MapReduce a Hadoop streamování aktivity, které odkazují ovlivněné propojené služby. Ujistěte se, že jsou kompatibilní s novým **osType** a **verze** výchozí hodnoty (**verze = 3.6**, **osType = Linux**) nebo explicitní HDInsight verze a verze operačního systému zadejte, že upgradujete na. 
  Další informace o kompatibilitě najdete v tématu [migrace z clusteru HDInsight se systémem Windows do clusteru se systémem Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) a [co jsou komponent systému Hadoop a verze, které jsou k dispozici v prostředí HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Chcete-li pokračovat v používání objektu pro vytváření dat verze 1 na vyžádání propojené služby HDInsight k vytvoření clusterů HDInsight se systémem Windows, explicitně nastavit **osType** k **Windows** před 15 prosince 2017. Doporučujeme migrovat do clusterů HDInsight se systémem Linux před 31 července 2018. 
- Pokud používáte na vyžádání propojené služby HDInsight k provedení Data Factory verze 1 DotNet vlastní aktivity, aktualizace definici JSON aktivity vlastní DotNet. místo toho použít Azure Batch propojené služby. Další informace najdete v tématu [použít vlastní aktivity v kanálu pro vytváření dat](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Pokud používáte stávající, přineste vlastní cluster HDInsight propojený zařízení v datové továrně verze 1 nebo vyžádáním přineste vlastní i na vyžádání propojené služby HDInsight v Azure Data Factory verze 2, žádná akce. V těchto scénářích je již vynutí nejnovější zásady podpory verze clusterů HDInsight. 
>
> 


## <a name="on-demand-compute-environment"></a>Na vyžádání výpočetním prostředí
V konfiguraci na vyžádání Data Factory plně spravuje výpočetní prostředí. Objekt pro vytváření dat automaticky vytvoří výpočetním prostředí, než je úloha odeslána pro zpracování dat. Po dokončení úlohy se odebere Data Factory výpočetním prostředí. 

Můžete vytvořit propojené služby pro prostředí výpočetní na vyžádání. Pomocí propojené služby, lze nakonfigurovat výpočetní prostředí a k řízení granulární nastavení pro provádění úlohy, Správa clusteru a zavádění akce.

> [!NOTE]
> V současné době konfiguraci na vyžádání je podporována pouze pro clustery služby HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure propojená služba HDInsight na vyžádání
Objekt pro vytváření dat můžete automaticky vytvořit cluster HDInsight se systémem Linux nebo systému Windows na vyžádání pro zpracování dat. Cluster se vytvoří ve stejné oblasti jako účet úložiště, který je spojen s clusterem. Použijte formát JSON **linkedServiceName** vlastnost k vytvoření clusteru.

Vezměte na vědomí následující *klíč* body o HDInsight na vyžádání propojené služby:

* Cluster HDInsight na vyžádání se nezobrazí ve vašem předplatném Azure. Služba Data Factory slouží ke správě clusteru HDInsight na vyžádání vaším jménem.
* Protokoly pro úlohy, které se spouštějí v clusteru HDInsight na vyžádání se zkopírují na účet úložiště, který je spojen s clusterem HDInsight. Pro přístup k tyto protokoly na portálu Azure přejděte do **podrobnosti o spuštění aktivit** podokně. Další informace najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).
* Budou se účtovat pouze pro spuštěné úlohy a čas, zda HDInsight cluster.

> [!IMPORTANT]
> Obvykle trvá *20 minut* nebo více ke zřízení clusteru HDInsight na vyžádání.
>
> 

### <a name="example"></a>Příklad:
Následující kód JSON určuje základě Linux na vyžádání propojené služby HDInsight. Data Factory automaticky vytvoří *systémem Linux* clusteru HDInsight se při zpracování dat řezu. 

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
> Vytvoří HDInsight cluster *výchozí kontejner* ve službě Azure Blob storage, který určíte v kódu JSON **linkedServiceName** vlastnost. Návrh neodstraní HDInsight tento kontejner při odstranění clusteru. V na vyžádání propojené služby HDInsight, HDInsight cluster vytvoří pokaždé, když řez je potřeba zpracovat, pokud neexistuje aktivní cluster (**timeToLive**). Po dokončení zpracování odstranění clusteru. 
>
> Jak se zpracovávají další řezy, uvidíte mnoho kontejnerů ve službě Blob storage. Pokud nepotřebujete kontejnery pro řešení potíží s úloh, můžete k odstranění kontejnerů snížit náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Můžete použít nástroje, jako je [Microsoft Storage Explorer](http://storageexplorer.com/) k odstranění kontejnerů v úložišti objektů Blob.
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Nastavte vlastnost type na **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet uzlů pracovního procesu a data v clusteru. Vytvoření clusteru HDInsight s 2 hlavních uzlů se kromě počet uzlů pracovního procesu, které zadáte pro tuto vlastnost. Uzly jsou velikosti Standard_D3, který má 4 jádra. 4 pracovního uzlu clusteru trvá 24 jader (4\*4 = 16 jader pro uzly pracovního procesu, plus 2\*4 = 8 jader pro head uzly). Podrobnosti o vrstvě Standard_D3 najdete v tématu [vytvořit systémem Linux Hadoop clusterů v HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ano      |
| timeToLive                   | Povolené doby nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho clusteru HDInsight na vyžádání zůstane aktivní až po dokončení aktivity spustit, pokud nejsou žádné aktivní úlohy v clusteru.<br /><br />Například pokud aktivitu spustit trvá 6 minut a **timeToLive** nastavena na 5 minut, v clusteru zůstane aktivní po dobu 5 minut po 6 minut zpracování aktivity při spuštění. Pokud jiné aktivity při spuštění je proveden v okně 6 minut, je zpracován stejného clusteru.<br /><br />Vytvoření clusteru HDInsight na vyžádání je náročná operace (může trvat nějakou dobu). Pomocí tohoto nastavení podle potřeby zvýšit výkon služby data factory pomocí opakovaného použití clusteru HDInsight na vyžádání.<br /><br />Pokud nastavíte **timeToLive** hodnotu **0**, odstranění clusteru ihned po dokončení spuštění aktivity. Ale pokud nastavíte na vysokou hodnotu, cluster může zůstat nečinnosti zbytečně výsledkem vysoké náklady. Je důležité nastavit na odpovídající hodnotu na základě potřeb.<br /><br />Pokud **timeToLive** správně nastavena hodnota, více kanálů můžete sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| verze                      | Verze clusteru HDInsight. Povolené HDInsight verze najdete v tématu [podporované HDInsight verze](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Pokud tato hodnota není zadaná, [nejnovější verze HDI výchozí](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions) se používá. | Ne       |
| linkedServiceName            | Propojenou službu úložiště Azure pro použití v clusteru na vyžádání pro ukládání a zpracování dat. HDInsight cluster vytvoří ve stejné oblasti jako účet úložiště.<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá jako úložiště Azure Data Lake Store. Pokud chcete uložit výsledek data z HDInsight zpracování v Data Lake Store, pomocí aktivity kopírování zkopírovat data z úložiště objektů Blob do Data Lake Store. </p> | Ano      |
| additionalLinkedServiceNames | Určuje další účty úložiště pro propojená služba HDInsight. Objekt pro vytváření dat zaregistruje účty úložiště vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako HDInsight cluster. HDInsight cluster vytvoří ve stejné oblasti jako účet úložiště, která je zadána **linkedServiceName** vlastnost. | Ne       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou **Linux** a **Windows**. Pokud tato hodnota není zadaná, **Linux** se používá.  <br /><br />Důrazně doporučujeme používat clustery HDInsight se systémem Linux. Datum vyřazení pro HDInsight v systému Windows je 31 července 2018. | Ne       |
| hcatalogLinkedServiceName    | Název SQL Azure, propojené služby, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání je vytvořená pomocí SQL database jako metaúložiště. | Ne       |

#### <a name="example-linkedservicenames-json"></a>Příklad: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Rozšířené vlastnosti
Pro podrobné konfiguraci clusteru HDInsight na vyžádání můžete určit následující vlastnosti:

| Vlastnost               | Popis                              | Požaduje se |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje parametry konfigurace jádra (core-site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hBaseConfiguration     | Určuje parametry konfigurace HBase (hbase-site.xml) pro HDInsight cluster. | Ne       |
| hdfsConfiguration      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro HDInsight cluster. | Ne       |
| hiveConfiguration      | Určuje parametry konfigurace Hive (hive-site.xml) pro HDInsight cluster. | Ne       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro HDInsight cluster. | Ne       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro HDInsight cluster. | Ne       |
| stormConfiguration     | Určuje parametry konfigurace Storm (storm-site.xml) pro HDInsight cluster. | Ne       |
| yarnConfiguration      | Určuje parametry konfigurace YARN (yarn-site.xml) pro HDInsight cluster. | Ne       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad: Konfigurace clusteru HDInsight na vyžádání pomocí rozšířených vlastností

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

### <a name="node-sizes"></a>Velikost uzlu
Pokud chcete zadat velikost head, data a uzly ZooKeeper, použijte následující vlastnosti: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Nastaví velikost hlavního uzlu. Výchozí hodnota je **Standard_D3**. Podrobnosti najdete v tématu [zadejte velikosti uzlu](#specify-node-sizes). | Ne       |
| dataNodeSize      | Nastaví velikost uzlu data. Výchozí hodnota je **Standard_D3**. | Ne       |
| zookeeperNodeSize | Nastaví velikost uzlu ZooKeeper. Výchozí hodnota je **Standard_D3**. | Ne       |

#### <a name="specify-node-sizes"></a>Zadejte velikosti uzlu
Řetězcové hodnoty, které je nutno zadat pro pomocí vlastností popsaných v předchozí části, najdete v části [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md). Hodnoty musí odpovídat rutiny a rozhraní API v odkazuje [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md). Velikost uzlu data velká (výchozí) má 7 GB paměti. Toto nemusí být dostatečné pro váš scénář. 

Pokud chcete vytvořit D4 velikost head uzlů a uzlů pracovního procesu, zadejte **Standard_D4** hodnotu **headNodeSize** a **dataNodeSize** vlastnosti: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud jste nastavili nesprávnou hodnotu pro tyto vlastnosti, zobrazí se následující zpráva:

  Nepodařilo se vytvořit cluster. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: 'PreClusterCreationValidationFailure'. 
  
Pokud se zobrazí tato zpráva, ujistěte se, že používáte rutiny a názvy rozhraní API z tabulky v [velikostí virtuálních počítačů](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Objekt pro vytváření dat v současné době nepodporuje clusterů HDInsight, které používají jako primární úložiště Data Lake Store. Používání Azure Storage jako primární úložiště pro clustery služby HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Přineste vlastní výpočetní prostředí
Existující výpočetním prostředí můžete zaregistrovat jako propojené služby ve službě Data Factory. Můžete spravovat výpočetním prostředí. Služba Data Factory výpočetním prostředí používá k provedení aktivity.

Tento typ konfigurace je podporována pro následující výpočetních prostředích:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL databáze, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure propojené služby HDInsight
Můžete vytvořit propojené služby HDInsight k registraci vlastní cluster HDInsight s Data Factory.

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
| type              | Nastavte vlastnost type na **HDInsight**. | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.        | Ano      |
| uživatelské jméno          | Název uživatelského účtu pro připojení k existujícímu clusteru HDInsight. | Ano      |
| heslo          | Heslo pro uživatelský účet.   | Ano      |
| linkedServiceName | Název úložiště, propojené služby, která odkazuje na objekt Blob úložiště používaný v clusteru HDInsight. <p>V současné době nelze zadat, že Data Lake Store propojené služby pro tuto vlastnost. Pokud HDInsight cluster má přístup do Data Lake Store, můžete může přistupovat k datům v Data Lake Store z Hive nebo Pig skriptů. </p> | Ano      |

## <a name="azure-batch-linked-service"></a>Azure Batch propojené služby
Můžete vytvořit Batch propojené služby pro registraci fondu služby Batch virtuálních počítačů (VM) do služby data factory. Vlastní aktivity rozhraní Microsoft .NET můžete spustit pomocí dávka nebo HDInsight.

Pokud začínáte pomocí služby Batch:

* Další informace o [základy Azure Batch](../../batch/batch-technical-overview.md).
* Další informace o [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) rutiny. Tuto rutinu použijte k vytvoření účtu Batch. Nebo můžete vytvořit účet Batch pomocí [portál Azure](../../batch/batch-account-create-portal.md). Podrobné informace o použití rutiny najdete v tématu [pomocí prostředí PowerShell ke správě účtu Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
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

Pro **accountName** vlastnost připojit **.\< Název oblasti\>**  na název vašeho účtu batch. Příklad:

```json
"accountName": "mybatchaccount.eastus"
```

Další možností je zajistit **batchUri** koncový bod. Příklad:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Nastavte vlastnost type na **AzureBatch**. | Ano      |
| název účtu       | Název účtu Batch.         | Ano      |
| accessKey         | Přístupový klíč pro účet Batch.  | Ano      |
| poolName          | Název fondu virtuálních počítačů.    | Ano      |
| linkedServiceName | Název úložiště, propojené služby, který je spojen s tuto dávku propojená služba. Tato propojená služba se používá pro pracovní soubory, které jsou nutné ke spuštění aktivity a k ukládání protokolů provádění aktivity. | Ano      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning propojené služby
Můžete vytvořit Machine Learning propojené služby pro registraci dávce Machine Learning vyhodnocovací koncový bod pro služby data factory.

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
| Typ       | Nastavte vlastnost type na **AzureML**. | Ano      |
| mlEndpoint | Adresu URL dávkového vyhodnocování.                   | Ano      |
| apiKey     | Rozhraní API modelu publikované prostoru.     | Ano      |

## <a name="azure-data-lake-analytics-linked-service"></a>Služba Azure Data Lake Analytics propojené
Můžete vytvořit službu Data Lake Analytics propojené výpočetní služba Data Lake Analytics propojení s objektem pro vytváření dat Azure. Data Lake Analytics U-SQL aktivitu v kanálu odkazuje na tato propojená služba. 

Následující tabulka popisuje obecné vlastnosti, které se používají v definici JSON:

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Nastavte vlastnost type na **AzureDataLakeAnalytics**. | Ano                                      |
| název účtu          | Název účtu Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Identifikátor URI Data Lake Analytics.           | Ne                                       |
| subscriptionId       | ID předplatného Azure.                    | Ne<br /><br />(Pokud není zadaný, předplatné objekt pro vytváření dat se používá.) |
| resourceGroupName    | Název skupiny prostředků Azure.                | Ne<br /><br /> (Pokud není zadaný, skupinu prostředků objekt pro vytváření dat se používá.) |

### <a name="authentication-options"></a>Možnosti ověřování
Data Lake Analytics propojené služby můžete zvolit ověřování pomocí objektu služby nebo přihlašovací údaje uživatele.

#### <a name="service-principal-authentication-recommended"></a>Objekt zabezpečení ověřování služby (doporučeno)
Pokud chcete používat ověřování hlavní služby, zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Potom udělte přístup k Azure AD do Data Lake Store. Podrobné pokyny najdete v tématu [Service-to-service ověřování](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Použijte objekt zabezpečení ověřování služby tak, že zadáte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID aplikace klienta.     | Ano      |
| servicePrincipalKey | Klíč aplikace.           | Ano      |
| tenant              | Klienta informace (název nebo klienta domény ID) kde se nachází aplikace. Chcete-li získat tyto informace, umístěte ukazatel myši v pravém horním rohu portálu Azure. | Ano      |

**Příkladu: Ověření objektu službu**
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

#### <a name="user-credential-authentication"></a>Ověření pověření uživatele
Pro ověření pověření uživatele pro Data Lake Analytics zadejte následující vlastnosti:

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| Autorizace | V editoru služby Data Factory, vyberte **Autorizovat** tlačítko. Zadejte pověření, které přiřadí URL pro autorizaci automaticky generovaný této vlastnosti. | Ano      |
| ID relace     | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a může být použit pouze jednou. Toto nastavení se automaticky generuje při pomocí editoru služby Data Factory. | Ano      |

**Příklad: Ověření pověření uživatele**
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
Autorizační kód, který jste vygenerovali výběrem **Autorizovat** tlačítko vyprší po uplynutí časového intervalu sady. 

Když vyprší platnost tokenu ověřování, může zobrazit následující chybová zpráva: 

  Přihlašovací údaje chyby operace: invalid_grant - AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS70008: Předloženému udělení přístupu je prošlý nebo odvolat. ID trasování: ID korelace d18629e8-af88-43c5-88e3-d8419eb1fca1: časové razítko fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

V následující tabulce jsou uvedeny platnosti podle typu účtu uživatele: 

| Typ uživatele                                | Platnost vyprší po                            |
| :--------------------------------------- | :--------------------------------------- |
| Uživatelské účty, které jsou *není* spravované službou Azure AD (Hotmail, živé atd.) | 12 hodin.                                 |
| Uživatelské účty, které *jsou* spravované službou Azure AD | 14 dnů po poslední řez spustit. <br /><br />90 dnů, pokud řez, který je založen na běží na základě OAuth propojené služby alespoň jednou za 14 dní. |

K zamezení nebo tuto chybu vyřešit, autorizujte výběrem **Autorizovat** tlačítko když vyprší platnost tokenu. Potom znovu nasaďte propojené služby. Můžete také vygenerovat hodnoty **sessionId** a **autorizace** vlastnosti programově pomocí následujícího kódu:

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

Podrobnosti o třídy objektu pro vytváření dat, které se používají v tomto příkladu kódu najdete v tématu:
* [Třída azuredatalakestorelinkedservice.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse – třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Přidat odkaz na Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro **WindowsFormsWebAuthenticationDialog** třídy. 

## <a name="azure-sql-linked-service"></a>Azure propojené služby SQL
Můžete vytvořit propojené služby SQL a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. Další informace najdete v tématu [konektor služby Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Služba Azure SQL Data Warehouse propojené
Můžete vytvořit SQL Data Warehouse propojené služby a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. Další informace najdete v tématu [konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Služba SQL Server propojené
Můžete vytvořit propojený systému SQL Server service a použít je s [aktivity uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. Další informace najdete v tématu [systému SQL Server konektoru](data-factory-sqlserver-connector.md#linked-service-properties).

