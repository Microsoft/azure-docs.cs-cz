---
title: Vytvoření prediktivních datových kanálů
description: Zjistěte, jak vytvořit prediktivní kanál pomocí Azure Machine Learning – aktivita dávkového spuštění v Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 44371c78a4d02588eef21aae5a4bba3d033763d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029988"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí služby Azure Machine Learning a Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuální verze](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat prediktivní analytická řešení. Z hlediska vysoké úrovně se provádí ve třech krocích:

1. **Vytvořte trénovací experiment**. Tento krok provést pomocí Azure Machine Learning Studio (klasické). Azure Machine Learning Studio (classic) je prostředí pro vizuální vývoj spolupráce, které používáte k trénování a testování modelu prediktivní analýzy pomocí trénovacích dat.
2. **Převeďte jej na prediktivní experiment**. Jakmile váš model byl trénovaný s existujícími daty a jste připraveni jej použít k skóre nových dat, připravíte a zjednodušíte experiment pro vyhodnocování.
3. **Nasaďte ji jako webovou službu**. Experiment s vyhodnocování můžete publikovat jako webovou službu Azure. Můžete odeslat data do modelu prostřednictvím tohoto koncového bodu webové služby a přijímat předpovědi výsledků z modelu.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory a Machine Learning společně
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikovanou webovou službu [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) pro prediktivní analýzy. Pomocí **aktivity dávkového spuštění** v kanálu Azure Data Factory můžete vyvolat webovou službu Azure Machine Learning Studio (klasické) a vytvořit předpovědi na data v dávce.

V průběhu času je třeba prediktivní modely v Azure Machine Learning Studio (klasické) vyhodnocování experimenty retrained pomocí nových vstupních datových sad. Model z kanálu datové továrny můžete přeškolit následujícím postupem:

1. Publikujte trénovací experiment (ne prediktivní experiment) jako webovou službu. Tento krok provést v Azure Machine Learning Studio (klasické) jako jste odhalili prediktivní experiment jako webovou službu v předchozím scénáři.
2. Použijte Azure Machine Learning Studio (klasické) dávkové spuštění aktivity k vyvolání webové služby pro trénovací experiment. V podstatě můžete použít Azure Machine Learning Studio (klasické) dávkové spuštění aktivity k vyvolání školení webové služby a vyhodnocování webové služby.

Po dokončení retraining, aktualizovat bodování webové služby (prediktivní experiment vystavena jako webová služba) s nově vyškolený model pomocí **Azure Machine Learning Studio (klasické) Aktualizace aktivity zdrojů**. Podrobnosti najdete v článku Aktualizace modelů pomocí článku [Aktualizace aktivity prostředků.](update-machine-learning-models.md)

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning

Vytvoříte propojenou službu **Azure Machine Learning,** která propojí webovou službu Azure Machine Learning s totodatovou továrně dat Azure. Propojená služba se používá azure machine learning dávkové provádění aktivity a [aktualizovat aktivitu prostředků](update-machine-learning-models.md).

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

V článku [Výpočetní propojené služby](compute-linked-services.md) článek popisy vlastností v definici JSON.

Azure Machine Learning podporuje klasické webové služby i nové webové služby pro prediktivní experiment. V datové továrně si můžete vybrat ten správný. Informace potřebné k vytvoření propojené služby Azure Machine https://services.azureml.netLearning získáte na jdete na stránku , kde jsou uvedeny všechny vaše (nové) webové služby a klasické webové služby. Klikněte na webovou službu, ke které chcete získat přístup, a klikněte na **stránku Spotřebovávat.** Zkopírujte **primární klíč** pro **vlastnost apiKey** a **dávkové požadavky** pro vlastnost **mlEndpoint.**

