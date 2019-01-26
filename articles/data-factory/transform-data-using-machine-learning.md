---
title: Vytvořit prediktivní datové kanály pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prediktivní kanál pomocí Azure Machine Learning – aktivita provedení dávky služby ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 052839c679fc3efa61fca612b5139ede1991890c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080238"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuální verze](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat řešení prediktivní analýzy. Z vyšší úrovně pohledu se provádí ve třech krocích:

1. **Vytvořit výukový experiment**. Tento krok můžete provést pomocí Azure Machine Learning studio. Azure Machine Learning studio je spolupráce na vývoji visual prostředí, který používáte pro trénování a testování modelu prediktivní analýzy pomocí trénovací data.
2. **Převeďte jej na prediktivní experiment**. Jakmile vyškolila modelu s existujícími daty a budete chtít použít ke stanovení skóre pro nová data, Příprava a zefektivnit experimentu pro vyhodnocení.
3. **Ho nasadit jako webovou službu**. Hodnoticí experimentu můžete publikovat jako webová služba Azure. Může odesílat data do modelu přes tento koncový bod webové služby a zobrazí výsledky předpovědí z modelu.

### <a name="data-factory-and-machine-learning-together"></a>Objekt pro vytváření dat a strojové učení najednou
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikované [Azure Machine Learning] [azure-machine-learning] webové služby pro prediktivní analýzy. Použití **aktivita provedení dávky služby** v kanálu Azure Data Factory, můžete vyvolat webové služby Azure Machine Learning studio k vytvoření predikcí data ve službě batch.

V průběhu času prediktivních modelů v Azure Machine Learning studio hodnocení experimentů musí být retrained pomocí nové vstupní datové sady. Provedením následujících kroků můžete přeučování modelů z kanálu Data Factory:

1. Publikujte jako webovou službu výukového experimentu (ne prediktivní experiment). Tento krok v aplikaci Azure Machine Learning studio udělat, jako jste to udělali vystavit prediktivní experiment jako webové služby v předchozím scénáři.
2. Pomocí Azure Machine Learning studio aktivita provedení dávky služby k vyvolání webové služby pro výukového experimentu. V podstatě můžete použít Azure Machine Learning studio aktivita provedení dávky k vyvolání webové služby školení a webová služba pro vyhodnocení.

Jakmile budete hotovi s přetrénování aktualizovat hodnoticí webové služby (prediktivní experiment jako webové služby) s nově trénovaného modelu s použitím **aktivita prostředku aktualizace Azure Machine Learning studio**. Zobrazit [aktualizace modelů pomocí aktivity aktualizace prostředku](update-machine-learning-models.md) , kde najdete podrobnosti.

## <a name="azure-machine-learning-linked-service"></a>Služba Azure Machine Learning propojený

