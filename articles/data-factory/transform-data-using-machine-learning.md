---
title: Vytvoření kanálů prediktivní dat pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak vytvořit prediktivní kanál pomocí Azure Machine Learning - aktivita provedení dávky v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 8d87facdf1724ddbcb3bb6d7c189d8ff41d44973
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytvořit prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Verze 2 – Preview](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat řešení prediktivní analýzy. Z vysoké úrovně hlediska se provádí v tři kroky:

1. **Vytvoření experimentu školení**. Tento krok se provádí pomocí nástroje Azure ML Studio. ML studio se spolupráce vizuální vývojové prostředí, které používáte a natrénuje a otestuje model prediktivní analýzy pomocí Cvičná data.
2. **Převést na prediktivní experiment**. Jakmile model byl vyzkoušen s existujícími daty a budete chtít použít skóre pro nová data, připravte a zjednodušit experimentu pro vyhodnocování.
3. **Nasadit jako webovou službu**. Vyhodnocování experimentu můžete publikovat jako Azure webové služby. Můžete posílat data do modelu přes tento koncový bod webové služby a přijímat výsledek předpovědi z modelu.  

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivita provedení dávky Machine Learning v V1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Objekt pro vytváření dat a strojové učení společně
Azure Data Factory můžete snadno vytvořit kanály, které používají publikované [Azure Machine Learning] [azure machine learning] webové služby pro prediktivní analýzy. Pomocí **aktivita provedení dávky** v kanál služby Azure Data Factory můžete vyvolat webové služby Azure ML provádět předpovědi na datech v dávce. 

V průběhu času prediktivní modely v Azure ML vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Model Azure ML z objektu pro vytváření dat kanál můžete přeučování provedením následujících kroků:

1. Publikujte výukový experiment (ne prediktivní experiment) jako webovou službu. Tento krok v Azure ML Studio můžete udělat, jako jste to udělali vystavit prediktivní experiment jako webovou službu v předchozím scénáři.
2. Použijte aktivita provedení dávky Azure ML k vyvolání webové služby pro výukový experiment. V podstatě můžete aktivita provedení dávky Azure ML k vyvolání webové služby školení a vyhodnocování webové služby.

Po dokončení práce s retraining, aktualizovat vyhodnocování webové služby (prediktivní experiment zveřejněné jako webovou službu) s nově trénovaného modelu pomocí **aktivita prostředku aktualizace Azure ML**. V tématu [aktualizace modelů pomocí aktivita prostředku aktualizace](update-machine-learning-models.md) článku.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning propojené služby

Vytvoříte **Azure Machine Learning** propojená služba propojení webové služby Azure Machine Learning s objektem pro vytváření dat Azure. Propojené služby se používá pomocí Azure Machine Learning dávky spuštění aktivity a [aktivita prostředku aktualizace](update-machine-learning-models.md). 


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
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

V tématu [výpočetní propojené služby](compute-linked-services.md) článku popisy o vlastnostech v definici JSON. 

Azure Machine Learning podporuje Classic webové služby a nové webové služby pro prediktivní experiment. Můžete zvolit ten správný používat z datové továrny. Chcete-li získat informace potřebné pro vytvoření propojené služby Azure Machine Learning, přejděte na https://services.azureml.net, kde jsou uvedeny všechny (Nový) webové služby a Classic webové služby. Klikněte na webovou službu chcete přistupovat a klikněte na **spotřebě** stránky. Kopírování **primární klíč** pro **apiKey** vlastnost, a **dávkových žádostí** pro **mlEndpoint** vlastnost. 

![Webové služby Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Aktivita služby Azure Machine Learning Batch Execution

Následující fragment kódu JSON definuje Azure Machine Learning dávkového spuštění aktivity. Definici aktivity obsahuje odkaz na službu Azure Machine Learning propojené, kterou jste vytvořili dříve. 

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
| description       | Popisuje, jakým způsobem aktivita naloží text.  | Ne       |
| type              | Aktivity Data Lake Analytics U-SQL, je typ aktivity **AzureMLBatchExecution**. | Ano      |
| linkedServiceName | Propojené služby Azure Machine Learning propojená služba. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| webServiceInputs  | Hodnota páry klíčů, mapování názvů Azure Machine Learning webové služby vstupy. Klíč se musí shodovat vstupní parametry definované v publikované Azure Machine Learning webovou službu. Hodnota je propojené služby úložiště Azure a FilePath vlastnosti dvojici zadání vstupní umístění objektu Blob. | Ne       |
| webServiceOutputs | Hodnota páry klíčů, mapování názvů Azure Machine Learning webové služby výstupy. Klíč se musí shodovat výstupní parametry definované v publikované Azure Machine Learning webovou službu. Hodnota je propojené služby úložiště Azure a FilePath vlastnosti pár zadání výstup objektu Blob umístění. | Ne       |
| globalParameters  | Páry klíčů, hodnota má být předán služba provedení dávky Azure ML koncový bod. Klíče musí shodovat s názvy definované v publikované webové služby Azure ML parametry webové služby. Hodnoty jsou předávány ve vlastnosti GlobalParameters požadavek provedení dávky Azure ML | Ne       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář 1: Experimenty pomocí webové služby vstupy/výstupy odkazující na data v Azure Blob Storage

V tomto scénáři Azure Machine Learning webovou službu díky předpovědi pomocí dat ze souboru v Azure blob storage a ukládá výsledky předpovědi ve službě blob storage. Následující kód JSON určuje objekt pro vytváření dat kanál s AzureMLBatchExecution aktivitu. Vstupní a výstupní data ve službě Azure Blog Storage se odkazuje pomocí pár LinkedName a cesta k souboru. V ukázce se liší propojené služby vstupy a výstupy, můžete použít jiné propojené služby pro každou z vaší vstupy/výstupy pro vytváření dat mohli vyzvednutí správné soubory a odeslat do služby Azure ML Web Service. 

> [!IMPORTANT]
> V Azure ML experiment, vstup webové služby a výstup porty a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které můžete použít pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně shodovat názvy v experimenty. Datová část požadavku ukázka můžete zobrazit na stránce pomůže spuštění dávky pro koncový bod služby Azure ML ověření očekávané mapování.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář 2: Experimenty, používání modulů Reader/Writer odkazovat na data v různých úložných
Další z typických možností při vytváření experimenty Azure ML je použití modulů importovat Data a výstupní Data. Import dat modul se používá k načtení dat do experimentu a výstupní Data modulu je k uložení dat z experimentů. Podrobnosti o importovat Data a výstupní Data modulech najdete v tématu [importovat Data](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [výstupní Data](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN.     

Při použití modulů importovat Data a výstupní Data, je vhodné použít parametr webové služby pro každou vlastnost tyto moduly. Tyto parametry webové umožňují konfiguraci hodnot, které za běhu. Můžete například vytvořit experimentu s modul importovat Data, která používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby, které chcete povolit příjemcům webové služby, zadejte jiný Server SQL Azure, názvem `YYY.database.windows.net`. Parametr webové služby můžete povolit tuto hodnotu nakonfigurovat.

> [!NOTE]
> Webové služby vstup a výstup se liší od parametry webové služby. V prvního scénáře jste viděli, jak vstup a výstup lze zadat pro Azure ML webové služby. V tomto scénáři můžete předat parametry webové služby, které odpovídají vlastnosti modulů importu dat a výstupní Data.
>
> 

Podívejme se na scénář použití parametry webové služby. Máte nasazené webové službě Azure Machine Learning, která používá modul čtečky číst data z jednoho zdroje dat podporované rozhraním Azure Machine Learning (například: Azure SQL Database). Po provedení dávkového spuštění výsledky jsou zapsány pomocí zapisovače modulu (Azure SQL Database).  Žádné webové služby vstupy a výstupy jsou definovány v experimenty. V takovém případě doporučujeme nakonfigurovat parametry relevantní webové služby pro moduly čtení a zápis. Tato konfigurace umožňuje čtečky nebo zapisovač moduly nakonfigurovat při použití AzureMLBatchExecution aktivity. Zadejte parametry webové služby v **globalParameters** část v kódu JSON aktivity následujícím způsobem.

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
> Parametry webové služby je malá a velká písmena, takže shodné názvy, které zadáte v aktivitě JSON těm, které jsou vystavené webovou službu.
>

Po dokončení práce s retraining, aktualizovat vyhodnocování webové služby (prediktivní experiment zveřejněné jako webovou službu) s nově trénovaného modelu pomocí **aktivita prostředku aktualizace Azure ML**. V tématu [aktualizace modelů pomocí aktivita prostředku aktualizace](update-machine-learning-models.md) článku.



## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
