---
title: Výpočetní prostředí podporovaná Azure Data Factory
description: Přečtěte si o výpočetních prostředích, která můžete použít v kanálech Azure Data Factory (například Azure HDInsight) k transformaci nebo zpracování dat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281545"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaná Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Výpočetní propojené služby v .](../compute-linked-services.md)

Tento článek vysvětluje výpočetní prostředí, které můžete použít ke zpracování nebo transformaci dat. Poskytuje také podrobnosti o různých konfiguracích (na vyžádání versus přineste si vlastní), které data factory podporuje při konfiguraci propojených služeb, které propojují tato výpočetní prostředí s toto datové továrně Azure.

Následující tabulka obsahuje seznam výpočetních prostředí, které jsou podporovány data factory a aktivity, které můžete spustit na nich. 

| Výpočetní prostředí                      | Aktivity                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Úl](data-factory-hive-activity.md), [Prase](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [Dotnet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Datový sklad Azure SQL](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Aktivita Uložená procedura](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Verze HDInsight podporované v datové továrně
Azure HDInsight podporuje několik verzí clusteru Hadoop, které můžete kdykoli nasadit. Každá podporovaná verze vytvoří konkrétní verzi distribuce Hortonworks Data Platform (HDP) a sadu komponent v distribuci. 

Společnost Microsoft aktualizuje seznam podporovaných verzí HDInsight nejnovějšími součástmi a opravami ekosystému Hadoop. Podrobné informace naleznete v [tématu Podporované verze HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-based HDInsight verze 3.3 byl vyřazen červenec 31, 2017. Data Factory verze 1 on-demand HDInsight propojené služby zákazníci dostali až do prosince 15, 2017, k testování a upgradu na novější verzi HDInsight. Windows-based HDInsight bude vyřazena červenec 31, 2018.
>
> 

### <a name="after-the-retirement-date"></a>Po datu odchodu do důchodu 

prosince 2017:

- Clustery HDInsight verze 3.3 (nebo starší verze) založené na Linuxu již nelze vytvářet pomocí služby propojené s rozhraním HDInsight na vyžádání v datové továrně verze 1. 
- Pokud [vlastnosti **osType** a **Version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nejsou explicitně zadány v definici JSON pro existující propojenou službu HDInsight data factory verze 1 na vyžádání, výchozí hodnota se změní z **Version=3.1, osType=Windows** na **Version=\<nejnovější výchozí verze\>HDI (https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

po 31.

- Pomocí služby HDInsight na vyžádání ve službě Data Factory verze 1 již nelze vytvářet žádnou verzi clusterů HDInsight založených na systému Windows. 

### <a name="recommended-actions"></a>Doporučené akce 

- Chcete-li zajistit, že můžete používat nejnovější součásti a opravy ekosystému Hadoop, aktualizujte [vlastnosti **osType** a **Version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) ovlivněné datové továrny verze 1 1 na vyžádání hdinsight propojené definice služeb na novější linuxové verze HDInsight (HDInsight 3.6). 
- před 15. prosincem 2017 otestujte aktivity streamování data factory verze 1 Hive, Pig, MapReduce a Hadoop, které odkazují na ovlivněnou propojenou službu. Ujistěte se, že jsou kompatibilní s novými výchozími hodnotami **osType** a **Version** (**Version = 3.6**, **osType = Linux**) nebo explicitní verzí HDInsight a typem operačního systému, na který upgradujete. 
  Další informace o kompatibilitě najdete [v tématu Migrace z clusteru HDInsight založeného na windows do clusteru založeného na Linuxu](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) a [Co jsou komponenty a verze Hadoopu dostupné v HDInsightu?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) 
- Chcete-li pokračovat v používání propojené služby HDInsight založené na službě HDInsight z továrny na data, abyste vytvořili clustery HDInsight založené na Windows, explicitně nastavte **osType** na **Windows** před 15. Doporučujeme migrovat do clusterů HDInsight založených na Linuxu před 31. 
- Pokud používáte on-demand HDInsight propojené služby ke spuštění data factory verze 1 DotNet vlastní aktivity, aktualizujte DotNet vlastní aktivity JSON definice místo toho použít Azure Batch propojené služby. Další informace naleznete v [tématu Použití vlastních aktivit v kanálu data factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Pokud používáte stávající, přineste si vlastní cluster HDInsight propojené zařízení v Datové továrně verze 1 nebo přineste si vlastní a on-demand hdinsight propojené služby v Azure Data Factory, žádná akce je vyžadována. V těchto scénářích je již vynucena zásada podpory nejnovější verze clusterů HDInsight. 
>
> 


## <a name="on-demand-compute-environment"></a>Výpočetní prostředí na vyžádání
V konfiguraci na vyžádání Data Factory plně spravuje výpočetní prostředí. Data Factory automaticky vytvoří výpočetní prostředí před odesláním úlohy pro zpracování dat. Po dokončení úlohy Data Factory odebere výpočetní prostředí. 

Můžete vytvořit propojenou službu pro výpočetní prostředí na vyžádání. Propojenou službu slouží ke konfiguraci výpočetního prostředí a k řízení podrobných nastavení pro provádění úloh, správu clusteru a zaváděcí akce.

> [!NOTE]
> V současné době je konfigurace na vyžádání podporována pouze pro clustery HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Propojená služba Azure HDInsight na vyžádání
Data Factory může automaticky vytvořit cluster HDInsight založený na Windows nebo Linuxu pro zpracování dat. Cluster je vytvořen ve stejné oblasti jako účet úložiště, který je přidružen ke clusteru. K vytvoření clusteru použijte vlastnost JSON **linkedServiceName.**

Všimněte si následujících *klíčových* bodů o propojené službě HDInsight na vyžádání:

* Cluster HDInsight na vyžádání se ve vašem předplatném Azure nezobrazuje. Služba Data Factory spravuje cluster HDInsight na vyžádání vaším jménem.
* Protokoly pro úlohy, které jsou spuštěny v clusteru HDInsight na vyžádání, se zkopírují do účtu úložiště, který je přidružen ke clusteru HDInsight. Chcete-li získat přístup k těmto protokolům, přejděte na portálu Azure do podokna **Podrobnosti spuštění aktivity.** Další informace naleznete v [tématu Sledování a správa kanálů](data-factory-monitor-manage-pipelines.md).
* Poplatky se vám budou účtovat pouze za dobu, po kterou je cluster HDInsight spuštěn a spuštěn.

> [!IMPORTANT]
> Obvykle trvá *20 minut* nebo více zřídit cluster HDInsight na vyžádání.
>
> 

### <a name="example"></a>Příklad
Následující JSON definuje linuxovou službu propojenou hdinsight na vyžádání. Data Factory automaticky vytvoří *linuxový* cluster HDInsight, když zpracovává řez dat. 

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
> Cluster HDInsight vytvoří *výchozí kontejner* v úložišti objektů Blob Azure, který zadáte ve vlastnosti JSON **linkedServiceName.** Podle návrhu HDInsight neodstraní tento kontejner při odstranění clusteru. V propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba řez zpracovat, pokud neexistuje živý cluster **(timeToLive).** Cluster je odstraněn po dokončení zpracování. 
>
> Při zpracování dalších řezů uvidíte mnoho kontejnerů ve vašem úložišti objektů Blob. Pokud nepotřebujete kontejnery pro řešení potíží úlohy, můžete chtít odstranit kontejnery ke snížení nákladů na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Pomocí nástroje, jako je [Microsoft Storage Explorer,](https://storageexplorer.com/) můžete odstranit kontejnery v úložišti objektů Blob.
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Nastavte vlastnost type na **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet pracovních uzlů a datových uzlů v clusteru. Cluster HDInsight je vytvořen se 2 hlavní uzly, kromě počtu pracovních uzlů, které zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, která má 4 jádra. Cluster uzlů se 4 pracovníky přebírá 24 jader (4\*4 = 16 jader pro pracovní uzly plus 2\*4 = 8 jader pro hlavní uzly). Podrobnosti o Standard_D3 vrstvě najdete v tématu [Vytváření clusterů Hadoop založených na Linuxu v HDInsight .](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) | Ano      |
| timeToLive                   | Povolená doba nečinnosti clusteru HDInsight na vyžádání. Určuje, jak dlouho zůstane cluster HDInsight na vyžádání po dokončení spuštění aktivity naživu, pokud v clusteru nejsou žádné další aktivní úlohy.<br /><br />Například pokud spuštění aktivity trvá 6 minut a **timeToLive** je nastavena na 5 minut, cluster zůstane naživu po dobu 5 minut po 6 minut zpracování aktivity spustit. Pokud je v 6minutovém okně spuštěna jiná aktivita, je zpracována stejným clusterem.<br /><br />Vytvoření clusteru HDInsight na vyžádání je nákladná operace (může to chvíli trvat). Toto nastavení použijte podle potřeby ke zlepšení výkonu datové továrny opětovným použitím clusteru HDInsight na vyžádání.<br /><br />Pokud nastavíte hodnotu **timeToLive** na **hodnotu 0**, cluster se odstraní, jakmile dojde ke spuštění aktivity. Pokud však nastavíte vysokou hodnotu, může cluster zůstat nečinný, což zbytečně vede k vysokým nákladům. Je důležité nastavit příslušnou hodnotu na základě vašich potřeb.<br /><br />Pokud je správně nastavena hodnota **timeToLive,** může více kanálů sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| version                      | Verze clusteru HDInsight. Povolené verze HDInsight uvidíte [v tématu Podporované verze HDInsightu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Pokud tato hodnota není zadána, použije se [nejnovější výchozí verze HDI.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) | Ne       |
| linkedServiceName            | Propojená služba Azure Storage, kterou má cluster na vyžádání používat pro ukládání a zpracování dat. Cluster HDInsight se vytvoří ve stejné oblasti jako tento účet úložiště.<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledná data ze zpracování HDInsight v úložišti datových jezer, zkopírujte data z úložiště objektů Blob do úložiště Data Lake Store pomocí funkce Kopírovat aktivitu. </p> | Ano      |
| additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight. Data Factory registruje účty úložiště vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight. Cluster HDInsight se vytvoří ve stejné oblasti jako účet úložiště, který je určen vlastností **linkedServiceName.** | Ne       |
| osTyp                       | Typ operačního systému. Povolené hodnoty jsou **Linux** a **Windows**. Pokud tato hodnota není **zadána,** linux se používá.  <br /><br />Důrazně doporučujeme používat clustery HDInsight založené na Linuxu. Datum odchodu do důchodu pro HDInsight v systému Windows je červenec 31, 2018. | Ne       |
| hcatalogLinkedServiceName    | Název propojené služby Azure SQL, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání je vytvořen pomocí databáze SQL jako metastore. | Ne       |

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
| coreKonfigurace      | Určuje základní konfigurační parametry (core-site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hKonfigurace základny     | Určuje konfigurační parametry HBase (hbase-site.xml) pro cluster HDInsight. | Ne       |
| hdfsKonfigurace      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro cluster HDInsight. | Ne       |
| hiveKonfigurace      | Určuje konfigurační parametry Hive (hive-site.xml) pro cluster HDInsight. | Ne       |
| mapReduceKonfigurace | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro cluster HDInsight. | Ne       |
| oozieKonfigurace     | Určuje konfigurační parametry Oozie (oozie-site.xml) pro cluster HDInsight. | Ne       |
| stormKonfigurace     | Určuje parametry konfigurace Storm (storm-site.xml) pro cluster HDInsight. | Ne       |
| yarnKonfigurace      | Určuje parametry konfigurace YARN (yarn-site.xml) pro cluster HDInsight. | Ne       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad: Konfigurace clusteru HDInsight na vyžádání s rozšířenými vlastnostmi

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
Chcete-li určit velikost uzlů hlavy, dat a ZooKeeper, použijte následující vlastnosti: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Nastaví velikost hlavního uzlu. Výchozí hodnota je **Standard_D3**. Podrobnosti naleznete v [tématu Specify node sizes](#specify-node-sizes). | Ne       |
| dataNodeSize      | Nastaví velikost datového uzlu. Výchozí hodnota je **Standard_D3**. | Ne       |
| zookeeperNodeVelikost | Nastaví velikost uzlu ZooKeeper. Výchozí hodnota je **Standard_D3**. | Ne       |

#### <a name="specify-node-sizes"></a>Určení velikostí uzlů
Hodnoty řetězců, které je nutné zadat pro vlastnosti popsané v předchozí části, naleznete v [tématu Velikosti virtuálních strojů](../../virtual-machines/linux/sizes.md). Hodnoty musí odpovídat rutinám a apim, na které se odkazuje ve [velikostech virtuálních strojů](../../virtual-machines/linux/sizes.md). Velikost datového uzlu Velké (výchozí) má 7 GB paměti. To nemusí být dostatečné pro váš scénář. 

Pokud chcete vytvořit hlavní uzly a pracovní uzly velikosti D4, zadejte **Standard_D4** jako hodnotu vlastností **headNodeSize** a **dataNodeSize:** 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud pro tyto vlastnosti nastavíte nesprávnou hodnotu, může se zobrazit následující zpráva:

  Vytvoření clusteru se nezdařilo. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: 'PreClusterCreationValidationFailure'. 
  
Pokud se zobrazí tato zpráva, ujistěte se, že používáte rutinu a názvy rozhraní API z tabulky ve [velikostech virtuálních počítačů](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> V současné době Data Factory nepodporuje HDInsight clustery, které používají Data Lake Store jako primární úložiště. Azure Storage použijte jako primární úložiště pro clustery HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Přineste si vlastní výpočetní prostředí
Můžete zaregistrovat existující výpočetní prostředí jako propojené služby v Datové továrně. Spravujete výpočetní prostředí. Služba Data Factory používá výpočetní prostředí ke spuštění aktivit.

Tento typ konfigurace je podporován pro následující výpočetní prostředí:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Datový sklad Azure SQL, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Propojená služba Azure HDInsight
Můžete vytvořit propojenou službu HDInsight a zaregistrovat vlastní cluster HDInsight pomocí data factory.

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
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Nastavte vlastnost type na **HDInsight**. | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.        | Ano      |
| uživatelské jméno          | Název uživatelského účtu, který se má použít pro připojení k existujícímu clusteru HDInsight. | Ano      |
| heslo          | Heslo pro uživatelský účet.   | Ano      |
| linkedServiceName | Název propojené služby úložiště, která odkazuje na úložiště objektů Blob používané clusterem HDInsight. <p>V současné době nelze zadat propojené služby Úložiště datových jezer pro tuto službu. Pokud má cluster HDInsight přístup k úložišti Data Lake Store, můžete přistupovat k datům v úložišti Data Lake Store ze skriptů Hive nebo Pig. </p> | Ano      |

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch
Můžete vytvořit dávkově propojenou službu pro registraci fondu dávek virtuálních počítačů (VM) do datové továrny. Vlastní aktivity microsoftu .NET můžete spustit pomocí služby Batch nebo HDInsight.

Pokud s používáním dávkové služby začínáte:

* Další informace o [základech Azure Batch](../../batch/batch-technical-overview.md).
* Další informace o rutině [New-AzureBatchAccount.](https://msdn.microsoft.com/library/mt125880.aspx) Tato rutina slouží k vytvoření dávkového účtu. Nebo můžete vytvořit účet Batch pomocí [portálu Azure](../../batch/batch-account-create-portal.md). Podrobné informace o použití rutiny najdete [v tématu Správa účtu Batch pomocí prostředí PowerShell](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Další informace o rutině [New-AzureBatchPool.](https://msdn.microsoft.com/library/mt125936.aspx) Pomocí této rutiny vytvořte fond dávek.

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

Pro vlastnost **accountName** připojte **.\< název\> oblasti** na název dávkového účtu. Například:

```json
"accountName": "mybatchaccount.eastus"
```

Další možností je poskytnout koncový bod **batchUri.** Například:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Nastavte vlastnost type na **AzureBatch**. | Ano      |
| accountName       | Název účtu Batch.         | Ano      |
| Accesskey         | Přístupový klíč pro účet Batch.  | Ano      |
| název_fondu          | Název fondu virtuálních her.    | Ano      |
| linkedServiceName | Název propojené služby úložiště, která je přidružena k této službě batch propojené. Tato propojená služba se používá pro pracovní soubory, které jsou nutné ke spuštění aktivity a k ukládání protokolů provádění aktivit. | Ano      |

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning
Můžete vytvořit propojenou službu Machine Learning pro registraci koncového bodu dávkového bodování Machine Learning do datové továrny.

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
| Vlastnost   | Popis                              | Požaduje se |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Nastavte vlastnost type na **AzureML**. | Ano      |
| mlEndpoint | Adresa URL dávkového bodování.                   | Ano      |
| apiKey     | Rozhraní API publikovaného modelu pracovního prostoru.     | Ano      |

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Můžete vytvořit propojenou službu Data Lake Analytics, která propojí výpočetní službu Data Lake Analytics s toto datovou továrnou Azure. Aktivita U-SQL služby Data Lake Analytics v kanálu odkazuje na tuto propojenou službu. 

Následující tabulka popisuje obecné vlastnosti, které se používají v definici JSON:

| Vlastnost                 | Popis                              | Požaduje se                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Nastavte vlastnost type na **AzureDataLakeAnalytics**. | Ano                                      |
| accountName          | Název účtu Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Identifikátor URI analýzy datového jezera.           | Ne                                       |
| subscriptionId       | ID předplatného Azure.                    | Ne<br /><br />(Pokud není zadán, odběr datové továrny se používá.) |
| resourceGroupName    | Název skupiny prostředků Azure.                | Ne<br /><br /> (Pokud není zadán, použije se skupina prostředků datové továrny.) |

### <a name="authentication-options"></a>Možnosti ověřování
Pro propojenou službu Data Lake Analytics si můžete vybrat mezi ověřováním pomocí instančního objektu nebo pověření uživatele.

#### <a name="service-principal-authentication-recommended"></a>Ověření instančního objektu (doporučeno)
Pokud chcete použít ověřování o jistině služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD). Pak udělte Azure AD přístup k úložiště datových jezer. Podrobné kroky naleznete v [tématu Ověřování mezi službami](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:
* ID aplikace
* Klíč aplikace 
* ID tenanta

Pomocí ověřování instančního objektu zadejte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID klienta aplikace.     | Ano      |
| servicePrincipalKey | Klíč aplikace.           | Ano      |
| Nájemce              | Informace o klientovi (název domény nebo ID klienta), kde je vaše aplikace umístěna. Chcete-li získat tyto informace, najeďte myší v pravém horním rohu portálu Azure. | Ano      |

**Příklad: Ověřování instančního objektu**
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

#### <a name="user-credential-authentication"></a>Ověřování pověření uživatele
Pro ověřování přihlašovacích údajů uživatele pro data Lake Analytics zadejte následující vlastnosti:

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| autorizace | V Editoru datových tovev vyberte tlačítko **Autorizovat.** Zadejte pověření, které této vlastnosti přiřadí automaticky vygenerovanou adresu URL autorizace. | Ano      |
| Sessionid     | ID relace OAuth z autorizační relace OAuth. Každé ID relace je jedinečné a lze jej použít pouze jednou. Toto nastavení se automaticky generuje při použití Editoru datových tocích. | Ano      |

**Příklad: Ověřování pověření uživatele**
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
Autorizační kód, který jste vygenerovali výběrem tlačítka **Autorizovat,** vyprší po nastaveném intervalu. 

Po vypršení platnosti ověřovacího tokenu se může zobrazit následující chybová zpráva: 

  Chyba operace pověření: invalid_grant - AADSTS70002: Chyba při ověřování pověření. AADSTS70008: Zadaný přístupový grant vypršel nebo byl odvolán. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korelační ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Časové razítko: 2015-12-15 21:09:31Z

V následující tabulce jsou uvedeny vypršení platnosti podle typu uživatelského účtu: 

| Typ uživatele                                | Platnost vyprší po                            |
| :--------------------------------------- | :--------------------------------------- |
| Uživatelské účty, které *nejsou* spravované službou Azure AD (Hotmail, Live a tak dále) | 12 hodin.                                 |
| Uživatelské účty *spravované* službou Azure AD | 14 dní po posledním spuštění řezu. <br /><br />90 dní, pokud řez založený na propojené službě založené na OAuth běží alespoň jednou za 14 dní. |

Chcete-li se této chybě vyhnout nebo ji vyřešit, znovu autorizacijte výběrem tlačítka **Autorizovat** po vypršení platnosti tokenu. Potom znovu nasadit propojené služby. Můžete také generovat hodnoty pro **sessionId** a **autorizace** vlastnosti programově pomocí následujícího kódu:

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

Podrobnosti o třídách Data Factory, které se používají v tomto příkladu kódu, naleznete v následujících tématech:
* [Třída AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Třída AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Třída AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Přidejte odkaz na soubor Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pro třídu **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Propojená služba Azure SQL
Můžete vytvořit službu propojenou sql a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu data factory. Další informace naleznete v tématu [Konektor Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Propojená služba Azure SQL Data Warehouse
Můžete vytvořit propojenou službu sql data warehouse a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu datové továrny. Další informace najdete v [tématu Konektor Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Propojená služba SQL Server
Můžete vytvořit propojenou službu SQL Server a použít ji s [aktivitou uložené procedury](data-factory-stored-proc-activity.md) k vyvolání uložené procedury z kanálu data factory. Další informace naleznete v tématu [KONEKTOR SERVERU SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