![Webové služby Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Aktivita dávkového spouštění azure machine learningu

Následující fragment JSON definuje aktivitu dávkového spouštění azure strojového učení. Definice aktivity obsahuje odkaz na propojenou službu Azure Machine Learning, kterou jste vytvořili dříve.

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
| type              | Pro aktivitu U-SQL analýzy datového jezera je typem aktivity **AzureMLBatchExecution**. | Ano      |
| linkedServiceName | Propojené služby s propojenou službou Azure Machine Learning. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| webServiceInputs  | Klíč, dvojice hodnot, mapování názvů vstupů webové služby Azure Machine Learning. Klíč musí odpovídat vstupním parametrům definovaným v publikované webové službě Azure Machine Learning. Hodnota je dvojice vlastností propojených úložišť Azure Storage a FilePath určující vstupní umístění objektů Blob. | Ne       |
| webServiceOutputs | Klíč, dvojice hodnot, mapování názvů výstupů webové služby Azure Machine Learning. Klíč musí odpovídat výstupním parametrům definovaným v publikované webové službě Azure Machine Learning. Hodnota je dvojice vlastností propojených úložišť Azure Storage a FilePath určující výstupní umístění objektů Blob. | Ne       |
| globalParameters  | Klíč, dvojice hodnot, které mají být předány koncovému bodu služby Batch Execution Service služby Azure Machine Learning Studio (klasické). Klíče musí odpovídat názvům parametrů webové služby definovaných v publikované webové službě Azure Machine Learning Studio (klasické). Hodnoty jsou předané ve vlastnosti GlobalParameters aplikace Azure Machine Learning Studio (klasický) požadavek na dávkové spuštění | Ne       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář 1: Experimenty pomocí vstupů a výstupů webové služby, které odkazují na data v úložišti objektů blob Azure

V tomto scénáři Azure Machine Learning webové služby provede předpovědi pomocí dat ze souboru v úložišti objektů blob Azure a ukládá výsledky předpověď v úložišti objektů blob. Následující JSON definuje kanál datové továrny s aktivitou AzureMLBatchExecution. Vstupní a výstupní data ve službě Azure Blog Storage se odkazuje pomocí dvojice LinkedName a FilePath. V ukázkové propojené službě vstupů a výstupů se liší, můžete použít různé propojené služby pro každý z vašich vstupů nebo výstupů pro Data Factory, aby bylo možné vyzvednout správné soubory a odeslat do Azure Machine Learning Studio (klasické) webové služby.

> [!IMPORTANT]
> V experimentu Azure Machine Learning Studio (klasický) mají vstupní a výstupní porty webové služby a globální parametry výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvy v experimentech. Můžete zobrazit datové části ukázkového požadavku na stránce nápovědy dávkového spuštění pro váš koncový bod Azure Machine Learning Studio (klasický) k ověření očekávaného mapování.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář 2: Experimenty používající moduly čtečky a zapisovače k odkazování na data v různých úložištích
Dalším běžným scénářem při vytváření experimentů Azure Machine Learning Studio (klasické) je použití modulů importu dat a výstupních dat. Modul Importdat se používá k načtení dat do experimentu a modul Výstupní data je pro ukládání dat z experimentů. Podrobnosti o modulech Import dat a výstupních dat naleznete v tématech [Import dat](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [výstupních dat](https://msdn.microsoft.com/library/azure/dn905984.aspx) v knihovně MSDN.

Při použití modulů Import dat a výstupních dat je vhodné použít parametr webové služby pro každou vlastnost těchto modulů. Tyto webové parametry umožňují konfigurovat hodnoty za běhu. Můžete například vytvořit experiment s modulem importu dat, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby chcete povolit uživatelům webové služby zadat další s `YYY.database.windows.net`názvem Azure SQL Server . Chcete-li povolit konfiguraci této hodnoty, můžete použít parametr webové služby.

> [!NOTE]
> Vstup a výstup webové služby se liší od parametrů webové služby. V prvním scénáři jste viděli, jak lze zadat vstup a výstup pro azure machine learning studio (klasické) webové služby. V tomto scénáři předáte parametry pro webovou službu, které odpovídají vlastnostem modulů Import data/výstupních dat.
>
>

Podívejme se na scénář pro použití parametrů webové služby. Máte nasazenou webovou službu Azure Machine Learning, která používá modul čtečky ke čtení dat z jednoho ze zdrojů dat podporovaných Azure Machine Learning (například Azure SQL Database). Po spuštění dávky se výsledky zapisují pomocí modulu Writer (Azure SQL Database).  V experimentech nejsou definovány žádné vstupy a výstupy webové služby. V takovém případě doporučujeme nakonfigurovat příslušné parametry webové služby pro moduly čtečky a zapisovače. Tato konfigurace umožňuje nakonfigurovat moduly čtečky a zapisovače při použití aktivity AzureMLBatchExecution. Parametry webové služby zadáte v části **globalParameters** v aktivitě JSON následujícím způsobem.

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
> Parametry webové služby rozlišují malá a velká písmena, takže se ujistěte, že názvy zadané v aktivitě JSON odpovídají názvům vystaveným webovou službou.
>

Po dokončení retraining, aktualizovat bodování webové služby (prediktivní experiment vystavena jako webová služba) s nově vyškolený model pomocí **Azure Machine Learning Studio (klasické) Aktualizace aktivity zdrojů**. Podrobnosti najdete v článku Aktualizace modelů pomocí článku [Aktualizace aktivity prostředků.](update-machine-learning-models.md)

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
