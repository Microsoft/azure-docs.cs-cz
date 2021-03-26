---
title: Výpočetní prostředí podporovaná nástrojem Azure Data Factory
description: Výpočetní prostředí, která se dají použít s Azure Data Factory kanály (například Azure HDInsight) k transformaci nebo zpracování dat.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 05/08/2019
ms.openlocfilehash: 2aa7afebede3759221674c48dd66256e740575a3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565941"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaná nástrojem Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto článku se dozvíte o různých výpočetních prostředích, která můžete použít ke zpracování nebo transformaci dat. Poskytuje taky podrobnosti o různých konfiguracích (na vyžádání a přineste si vlastní) podporované Data Factory při konfiguraci propojených služeb, které propojuje tato výpočetní prostředí s objektem pro vytváření dat Azure.

Následující tabulka uvádí seznam výpočetních prostředí podporovaných Data Factory a aktivit, které na nich můžou běžet. 

| Výpočetní prostředí                                          | Aktivity                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [Podregistr](transform-data-using-hadoop-hive.md), [prase](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [streamování Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Vlastní](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klasický)](#azure-machine-learning-studio-classic-linked-service) | [Machine Learning Studio (klasické) aktivity: dávkové provádění a aktualizace prostředku](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning spustit kanál](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Uložená procedura](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Poznámkový blok](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Funkce Azure Functions](#azure-function-linked-service)         | [Aktivita funkce Azure](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>Výpočetní prostředí HDInsight

Podrobnosti o podporovaných typech propojených služeb úložiště pro konfiguraci na vyžádání a BYOC (Přineste si vlastní výpočetní prostředí) najdete v níže uvedené tabulce.

| V propojené službě COMPUTE | Název vlastnosti                | Description                                                  | Objekt blob | ADLS Gen2 | Azure SQL DB | ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| Na vyžádání                 | linkedServiceName            | Azure Storage propojená služba, kterou má cluster na vyžádání použít k ukládání a zpracování dat. | Yes  | Yes       | No           | No         |
|                           | additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. | Yes  | No        | No           | No         |
|                           | hcatalogLinkedServiceName    | Název propojené služby Azure SQL, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí Azure SQL Database jako metastore. | No   | No        | Yes          | No         |
| BYOC                      | linkedServiceName            | Odkaz na propojenou službu Azure Storage.                | Yes  | Yes       | No           | No         |
|                           | additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. | No   | No        | No           | No         |
|                           | hcatalogLinkedServiceName    | Odkaz na propojenou službu Azure SQL, která odkazuje na databázi HCatalog. | No   | No        | No           | No         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Propojená služba Azure HDInsight na vyžádání

V tomto typu konfigurace je výpočetní prostředí plně spravované službou Azure Data Factory. Služba Data Factory ji automaticky vytvoří před odesláním úlohy ke zpracování dat a po jejím dokončení se odstraní. Můžete vytvořit propojenou službu pro výpočetní prostředí na vyžádání, nakonfigurovat ji a řídit detailní nastavení pro provádění úloh, správu clusterů a spouštěcí akce.

> [!NOTE]
> Konfigurace na vyžádání se v současné době podporuje jenom u clusterů Azure HDInsight. Azure Databricks podporuje také úlohy na vyžádání s využitím clusterů úloh. Další informace najdete v tématu [propojená služba Azure datacihly](#azure-databricks-linked-service).

Služba Azure Data Factory může automaticky vytvořit cluster HDInsight na vyžádání pro zpracování dat. Cluster se vytvoří ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName ve formátu JSON) přidružený ke clusteru. Účet úložiště `must` je účet standardního Azure Storage pro obecné účely. 

Všimněte si následujících **důležitých** bodů pro propojenou službu HDInsight na vyžádání:

* Cluster HDInsight na vyžádání se vytvoří v rámci vašeho předplatného Azure. Cluster můžete v Azure Portal zobrazit, když je cluster spuštěný a spuštěný. 
* Protokoly pro úlohy, které jsou spuštěné na clusteru HDInsight na vyžádání, se zkopírují do účtu úložiště přidruženého ke clusteru HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definované v definici propojené služby se používají pro přihlášení ke clusteru pro podrobné řešení potíží během životního cyklu clusteru. 
* Účtují se vám jenom čas, kdy je cluster HDInsight v provozu a spouští úlohy.
* Můžete použít **akci skriptu** s propojenou službou Azure HDInsight na vyžádání.  

> [!IMPORTANT]
> Zřizování clusteru Azure HDInsight na vyžádání obvykle trvá **20 minut** nebo déle.

#### <a name="example"></a>Příklad

Následující JSON definuje propojenou službu HDInsight na vyžádání v systému Linux. Služba Data Factory pro zpracování požadované aktivity automaticky vytvoří cluster HDInsight se **systémem Linux** . 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Cluster HDInsight vytvoří **výchozí kontejner** v úložišti objektů blob, které jste zadali ve formátu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní. 
>
> V případě více spuštění aktivit se v úložišti objektů BLOB v Azure zobrazuje spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Pomocí nástrojů, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/) , odstraňte kontejnery ve službě Azure Blob Storage.

