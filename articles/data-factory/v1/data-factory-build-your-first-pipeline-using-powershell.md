---
title: "Sestavení prvního objektu pro vytváření dat (PowerShell) | Dokumentace Microsoftu"
description: "V tomto kurzu vytvoříte pomocí prostředí Azure PowerShell ukázkový kanál služby Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: 
editor: 
ms.assetid: 22ec1236-ea86-4eb7-b903-0e79a58b90c7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bea8f944e59774abdb70f0f1cad37cfe0749b195
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Kurz: Sestavení prvního objektu pro vytváření dat Azure pomocí prostředí Azure PowerShell
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory verze 2](../quickstart-create-data-factory-powershell.md).

V tomto článku vytvoříte první objekt pro vytváření dat Azure pomocí prostředí Azure PowerShell. Pokud chcete udělat kurz pomocí jiných nástrojů nebo sad SDK, vyberte jednu z možností z rozevíracího seznamu.

Kanál v tomto kurzu má jednu aktivitu: **aktivitu HDInsight Hive**. Tato aktivita spouští skript Hive v clusteru Azure HDInsight, který transformuje vstupní data pro vytvoření výstupních dat. Spuštění kanálu je naplánované jednou za měsíc mezi zadaným počátečním a koncovým časem. 

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Nekopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace najdete v tématu [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Požadavky
* Přečtěte si článek [Přehled kurzu](data-factory-build-your-first-pipeline.md) a proveďte **nutné** kroky.
* Podle pokynů v článku [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) si na počítač nainstalujte nejnovější verzi prostředí Azure PowerShell.
* (volitelné) Tento článek nepopisuje všechny rutiny služby Data Factory. Úplnou dokumentaci o rutinách služby Data Factory najdete v článku [Referenční informace o rutinách služby Data Factory](/powershell/module/azurerm.datafactories).

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku vytvoříte pomocí prostředí Azure PowerShell objekt pro vytváření dat Azure s názvem **FirstDataFactoryPSH**. Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu Hivu HDInsight pro spuštění skriptu Hive, který umožňuje transformovat vstupní data. V tomto kroku začneme vytvořením objektu pro vytváření dat.

1. Otevřete prostředí Azure PowerShell a spusťte následující příkaz. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu. Pokud ho zavřete a znovu otevřete, tyto příkazy bude potřeba znovu spustit.
   * Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal.
    ```PowerShell
    Login-AzureRmAccount
    ```    
   * Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet.
    ```PowerShell
    Get-AzureRmSubscription 
    ```
   * Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Mělo by to být stejné předplatné, které jste použili na webu Azure Portal.
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```     
2. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup**:
    
    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Některé kroky v tomto kurzu vychází z předpokladu, že používáte skupinu prostředků s názvem ADFTutorialResourceGroup. Pokud máte jinou skupinu prostředků, použijte ji v postupech v tomto kurzu místo skupiny ADFTutorialResourceGroup.
3. Spusťte rutinu **New-AzureRmDataFactory**, která vytvoří objekt pro vytváření dat s názvem **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba **Název objektu pro vytváření dat FirstDataFactoryPSH není k dispozici**, název změňte (třeba na název váš_název_FirstDataFactoryPSH). Při provádění postupů v tomto kurzu potom tento název používejte místo názvu ADFTutorialFactoryPSH. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
* Instance služby Data Factory můžete vytvářet jenom tehdy, když jste přispěvatelem/správcem předplatného Azure.
* Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.
* Pokud se zobrazí chyba „**Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory**“, proveďte některý z těchto kroků a znovu zkuste název publikovat:

  * Spuštěním následujícího příkazu v prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory:

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Spuštěním následujícího příkazu si můžete ověřit, zda je zprostředkovatel služby Data Factory zaregistrovaný:

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Přihlaste se na web [Azure Portal ](https://portal.azure.com) pomocí předplatného Azure a přejděte do okna Objekt pro vytváření dat nebo na webu Azure Portal vytvořte objekt pro vytváření dat. Zprostředkovatel se při takovém postupu zaregistruje automaticky.

Před vytvořením kanálu je nejdřív potřeba vytvořit několik entit služby Data Factory. Nejprve vytvoříte propojené služby, které propojí úložiště dat a výpočetní služby s vaším úložištěm dat, definujete vstupní a výstupní datové sady reprezentující vstupní a výstupní data v propojených úložištích dat, a poté vytvoříte kanál s aktivitou, která tyto datové sady používá.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku propojíte svůj účet služby Azure Storage a cluster Azure HDInsight na vyžádání s objektem pro vytváření dat. Účet služby Azure Storage v této ukázce obsahuje vstupní a výstupní data pro kanál. Propojená služba HDInsight slouží v této ukázce ke spuštění skriptu Hive určeného v aktivitě kanálu. Určete, jaké úložiště dat a výpočetní služby se ve vašem scénáři používají, a vytvořením propojených služeb propojte tyto služby s objektem pro vytváření dat.

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku propojíte se svým objektem pro vytváření dat svůj účet služby Azure Storage. Stejný účet služby Azure Storage použijete také k uložení vstupních a výstupních dat a souboru skriptu HQL.

1. Ve složce C:\ADFGetStarted vytvořte soubor JSON s názvem StorageLinkedService.json s následujícím obsahem. Pokud složka ADFGetStarted neexistuje, vytvořte ji.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Nahraďte **název účtu** názvem účtu služby Azure Storage a **klíč účtu** přístupovým klíčem k účtu Azure Storage. Chcete-li zjistit, jak získat přístupový klíč k úložišti, přečtěte si informace o zobrazení, kopírování a opětovném vygenerování přístupových klíčů k úložišti v tématu [Správa účtu úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
2. V prostředí Azure PowerShell přejděte do složky ADFGetStarted.
3. K vytvoření propojené služby můžete použít rutinu **New-AzureRmDataFactoryLinkedService**. Tato rutina a další rutiny služby Data Factory používané v tomto kurzu vyžadují, abyste zadali hodnoty parametrů *ResourceGroupName* a *DataFactoryName*. Alternativně můžete pomocí rutiny **Get-AzureRmDataFactory** načíst objekt **DataFactory** a tento objekt předat, abyste nemuseli při každém spouštění rutiny zadávat hodnoty parametrů *ResourceGroupName* a *DataFactoryName*. Spuštěním následujícího příkazu přiřaďte výstup rutiny **Get-AzureRmDataFactory** k proměnné **$df**.

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Nyní spusťte rutinu **New-AzureRmDataFactoryLinkedService**, která vytvoří propojenou službu **StorageLinkedService**.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Pokud jste nespustili rutinu **Get-AzureRmDataFactory** a nepřiřadili výstup k proměnné **$df**, bude potřeba zadat následující hodnoty parametrů *ResourceGroupName* a *DataFactoryName*.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Pokud v průběhu kurzu zavřete prostředí Azure PowerShell, bude při dalším spuštění prostředí Azure PowerShell potřeba znovu spustit rutinu **Get-AzureRmDataFactory**, abyste mohli kurz dokončit.

### <a name="create-azure-hdinsight-linked-service"></a>Vytvoření propojené služby Azure HDInsight
V tomto kroku propojíte se svým objektem pro vytváření dat cluster HDInsight na vyžádání. Cluster HDInsight se automaticky vytvoří za běhu, a až dokončí zpracování, po určité zadané době nečinnosti se odstraní. Místo clusteru HDInsight na vyžádání můžete použít také vlastní cluster HDInsight. Podrobnosti najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md).

1. Ve složce **C:\ADFGetStarted** vytvořte soubor JSON s názvem **HDInsightOnDemandLinkedService**.json s následujícím obsahem.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

   | Vlastnost | Popis |
   |:--- |:--- |
   | ClusterSize |Určuje velikost clusteru HDInsight. |
   | TimeToLive |Určuje dobu nečinnosti před odstraněním clusteru HDInsight. |
   | linkedServiceName |Určuje účet úložiště, který se používá k ukládání protokolů generovaných clusterem HDInsight |

    Je třeba počítat s následujícím:

   * Pomocí tohoto kódu JSON služba Data Factory vytvoří cluster HDInsight **se systémem Linux** za vás. Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
   * Místo clusteru HDInsight na vyžádání můžete použít také **vlastní cluster HDInsight**. Podrobnosti najdete v tématu [Propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
   * Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je zpracován určitý řez, pokud neexistuje aktivní cluster (**timeToLive**). Po dokončení zpracování se cluster automaticky odstraní.

       Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů používají následující formát: „adf**název_vašeho_objektu_pro_vytváření_dat**-**název_propojené_služby**-razítko_data_a_času“. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).

     Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
2. Spusťte rutinu **New-AzureRmDataFactoryLinkedService**, která vytvoří propojenou službu s názvem HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data pro zpracování Hive. Tyto datové sady odkazují na službu **StorageLinkedService**, kterou už jste v tomto kurzu vytvořili. Propojená služba odkazuje na účet služby Azure Storage a datové sady určují kontejner, složku a název souboru v úložišti, který obsahuje vstupní a výstupní data.

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. Ve složce **C:\ADFGetStarted** vytvořte soubor JSON s názvem **InputTable.json** s následujícím obsahem.

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Kód JSON určuje datovou sadu s názvem **AzureBlobInput**, která představuje vstupní data pro aktivitu v kanálu. Kromě toho určuje, že se vstupní data nachází v kontejneru objektů blob s názvem **adfgetstarted** ve složce s názvem **inputdata**.

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

   | Vlastnost | Popis |
   |:--- |:--- |
   | type |Vlastnost type je nastavená na hodnotu AzureBlob, protože se data nachází ve službě Azure Blob Storage. |
   | linkedServiceName |Odkazuje na službu StorageLinkedService, kterou jste vytvořili předtím. |
   | fileName |Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto případě se zpracovává jenom soubor input.log. |
   | type |Soubory protokolů jsou v textovém formátu, takže použijeme hodnotu TextFormat. |
   | columnDelimiter |Sloupce v souborech protokolu jsou oddělené znakem čárky (,). |
   | frequency/interval |Frekvence je nastavená na hodnotu Month (Měsíc) a interval je 1, takže vstupní řezy jsou dostupné jednou za měsíc. |
   | external |Pokud vstupní data nevygenerovala služba Data Factory, je tato vlastnost nastavená na hodnotu true. |
2. Spuštěním následujícího příkazu v prostředí Azure PowerShell vytvořte datovou sadu služby Data Factory:

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady
Nyní vytvoříte výstupní datovou sadu, která bude představovat výstupní data ve službě Azure Blob Storage.

1. Ve složce **C:\ADFGetStarted** vytvořte soubor JSON s názvem **OutputTable.json** s následujícím obsahem.

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Kód JSON definuje datovou sadu s názvem **AzureBlobOutput**, která představuje výstupní data pro aktivitu v kanálu. Kromě toho určuje, že se mají výsledky ukládat do kontejneru objektů blob s názvem **adfgetstarted** do složky s názvem **partitioneddata**. Oddíl **availability** určuje, že se výstupní sada vytváří jednou měsíčně.
2. Spuštěním následujícího příkazu v prostředí Azure PowerShell vytvořte datovou sadu služby Data Factory:

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte svůj první kanál s aktivitou **HDInsightHive**. Vstupní řez je dostupný jednou měsíčně (frequency: Month, interval: 1), výstupní řez se vytváří také jednou měsíčně a vlastnost scheduler pro aktivitu je také nastavena na jednou měsíčně. Nastavení výstupní datové sady a vlastnosti scheduler se musí shodovat. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Vysvětlení vlastností použitých v následujícím kódu JSON najdete na konci této části.

1. Ve složce C:\ADFGetStarted vytvořte soubor JSON s názvem MyFirstPipelinePSH.json s následujícím obsahem:

   > [!IMPORTANT]
   > V kódu JSON nahraďte **storageaccountname** názvem vašeho účtu úložiště.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    V tomto fragmentu kódu JSON vytváříte kanál sestávající z jediné aktivity, která zpracovává data v clusteru HDInsight pomocí skriptu Hive.

    Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService s názvem **StorageLinkedService**) a ve složce **script** v kontejneru **adfgetstarted**.

    Oddíl **defines** určuje nastavení běhového prostředí, které se předá skriptu Hive jako konfigurační hodnoty Hive (např. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Vlastnosti kanálu **start** a **end** určují období aktivity kanálu.

    V kódu JSON aktivity určujete, že má skript Hive běžet ve výpočetní službě určené vlastností **linkedServiceName**, tedy **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > V části kódu JSON kanálu v tématu [Kanály a aktivity ve službě Azure Data Factory](data-factory-create-pipelines.md) najdete podrobnosti o vlastnostech JSON použitých v příkladu.

2. Ujistěte se, že se ve složce **adfgetstarted/inputdata** ve službě Azure Blob Storage nachází soubor **input.log**, a spuštěním následujícího příkazu kanál nasaďte. Časy **start** a **end** jsou nastavené na minulost a vlastnost **isPaused** má hodnotu false, takže se kanál (aktivita v kanálu) spustí hned po nasazení.

    ```PowerShell
    New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Úspěšně jste vytvořili první kanál pomocí prostředí Azure PowerShell, blahopřejeme!

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku budete pomocí prostředí Azure PowerShell monitorovat, co se děje v objektu pro vytváření dat Azure.

1. Spusťte rutinu **Get-AzureRmDataFactory** a přiřaďte výstup k proměnné **$df**.

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Spuštěním rutiny **Get-AzureRmDataFactorySlice** získejte podrobnosti o všech řezech tabulky **EmpSQLTable**, která je výstupní tabulkou kanálu.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Všimněte si, že hodnota StartDateTime, kterou tady určíte, je stejná jako počáteční čas uvedený v kódu JSON kanálu. Zde je ukázkový výstup:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Spuštěním rutiny **Get-AzureRmDataFactoryRun** získáte podrobnosti o spouštění aktivity pro určitý řez.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Zde je ukázkový výstup: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Rutinu můžete spouštět opakovaně, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. Pokud je řez ve stavu Připraveno, zkontrolujte, jestli se ve složce **partitioneddata** v kontejneru **adfgetstarted** ve službě Blob Storage nachází výstupní data.  Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá.

    ![Výstupní data](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá (přibližně 20 minut). Proto počítejte s tím, že zpracování řezu kanálem bude trvat **přibližně 30 minut**.
>
> Po úspěšném zpracování řezu se vstupní soubor odstraní. Pokud tedy chcete spustit řez znovu nebo si znovu projít tento kurz, načtěte vstupní soubor (input.log) do složky inputdata v kontejneru adfgetstarted.
>
>

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro zpracování dat Azure, který zpracovává data pomocí skriptu Hive v clusteru HDInsight Hadoop. Pomocí editoru služby Data Factory na webu Azure Portal jste provedli tyto kroky:

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste dvě **propojené služby**:
   1. Propojená služba **Azure Storage** spojuje úložiště objektů blob Azure, které obsahuje vstupní/výstupní soubory, s objektem pro vytváření dat.
   2. Propojená služba na vyžádání **Azure HDInsight** spojuje cluster na vyžádání HDInsight Hadoop s objektem pro vytváření dat. Služba Azure Data Factory vytvoří cluster HDInsight Hadoop ve chvíli, kdy je potřeba zpracovat vstupní data a vygenerovat výstupní data.
3. Vytvořili jste dvě **datové sady**, které popisují vstupní a výstupní data aktivity HDInsight Hive v kanálu.
4. Vytvořili jste **kanál** s aktivitou **HDInsight Hive**.

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili kanál s aktivitou transformace (aktivita HDInsight), která v clusteru Azure HDInsight na vyžádání spouští skript Hive. Pokud chcete zjistit, jak pomocí aktivity kopírování zkopírovat data z Azure Blob do Azure SQL, projděte si článek [Kurz: Kopírování dat z Azure Blob do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Viz také
| Téma | Popis |
|:--- |:--- |
| [Referenční informace o rutinách služby Data Factory](/powershell/module/azurerm.datafactories) |Tady najdete rozsáhlou dokumentaci o rutinách služby Data Factory. |
| [Kanály](data-factory-create-pipelines.md) |Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a porozumět tomu, jak se dají ve vaší situaci nebo firmě použít k sestavení kompletních pracovních postupů založených na datech. |
| [Datové sady](data-factory-create-datasets.md) |Tento článek vám pomůže pochopit datové sady ve službě Azure Data Factory. |
| [Plánování a provádění](data-factory-scheduling-and-execution.md) |Tento článek vysvětluje aspekty plánování a provádění aplikačního modelu služby Azure Data Factory. |
| [Monitorování a správa kanálů pomocí monitorovací aplikace](data-factory-monitor-manage-app.md) |Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí aplikace pro monitorování a správu. |
