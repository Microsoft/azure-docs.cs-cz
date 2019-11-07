---
title: Vytváření prediktivních datových kanálů pomocí Azure Data Factory
description: Naučte se, jak vytvořit prediktivní kanál pomocí aktivity Azure Machine Learning-batch execution v Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 24568940a0f6e550ae0fe7658b81ba1c3b3d3556
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683776"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuální verze](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje sestavovat, testovat a nasazovat řešení prediktivní analýzy. Z hlediska vysoké úrovně je to provedeno ve třech krocích:

1. **Vytvořte školicí experiment**. Tento krok provedete pomocí nástroje Azure Machine Learning Studio. Azure Machine Learning Studio je vývojové prostředí pro týmovou spolupráci, které slouží k výuce a testování modelu prediktivní analýzy pomocí školicích dat.
2. **Převeďte ji na prediktivní experiment**. Jakmile se model vyškole pomocí stávajících dat a jste připraveni ho použít k vyhodnocení nových dat, připravujete a zjednodušte experimenty pro bodování.
3. **Nasaďte ji jako webovou službu**. Experiment bodování můžete publikovat jako webovou službu Azure. Můžete odesílat data do modelu prostřednictvím tohoto koncového bodu webové služby a získávat výsledky předpovědi z modelu.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory a Machine Learning dohromady
Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) pro prediktivní analýzy. Pomocí **aktivity dávkového spuštění** v kanálu Azure Data Factory můžete vyvolat webovou službu Azure Machine Learning Studio, abyste předpovědi data ve službě Batch.

Prediktivní modely ve vyhodnocování Azure Machine Learning studia se v průběhu času musí přenášet pomocí nových vstupních datových sad. Model můžete přeškolit z Data Factory kanálu, a to provedením následujících kroků:

1. Publikujte experimenty školení (ne prediktivní experiment) jako webovou službu. Tento krok provedete v nástroji Azure Machine Learning Studio jako při vystavení prediktivního experimentu jako webové služby v předchozím scénáři.
2. Pomocí aktivity spustit dávku Azure Machine Learning studia můžete vyvolat webovou službu pro experimenty školení. V podstatě můžete použít aktivitu spuštění aplikace Azure Machine Learning Studio k vyvolání webové služby pro vyhodnocování webové služby a bodování.

Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity prostředku Azure Machine Learning Studio Update**. Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](update-machine-learning-models.md) .

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning

Vytvoříte propojenou službu **Azure Machine Learning** k propojení webové služby Azure Machine Learning s objektem pro vytváření dat Azure. Propojená služba se používá v aktivitě Azure Machine Learning provádění služby Batch a při [aktualizaci aktivity prostředku](update-machine-learning-models.md).

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

Azure Machine Learning podporuje klasické webové služby a nové webové služby pro prediktivní experiment. Můžete vybrat jednu z nich, která bude použita z Data Factory. Chcete-li získat informace potřebné k vytvoření propojené služby Azure Machine Learning, přečtěte si https://services.azureml.net, kde jsou uvedeny všechny vaše (nové) webové služby a klasické webové služby. Klikněte na webovou službu, ke které chcete získat přístup, a klikněte na **spotřebovat** stránku. Zkopírujte **primární klíč** pro vlastnost **ApiKey** a **dávkové požadavky** na vlastnost **mlEndpoint** .

