---
title: Vytváření prediktivních datových kanálů
description: Naučte se vytvořit prediktivní kanál pomocí Azure Machine Learning Studio (Classic) – aktivita spuštění dávky v Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 50ef97bca0a5359c49ba2f18b1ec789ab076350a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637730"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Vytvoření prediktivního kanálu pomocí Azure Machine Learning Studio (Classic) a Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuální verze](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Machine Learning Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje sestavovat, testovat a nasazovat řešení prediktivní analýzy. Z hlediska vysoké úrovně je to provedeno ve třech krocích:

1. **Vytvořte školicí experiment** . Tento krok provedete pomocí Azure Machine Learning Studio (Classic). Azure Machine Learning Studio (Classic) je vizuální vývojové prostředí pro týmovou spolupráci, které slouží k výuce a testování prediktivních analytických modelů pomocí školicích dat.
2. **Převeďte ji na prediktivní experiment** . Jakmile se model vyškole pomocí stávajících dat a jste připraveni ho použít k vyhodnocení nových dat, připravujete a zjednodušte experimenty pro bodování.
3. **Nasaďte ji jako webovou službu** . Experiment bodování můžete publikovat jako webovou službu Azure. Můžete odesílat data do modelu prostřednictvím tohoto koncového bodu webové služby a získávat výsledky předpovědi z modelu.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory a Azure Machine Learning Studio (Classic) společně
Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu [Azure Machine Learning Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning) pro prediktivní analýzy. Pomocí **aktivity dávkového spuštění** v kanálu Azure Data Factory můžete vyvolat webovou službu Azure Machine Learning Studio (Classic), která předpovědi data ve službě Batch.

Prediktivní modely v Azure Machine Learning Studio (klasický) experimenty bodování se v průběhu času musí přenášet pomocí nových vstupních datových sad. Model můžete přeškolit z Data Factory kanálu, a to provedením následujících kroků:

1. Publikujte experimenty školení (ne prediktivní experiment) jako webovou službu. Tento krok provedete v Azure Machine Learning Studio (Classic) jako při zveřejnění prediktivního experimentu jako webové služby v předchozím scénáři.
2. K vyvolání webové služby pro experimenty pro školení použijte aktivitu spuštění dávkového zpracování Azure Machine Learning Studio (Classic). V podstatě můžete použít aktivitu spuštění dávky Azure Machine Learning Studio (Classic) k vyvolání webové služby pro vyhodnocování webové služby a bodování.

Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity aktualizace prostředku Azure Machine Learning Studio (Classic)** . Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](update-machine-learning-models.md) .

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Propojená služba Azure Machine Learning Studio (Classic)