Vytvoření **Azure Machine Learning** propojenou službu, která propojí webové služby Azure Machine Learning do služby Azure data factory. Propojená služba používá aktivita provedení dávky služby do aplikace Azure Machine Learning a [aktivita prostředku aktualizace](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "apiVersion" : "2017-09-01-preview",
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

Zobrazit [propojené služby Compute](compute-linked-services.md) najdete popisy vlastností v definici JSON.

Azure Machine Learning podporuje klasické webové služby a nové webové služby pro prediktivní experiment. Můžete zvolit ten správný ze služby Data Factory. Pokud chcete získat informace potřebné k vytvoření propojené služby Azure Machine Learning, přejděte na https://services.azureml.net, kde jsou uvedeny všechny (Nový) webové služby a klasické webové služby. Klikněte na webové služby, které chcete získat přístup a klikněte na tlačítko **využívání** stránky. Kopírování **primární klíč** pro **apiKey** vlastnost, a **dávkových žádostí** pro **mlEndpoint** vlastnost.

![Azure Machine Learning Web Services](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Aktivita provedení dávky služby Machine Learning Azure

Následující fragment kódu JSON definuje aktivita provedení dávky služby Machine Learning Azure. Definice aktivity obsahuje odkaz na službu Azure Machine Learning propojený, kterou jste vytvořili dříve.

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
| description       | Text popisující, jakým způsobem aktivita naloží.  | Ne       |
| type              | Aktivita Data Lake Analytics U-SQL je typ aktivity **AzureMLBatchExecution**. | Ano      |
| linkedServiceName | Propojené služby Azure Machine Learning propojenou službu. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| webServiceInputs  | Klíč, hodnota dvojice mapování názvů Azure Machine Learning Web Service vstupy. Klíč musí odpovídat vstupní parametry definované v webové služby pro publikované Azure Machine Learning. Hodnota je propojené služby Azure Storage a FilePath vlastnosti páru určují vstupní umístění objektu Blob. | Ne       |
| webServiceOutputs | Klíč, hodnota dvojice mapování názvů Azure Machine Learning Web Service výstupy. Klíč musí odpovídat výstupní parametry definované v webové služby pro publikované Azure Machine Learning. Hodnota je propojené služby Azure Storage a FilePath dvojici vlastností zadání výstup Blob umístění. | Ne       |
| globalParameters  | Páry klíčů, hodnota má být předán služba Batch Execution koncový bod Azure Machine Learning studio. Klíče musí shodovat s názvy parametrů webové služby definované v publikované webové služby Azure Machine Learning studio. Hodnoty se předávají ve vlastnosti GlobalParameters žádost o Azure Machine Learning studio batch spuštění | Ne       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář 1: Experimentů využívajících webové služby vstupů nebo výstupů, které odkazují na data ve službě Azure Blob Storage

V tomto scénáři službu Azure Machine Learning Web vytváří předpovědi pomocí dat ze souboru ve službě Azure blob storage a uchovává výsledky předpovědí ve službě blob storage. Následující kód JSON definuje kanál služby Data Factory s aktivitou AzureMLBatchExecution. Vstupní a výstupní data ve službě Azure Blog Storage se odkazuje pomocí dvojice LinkedName a cesta k souboru. V ukázce se liší propojené služby vstupy a výstupy, budete moci vybrat správné soubory a odeslat do webové služby Azure Machine Learning studio můžete použít jiné propojené služby pro každou z vašich vstupů nebo výstupů pro službu Data Factory.

> [!IMPORTANT]
> V experimentu služby Azure Machine Learning studio, vstup webové služby a výstupní porty a globální parametry mají výchozí názvy ("vstup1", "vstup2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvům v experimenty. Na stránce nápovědy spouštění dávek pro koncový bod Azure Machine Learning studio můžete ověřit očekávané mapování můžete zobrazit ukázkovou datovou část požadavku.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář 2: Používání modulů čtení/zápis odkazovat na data v různých úložných experimentů
Další z typických možností při vytváření experimenty Azure Machine Learning studio je použití modulů importovat Data a výstupní Data. Modul pro Import dat se používá k načtení dat do experimentu a výstupní Data modulu je k uložení dat z experimentů. Podrobnosti o moduly Import dat a výstupních dat najdete v tématu [Import dat](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [výstupní Data](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN.

Pokud používáte moduly Import dat a výstupní Data, je vhodné použít parametr webové služby pro každou vlastnost tyto moduly. Tyto parametry webové umožňují nakonfigurovat hodnoty za běhu. Můžete například vytvořit experimentu pomocí modulu Import dat, která používá službu Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby, chcete povolit uživatelům webové služby, zadejte jiný Server SQL Azure volá `YYY.database.windows.net`. Parametr webové služby můžete povolit tuto hodnotu a nakonfigurovat.

> [!NOTE]
> Webové služby vstup a výstup se liší od parametrů webové služby. V první scénář jste viděli, jak vstup a výstup se dá nastavit pro Azure Machine Learning studio webové služby. V tomto scénáři předat parametry webové služby, které odpovídají vlastnosti moduly Import dat a výstupní Data.
>
>

Podívejme se na scénář použití parametrů webové služby. Máte nasazené webové služby Azure Machine Learning, který používá modul čtečky číst data z jednoho zdroje dat podporované službou Azure Machine Learning (například: Azure SQL Database). Po provedení příkazu batch se provádí, výsledky jsou zapsány pomocí zapisovače modulu (Azure SQL Database).  Žádné webové služby vstupy a výstupy jsou definovány v experimenty. V takovém případě doporučujeme nakonfigurovat parametry příslušné webové služby pro moduly čtečky a zapisovače. Tato konfigurace umožňuje čtení/zápis moduly, které nakonfigurují při použití AzureMLBatchExecution aktivity. Zadejte parametry webové služby v **globalParameters** části v kódu JSON aktivity následujícím způsobem.

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
> Parametry webové služby jsou malá a velká písmena, zajistěte proto, že názvy, které zadáte v aktivitě JSON odpovídají těm, které jsou vystavené webové služby.
>

Jakmile budete hotovi s přetrénování aktualizovat hodnoticí webové služby (prediktivní experiment jako webové služby) s nově trénovaného modelu s použitím **aktivita prostředku aktualizace Azure Machine Learning studio**. Zobrazit [aktualizace modelů pomocí aktivity aktualizace prostředku](update-machine-learning-models.md) , kde najdete podrobnosti.

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
