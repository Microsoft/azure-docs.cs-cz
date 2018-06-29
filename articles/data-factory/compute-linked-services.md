---
title: Výpočetní prostředí podporovaných službou Azure Data Factory | Microsoft Docs
description: Další informace o výpočetní prostředí, které můžete použít v Azure Data Factory kanály (například Azure HDInsight) k datům transformace nebo proces.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: douglasl
ms.openlocfilehash: 14cb59487788f272533fd7ec7eccf313654bf857
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082803"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Výpočetní prostředí podporovaných službou Azure Data Factory
Tento článek vysvětluje různé výpočetní prostředí, které můžete použít k datům procesu nebo transformace. Obsahuje také podrobnosti o různých konfiguracích (na vyžádání oproti přineste si vlastní) podporovaných službou Data Factory při konfiguraci propojených služeb propojení tyto výpočetní prostředí s objektem pro vytváření dat Azure.

Následující tabulka obsahuje seznam výpočetních prostředích nepodporuje objekt pro vytváření dat a aktivity, které můžete spustit na ně. 

| Výpočetní prostředí                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight na vyžádání](#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [streamování Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Vlastní](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [systému SQL Server](#sql-server-linked-service) | [Uložená procedura](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Na vyžádání výpočetním prostředí
V tomto typu konfigurace je plně spravovaná výpočetní prostředí pomocí služby Azure Data Factory. Je vytvořeno automaticky pomocí služby Data Factory předtím, než je úloha odeslána zpracování dat a odebrat při dokončení úlohy. Můžete vytvořit propojená služba na vyžádání výpočetní prostředí, konfiguraci a řídit granulární nastavení pro provádění úlohy, správu clusteru a zavádění akce.

> [!NOTE]
> V konfiguraci na vyžádání v současné době podporuje pouze pro clustery služby Azure HDInsight.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure propojená služba HDInsight na vyžádání
Služba Azure Data Factory umožňuje automatické vytvoření clusteru HDInsight na vyžádání k datům procesu. Cluster se vytvoří ve stejné oblasti jako účet úložiště (vlastnost linkedServiceName v kódu JSON) přidružen ke clusteru. Účet úložiště musí být účet úložiště Azure pro obecné účely úrovně standard. 

Vezměte na vědomí následující **důležité** body o HDInsight na vyžádání propojené služby:

* Cluster HDInsight na vyžádání se vytvořil v rámci vašeho předplatného Azure. Budete moci zobrazit clusteru na portálu Azure, když je cluster v provozu a spuštěná. 
* Protokoly pro úlohy, které se spouštějí v clusteru HDInsight na vyžádání se zkopírují do účtu úložiště přidruženého ke clusteru HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definované v definice propojené služby se používají k přihlášení do clusteru pro podrobné řešení potíží během životního cyklu clusteru. 
* Musíte platit pouze za čas, kdy se HDInsight cluster je nahoru a spuštěné úlohy.
* Akce skriptu nelze použít s propojené služby Azure HDInsight na vyžádání. Pokud musíte nainstalovat další závislosti, například zvažte použití Azure Automation. Chcete-li spustit skript prostředí PowerShell, který provede následující akce:  
  a. Vytvoření clusteru HDInsight.  
  b. Spusťte skript akce třeba nainstalovat další závislosti.  
  c. Objekt pro vytváření dat kanál spouštět.  
  d. Odstranění clusteru.  

> [!IMPORTANT]
> Obvykle trvá **20 minut** nebo více ke zřízení clusteru Azure HDInsight na vyžádání.

### <a name="example"></a>Příklad:
Následující kód JSON určuje základě Linux na vyžádání propojené služby HDInsight. Služba Data Factory automaticky vytvoří **systémem Linux** clusteru HDInsight ke zpracování požadované aktivity. 

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
> Jako další aktivita spuštěna, uvidíte mnoho kontejnerů ve službě Azure blob storage. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Vlastnosti
| Vlastnost                     | Popis                              | Požaduje se |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Vlastnost typu musí být nastavená na **HDInsightOnDemand**. | Ano      |
| clusterSize                  | Počet uzlů pracovního procesu nebo data v clusteru. Vytvoření clusteru HDInsight s 2 hlavních uzlech spolu s počtem uzlů pracovního procesu, který jste zadali pro tuto vlastnost. Uzly jsou velikosti Standard_D3, který má 4 jádra, 4 pracovní uzly clusteru trvá 24 jader (4\*4 = 16 jader pro uzly pracovního procesu, plus 2\*4 = 8 jader pro head uzly). V tématu [nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) podrobnosti. | Ano      |
| linkedServiceName            | Propojená služba má být používána clusteru na vyžádání pro ukládání a zpracování dat Azure Storage. HDInsight cluster vytvoří ve stejné oblasti jako účet úložiště Azure. Pro Azure HDInsight platí omezení celkového počtu jader, které můžete v jednotlivých podporovaných oblastech použít. Ujistěte se, že máte dostatek základní kvóty v této oblasti Azure ke splnění požadovaných parametr clusterSize. Podrobnosti najdete v části [nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>V současné době nelze vytvořit cluster HDInsight na vyžádání, který používá jako úložiště Azure Data Lake Store. Pokud chcete uložit výsledek data z HDInsight zpracování v Azure Data Lake Store, pomocí aktivity kopírování zkopírovat data z Azure Blob Storage do Azure Data Lake Store. </p> | Ano      |
| clusterResourceGroup         | HDInsight cluster vytvoří v této skupině prostředků. | Ano      |
| TimeToLive                   | Povolené doby nečinnosti pro cluster HDInsight na vyžádání. Určuje, jak dlouho clusteru HDInsight na vyžádání zůstane aktivní po dokončení činnosti spustit, pokud nejsou žádné aktivní úlohy v clusteru. Minimální povolená hodnota je 5 minut (00: 05:00).<br/><br/>Například pokud spuštění aktivity trvá 6 minut a timetolive nastavena na 5 minut, clusteru zůstává aktivní po dobu 5 minut po spuštění 6 minut zpracování aktivity. Pokud se okno 6 minut proveden jiné aktivity při spuštění, je zpracován stejného clusteru.<br/><br/>Vytvoření clusteru HDInsight na vyžádání je náročná operace (může trvat), takže použití tohoto nastavení podle potřeby ke zlepšení výkonu služby data factory pomocí opakovaného použití clusteru HDInsight na vyžádání.<br/><br/>Pokud hodnota timetolive nastavíte na 0, odstranění clusteru ihned po dokončení spuštění aktivity. Vzhledem k tomu, pokud jste nastavili na vysokou hodnotu, cluster může zůstat nečinné přihlášení pro některá řešení potíží s účel, ale může mít za následek vysoké náklady. Proto je důležité nastavit odpovídající hodnotu na základě potřeb.<br/><br/>Pokud je hodnota vlastnosti timetolive správně nastavena, více kanálů sdílet instanci clusteru HDInsight na vyžádání. | Ano      |
| clusterType                  | Typ clusteru HDInsight, který se má vytvořit. Povolené hodnoty jsou "hadoop" a "spark". Pokud není zadáno, výchozí hodnota je hadoop. Balíček zabezpečení Enterprise povoleno clusteru není aktuálně podporována. | Ne       |
| verze                      | Verze clusteru HDInsight. Pokud není zadaný, používá aktuální verze definované výchozí HDInsight. | Ne       |
| hostSubscriptionId           | ID předplatného Azure, použít k vytvoření clusteru HDInsight. Pokud není zadaný, používá ID předplatného Azure přihlašovacího kontextu. | Ne       |
| clusterNamePrefix           | Předpona názvu clusteru HDI, časového razítka se automaticky připojí na konci názvu clusteru| Ne       |
| sparkVersion                 | Verze spark, pokud je typ clusteru "Spark" | Ne       |
| additionalLinkedServiceNames | Určuje, že další účty úložiště pro HDInsight propojená služba tak, aby služba Data Factory můžete zaregistrovat vaším jménem. Tyto účty úložiště musí být ve stejné oblasti jako cluster HDInsight, který je vytvořen ve stejné oblasti jako účet úložiště určeného linkedServiceName. | Ne       |
| osType                       | Typ operačního systému. Povolené hodnoty jsou: systémy Linux a Windows (pro HDInsight pouze 3.3). Výchozí hodnota je Linux. | Ne       |
| hcatalogLinkedServiceName    | Název Azure SQL propojené služby, které odkazují na databázi HCatalog. Cluster HDInsight na vyžádání je vytvořená pomocí Azure SQL database jako metaúložiště. | Ne       |
| connectVia                   | Integrace modulu Runtime použít k odesílání aktivity k této propojené služby HDInsight. Pro na vyžádání propojené služby HDInsight podporuje pouze Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne       |
| clusterUserName                   | Uživatelské jméno pro přístup ke clusteru. | Ne       |
| clusterPassword                   | Heslo v typu zabezpečený řetězec pro přístup ke clusteru. | Ne       |
| clusterSshUserName         | Uživatelské jméno k SSH se vzdáleně připojit k uzlu clusteru (pro Linux). | Ne       |
| clusterSshPassword         | Heslo v typu zabezpečený řetězec pro SSH vzdálené připojení uzlu clusteru (pro Linux). | Ne       |


> [!IMPORTANT]
> HDInsight podporuje více verzích clusterů Hadoop, které lze nasadit. Každou verzi volbu vytvoří na konkrétní verzi rozdělení Hortonworks Data Platform (HDP) a sadu součástí, které jsou obsaženy v rámci této distribuce. Seznam podporovaných verzí HDInsight zajišťuje, aktualizovaných poskytovat nejnovější komponenty ekosystém Hadoop a opravy. Zajistěte, aby vždy odkazují na nejnovější informace z [HDInsight podporované verze a operační systém typu](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zajistit používáte podporovanou verzi HDInsight. 
>
> 
> [!IMPORTANT]
> V současné době HDInsight, HBase, interaktivní dotazu (Hive LLAP), Storm a podnikové zabezpečení povolené (doméně) nepodporuje propojené služby clusterů. 
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

Služba HDInsight na vyžádání propojené vyžaduje ověření služby objektu k vytvoření clusterů HDInsight vaším jménem. Použít objekt zabezpečení ověřování služby, registrace entitu aplikace v Azure Active Directory (Azure AD) a udělit mu **Přispěvatel** role předplatné nebo skupinu prostředků, ve kterém se vytvoří HDInsight cluster. Podrobné pokyny najdete v tématu [použití portálu k vytvoření aplikace a služby objekt zabezpečení, které mají přístup k prostředkům Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Poznamenejte si následující hodnoty, které můžete použít k definování propojené služby:

- ID aplikace
- Klíč aplikace 
- ID tenanta

Použijte objekt zabezpečení ověřování služby tak, že zadáte následující vlastnosti:

| Vlastnost                | Popis                              | Požaduje se |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Zadejte ID aplikace klienta.     | Ano      |
| **servicePrincipalKey** | Zadejte klíč aplikace.           | Ano      |
| **klienta**              | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano      |

### <a name="advanced-properties"></a>Rozšířené vlastnosti

Můžete také zadat následující vlastnosti pro podrobné konfiguraci clusteru HDInsight na vyžádání.

| Vlastnost               | Popis                              | Požaduje se |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Určuje parametry konfigurace jádra (jako základní site.xml) pro vytvoření clusteru HDInsight. | Ne       |
| hBaseConfiguration     | Určuje parametry konfigurace HBase (hbase-site.xml) pro HDInsight cluster. | Ne       |
| hdfsConfiguration      | Určuje parametry konfigurace HDFS (hdfs-site.xml) pro HDInsight cluster. | Ne       |
| hiveConfiguration      | Určuje parametry konfigurace hive (hive-site.xml) pro HDInsight cluster. | Ne       |
| mapReduceConfiguration | Určuje parametry konfigurace MapReduce (mapred-site.xml) pro HDInsight cluster. | Ne       |
| oozieConfiguration     | Určuje parametry konfigurace Oozie (oozie-site.xml) pro HDInsight cluster. | Ne       |
| stormConfiguration     | Určuje parametry konfigurace Storm (storm-site.xml) pro HDInsight cluster. | Ne       |
| yarnConfiguration      | Určuje parametry konfigurace Yarn (yarn-site.xml) pro HDInsight cluster. | Ne       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Příklad – konfigurace clusteru HDInsight na vyžádání pomocí rozšířených vlastností

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

### <a name="node-sizes"></a>Velikost uzlu
Zadávat lze velikosti head, data a uzly zookeeper s následujícími vlastnostmi: 

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Určuje velikost hlavního uzlu. Výchozí hodnota je: Standard_D3. Najdete v článku **určení velikosti uzlu** podrobnosti. | Ne       |
| dataNodeSize      | Určuje velikost uzlu data. Výchozí hodnota je: Standard_D3. | Ne       |
| zookeeperNodeSize | Určuje velikost uzlu správce Zoo. Výchozí hodnota je: Standard_D3. | Ne       |

#### <a name="specifying-node-sizes"></a>Určení velikosti uzlu
Najdete v článku [velikosti virtuálních počítačů](../virtual-machines/linux/sizes.md) článku řetězcové hodnoty je třeba zadat vlastností uvedených v předchozí části. Hodnoty musí odpovídat **rutiny & rozhraní API** popsána v článku. Jak můžete vidět v následujícím článku, má datový uzel velikosti velké (výchozí) 7 GB paměti, která nemusí být dostatečně vhodné pro váš scénář. 

Pokud chcete vytvořit D4 velikost head uzlů a uzlů pracovního procesu, zadejte **Standard_D4** jako hodnota vlastnosti headNodeSize a dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Pokud zadáte chybnou hodnotu pro tyto vlastnosti, může se zobrazit následující **Chyba:** vytvoření clusteru se nezdařilo. Výjimka: Operaci vytvoření clusteru nelze dokončit. Operace se nezdařila, kód chyby je 400. Zanechaný stav clusteru: Chyba. Zpráva: 'PreClusterCreationValidationFailure'. Jakmile se zobrazí tato chyba, ujistěte se, že používáte **RUTINY & rozhraní API** názvem z tabulky v [velikosti virtuálních počítačů](../virtual-machines/linux/sizes.md) článku.        

## <a name="bring-your-own-compute-environment"></a>Výpočetní prostředí
V tomto typu konfigurace uživatelé mohou registrovat stávající výpočetní prostředí jako propojené služby ve službě Data Factory. Výpočetní prostředí spravuje uživatele a služba Data Factory používá, je spuštění aktivity.

Tento typ konfigurace je podporována pro následující výpočetních prostředích:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Databáze Azure SQL, Azure SQL DW, systému SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure propojené služby HDInsight
Můžete vytvořit propojené služby Azure HDInsight k registraci vlastní cluster HDInsight s Data Factory.

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
| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| type              | Vlastnost typu musí být nastavená na **HDInsight**. | Ano      |
| clusterUri        | Identifikátor URI clusteru HDInsight.        | Ano      |
| uživatelské jméno          | Zadejte jméno uživatele, který se má použít pro připojení k existujícímu clusteru HDInsight. | Ano      |
| heslo          | Zadejte heslo pro uživatelský účet.   | Ano      |
| linkedServiceName | Název úložiště Azure, propojené služby, která odkazuje na Azure blob storage používaný v clusteru HDInsight. <p>V současné době nelze zadat, že Azure Data Lake Store propojené služby pro tuto vlastnost. Pokud HDInsight cluster má přístup do Data Lake Store, k datům v Azure Data Lake Store můžete přistupovat z Hive nebo Pig skriptů. </p> | Ano      |
| connectVia        | Integrace modulu Runtime použít k odesílání aktivity k této propojené službě. Můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne       |

> [!IMPORTANT]
> HDInsight podporuje více verzích clusterů Hadoop, které lze nasadit. Každou verzi volbu vytvoří na konkrétní verzi rozdělení Hortonworks Data Platform (HDP) a sadu součástí, které jsou obsaženy v rámci této distribuce. Seznam podporovaných verzí HDInsight zajišťuje, aktualizovaných poskytovat nejnovější komponenty ekosystém Hadoop a opravy. Zajistěte, aby vždy odkazují na nejnovější informace z [HDInsight podporované verze a operační systém typu](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) zajistit používáte podporovanou verzi HDInsight. 
>
> [!IMPORTANT]
> V současné době HDInsight, HBase, interaktivní dotazu (Hive LLAP), Storm a podnikové zabezpečení povolené (doméně) nepodporuje propojené služby clusterů. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch propojené služby

Můžete vytvořit služby Azure Batch propojené můžete zaregistrovat fondu služby Batch virtuálních počítačů (VM) pro služby data factory. Vlastní aktivity pomocí Azure Batch můžete spustit.

Najdete v následujících tématech, pokud začínáte používat službu Azure Batch:

* [Základy Azure Batch](../batch/batch-technical-overview.md) Přehled služby Azure Batch.
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) rutiny k vytvoření účtu Azure Batch (nebo) [portál Azure](../batch/batch-account-create-portal.md) vytvoření účtu Azure Batch pomocí portálu Azure. V tématu [pomocí prostředí PowerShell ke správě účtu Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) téma pro podrobné pokyny k použití rutiny.
* [Nový-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) rutiny vytvoření fondu Azure Batch.

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
| type              | Vlastnost typu musí být nastavená na **AzureBatch**. | Ano      |
| název účtu       | Název účtu Azure Batch.         | Ano      |
| accessKey         | Přístupový klíč pro účet Azure Batch.  | Ano      |
| batchUri          | Adresa URL ke svému účtu Azure Batch, ve formátu https://*batchaccountname.region*. batch.azure.com. | Ano      |
| poolName          | Název fondu virtuálních počítačů.    | Ano      |
| linkedServiceName | Název úložiště Azure propojená služba přidruženého k této službě Azure Batch propojený. Tato propojená služba se používá pro pracovní soubory potřebné ke spuštění aktivity. | Ano      |
| connectVia        | Integrace modulu Runtime použít k odesílání aktivity k této propojené službě. Můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning propojené služby
Vytváření služby Azure Machine Learning propojené k registraci dávce Machine Learning vyhodnocovací koncový bod pro služby data factory.

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
| Typ                   | Vlastnost typu musí být nastavená na: **AzureML**. | Ano                                      |
| mlEndpoint             | Adresu URL dávkového vyhodnocování.                   | Ano                                      |
| apiKey                 | Rozhraní API modelu publikované prostoru.     | Ano                                      |
| updateResourceEndpoint | URL prostředku aktualizace pro koncový bod Azure ML Web Service používá k aktualizaci souborem trained model prediktivní webové služby | Ne                                       |
| servicePrincipalId     | Zadejte ID aplikace klienta.     | Vyžaduje, pokud je zadán updateResourceEndpoint |
| servicePrincipalKey    | Zadejte klíč aplikace.           | Vyžaduje, pokud je zadán updateResourceEndpoint |
| tenant                 | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Vyžaduje, pokud je zadán updateResourceEndpoint |
| connectVia             | Integrace modulu Runtime použít k odesílání aktivity k této propojené službě. Můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Služba Azure Data Lake Analytics propojené
Vytvoříte **Azure Data Lake Analytics** propojená služba Azure Data Lake Analytics výpočetní služby s objektem pro vytváření dat Azure. Data Lake Analytics U-SQL aktivitu v kanálu odkazuje na tato propojená služba. 

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
| type                 | Vlastnost typu musí být nastavená na: **AzureDataLakeAnalytics**. | Ano                                      |
| název účtu          | Název účtu Azure Data Lake Analytics.  | Ano                                      |
| dataLakeAnalyticsUri | Identifikátor URI služby Azure Data Lake Analytics.           | Ne                                       |
| subscriptionId       | Id předplatného Azure                    | Ne (když není určeno, předplatné objektu pro vytváření dat se používá). |
| resourceGroupName    | Název skupiny prostředků Azure                | Ne (když není určeno, skupinu prostředků objektu pro vytváření dat se používá). |
| servicePrincipalId   | Zadejte ID aplikace klienta.     | Ano                                      |
| servicePrincipalKey  | Zadejte klíč aplikace.           | Ano                                      |
| tenant               | Zadejte informace o klienta (název nebo klienta domény ID) v rámci které se nachází aplikace. Můžete ji načíst podržením ukazatele myši v pravém horním rohu portálu Azure. | Ano                                      |
| connectVia           | Integrace modulu Runtime použít k odesílání aktivity k této propojené službě. Můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne                                       |



## <a name="azure-databricks-linked-service"></a>Služba Azure Databricks propojené
Můžete vytvořit **propojená služba Azure Databricks** k registraci Databricks pracovní prostor, který použijete ke spuštění Databricks workloads(notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Příklad: Databricks v novém clusteru úlohy

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "eastus.azuredatabricks.net",
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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Příklad: použití existujícího interaktivní clusteru v Databricks

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
| type                 | Vlastnost typu musí být nastavená na: **AzureDatabricks**. | Ano                                      |
| Doména               | Zadejte oblast Azure, odpovídajícím způsobem podle oblasti Databricks pracovního prostoru. Příklad: https://eastus.azuredatabricks.net | Ano                                 |
| accessToken          | Přístupový token, je nutné k ověření Azure Databricks Data Factory. Přístupový token, je třeba vytvořit v pracovním prostoru databricks. Kroky k nalezení přístupový token naleznete podrobnější [sem](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ano                                       |
| existingClusterId    | ID stávajícího clusteru spustit všechny úlohy v tomto clusteru. To by měl být již vytvořené interaktivní clusteru. Budete muset ručně restartovat clusteru, pokud přestane reagovat. Databricks navrhnout probíhajících úloh na nových clusterů pro větší spolehlivost. Můžete najít ID clusteru interaktivní clusteru na Databricks -> prostoru clustery -> Interaktivní název clusteru -> Konfigurace -> značky. [Další informace](https://docs.databricks.com/user-guide/clusters/tags.html) | Ne 
| newClusterVersion    | Verze clusteru Spark. V databricks vytvoří cluster úlohy. | Ne  |
| newClusterNumOfWorker| Počet uzlů pracovního procesu, které by měly mít tento cluster. Cluster má jeden ovladač Spark a num_workers vykonavatelů celkem num_workers + 1 uzly Spark. Řetězec formátu Int32, jako je "1" znamená numOfWorker 1 nebo "1:10" znamená automatickému škálování z 1 jako minimální a 10 jako maximální počet.  | Ne                |
| newClusterNodeType   | Toto pole kóduje prostřednictvím jednu hodnotu, prostředky, které jsou k dispozici na všech uzlech Spark v tomto clusteru. Například Spark dají zřizovat a optimalizované pro zatížení s intenzivním paměti nebo výpočetní uzly toto pole je povinné pro nový cluster                | Ne               |
| newClusterSparkConf  | Sada volitelné, zadán uživatel páry klíč hodnota konfigurace Spark. Uživatele můžete také předat řetězec volby navíc JVM ovladače a vykonavatelů prostřednictvím spark.driver.extraJavaOptions a spark.executor.extraJavaOptions v uvedeném pořadí. | Ne  |


## <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Vytvoření služby Azure SQL propojené a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. V tématu [konektor služby Azure SQL](connector-azure-sql-database.md#linked-service-properties) článku podrobnosti o této propojené službě.

## <a name="azure-sql-data-warehouse-linked-service"></a>Služba Azure SQL Data Warehouse propojené
Vytvoření služby Azure SQL Data Warehouse propojené a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. V tématu [konektor služby Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) článku podrobnosti o této propojené službě.

## <a name="sql-server-linked-service"></a>Služba SQL Server propojené
Vytvoření služby SQL serveru propojená a použít je s [aktivity uložené procedury](transform-data-using-stored-procedure.md) k vyvolání uložené procedury z objektu pro vytváření dat kanál. V tématu [systému SQL Server konektoru](connector-sql-server.md#linked-service-properties) článku podrobnosti o této propojené službě.

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - pravidla po pojmenování
Následující tabulka obsahuje pravidla pojmenování artefaktů služby Data Factory.

| Název                             | Jedinečnost názvu                          | Ověřovací kontroly                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Jedinečná napříč Microsoft Azure. Názvy jsou velká a malá písmena, který je `MyDF` a `mydf` odkazovat na stejné služby data factory. | <ul><li>Každý objekt pro vytváření dat je vázaný na přesně jedno předplatné.</li><li>Názvy objektů musí začínat písmenem nebo číslicí a může obsahovat pouze písmena, číslice a pomlčky (-) znaků.</li><li>Každý znak pomlčka (-) musí být okamžitě a následnou písmenem nebo číslem. Po sobě jdoucí pomlčky nejsou povolené v názvech kontejneru.</li><li>Název může být 3 až 63 znaků dlouhý.</li></ul> |
| Propojených služeb/tabulek/kanálů | Jedinečný s ve službě data factory. Názvy jsou velká a malá písmena. | <ul><li>Maximální počet znaků v názvu tabulky: 260.</li><li>Názvy objektů musí začínat písmenem, číslo nebo podtržítko (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Skupina prostředků                   | Jedinečná napříč Microsoft Azure. Názvy jsou velká a malá písmena. | <ul><li>Maximální počet znaků: 1 000.</li><li>Název může obsahovat písmena, číslice a tyto znaky: "-", "_",","a"."</li></ul> |

## <a name="next-steps"></a>Další postup
Seznam aktivit transformace podporovaných službou Azure Data Factory najdete v tématu [transformovat data](transform-data.md).