Vytvoříte propojenou službu **Azure Machine Learning Studio (Classic)** pro propojení webové služby Azure Machine Learning Studio (Classic) s objektem pro vytváření dat Azure. Propojená služba se používá v aktivitě dávkového spuštění Azure Machine Learning Studio (Classic) a [aktualizuje aktivitu prostředku](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Popisy týkající se vlastností v definici JSON najdete v článku věnovaném [výpočetním propojeným službám](compute-linked-services.md) .

Azure Machine Learning Studio (Classic) podporuje klasické webové služby a nové webové služby pro prediktivní experiment. Můžete vybrat jednu z nich, která bude použita z Data Factory. Chcete-li získat informace potřebné k vytvoření propojené služby Azure Machine Learning Studio (Classic), přejít na https://services.azureml.net , kde jsou uvedeny všechny vaše (nové) webové služby a klasické webové služby. Klikněte na webovou službu, ke které chcete získat přístup, a klikněte na **spotřebovat** stránku. Zkopírujte **primární klíč** pro vlastnost **ApiKey** a **dávkové požadavky** na vlastnost **mlEndpoint** .

![Webové služby Azure Machine Learning Studio (klasické)](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Aktivita spuštění dávky Azure Machine Learning Studio (Classic)

Následující fragment kódu JSON definuje aktivitu spuštění dávky Azure Machine Learning Studio (Classic). Definice aktivity obsahuje odkaz na propojenou službu Azure Machine Learning Studio (Classic), kterou jste vytvořili dříve.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Vlastnost          | Popis                              | Povinné |
| :---------------- | :--------------------------------------- | :------- |
| name              | Název aktivity v kanálu     | Ano      |
| description       | Text popisující, co aktivita dělá.  | Ne       |
| typ              | Pro aktivitu Data Lake Analytics U-SQL je typ aktivity **AzureMLBatchExecution** . | Ano      |
| linkedServiceName | Propojené služby pro propojenou službu Azure Machine Learning Studio (Classic). Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| webServiceInputs  | Páry klíč-hodnota, mapování názvů vstupů webové služby Azure Machine Learning Studio (Classic). Klíč musí odpovídat vstupním parametrům definovaným v publikované webové službě Azure Machine Learning Studio (Classic). Hodnota je dvojice vlastností Azure Storage propojených služeb a FilePath určující umístění vstupních objektů BLOB. | Ne       |
| webServiceOutputs | Páry klíč-hodnota, mapování názvů Azure Machine Learning Studio (klasických) výstupů webové služby. Klíč musí odpovídat výstupním parametrům definovaným v publikované webové službě Azure Machine Learning Studio (Classic). Hodnota je dvojice vlastností Azure Storage propojených služeb a FilePath určující umístění výstupního objektu BLOB. | Ne       |
| globalParameters  | Páry klíč-hodnota, které mají být předány do koncového bodu služby Azure Machine Learning Studio (Classic) Batch. Klíče musí odpovídat názvům parametrů webové služby, které jsou definovány v publikované webové službě Azure Machine Learning Studio (Classic). Hodnoty se předávají do Azure Machine Learning Studio vlastnosti GlobalParameters (Classic) žádosti o spuštění dávky. | Ne       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář 1: experimenty s využitím vstupů a výstupů webové služby, které odkazují na data v Azure Blob Storage

V tomto scénáři předpovědi webová služba Azure Machine Learning Studio (Classic) využívá data ze souboru v úložišti objektů BLOB v Azure a ukládá výsledky předpovědi do úložiště objektů BLOB. Následující JSON definuje Data Factory kanál s aktivitou AzureMLBatchExecution. Na vstupní a výstupní data v Azure blogu Storage se odkazuje pomocí páru s odkazem a FilePath. V ukázce propojené služby vstupů a výstupů se liší, ale můžete použít různé propojené služby pro každé vstupy a výstupy pro Data Factory, abyste mohli vybírat správné soubory a odesílat je do webové služby Azure Machine Learning Studio (Classic).

> [!IMPORTANT]
> V Azure Machine Learning Studio (klasický) experiment, vstupní a výstupní porty webové služby a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které použijete pro nastavení webServiceInputs, webServiceOutputs a globalParameters, se musí přesně shodovat s názvy v experimentech. Chcete-li ověřit očekávané mapování, můžete zobrazit datovou část s ukázkovým požadavkem na stránce s nápovědu pro spuštění služby Batch pro váš koncový bod Azure Machine Learning Studio (Classic).


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář 2: experimenty pomocí modulů pro čtení a zápis, které odkazují na data v různých úložištích
Dalším běžným scénářem při vytváření Azure Machine Learning Studio (klasických) experimentů je použít Import dat a výstupní datové moduly. Modul Import dat slouží k načtení dat do experimentu a modulu výstupních dat je uložit data z experimentů. Podrobnosti o importu dat a modulech výstupních dat najdete v tématech [Import dat](/azure/machine-learning/studio-module-reference/import-data) a [výstupní data](/azure/machine-learning/studio-module-reference/export-data) v knihovně MSDN.

Při použití modulů importovat data a Output data je vhodné použít parametr webové služby pro každou vlastnost těchto modulů. Tyto webové parametry umožňují konfigurovat hodnoty za běhu. Můžete například vytvořit experiment s modulem importu dat, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby budete chtít povolit uživatelům webové služby, aby určili jiný logický SQL Server s názvem `YYY.database.windows.net` . Tuto hodnotu můžete nakonfigurovat pomocí parametru webové služby.

> [!NOTE]
> Vstup a výstup webové služby se liší od parametrů webové služby. V prvním scénáři jste viděli, jak lze zadat vstup a výstup pro webovou službu Azure Machine Learning Studio (Classic). V tomto scénáři předáte parametry webové služby, které odpovídají vlastnostem importovat datové/výstupní datové moduly.

Pojďme se podívat na scénář použití parametrů webové služby. Máte nasazenou webovou službu Azure Machine Learning Studio (Classic), která používá modul čtenáře ke čtení dat z jednoho zdroje dat podporovaného Azure Machine Learning Studio (Classic) (například: Azure SQL Database). Po provedení dávky se výsledky zapisují pomocí modulu zapisovače (Azure SQL Database).  V experimentech nejsou definovány vstupy a výstupy webové služby. V takovém případě doporučujeme nakonfigurovat parametry relevantní webové služby pro moduly pro čtení a zápis. Tato konfigurace umožňuje, aby byly moduly pro čtení a zápis nastaveny při použití aktivity AzureMLBatchExecution. Parametry webové služby v části **globalParameters** v kódu JSON aktivity určíte následujícím způsobem.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> V parametrech webové služby se rozlišují velká a malá písmena, proto zajistěte, aby názvy zadané v kódu JSON aktivity odpovídaly těm, které jsou zpřístupněny webovou službou.

Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity aktualizace prostředku Azure Machine Learning Studio (Classic)** . Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](update-machine-learning-models.md) .

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)