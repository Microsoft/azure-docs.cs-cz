---
title: Výpočetní prostředí podporovaná Azure Data Factory
description: Přečtěte si o výpočetních prostředích, která můžete použít v kanálech Azure Data Factory (například Azure HDInsight) k transformaci nebo zpracování dat.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 4545a75cc2082c21dcb87986eba819ebe39adf7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246341"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaná Azure Data Factory
Tento článek vysvětluje různá výpočetní prostředí, která můžete použít ke zpracování nebo transformaci dat. Poskytuje také podrobnosti o různých konfiguracích (na vyžádání vs přinést vlastní) podporované Data Factory při konfiguraci propojených služeb propojení těchto výpočetních prostředí do datové továrny Azure.

Následující tabulka obsahuje seznam výpočetních prostředí podporovaných data factory a aktivity, které můžete spustit na nich. 

| Výpočetní prostředí                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [Úl](transform-data-using-hadoop-hive.md), [Prase](transform-data-using-hadoop-pig.md), [Jiskra](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Vlastní](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Kanál spuštění Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Kanál spuštění Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Datový sklad Azure SQL](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Uložená procedura](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Poznámkový](transform-data-databricks-notebook.md)blok , [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Funkce Azure Functions](#azure-function-linked-service)         | [Aktivita funkce Azure](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Výpočetní prostředí HDInsight na vyžádání
V tomto typu konfigurace je výpočetní prostředí plně spravované službou Azure Data Factory. Je automaticky vytvořen službou Data Factory před odesláním úlohy ke zpracování dat a odebrána po dokončení úlohy. Můžete vytvořit propojenou službu pro výpočetní prostředí na vyžádání, nakonfigurovat ji a řídit podrobná nastavení pro provádění úloh, správu clusteru a zaváděcí akce.

> [!NOTE]
> Konfigurace na vyžádání je aktuálně podporovaná jenom pro clustery Azure HDInsight. Azure Databricks také podporuje úlohy na vyžádání pomocí clusterů úloh, další podrobnosti najdete v [propojené službě Azure databricks.](#azure-databricks-linked-service)

## <a name="azure-hdinsight-on-demand-linked-service"></a>Propojená služba Azure HDInsight na vyžádání
Služba Azure Data Factory může automaticky vytvořit cluster HDInsight na vyžádání pro zpracování dat. Cluster je vytvořen ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v JSON) přidružený ke clusteru. Účet úložiště musí být účet úložiště pro obecné účely standardní Azure. 

Všimněte si následujících **důležitých** bodů o propojené službě HDInsight na vyžádání:

* Cluster HDInsight na vyžádání se vytvoří v rámci vašeho předplatného Azure. Cluster uvidíte na portálu Azure, když je cluster v provozu. 
* Protokoly pro úlohy, které jsou spuštěny v clusteru HDInsight na vyžádání, se zkopírují do účtu úložiště přidruženého ke clusteru HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definované v definici propojené služby se používají k přihlášení do clusteru pro podrobné řešení potíží během životního cyklu clusteru. 
* Poplatky se vám budou účtovat pouze za dobu, kdy je cluster HDInsight spuštěna a spuštěna úlohy.
* **Akci skriptu** můžete použít s propojenou službou Azure HDInsight na vyžádání.  

> [!IMPORTANT]
> Obvykle trvá **20 minut** nebo více zřídit cluster Azure HDInsight na vyžádání.

### <a name="example"></a>Příklad
Následující JSON definuje linuxovou službu propojenou hdinsight na vyžádání. Služba Data Factory automaticky vytvoří cluster HDInsight **založený na Linuxu** pro zpracování požadované aktivity. 

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
> Cluster HDInsight vytvoří **výchozí kontejner** v úložišti objektů blob, které jste zadali v JSON **(linkedServiceName).** Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní. 
>
> Při spuštění další aktivity uvidíte mnoho kontejnerů ve vašem úložišti objektů blob Azure. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](https://storageexplorer.com/).
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Vlastnost type by měla být nastavena na **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet uzlů pracovníka/dat v clusteru. Cluster HDInsight je vytvořen se 2 hlavníu uzly spolu s počtem pracovních uzlů, které zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, která má 4 jádra, takže cluster pracovních\*uzlů 4 zabírá 24 jader\*(4 4 = 16 jader pro pracovní uzly plus 2 4 = 8 jader pro hlavní uzly). Podrobnosti najdete [v tématu Nastavení clusterů v HDInsightu pomocí Hadoopu, Sparku, Kafky a dalších](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) podrobností. | Ano      |
| linkedServiceName            | Propojená služba Azure Storage, kterou má cluster na vyžádání používat pro ukládání a zpracování dat. Cluster HDInsight se vytvoří ve stejné oblasti jako tento účet úložiště Azure. Pro Azure HDInsight platí omezení celkového počtu jader, která můžete v jednotlivých podporovaných oblastech Azure použít. Ujistěte se, že máte dostatek kvót jádra v této oblasti Azure ke splnění požadované clusterSize. Podrobnosti najdete v části [Nastavení clusterů v HDInsightu pomocí Hadoop, Spark, Kafka a dalších](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledná data ze zpracování HDInsight v Úložišti datových jezer Azure, použijte aktivitu kopírování ke kopírování dat z úložiště objektů blob Azure do Úložiště datových jezer Azure. </p> | Ano      |
| clusterResourceGroup         | Cluster HDInsight je vytvořen v této skupině prostředků. | Ano      |
| timetolive                   | Povolená doba nečinnosti clusteru HDInsight na vyžádání. Určuje, jak dlouho zůstane cluster HDInsight na vyžádání po dokončení spuštění aktivity naživu, pokud v clusteru nejsou žádné další aktivní úlohy. Minimální povolená hodnota je 5 minut (00:05:00).<br/><br/>Například pokud spuštění aktivity trvá 6 minut a timetolive je nastavena na 5 minut, cluster zůstane naživu po dobu 5 minut po 6 minut zpracování aktivity spustit. Pokud je spuštěna jiná aktivita s oknem 6 minut, je zpracována stejným clusterem.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je nákladná operace (může chvíli trvat), takže toto nastavení použijte podle potřeby ke zlepšení výkonu datové továrny opětovným použitím clusteru HDInsight na vyžádání.<br/><br/>Pokud nastavíte hodnotu timetolive na hodnotu 0, cluster se odstraní, jakmile se spustí aktivita. Vzhledem k tomu, pokud nastavíte vysokou hodnotu, cluster může zůstat nečinný pro přihlášení pro některé účely řešení potíží, ale to může mít za následek vysoké náklady. Proto je důležité nastavit příslušnou hodnotu na základě vašich potřeb.<br/><br/>Pokud je správně nastavena hodnota vlastnosti timetolive, může více kanálů sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| typ clusteru                  | Typ clusteru HDInsight, který má být vytvořen. Povolené hodnoty jsou "hadoop" a "jiskra". Pokud není zadán, výchozí hodnota je hadoop. Cluster s povoleným balíčkem podnikového zabezpečení nelze vytvořit na vyžádání, místo toho použijte [existující cluster / přineste vlastní výpočetní prostředky](#azure-hdinsight-linked-service). | Ne       |
| version                      | Verze clusteru HDInsight. Pokud není zadán, používá aktuální HDInsight definované výchozí verze. | Ne       |
| hostSubscriptionId           | ID předplatného Azure použité k vytvoření clusteru HDInsight. Pokud není zadán, použije ID předplatného vašeho přihlašovacího kontextu Azure. | Ne       |
| clusterNamePrefix           | Předpona názvu clusteru HDI, časové razítko bude automaticky připojeno na konci názvu clusteru| Ne       |
| sparkVersion                 | Verze jiskry, pokud je typ clusteru "Spark" | Ne       |
| additionalLinkedServiceNames | Určuje další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight, který je vytvořen ve stejné oblasti jako účet úložiště určený linkedServiceName. | Ne       |
| osTyp                       | Typ operačního systému. Povolené hodnoty jsou: Linux a Windows (pouze pro HDInsight 3.3). Výchozí je Linux. | Ne       |
| hcatalogLinkedServiceName    | Název propojené služby Azure SQL, která přejděte na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří pomocí databáze Azure SQL jako metastore. | Ne       |
| connectVia                   | Prostředí Integrace Runtime, které se má použít k odeslání aktivit do této propojené služby HDInsight. Pro propojené služby HDInsight na vyžádání podporuje jenom prostředí Azure Integration Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne       |
| clusterUserName                   | Uživatelské jméno pro přístup ke clusteru. | Ne       |
| clusterPassword                   | Heslo v typu zabezpečeného řetězce pro přístup ke clusteru. | Ne       |
| clusterSshUserName         | Uživatelské jméno ssh vzdáleně připojí k uzlu clusteru (pro Linux). | Ne       |
| clusterSshPassword         | Heslo typu zabezpečeného řetězce k uzlu SSH vzdáleně připojit uzel clusteru (pro Linux). | Ne       |
| akce skriptu | Zadejte skript pro [vlastní nastavení clusteru HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) během vytváření clusteru na vyžádání. <br />V současné době nástroj pro vytváření uživatelského rozhraní Azure Data Factory podporuje určení pouze akce skriptu 1, ale toto omezení můžete získat v JSON (zadat více akcí skriptu v JSON). | Ne |


> [!IMPORTANT]
> HDInsight podporuje více verzí clusteru Hadoop, které lze nasadit. Každá volba verze vytvoří konkrétní verzi distribuce Hortonworks Data Platform (HDP) a sadu součástí, které jsou obsaženy v této distribuci. Seznam podporovaných verzí HDInsight se neustále aktualizuje, aby poskytoval nejnovější součásti a opravy ekosystému Hadoop. Ujistěte se, že vždy odkazovat na nejnovější informace [o podporované verzi HDInsight a typ operačního systému,](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) abyste se ujistili, že používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> V současné době hdinsight propojené služby nepodporuje HBase, interaktivní dotaz (Hive LLAP), Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames Příklad JSON

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Propojená služba HDInsight na vyžádání vyžaduje ověření hlavního povinného servisu k vytvoření clusterů HDInsight vaším jménem. Pokud chcete použít ověřování na uměle virtuálním objektu služby, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí roli **přispěvatele** předplatného nebo skupiny prostředků, ve které se vytvoří cluster HDInsight. Podrobné kroky najdete [v tématu Vytvoření instancí aplikace a služby Azure Active Directory, které mají přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Pomocí ověřování instančního objektu zadejte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta aplikace.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **Nájemce**              | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano      |

### <a name="advanced-properties"></a>Upřesnit vlastnosti

Můžete také zadat následující vlastnosti pro podrobnou konfiguraci clusteru HDInsight na vyžádání.

| Vlastnost               | Popis                              | Požaduje se |
| :--------------------- | :--------------------------------------- | :------- |
| coreKonfigurace      | Určuje základní konfigurační parametry (jako v souboru core-site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hKonfigurace základny     | Určuje konfigurační parametry HBase (hbase-site.xml) pro cluster HDInsight. | Ne       |
| hdfsKonfigurace      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro cluster HDInsight. | Ne       |
| hiveKonfigurace      | Určuje parametry konfigurace podregistru (hive-site.xml) pro cluster HDInsight. | Ne       |
| mapReduceKonfigurace | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro cluster HDInsight. | Ne       |
| oozieKonfigurace     | Určuje konfigurační parametry Oozie (oozie-site.xml) pro cluster HDInsight. | Ne       |
| stormKonfigurace     | Určuje parametry konfigurace Storm (storm-site.xml) pro cluster HDInsight. | Ne       |
| yarnKonfigurace      | Určuje parametry konfigurace příze (yarn-site.xml) pro cluster HDInsight. | Ne       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad – Konfigurace clusteru HDInsight na vyžádání s rozšířenými vlastnostmi

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

### <a name="node-sizes"></a>Velikosti uzlů
Velikosti uzlů hlavy, dat a zookeeperu můžete určit pomocí následujících vlastností: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Určuje velikost hlavního uzlu. Výchozí hodnota je: Standard_D3. Podrobnosti najdete v části **Určení velikosti uzlů.** | Ne       |
| dataNodeSize      | Určuje velikost datového uzlu. Výchozí hodnota je: Standard_D3. | Ne       |
| zookeeperNodeVelikost | Určuje velikost uzlu Zoo Keeper. Výchozí hodnota je: Standard_D3. | Ne       |

#### <a name="specifying-node-sizes"></a>Určení velikostí uzlů
V článku [Velikosti virtuálních počítačů](../virtual-machines/linux/sizes.md) najdete hodnoty řetězců, které je potřeba zadat pro vlastnosti uvedené v předchozí části. Hodnoty musí odpovídat **CMDLETs & APIS** odkazuje v článku. Jak můžete vidět v článku, datový uzel velké (výchozí) velikost má 7 GB paměti, což nemusí být dost dobré pro váš scénář. 

Pokud chcete vytvořit hlavní uzly a pracovní uzly velikosti D4, zadejte **Standard_D4** jako hodnotu vlastností headNodeSize a dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud pro tyto vlastnosti zadáte nesprávnou hodnotu, může se zobrazit následující **chyba:** Vytvoření clusteru se nezdařilo. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: 'PreClusterCreationValidationFailure'. Když se zobrazí tato chyba, ujistěte se, že používáte **název CMDLET & APIS** z tabulky v [části Velikosti virtuálních počítačů.](../virtual-machines/linux/sizes.md)        

## <a name="bring-your-own-compute-environment"></a>Přineste si vlastní výpočetní prostředí
V tomto typu konfigurace mohou uživatelé zaregistrovat již existující výpočetní prostředí jako propojenou službu v datové továrně. Výpočetní prostředí je spravováno uživatelem a služba Data Factory ho používá k provádění aktivit.

Tento typ konfigurace je podporován pro následující výpočetní prostředí:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Propojená služba Azure HDInsight
Můžete vytvořit propojenou službu Azure HDInsight a zaregistrovat svůj vlastní cluster HDInsight pomocí Data Factory.

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
| Vlastnost          | Popis                                                  | Požaduje se |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | Vlastnost type by měla být nastavena na **HDInsight**.            | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.                            | Ano      |
| uživatelské jméno          | Zadejte jméno uživatele, který se má použít k připojení k existujícímu clusteru HDInsight. | Ano      |
| heslo          | Zadejte heslo pro uživatelský účet.                       | Ano      |
| linkedServiceName | Název propojené služby Azure Storage, která odkazuje na úložiště objektů blob Azure používané clusterem HDInsight. <p>V současné době nelze zadat propojené služby Azure Data Lake Store pro tuto službu. Pokud má cluster HDInsight přístup k úložišti datových jezer, můžete přistupovat k datům v úložišti Azure Data Lake Store ze skriptů Hive/Pig. </p> | Ano      |
| IsEspEnabled      | Zadejte hodnotu*true*, pokud je cluster HDInsight [povolen.](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) Výchozí hodnota je '*false*'. | Ne       |
| connectVia        | Prostředí Integrace Runtime, které se používá k odeslání aktivity do této propojené služby. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. <br />Pro balíček zabezpečení rozlehlé sítě (ESP) s podporou hdinsight clusteru použít runtime integrace s vlastním hostitelem, který má zorné pole do clusteru nebo by měl být nasazen uvnitř stejné virtuální sítě jako cluster ESP HDInsight. | Ne       |

> [!IMPORTANT]
> HDInsight podporuje více verzí clusteru Hadoop, které lze nasadit. Každá volba verze vytvoří konkrétní verzi distribuce Hortonworks Data Platform (HDP) a sadu součástí, které jsou obsaženy v této distribuci. Seznam podporovaných verzí HDInsight se neustále aktualizuje, aby poskytoval nejnovější součásti a opravy ekosystému Hadoop. Ujistěte se, že vždy odkazovat na nejnovější informace [o podporované verzi HDInsight a typ operačního systému,](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) abyste se ujistili, že používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> V současné době hdinsight propojené služby nepodporuje HBase, interaktivní dotaz (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete vytvořit azure batch propojené služby zaregistrovat dávkový fond virtuálních počítačů (VM) do datové továrny. Vlastní aktivitu můžete spustit pomocí Azure Batch.

Pokud se službou Azure Batch tenete s azure batch, přečtěte si následující články:

* [Základy Azure Batch](../batch/batch-technical-overview.md) pro přehled služby Azure Batch.
* Nová rutina [AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) k vytvoření portálu Azure Batch (nebo) [portálu Azure](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí portálu Azure Portal. Podrobný článek o použití [PowerShellu ke správě dávkového účtu Azure,](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) kde najdete podrobné pokyny k používání rutiny.
* [Rutina New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) pro vytvoření fondu Azure Batch.

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
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Vlastnost type by měla být nastavena na **AzureBatch**. | Ano      |
| accountName       | Název účtu Azure Batch.         | Ano      |
| Accesskey         | Přístupový klíč pro účet Azure Batch.  | Ano      |
| batchUri          | Adresa URL vašeho účtu Azure Batch ve formátu https://*batchaccountname.region*.batch.azure.com. | Ano      |
| název_fondu          | Název fondu virtuálních počítačů.    | Ano      |
| linkedServiceName | Název propojené služby Azure Storage přidružené k této propojené službě Azure Batch. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity. | Ano      |
| connectVia        | Prostředí Integrace Runtime, které se používá k odeslání aktivity do této propojené služby. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne       |

## <a name="azure-machine-learning-studio-linked-service"></a>Propojená služba Azure Machine Learning Studio
Vytvoříte propojenou službu Azure Machine Learning Studio pro registraci koncového bodu dávkového bodování Machine Learning do datové továrny.

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
| Vlastnost               | Popis                              | Požaduje se                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Vlastnost type by měla být nastavena na: **AzureML**. | Ano                                      |
| mlEndpoint             | Adresa URL dávkového bodování.                   | Ano                                      |
| apiKey                 | Rozhraní API publikovaného modelu pracovního prostoru.     | Ano                                      |
| aktualizovatResourceEndpoint | Adresa URL aktualizačního prostředku pro koncový bod webové služby Azure Machine Learning, který slouží k aktualizaci prediktivní webové služby pomocí trénovaného souboru modelu | Ne                                       |
| servicePrincipalId     | Zadejte ID klienta aplikace.     | Povinné, pokud je zadán updateResourceEndpoint |
| servicePrincipalKey    | Zadejte klíč aplikace.           | Povinné, pokud je zadán updateResourceEndpoint |
| Nájemce                 | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Povinné, pokud je zadán updateResourceEndpoint |
| connectVia             | Prostředí Integrace Runtime, které se používá k odeslání aktivity do této propojené služby. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne                                       |

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning
Vytvoříte propojenou službu Azure Machine Learning pro připojení pracovního prostoru Azure Machine Learning k datové továrně.

> [!NOTE]
> V současné době je podporováno pouze ověřování instančního objektu služby pro propojenou službu Azure Machine Learning.

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
| Vlastnost               | Popis                              | Požaduje se                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Vlastnost type by měla být nastavena na: **AzureMLService**. | Ano                                      |
| subscriptionId         | ID předplatného Azure              | Ano                                      |
| resourceGroupName      | jméno | Ano                                      |
| mlNázev_pracovního_        | Název pracovního prostoru Azure Machine Learning | Ano  |
| servicePrincipalId     | Zadejte ID klienta aplikace.     | Ne |
| servicePrincipalKey    | Zadejte klíč aplikace.           | Ne |
| Nájemce                 | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Povinné, pokud je zadán updateResourceEndpoint | Ne |
| connectVia             | Prostředí Integrace Runtime, které se používá k odeslání aktivity do této propojené služby. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne |    

## <a name="azure-data-lake-analytics-linked-service"></a>Propojená služba Azure Data Lake Analytics
Vytvoříte propojenou službu **Azure Data Lake Analytics,** která propojí výpočetní službu Azure Data Lake Analytics s toto datovou továrnou Azure. Aktivita U-SQL služby Data Lake Analytics v kanálu odkazuje na tuto propojenou službu. 

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

| Vlastnost             | Popis                              | Požaduje se                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | Vlastnost type by měla být nastavena na: **AzureDataLakeAnalytics**. | Ano                                      |
| accountName          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Identifikátor URI analýzy datového jezera Azure.           | Ne                                       |
| subscriptionId       | ID předplatného Azure                    | Ne                                       |
| resourceGroupName    | Název skupiny prostředků Azure                | Ne                                       |
| servicePrincipalId   | Zadejte ID klienta aplikace.     | Ano                                      |
| servicePrincipalKey  | Zadejte klíč aplikace.           | Ano                                      |
| Nájemce               | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Můžete načíst na jenom myši v pravém horním rohu portálu Azure. | Ano                                      |
| connectVia           | Prostředí Integrace Runtime, které se používá k odeslání aktivity do této propojené služby. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne                                       |



## <a name="azure-databricks-linked-service"></a>Propojená služba Azure Databricks
Můžete vytvořit **azure databricks propojené služby** pro registraci Databricks pracovního prostoru, který budete používat ke spuštění úlohy Databricks (poznámkový blok, jar, python). 
> [!IMPORTANT]
> Propojené služby Databricks podporují [fondy instancí](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Příklad - Použití nového clusteru úloh v Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Příklad – použití existujícího interaktivního clusteru v databricks

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

| Vlastnost             | Popis                              | Požaduje se                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| jméno                 | Název propojené služby               | Ano   |
| type                 | Vlastnost type by měla být nastavena na: **Azure Databricks**. | Ano                                      |
| doména               | Zadejte oblast Azure odpovídajícím způsobem na základě oblasti pracovního prostoru Databricks. Příklad: https://eastus.azuredatabricks.net | Ano                                 |
| accessToken          | Přístupový token je vyžadován pro Data Factory k ověření Azure Databricks. Přístupový token je třeba vygenerovat z pracovního prostoru databricks. Podrobnější kroky k nalezení přístupového tokenu naleznete [zde](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ano                                       |
| existingClusterId    | ID clusteru existujícího clusteru pro spuštění všech úloh v tomto. Měl by se jedná o již vytvořený interaktivní cluster. Pokud přestane reagovat, bude pravděpodobně nutné cluster restartovat ručně. Databricks navrhnout spuštění úlohy na nové clustery pro větší spolehlivost. ID clusteru interaktivního clusteru v pracovním prostoru Databricks -> clustery -> název interaktivního clusteru -> Konfigurace -> značky. [Další podrobnosti](https://docs.databricks.com/user-guide/clusters/tags.html) | Ne 
| instancePoolId    | ID fondu instancí existujícího fondu v pracovním prostoru databricks.  | Ne  |
| nová verze Cluster    | Verze Spark clusteru. Vytvoří cluster úloh v datebricks. | Ne  |
| newClusterNumOfWorker| Počet pracovních uzlů, které by tento cluster měl mít. Cluster má jeden ovladač Spark a num_workers vykonavatelé pro celkem num_workers + 1 uzly Spark. Řetězec formátovaný Int32, jako "1" znamená numOfWorker je 1 nebo "1:10" znamená automatické škálování od 1 jako min a 10 jako max.  | Ne                |
| nový typ clusteruNodeTyp   | Toto pole kóduje prostřednictvím jedné hodnoty prostředky, které jsou k dispozici pro každý z uzlů Spark v tomto clusteru. Například uzly Spark mohou být zřízeny a optimalizovány pro úlohy náročné na paměť nebo výpočetní prostředky. Toto pole je vyžadováno pro nový cluster.                | Ne               |
| novýClusterSparkConf  | sada volitelných, uživatelem určených párů konfiguračních klíčů a hodnot Spark. Uživatelé mohou také předat v řetězci dalších možností JVM na řidiče a vykonavatelé přes spark.driver.extraJavaOptions a spark.executor.extraJavaOptions příslušně. | Ne  |
| nové clusterinitscripty| sada volitelných, uživatelem definovaných inicializačních skriptů pro nový cluster. Určení cesty DBFS k init skriptům. | Ne  |


## <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Vytvoříte propojené služby Azure SQL a použít ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) vyvolat uloženou proceduru z kanálu datové továrny. Podrobnosti o této propojené službě najdete v článku azure [sql connector.](connector-azure-sql-database.md#linked-service-properties)

## <a name="azure-sql-data-warehouse-linked-service"></a>Propojená služba Azure SQL Data Warehouse
Vytvoříte propojenou službu Azure SQL Data Warehouse a použijete ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z kanálu datové továrny. Podrobnosti o této propojené službě najdete v článku [konektoru Azure SQL Data Warehouse Connector.](connector-azure-sql-data-warehouse.md#linked-service-properties)

## <a name="sql-server-linked-service"></a>Propojená služba SQL Server
Vytvoříte propojenou službu sql serveru a použijete ji s [aktivitou uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z kanálu data factory. Podrobnosti o této propojené službě naleznete v článku [konektoru serveru SQL Server.](connector-sql-server.md#linked-service-properties)

## <a name="azure-function-linked-service"></a>Propojená služba Azure Function
Vytvoříte propojené služby Azure funkce a použít ji s [aktivitou Funkce Azure](control-flow-azure-function-activity.md) ke spuštění Funkce Azure v kanálu datové továrny. Návratový typ funkce Azure musí být `JObject`platný . (Mějte na paměti, *not* že `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) není .) Jakýkoli návratový `JObject` typ jiný než selže a vyvolá chybu uživatele *Obsah odpovědi není platný JObject*.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type   | Vlastnost type musí být nastavena na: **AzureFunction.** | ano |
| adresa URL aplikace funkce | Adresa URL aplikace Azure Function App. Formát `https://<accountname>.azurewebsites.net`je . Tato adresa URL je hodnota v části **ADRESA URL** při zobrazení aplikace funkce na webu Azure Portal  | ano |
| funkční klávesa | Přístupový klíč pro funkci Azure. Klikněte na sekci **Spravovat** pro příslušnou funkci a zkopírujte funkční **klíč** nebo **klíč Hostitele**. Další informace najdete tady: [Aktivační události a vazby http funkce Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ano |
|   |   |   |

## <a name="next-steps"></a>Další kroky
Seznam transformačních aktivit podporovaných službou Azure Data Factory najdete v [tématu Transformace dat](transform-data.md).