![Azure Machine Learning webové služby](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Aktivita spuštění Azure Machine Learning dávky

Následující fragment kódu JSON definuje aktivitu spuštění Azure Machine Learning dávky. Definice aktivity obsahuje odkaz na propojenou službu Azure Machine Learning, kterou jste vytvořili dříve.

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

| Vlastnost          | Popis                              | Požaduje se |
| :---------------- | :--------------------------------------- | :------- |
| jméno              | Název aktivity v kanálu     | Ano      |
| description       | Text popisující, co aktivita dělá.  | Ne       |
| type              | Pro aktivitu Data Lake Analytics U-SQL je typ aktivity **AzureMLBatchExecution**. | Ano      |
| linkedServiceName | Propojené služby pro propojenou službu Azure Machine Learning. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| webServiceInputs  | Páry klíč-hodnota, mapování názvů Azure Machine Learning vstupů webové služby. Klíč musí odpovídat vstupním parametrům definovaným v publikované webové službě Azure Machine Learning. Hodnota je dvojice vlastností Azure Storage propojených služeb a FilePath určující umístění vstupních objektů BLOB. | Ne       |
| webServiceOutputs | Páry klíč-hodnota, mapování názvů Azure Machine Learning výstupů webové služby. Klíč musí odpovídat výstupním parametrům definovaným v publikované webové službě Azure Machine Learning. Hodnota je dvojice vlastností Azure Storage propojených služeb a FilePath určující umístění výstupního objektu BLOB. | Ne       |
| globalParameters  | Páry klíč-hodnota, které se mají předávat do koncového bodu služby Azure Machine Learning Studio Batch pro spuštění. Klíče se musí shodovat s názvy parametrů webové služby, které jsou definované v publikované webové službě Azure Machine Learning Studio. Hodnoty se předávají do vlastnosti GlobalParameters žádosti o spuštění dávky Azure Machine Learning studia. | Ne       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář 1: experimenty s využitím vstupů a výstupů webové služby, které odkazují na data v Azure Blob Storage

V tomto scénáři používá webová služba Azure Machine Learning předpovědi data ze souboru v úložišti objektů BLOB v Azure a ukládá výsledky předpovědi do úložiště objektů BLOB. Následující JSON definuje Data Factory kanál s aktivitou AzureMLBatchExecution. Na vstupní a výstupní data v Azure blogu Storage se odkazuje pomocí páru s odkazem a FilePath. V ukázce propojené služby vstupů a výstupů se liší, ale můžete použít různé propojené služby pro každé z vašich vstupů a výstupů pro Data Factory, abyste mohli vybírat správné soubory a odesílat je do webové služby Azure Machine Learning Studio.

> [!IMPORTANT]
> Ve vašem experimentu Azure Machine Learning Studio, vstupní a výstupní porty webové služby a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které použijete pro nastavení webServiceInputs, webServiceOutputs a globalParameters, se musí přesně shodovat s názvy v experimentech. Chcete-li ověřit očekávané mapování, můžete zobrazit datovou část s ukázkovým požadavkem na stránce s nápovědu pro spuštění dávky pro váš koncový bod služby Azure Machine Learning Studio.
>
>

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
Dalším běžným scénářem při vytváření experimentů Azure Machine Learning studia je použití importovaných dat a modulů výstupních dat. Modul Import dat slouží k načtení dat do experimentu a modulu výstupních dat je uložit data z experimentů. Podrobnosti o importu dat a modulech výstupních dat najdete v tématech [Import dat](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [výstupní data](https://msdn.microsoft.com/library/azure/dn905984.aspx) v knihovně MSDN.

Při použití modulů importovat data a Output data je vhodné použít parametr webové služby pro každou vlastnost těchto modulů. Tyto webové parametry umožňují konfigurovat hodnoty za běhu. Můžete například vytvořit experiment s modulem importu dat, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby budete chtít povolit uživatelům webové služby, aby určili další SQL Server Azure s názvem `YYY.database.windows.net`. Tuto hodnotu můžete nakonfigurovat pomocí parametru webové služby.

> [!NOTE]
> Vstup a výstup webové služby se liší od parametrů webové služby. V prvním scénáři jste viděli, jak lze zadat vstupní a výstupní výstup pro webovou službu Azure Machine Learning Studio. V tomto scénáři předáte parametry webové služby, které odpovídají vlastnostem importovat datové/výstupní datové moduly.
>
>

Pojďme se podívat na scénář použití parametrů webové služby. Máte nasazenou Azure Machine Learning webovou službu, která používá modul čtenáře ke čtení dat z jednoho zdroje dat podporovaného Azure Machine Learning (například: Azure SQL Database). Po provedení dávky se výsledky zapisují pomocí modulu zapisovače (Azure SQL Database).  V experimentech nejsou definovány vstupy a výstupy webové služby. V takovém případě doporučujeme nakonfigurovat parametry relevantní webové služby pro moduly pro čtení a zápis. Tato konfigurace umožňuje, aby byly moduly pro čtení a zápis nastaveny při použití aktivity AzureMLBatchExecution. Parametry webové služby v části **globalParameters** v kódu JSON aktivity určíte následujícím způsobem.

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
>

Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity prostředku Azure Machine Learning Studio Update**. Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](update-machine-learning-models.md) .

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