#### <a name="properties"></a>Vlastnosti

| Vlastnost                     | Popis                              | Povinné |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Vlastnost Type by měla být nastavená na **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Počet uzlů pracovních procesů nebo datových uzlů v clusteru. Cluster HDInsight se vytvoří s 2 hlavními uzly spolu s počtem pracovních uzlů, které pro tuto vlastnost zadáte. Uzly mají velikost Standard_D3 se 4 jádry, takže cluster se čtyřmi pracovními uzly má 24 jader (4 \* 4 = 16 jader pro pracovní uzly a 2 \* 4 = 8 jader pro hlavní uzly). Podrobnosti najdete v tématu [Nastavení clusterů v HDInsight se systémem Hadoop, Spark, Kafka a další](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . | Yes      |
| linkedServiceName            | Azure Storage propojená služba, kterou má cluster na vyžádání použít k ukládání a zpracování dat. Cluster HDInsight se vytvoří ve stejné oblasti jako tento účet Azure Storage. Pro Azure HDInsight platí omezení celkového počtu jader, která můžete v jednotlivých podporovaných oblastech Azure použít. Ujistěte se, že je v oblasti Azure dostatek základních kvót, aby splňovaly požadované clusterSize. Podrobnosti najdete [v tématu Nastavení clusterů v HDInsight se systémem Hadoop, Spark, Kafka a dalšími](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) .<p>V současné době nemůžete vytvořit cluster HDInsight na vyžádání, který jako úložiště používá Azure Data Lake Storage (Gen 2). Pokud chcete uložit výsledná data ze zpracování HDInsight v Azure Data Lake Storage (Gen 2), zkopírujte pomocí aktivity kopírování data z Azure Blob Storage do Azure Data Lake Storage (Gen 2). </p> | Yes      |
| clusterResourceGroup         | Cluster HDInsight se vytvoří v této skupině prostředků. | Yes      |
| TimeToLive                   | Povolený čas nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho zůstane cluster HDInsight na vyžádání aktivní po dokončení spuštění aktivity, pokud v clusteru nejsou žádné další aktivní úlohy. Minimální povolená hodnota je 5 minut (00:05:00).<br/><br/>Pokud například spuštění aktivity trvá 6 minut a TimeToLive je nastaveno na 5 minut, zůstane cluster aktivní po dobu 5 minut po 6 minutách zpracování spuštění aktivity. Pokud je spuštěný jiný běh aktivity s oknem o 6 minut, zpracuje ho stejný cluster.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je náročná operace (může chvíli trvat), proto toto nastavení použijte, pokud chcete zlepšit výkon objektu pro vytváření dat, a to tak, že znovu použijete cluster HDInsight na vyžádání.<br/><br/>Pokud nastavíte hodnotu TimeToLive na 0, cluster se odstraní hned po dokončení spuštění aktivity. Pokud nastavíte vysokou hodnotu, cluster může zůstat nečinný, abyste se mohli přihlásit k nějakému účelu řešení potíží, ale může to mít za následek vysoké náklady. Proto je důležité nastavit odpovídající hodnotu podle svých potřeb.<br/><br/>Pokud je hodnota vlastnosti TimeToLive správně nastavená, může více kanálů sdílet instanci clusteru HDInsight na vyžádání. | Yes      |
| clusterType                  | Typ clusteru HDInsight, který se má vytvořit Povolené hodnoty jsou Hadoop a Spark. Pokud není zadaný, použije se výchozí hodnota Hadoop. Cluster s povoleným Balíček zabezpečení podniku nejde vytvořit na vyžádání, místo toho použijte [existující cluster nebo využijte vlastní výpočetní](#azure-hdinsight-linked-service)prostředky. | No       |
| verze                      | Verze clusteru HDInsight. Pokud není zadaný, použije se aktuálně definovaná výchozí verze HDInsight. | No       |
| hostSubscriptionId           | ID předplatného Azure, které se používá k vytvoření clusteru HDInsight. Pokud není zadaný, použije se ID předplatného vašeho přihlašovacího kontextu Azure. | No       |
| clusterNamePrefix           | Předpona názvu clusteru HDI, časové razítko se automaticky připojí na konec názvu clusteru.| No       |
| sparkVersion                 | Verze Sparku, pokud je typ clusteru "Spark" | No       |
| additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight, který je vytvořený ve stejné oblasti jako účet úložiště zadaný pomocí linkedServiceName. | No       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou: Linux a Windows (jenom pro HDInsight 3,3). Výchozí hodnota je Linux. | No       |
| hcatalogLinkedServiceName    | Název propojené služby Azure SQL, která odkazuje na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí Azure SQL Database jako metastore. | No       |
| connectVia                   | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby HDInsight. Pro propojenou službu HDInsight na vyžádání podporuje jenom Azure Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No       |
| clusterUserName                   | Uživatelské jméno pro přístup ke clusteru. | No       |
| clusterPassword                   | Heslo v typu zabezpečeného řetězce pro přístup ke clusteru | No       |
| clusterSshUserName         | Uživatelské jméno pro SSH se vzdáleně připojuje k uzlu clusteru (pro Linux). | No       |
| clusterSshPassword         | Heslo v typu zabezpečeného řetězce pro vzdálené připojení protokolu SSH k uzlu clusteru (pro Linux). | No       |
| Nenašly | Zadejte skript pro [přizpůsobení clusteru HDInsight](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) během vytváření clusteru na vyžádání. <br />V současné době Nástroj pro tvorbu uživatelského rozhraní Azure Data Factory podporuje zadání pouze 1 akce skriptu, ale toto omezení můžete obdržet ve formátu JSON (zadat více akcí skriptu ve formátu JSON). | No |


> [!IMPORTANT]
> Služba HDInsight podporuje více verzí clusteru Hadoop, které lze nasadit. Volba každé verze vytvoří konkrétní verzi distribuce Hortonworks data Platform (HDP) a sadu součástí, které jsou obsaženy v rámci této distribuce. Seznam podporovaných verzí HDInsight se aktualizuje, aby poskytoval nejnovější součásti a opravy ekosystému Hadoop. Nezapomeňte vždy použít nejnovější informace o [podporované verzi HDInsight a typu operačního systému](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) , abyste měli jistotu, že používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> Propojené služby HDInsight v současné době nepodporují adaptéry HBA, interaktivní dotazy (LLAP podregistru), zaplavení. 

* Příklad additionalLinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Propojená služba HDInsight na vyžádání vyžaduje k vytvoření clusterů HDInsight vaším jménem ověřování instančního objektu. Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí roli **přispěvatele** předplatného nebo skupiny prostředků, ve které se cluster HDInsight vytvoří. Podrobný postup najdete v tématu [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Použijte ověřování instančního objektu zadáním následujících vlastností:

| Vlastnost                | Popis                              | Povinné |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta aplikace.     | Yes      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Yes      |
| **tenant**              | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Yes      |

#### <a name="advanced-properties"></a>Upřesnit vlastnosti

Pro podrobnou konfiguraci clusteru HDInsight na vyžádání můžete také zadat následující vlastnosti.

| Vlastnost               | Popis                              | Povinné |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje základní konfigurační parametry (jako v core-site.xml) pro vytvoření clusteru HDInsight. | No       |
| hBaseConfiguration     | Určuje konfigurační parametry HBA (hbase-site.xml) pro cluster HDInsight. | No       |
| hdfsConfiguration      | Určuje konfigurační parametry HDFS (hdfs-site.xml) pro cluster HDInsight. | No       |
| hiveConfiguration      | Určuje parametry konfigurace podregistru (hive-site.xml) pro cluster HDInsight. | No       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro cluster HDInsight. | No       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro cluster HDInsight. | No       |
| stormConfiguration     | Určuje parametry konfigurace zaplavení (storm-site.xml) pro cluster HDInsight. | No       |
| yarnConfiguration      | Určuje konfigurační parametry příze (yarn-site.xml) pro cluster HDInsight. | No       |

* Příklad – konfigurace clusteru HDInsight na vyžádání s pokročilými vlastnostmi

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

#### <a name="node-sizes"></a>Velikosti uzlů
Velikosti uzlů Head, data a Zookeeper můžete zadat pomocí následujících vlastností: 

| Vlastnost          | Popis                              | Povinné |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Určuje velikost hlavního uzlu. Výchozí hodnota je: Standard_D3. Podrobnosti najdete v části **Určení velikosti uzlů** . | No       |
| dataNodeSize      | Určuje velikost datového uzlu. Výchozí hodnota je: Standard_D3. | No       |
| zookeeperNodeSize | Určuje velikost uzlu v police pro sadu zoolog. Výchozí hodnota je: Standard_D3. | No       |

* Určení velikosti uzlů viz [velikosti Virtual Machines](../virtual-machines/sizes.md) článku pro řetězcové hodnoty, které potřebujete zadat pro vlastnosti uvedené v předchozí části. Hodnoty musí splňovat **rutiny & rozhraní API** , na které se odkazuje v článku. Jak vidíte v článku, má datový uzel velké (výchozí) velikosti velikost 7 GB paměti, což pro váš scénář nemusí být dostatečné. 

Pokud chcete vytvořit hlavní uzly velikosti D4 a pracovní uzly, zadejte **Standard_D4** jako hodnotu pro vlastnosti HeadNodeSize a dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud pro tyto vlastnosti zadáte chybnou hodnotu, může se zobrazit následující **Chyba:** nepovedlo se vytvořit cluster. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: ' PreClusterCreationValidationFailure '. Když se zobrazí tato chyba, ujistěte se, že používáte **rutinu & název rozhraní API** z tabulky ve [velikosti Virtual Machines](../virtual-machines/sizes.md) článku.

### <a name="bring-your-own-compute-environment"></a>Přineste si vlastní výpočetní prostředí
V tomto typu konfigurace můžou uživatelé zaregistrovat už existující výpočetní prostředí jako propojenou službu v Data Factory. Výpočetní prostředí spravuje uživatel a služba Data Factory ji používá ke spuštění aktivit.

Tento typ konfigurace se podporuje pro následující výpočetní prostředí:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Propojená služba Azure HDInsight
Můžete vytvořit propojenou službu Azure HDInsight a zaregistrovat si vlastní cluster HDInsight s Data Factory.

### <a name="example"></a>Příklad

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                                                  | Povinné |
| ----------------- | ------------------------------------------------------------ | -------- |
| typ              | Vlastnost Type by měla být nastavená na **HDInsight**.            | Yes      |
| clusterUri        | Identifikátor URI clusteru HDInsight.                            | Yes      |
| username          | Zadejte jméno uživatele, který se má použít pro připojení k existujícímu clusteru HDInsight. | Yes      |
| heslo          | Zadejte heslo pro uživatelský účet.                       | Yes      |
| linkedServiceName | Název propojené služby Azure Storage, která odkazuje na úložiště objektů BLOB v Azure používané clusterem HDInsight. <p>V současné době nelze pro tuto vlastnost zadat propojenou službu Azure Data Lake Storage (Gen 2). Pokud má cluster HDInsight přístup k Data Lake Store, můžete k datům v Azure Data Lake Storage (Gen 2) přistupovat ze skriptů z podregistru nebo vepřového masa. </p> | Yes      |
| isEspEnabled      | Pokud je cluster HDInsight [balíček zabezpečení podniku](../hdinsight/domain-joined/apache-domain-joined-architecture.md) povolený, zadejte *hodnotu true*. Výchozí hodnota je *false (NEPRAVDA*). | No       |
| connectVia        | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby. Můžete použít Azure Integration Runtime nebo místní Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. <br />U clusteru HDInsight s povoleným Balíček zabezpečení podniku (ESP) se používá místní prostředí Integration runtime, které má na clusteru řadu pohledů, nebo by se mělo nasadit do stejného Virtual Network jako cluster protokolu ESP HDInsight. | No       |

> [!IMPORTANT]
> Služba HDInsight podporuje více verzí clusteru Hadoop, které lze nasadit. Volba každé verze vytvoří konkrétní verzi distribuce Hortonworks data Platform (HDP) a sadu součástí, které jsou obsaženy v rámci této distribuce. Seznam podporovaných verzí HDInsight se aktualizuje, aby poskytoval nejnovější součásti a opravy ekosystému Hadoop. Nezapomeňte vždy použít nejnovější informace o [podporované verzi HDInsight a typu operačního systému](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) , abyste měli jistotu, že používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> Propojené služby HDInsight v současné době nepodporují adaptéry HBA, interaktivní dotazy (LLAP podregistru), zaplavení. 
>
> 

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete vytvořit propojenou službu Azure Batch k registraci fondu služby Batch virtuálních počítačů do objektu pro vytváření dat. Vlastní aktivitu můžete spustit pomocí Azure Batch.

Pokud Azure Batch službu nepoužíváte, přečtěte si následující články:

* [Azure Batch základy](../batch/batch-technical-overview.md) pro přehled služby Azure Batch.
* Rutina [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) pro vytvoření účtu Azure Batch (nebo) [Azure Portal](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí Azure Portal. Podrobné pokyny k používání rutiny najdete v článku [použití PowerShellu ke správě Azure Batch účtu](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) .
* Rutina [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) pro vytvoření fondu Azure Batch.

> [!IMPORTANT]
> Při vytváření nového fondu Azure Batch musí být použit ' VirtualMachineConfiguration ' a ne ' CloudServiceConfiguration '. Další podrobnosti najdete v tématu [pokyny k migraci fondu Azure Batch](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md). 

### <a name="example"></a>Příklad

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Vlastnosti
| Vlastnost          | Popis                              | Povinné |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Vlastnost Type by měla být nastavená na **AzureBatch**. | Yes      |
| accountName       | Název Azure Batch účtu         | Yes      |
| accessKey         | Přístupový klíč pro účet Azure Batch  | Yes      |
| batchUri          | Adresa URL účtu Azure Batch ve formátu https://*batchaccountname. region*. batch.Azure.com. | Yes      |
| poolName          | Název fondu virtuálních počítačů.    | Yes      |
| linkedServiceName | Název propojené služby Azure Storage přidružené k této Azure Batch propojené službě. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity. | Yes      |
| connectVia        | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby. Můžete použít Azure Integration Runtime nebo místní Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Propojená služba Azure Machine Learning Studio (Classic)
Vytvoříte propojenou službu Azure Machine Learning Studio (Classic) k registraci koncového bodu vyhodnocování Machine Learning Studio (Classic) dávkového vyhodnocování pro datovou továrnu.

### <a name="example"></a>Příklad

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Vlastnosti
| Vlastnost               | Popis                              | Povinné                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Vlastnost Type by měla být nastavená na: **AzureML**. | Yes                                      |
| mlEndpoint             | Adresa URL dávkového vyhodnocování                   | Yes                                      |
| apiKey                 | Rozhraní API modelu publikovaného pracovního prostoru.     | Yes                                      |
| updateResourceEndpoint | Adresa URL prostředku aktualizace pro koncový bod webové služby Azure Machine Learning Studio (Classic), který se používá k aktualizaci prediktivní webové služby pomocí souboru trained model | No                                       |
| servicePrincipalId     | Zadejte ID klienta aplikace.     | Vyžaduje se, pokud je zadaný updateResourceEndpoint. |
| servicePrincipalKey    | Zadejte klíč aplikace.           | Vyžaduje se, pokud je zadaný updateResourceEndpoint. |
| tenant                 | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Vyžaduje se, pokud je zadaný updateResourceEndpoint. |
| connectVia             | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby. Můžete použít Azure Integration Runtime nebo místní Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No                                       |

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning
Vytvoříte propojenou službu Azure Machine Learning pro připojení pracovního prostoru Azure Machine Learning k datové továrně.

> [!NOTE]
> V současné době je pro propojenou službu Azure Machine Learning podporována pouze ověřování instančního objektu.

### <a name="example"></a>Příklad

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Vlastnosti

| Vlastnost               | Popis                              | Povinné                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Vlastnost Type by měla být nastavená na: **AzureMLService**. | Yes                                      |
| subscriptionId         | ID předplatného Azure              | Yes                                      |
| resourceGroupName      | name | Yes                                      |
| mlWorkspaceName        | Název pracovního prostoru Azure Machine Learning | Yes  |
| servicePrincipalId     | Zadejte ID klienta aplikace.     | No |
| servicePrincipalKey    | Zadejte klíč aplikace.           | No |
| tenant                 | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Vyžaduje se, pokud je zadaný updateResourceEndpoint. |
| connectVia             | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby. Můžete použít Azure Integration Runtime nebo místní Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No |

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics** pro propojení Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

### <a name="example"></a>Příklad

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Vlastnosti

| Vlastnost             | Popis                              | Povinné                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| typ                 | Vlastnost Type by měla být nastavená na: **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Azure Data Lake Analytics název účtu.  | Yes                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics identifikátor URI.           | No                                       |
| subscriptionId       | ID předplatného Azure                    | No                                       |
| resourceGroupName    | Název skupiny prostředků Azure                | No                                       |
| servicePrincipalId   | Zadejte ID klienta aplikace.     | Yes                                      |
| servicePrincipalKey  | Zadejte klíč aplikace.           | Yes                                      |
| tenant               | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Yes                                      |
| connectVia           | Integration Runtime, která se má použít k odeslání aktivit do této propojené služby. Můžete použít Azure Integration Runtime nebo místní Integration Runtime. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No                                       |



## <a name="azure-databricks-linked-service"></a>Propojená služba Azure Databricks
Můžete vytvořit **propojenou službu Azure Databricks** k registraci pracovního prostoru datacihly, který používáte ke spouštění úloh datacihly (Poznámkový blok, JAR, Python). 
> [!IMPORTANT]
> Propojené služby datacihly podporují [fondy instancí](https://aka.ms/instance-pools) & ověřování spravované identity přiřazené systémem.

### <a name="example---using-new-job-cluster-in-databricks"></a>Příklad – použití nového clusteru úloh v datacihlech

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Příklad: použití existujícího interaktivního clusteru v datacihlách

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Vlastnosti

| Vlastnost             | Popis                              | Vyžadováno                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Název propojené služby               | Yes   |
| typ                 | Vlastnost Type by měla být nastavená na: **Azure Databricks**. | Yes                                      |
| doména               | Podle oblasti pracovního prostoru datacihly určete příslušné oblasti Azure. Příklad: https://eastus.azuredatabricks.net | Yes                                 |
| accessToken          | Pro Data Factory ověřování pro Azure Databricks je vyžadován přístupový token. Přístupový token se musí vygenerovat z pracovního prostoru datacihly. Podrobnější kroky, jak najít přístupový token, najdete [tady](/azure/databricks/dev-tools/api/latest/authentication#generate-token) .  | No                                       |
| MSI          | K ověření Azure Databricks použijte spravovanou identitu Data Factory (přiřazenou systémem). Přístupový token nepotřebujete při použití ověřování MSI.  | No                                       |
| existingClusterId    | ID clusteru existujícího clusteru, ve kterém se mají spouštět všechny úlohy. Mělo by se jednat o již vytvořený interaktivní cluster. Pokud přestane reagovat, může být nutné cluster restartovat ručně. Datacihly navrhují spuštěné úlohy na nových clusterech kvůli větší spolehlivosti. ID clusteru interaktivního clusteru můžete najít v pracovním prostoru datacihly – > clustery – > interaktivní název clusteru – > značky konfigurace >. [Další podrobnosti](https://docs.databricks.com/user-guide/clusters/tags.html) | No 
| instancePoolId    | ID fondu instancí existujícího fondu v pracovním prostoru datacihly  | No  |
| newClusterVersion    | Verze Sparku clusteru Vytvoří cluster úloh v datacihlách. | No  |
| newClusterNumOfWorker| Počet uzlů pracovního procesu, které má tento cluster mít. Cluster má jeden ovladač Spark a num_workers prováděcí modul pro celkový počet uzlů num_workers + 1 Spark. Řetězec s formátováním Int32, jako je "1" znamená numOfWorker je 1 nebo "1:10" znamená automatické škálování od 1 jako minimum a 10 jako Max.  | No                |
| newClusterNodeType   | Toto pole kóduje prostřednictvím jedné hodnoty prostředky, které jsou k dispozici pro každý uzel Spark v tomto clusteru. Například uzly Spark můžou být zřízené a optimalizované pro úlohy náročné na paměť nebo výpočetní výkon. Toto pole je povinné pro nový cluster.                | No               |
| newClusterSparkConf  | sada volitelných uživatelsky definovaných párů klíč-hodnota konfigurace Sparku. Do ovladače a prováděcích modulů přes Spark. Driver. extraJavaOptions a spark.executor. extraJavaOptions můžou uživatelé taky předat řetězec dalších možností JVM. | No  |
| newClusterInitScripts| sada volitelných uživatelem definovaných inicializačních skriptů pro nový cluster. Zadání cesty DBFS ke skriptům init. | No  |


## <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database

Vytvoříte propojenou službu Azure SQL a použijete ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z data Factoryho kanálu. Podrobnosti o této propojené službě najdete v článku [konektor Azure SQL](connector-azure-sql-database.md#linked-service-properties) .

## <a name="azure-synapse-analytics-linked-service"></a>Propojená služba Azure synapse Analytics

Vytvoříte propojenou službu Azure synapse Analytics a použijete ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z data Factoryho kanálu. Podrobnosti o této propojené službě najdete v článku [konektor služby Azure synapse Analytics](connector-azure-sql-data-warehouse.md#linked-service-properties) .

## <a name="sql-server-linked-service"></a>Propojená služba SQL Server

Vytvoříte propojenou službu SQL Server a použijete ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z kanálu Data Factory. Podrobnosti o této propojené službě najdete v článku [SQL Server konektoru](connector-sql-server.md#linked-service-properties) .

## <a name="azure-function-linked-service"></a>Propojená služba Functions Azure

Vytvoříte propojenou službu Azure functions a použijete ji s [aktivitou funkce Azure](control-flow-azure-function-activity.md) ke spouštění Azure Functions v kanálu Data Factory. Návratový typ funkce Azure musí být platný `JObject` . (Mějte na paměti, [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) že JArray *není* `JObject` .) Libovolný návratový typ jiný než `JObject` neúspěch a vyvolá *obsah odpovědi* na chyb uživatele není platný JObject.

| **Vlastnost** | **Popis** | **Povinné** |
| --- | --- | --- |
| typ   | Vlastnost Type musí být nastavená na: **AzureFunction** . | ano |
| Adresa URL aplikace Function App | Adresa URL pro Azure Function App. Formát je `https://<accountname>.azurewebsites.net` . Tato adresa URL je hodnota v sekci **URL** při zobrazení Function App v Azure Portal  | ano |
| klíč funkce | Přístupový klíč pro funkci Azure Functions Klikněte na část **Správa** příslušné funkce a zkopírujte buď **klíč funkce** , nebo **klíč hostitele**. Další informace najdete tady: [aktivační události a vazby HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) . | ano |
|   |   |   |

## <a name="next-steps"></a>Další kroky

Seznam aktivit transformace, které podporuje Azure Data Factory, najdete v tématu [transformace dat](transform-data.md).
