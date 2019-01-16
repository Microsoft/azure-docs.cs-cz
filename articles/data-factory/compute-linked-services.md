---
title: Výpočetní prostředí podporovaných službou Azure Data Factory | Dokumentace Microsoftu
description: Další informace o výpočetní prostředí, které můžete použít k transformaci nebo zpracování dat v Azure Data Factory kanálů (jako je například Azure HDInsight).
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: 5e620b03f5588369fc73a62f2019d857766596fd
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321938"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaných službou Azure Data Factory
Tento článek vysvětluje různých výpočetních prostředích, které můžete použít k zpracovávat a transformovat data. Obsahuje také podrobnosti o různých konfigurací (na vyžádání vs. funkce přineste si vlastní) podporovaných službou Data Factory při konfiguraci propojené služby propojení těchto výpočetních prostředí do služby Azure data factory.

Následující tabulka obsahuje seznam podporovaných objektu pro vytváření dat a aktivity, které v nich dají spustit výpočetní prostředí. 

| Výpočetní prostředí                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Vlastní](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a aktualizace prostředku](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [systému SQL Server](#sql-server-linked-service) | [Uložená procedura](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Prostředí compute HDInsight na vyžádání
U tohoto typu konfigurace je plně spravované výpočetní prostředí pomocí služby Azure Data Factory. Je automaticky vytvořen ve službě Data Factory předtím, než je úlohy odeslané do dat procesu a odebrat, pokud je úloha dokončena. Vytvoření propojené služby pro výpočetní prostředky na vyžádání prostředí, nakonfigurujte ji a řídit granulární nastavení pro provádění úloh, správu clusteru a spuštění akce.

> [!NOTE]
> Konfigurace na vyžádání je momentálně podporována pouze pro clustery Azure HDInsight. Azure Databricks také podporuje úlohy na vyžádání pomocí úlohy clusterů, přečtěte si [propojená služba Azure databricks](#azure-databricks-linked-service) další podrobnosti.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight na vyžádání propojené služby
Služba Azure Data Factory můžete automaticky vytvořit cluster HDInsight na vyžádání zpracovávat data. Vytvoření clusteru ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v kódu JSON) přidružené ke clusteru. Účet úložiště musí být účet Azure storage pro obecné účely úrovně standard. 

Mějte na paměti následující **důležité** propojená služba HDInsight na vyžádání body:

* Cluster HDInsight na vyžádání se vytvoří v rámci vašeho předplatného Azure. Máte možnost clusteru na portálu Azure zjistíte, že cluster běží a že běží. 
* V protokolech úloh, které jsou spuštěny v clusteru HDInsight na vyžádání se zkopírují do účtu úložiště, které jsou přidružené ke clusteru HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definované v definici propojené služby slouží k přihlášení ke clusteru pro komplexní řešení potíží během životního cyklu clusteru. 
* Se účtují jenom za čas, kdy je HDInsight cluster nahoru a spuštěné úlohy.
* Můžete použít **akce skriptu** s Azure HDInsight na vyžádání propojenou službu.  

> [!IMPORTANT]
> Obvykle trvá **20 minut** nebo více jak zřídit cluster Azure HDInsight na vyžádání.

### <a name="example"></a>Příklad:
Následující kód JSON určuje HDInsight propojené služby na vyžádání založené na Linuxu. Služba Data Factory automaticky vytvoří **založených na Linuxu** clusteru HDInsight ke zpracování požadované aktivity. 

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
> Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní. 
>
> Při spuštění další aktivity, zobrazí spousta kontejnerů ve službě Azure blob storage. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Vlastnost type by měla být nastavená na **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet pracovních procesů a datových uzlů do clusteru. Vytvoření clusteru HDInsight s 2 hlavní uzly spolu s počtem uzlů pracovního procesu, kterou zadáte pro tuto vlastnost. Uzly mají velikost Standard_D3, který má 4 jádra, 4 pracovní uzel clusteru trvá 24 jader (4\*4 = 16 jader pro pracovní uzly a navíc 2\*4 = 8 jader pro hlavní uzly). Zobrazit [nastavení clusterů v HDInsight pomocí Hadoop, Spark, Kafka a další](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) podrobnosti. | Ano      |
| linkedServiceName            | Propojená služba clusteru na vyžádání používané pro ukládání a zpracování dat v Azure Storage. HDInsight cluster vytvoří ve stejné oblasti jako tento účet úložiště Azure. Pro Azure HDInsight platí omezení celkového počtu jader, která můžete v jednotlivých podporovaných oblastech Azure použít. Ujistěte se, že máte dostatek kvóty jader v dané oblasti Azure podle vyžaduje parametr clusterSize. Podrobnosti najdete v [nastavení clusterů v HDInsight pomocí Hadoop, Spark, Kafka a další](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, která používá Azure Data Lake Store jako úložiště. Pokud chcete uložit výsledných dat z HDInsight zpracování do Azure Data Lake Store, použijte aktivitu kopírování ke zkopírování dat z Azure Blob Storage do Azure Data Lake Store. </p> | Ano      |
| clusterResourceGroup         | HDInsight cluster vytvoří v této skupině prostředků. | Ano      |
| TimeToLive                   | Povolené prodlevy pro cluster HDInsight na vyžádání. Určuje, jak dlouho zůstane aktivní cluster HDInsight na vyžádání po dokončení aktivity spustit, pokud v clusteru nejsou žádné aktivní úlohy. Minimální povolená hodnota je 5 minut (00: 05:00).<br/><br/>Například pokud spuštění aktivit trvá 6 minut a timetolive nastavena na 5 minut, clusteru zůstává aktivní po 5 minutách od 6 minut výpočetního aktivity spustit. Pokud se spuštění další aktivity provádí s oknem 6 minut, je zpracován programovacím modelem stejného clusteru.<br/><br/>Vytváření clusteru HDInsight na vyžádání je náročná operace (akce může trvat), takže použití tohoto nastavení jako potřebné ke zlepšení výkonu služby data factory pomocí opakovaného použití clusteru služby HDInsight na vyžádání.<br/><br/>Pokud hodnota timetolive nastavíte na 0, cluster odstraní co nejdříve po dokončení spuštění aktivity. Vzhledem k tomu, pokud nastavíte vysokou hodnotu, clusteru může zůstat nečinná pro přihlášení pro některá řešení potíží s účelem, ale může způsobit vysoké náklady. Proto je důležité, že nastavíte příslušnou hodnotu na základě vašich potřeb.<br/><br/>Pokud je hodnota vlastnosti timetolive správně nastavena, více kanálů mohou sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| clusterType                  | Typ vytvoření clusteru HDInsight. Povolené hodnoty jsou "hadoop" a "spark". Pokud není zadán, výchozí hodnota je hadoop. Enterprise Security Package povoleno clusteru nelze vytvářet na vyžádání, místo toho použijte [existující cluster / přineste vlastní výpočetní](#azure-hdinsight-linked-service). | Ne       |
| version                      | Verze clusteru HDInsight. Pokud není zadán, používá aktuální verze definovaný výchozí HDInsight. | Ne       |
| hostSubscriptionId           | ID předplatného Azure používá k vytvoření clusteru HDInsight. Pokud není zadán, použije ID předplatného kontext přihlášení k Azure. | Ne       |
| clusterNamePrefix           | Předpona názvu clusteru Hdinsight, časové razítko se automaticky připojí na konec názvu clusteru| Ne       |
| sparkVersion                 | Verze sparku, pokud je typ clusteru "Spark" | Ne       |
| additionalLinkedServiceNames | Určuje další účty úložiště pro HDInsight propojenou službu tak, aby služba Data Factory můžete zaregistrovat vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight, který se vytvoří ve stejné oblasti jako účet úložiště zadaný ve vlastnost linkedServiceName. | Ne       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou: Systémy Linux a Windows (pro HDInsight 3.3 pouze). Výchozí hodnota je Linux. | Ne       |
| hcatalogLinkedServiceName    | Název serveru Azure SQL propojenou službu, které odkazují na databázi HCatalog. Cluster HDInsight na vyžádání se vytvoří s využitím Azure SQL database jako metastore. | Ne       |
| connectVia                   | Prostředí Integration Runtime, který se má použít k odeslání aktivit do této HDInsight propojenou službu. HDInsight propojené služby na vyžádání podporuje pouze prostředí Azure Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne       |
| clusterUserName                   | Uživatelské jméno pro přístup ke clusteru. | Ne       |
| clusterPassword                   | Heslo v typu zabezpečený řetězec pro přístup ke clusteru. | Ne       |
| clusterSshUserName         | Uživatelské jméno pro SSH vzdálené připojení k uzlu clusteru (pro Linux). | Ne       |
| clusterSshPassword         | Heslo v typu zabezpečený řetězec SSH vzdálené připojení uzlu clusteru (pro Linux). | Ne       |
| scriptActions | Zadejte skript pro [přizpůsobení clusterů HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) při vytváření clusteru na vyžádání. <br />V současné době podporuje uživatelské rozhraní služby Azure Data Factory pro vytváření zadat pouze 1 skriptových akcí, ale můžete získat pomocí tohoto omezení v kódu JSON (určení více akcí skriptů v kódu JSON). | Ne |


> [!IMPORTANT]
> HDInsight podporuje více verzích clusterů systému Hadoop, které mohou být nasazeny. Každou volbu verze vytvoří konkrétní verze dané distribuce Hortonworks Data Platform (HDP) a sada komponent, které jsou obsaženy v rámci příslušné distribuci. Seznam podporovaných verzí HDInsight drží jsou aktualizované tak, aby nejnovější komponenty ekosystému Hadoop a opravy. Ujistěte se, že vždycky odkazovat na nejnovější informace o [HDInsight podporované verze a typ operačního systému](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zajistit používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> V současné době HDInsight propojené služby nepodporuje HBase, interaktivní dotazy (Hive LLAP), Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON example

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Služba HDInsight na vyžádání propojené vyžaduje ověřování instančního objektu služby a vaším jménem vytvářet clustery HDInsight. Použít ověřování instančních objektů, zaregistrujte aplikaci entity ve službě Azure Active Directory (Azure AD) a jí udělit **Přispěvatel** pro dané předplatné nebo skupinu prostředků, ve kterém se vytvoří HDInsight cluster. Podrobné pokyny najdete v článku [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Použijte ověřování instančních objektů zadáním následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID klienta vaší aplikace.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **tenanta**              | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano      |

### <a name="advanced-properties"></a>Upřesňující vlastnosti

Můžete také zadat následující vlastnosti pro podrobnou konfiguraci clusteru HDInsight na vyžádání.

| Vlastnost               | Popis                              | Požaduje se |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje základní konfigurační parametry (jako v core-site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hBaseConfiguration     | Určuje parametry konfigurace HBase (hbase-site.xml) pro HDInsight cluster. | Ne       |
| hdfsConfiguration      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro HDInsight cluster. | Ne       |
| hiveConfiguration      | Určuje parametry konfigurace hive (hive-site.xml) pro HDInsight cluster. | Ne       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro HDInsight cluster. | Ne       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro HDInsight cluster. | Ne       |
| stormConfiguration     | Určuje parametry konfigurace Storm (storm-site.xml) pro HDInsight cluster. | Ne       |
| yarnConfiguration      | Určuje parametry konfigurace Yarn (yarn-site.xml) pro HDInsight cluster. | Ne       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad: Konfigurace clusteru HDInsight na vyžádání s upřesňující vlastnosti

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
Můžete zadat velikost head, dat a uzly zookeeper s následujícími vlastnostmi: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Určuje velikost hlavního uzlu. Výchozí hodnota je: Standard_D3. Zobrazit **určení velikosti uzlů** podrobné informace. | Ne       |
| dataNodeSize      | Určuje velikost datový uzel. Výchozí hodnota je: Standard_D3. | Ne       |
| zookeeperNodeSize | Určuje velikost uzlu správce Zoo. Výchozí hodnota je: Standard_D3. | Ne       |

#### <a name="specifying-node-sizes"></a>Určení velikosti uzlů
Najdete v článku [velikostí virtuálních počítačů](../virtual-machines/linux/sizes.md) článku pro řetězcové hodnoty je třeba zadat vlastností uvedených v předchozí části. Hodnoty musí odpovídat **rutiny a rozhraní API** odkazované v tomto článku. Jak je vidět v následujícím článku, má datový uzel (výchozí) velké velikosti 7 GB paměti, což nemusí být dostatečné pro váš scénář. 

Pokud chcete vytvořit D4 velikosti hlavní uzly a uzly pracovního procesu, zadejte **Standard_D4** hodnotu vlastností headNodeSize a dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud zadáte chybnou hodnotu pro tyto vlastnosti, může se zobrazit následující **Chyba:** Nepovedlo se vytvořit cluster. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: "Chyba". Zpráva: "PreClusterCreationValidationFailure". Když se zobrazí tato chyba, ujistěte se, že používáte **RUTINY a rozhraní API** název tabulky [velikostí virtuálních počítačů](../virtual-machines/linux/sizes.md) článku.        

## <a name="bring-your-own-compute-environment"></a>Výpočetní prostředí
U tohoto typu konfigurace nastavení uživatelé můžou registrovat již existující výpočetní prostředí jako propojenou službu ve službě Data Factory. Výpočetní prostředí spravuje uživatel a služba Data Factory se používá ke spuštění aktivity.

Tento typ konfigurace platí pro následující výpočetních prostředích:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight, propojené služby
Můžete vytvořit služby Azure HDInsight propojené můžete zaregistrovat vlastní cluster HDInsight s Data Factory.

### <a name="example"></a>Příklad:

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
| type              | Vlastnost type by měla být nastavená na **HDInsight**.            | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.                            | Ano      |
| uživatelské jméno          | Zadejte jméno uživatele, který se má použít pro připojení do existujícího clusteru HDInsight. | Ano      |
| heslo          | Zadejte heslo pro uživatelský účet.                       | Ano      |
| linkedServiceName | Název propojenou službu Azure Storage, který odkazuje na úložiště objektů blob v Azure používá HDInsight cluster. <p>V současné době nelze zadat, že že Azure Data Lake Store propojené služby pro tuto vlastnost. Pokud se HDInsight cluster musí přístupu Data Lake Store, může přístup k datům v Azure Data Lake Store pomocí skriptů Hive a Pig. </p> | Ano      |
| isEspEnabled      | Zadejte "*true*' Pokud je cluster HDInsight [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction) povolena. Výchozí hodnota je "*false*". | Ne       |
| connectVia        | Integration Runtime, který se má použít k odeslání aktivity k této propojené službě. Můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. <br />Enterprise Security Package (ESP) povoleno použití v clusteru HDInsight místní prostředí integration runtime, který má dohled do clusteru nebo musí být nasazené ve stejné virtuální síti jako cluster HDInsight ESP. | Ne       |

> [!IMPORTANT]
> HDInsight podporuje více verzích clusterů systému Hadoop, které mohou být nasazeny. Každou volbu verze vytvoří konkrétní verze dané distribuce Hortonworks Data Platform (HDP) a sada komponent, které jsou obsaženy v rámci příslušné distribuci. Seznam podporovaných verzí HDInsight drží jsou aktualizované tak, aby nejnovější komponenty ekosystému Hadoop a opravy. Ujistěte se, že vždycky odkazovat na nejnovější informace o [HDInsight podporované verze a typ operačního systému](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zajistit používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> V současné době HDInsight propojené služby nepodporuje HBase, interaktivní dotazy (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Služba Azure Batch propojené

Můžete vytvořit služby propojené služby Azure Batch můžete fondu služby Batch virtuálních počítačů (VM) zaregistrovat do služby data factory. Můžete spustit pomocí služby Azure Batch pro vlastní aktivity.

Najdete v následujících tématech, pokud jste ještě službu Azure Batch:

* [Základy Azure Batch](../batch/batch-technical-overview.md) Přehled služby Azure Batch.
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) rutina pro vytvoření účtu Azure Batch (nebo) [webu Azure portal](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí webu Azure portal. Zobrazit [použití Powershellu ke správě účtu Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) najdete podrobné pokyny k použití rutiny.
* [Nový-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) rutina pro vytvoření fondu služby Azure Batch.

### <a name="example"></a>Příklad:

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
| type              | Vlastnost type by měla být nastavená na **AzureBatch**. | Ano      |
| accountName       | Název účtu služby Azure Batch.         | Ano      |
| accessKey         | Přístupový klíč pro účet Azure Batch.  | Ano      |
| batchUri          | Adresa URL k účtu Azure Batch ve formátu https://*batchaccountname.region*. batch.azure.com. | Ano      |
| poolName          | Název fondu virtuálních počítačů.    | Ano      |
| linkedServiceName | Název služby Azure Storage propojená služba spojené s touto službou propojené služby Azure Batch. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity. | Ano      |
| connectVia        | Integration Runtime, který se má použít k odeslání aktivity k této propojené službě. Můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne       |

## <a name="azure-machine-learning-linked-service"></a>Služba Azure Machine Learning propojený
Vytvoření služby Azure Machine Learning propojený zaregistrovat Machine Learning dávkového vyhodnocování koncový bod s datovou továrnou.

### <a name="example"></a>Příklad:

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
| Typ                   | Vlastnost type by měla být nastavená na: **AzureML**. | Ano                                      |
| mlEndpoint             | Adresu URL dávkového bodování.                   | Ano                                      |
| apiKey                 | Rozhraní API publikované pracovního prostoru modelu.     | Ano                                      |
| updateResourceEndpoint | Adresu URL prostředku aktualizace pro koncový bod webové služby Azure ML použitý k aktualizaci souboru trénovaného modelu prediktivní webové služby | Ne                                       |
| servicePrincipalId     | Zadejte ID klienta vaší aplikace.     | Povinné, pokud je zadán updateResourceEndpoint |
| servicePrincipalKey    | Zadejte klíč aplikace.           | Povinné, pokud je zadán updateResourceEndpoint |
| tenant                 | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Povinné, pokud je zadán updateResourceEndpoint |
| connectVia             | Integration Runtime, který se má použít k odeslání aktivity k této propojené službě. Můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics propojené služby
Vytvoření **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby Azure data Factory. Aktivita Data Lake Analytics U-SQL v kanálu odkazuje na tuto propojenou službu. 

### <a name="example"></a>Příklad:

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
            "subscriptionId": "<optional, subscription id of ADLA>",
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
| type                 | Vlastnost type by měla být nastavená na: **AzureDataLakeAnalytics**. | Ano                                      |
| accountName          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Ne                                       |
| subscriptionId       | Id předplatného Azure                    | Ne                                       |
| resourceGroupName    | Název skupiny prostředků Azure                | Ne                                       |
| servicePrincipalId   | Zadejte ID klienta vaší aplikace.     | Ano                                      |
| servicePrincipalKey  | Zadejte klíč aplikace.           | Ano                                      |
| tenant               | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano                                      |
| connectVia           | Integration Runtime, který se má použít k odeslání aktivity k této propojené službě. Můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks propojené služby
Můžete vytvořit **propojená služba Azure Databricks** k registraci pracovního prostoru Databricks, který použijete ke spuštění Databricks workloads(notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Příklad: použití nového clusteru úloh v Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Příklad – použití existujících interaktivní clusteru ve službě Databricks

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
| type                 | Vlastnost type by měla být nastavená na: **AzureDatabricks**. | Ano                                      |
| Doména               | Zadejte oblast Azure, odpovídajícím způsobem podle oblasti pracovního prostoru Databricks. Příklad: https://eastus.azuredatabricks.net | Ano                                 |
| accessToken          | Přístupový token je vyžadován pro Data Factory k ověření do Azure Databricks. Přístupový token musí být generovány v pracovním prostoru databricks. Podrobný postup najít přístupový token najdete [zde](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ano                                       |
| existingClusterId    | ID clusteru ze stávajícího clusteru můžete spouštět všechny úlohy na to. To by měl být již byly vytvořeny interaktivní clusteru. Budete muset ručně restartovat clusteru, pokud přestane reagovat. Databricks navrhnout, spouštění úloh na nových clusterů pro větší spolehlivost. Můžete najít ID clusteru Cluster Interactive v -> pracovní prostor Databricks clustery -> Interaktivní název clusteru -> Konfigurace -> značky. [Další podrobnosti](https://docs.databricks.com/user-guide/clusters/tags.html) | Ne 
| newClusterVersion    | Verze clusteru Spark. Clusteru úloh vytvoří ve službě databricks. | Ne  |
| newClusterNumOfWorker| Počet pracovních uzlů, které by měly mít tento cluster. Cluster bude mít jeden ovladač Spark a num_workers prováděcí moduly celkem num_workers + 1 uzlů Spark. Řetězec ve formátu Int32, jako je "1" znamená, že numOfWorker 1 nebo "1:10" znamená, že automatické škálování od 1 jako minimální a 10 jako maximální.  | Ne                |
| newClusterNodeType   | Toto pole kóduje prostřednictvím jednu hodnotu, prostředky dostupné pro každý z uzlů Sparku v tomto clusteru. Například Spark uzlů se dají zřizovat a optimalizované pro úlohy náročné na paměť nebo výpočetní toto pole je povinné pro nový cluster                | Ne               |
| newClusterSparkConf  | Sada volitelné, zadané uživatelem páry klíč hodnota konfigurace Spark. Uživatele můžete také předat v řetězci další možnosti JVM ovladače a prováděcí moduly prostřednictvím spark.driver.extraJavaOptions a spark.executor.extraJavaOptions v uvedeném pořadí. | Ne  |


## <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Vytvoření propojené služby Azure SQL a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) vyvolat uloženou proceduru z kanálu Data Factory. Zobrazit [konektor služby Azure SQL](connector-azure-sql-database.md#linked-service-properties) , kde najdete podrobnosti o této propojené službě.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse propojené služby
Vytvoření služby Azure SQL Data Warehouse propojené a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) vyvolat uloženou proceduru z kanálu Data Factory. Zobrazit [konektor Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) , kde najdete podrobnosti o této propojené službě.

## <a name="sql-server-linked-service"></a>Propojené služby SQL serveru
Vytvoření propojené služby SQL serveru a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) vyvolat uloženou proceduru z kanálu Data Factory. Zobrazit [konektor SQL serveru](connector-sql-server.md#linked-service-properties) , kde najdete podrobnosti o této propojené službě.

## <a name="next-steps"></a>Další postup
Seznam aktivit transformace podporovaných službou Azure Data Factory najdete v tématu [transformovat data](transform-data.md).
