---
title: Vytváření prediktivních datových kanálů pomocí Azure Data Factory
description: Popisuje, jak vytvořit prediktivní kanály pomocí Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683145"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí služby Azure Machine Learning a Azure Data Factory

> [!div class="op_single_selector" title1="Transformační aktivity"]
> * [Aktivita úlu](data-factory-hive-activity.md)
> * [Aktivita prasat](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
> * [Aktivita jiskry](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Úvod
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma transformace dat pomocí strojového učení v datové továrně](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat prediktivní analytická řešení. Z hlediska vysoké úrovně se provádí ve třech krocích:

1. **Vytvořte trénovací experiment**. Tento krok provést pomocí Azure Machine Learning studio. Azure Machine Learning studio je kolaborativní prostředí pro vizuální vývoj, které používáte k trénování a testování modelu prediktivní analýzy pomocí trénovacích dat.
2. **Převeďte jej na prediktivní experiment**. Jakmile váš model byl trénovaný s existujícími daty a jste připraveni jej použít k skóre nových dat, připravíte a zjednodušíte experiment pro vyhodnocování.
3. **Nasaďte ji jako webovou službu**. Experiment s vyhodnocování můžete publikovat jako webovou službu Azure. Můžete odeslat data do modelu prostřednictvím tohoto koncového bodu webové služby a přijímat předpovědi výsledků fro modelu.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory je cloudová služba integrace dat, která orchestruje a automatizuje **pohyb** a **transformaci** dat. Pomocí Azure Data Factory můžete vytvářet řešení pro integraci dat, která můžou ingestovat data z různých úložišť dat, transformovat nebo zpracovat data a publikovat data výsledků do úložišť dat.

Služba Data Factory umožňuje vytvářet datové kanály, které přesouvají a transformují data, a následně tyto kanály spouštět podle zadaného plánu (každou hodinu, denně, týdně atd.). Poskytuje také bohaté vizualizace pro zobrazení rodokmenu a závislostí mezi vašimi datovými kanály a monitorování všech datových kanálů z jednoho jednotného zobrazení pro jednodušší identifikaci problémů a nastavení výstrah monitorování.

Podívejte [se na úvod do Azure Data Factory](data-factory-introduction.md) a [sestavte si první](data-factory-build-your-first-pipeline.md) články kanálu, abyste mohli rychle začít se službou Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory a Machine Learning společně
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikovanou webovou službu [Azure Machine Learning][azure-machine-learning] pro prediktivní analýzy. Pomocí **aktivity dávkového spuštění** v kanálu Azure Data Factory můžete vyvolat webovou službu Studio Azure Machine Learning a vytvořit předpovědi na data v dávce. Podrobnosti najdete v tématu Vyvolání webové služby studia Azure Machine Learning studio pomocí části Aktivita dávkového spuštění.

V průběhu času je třeba prediktivní modely v Azure Machine Learning studio hodnocení experimenty retrained pomocí nových vstupních datových sad. Model studia Azure Machine Learning můžete přeškolit z kanálu datové továrny následujícím způsobem:

1. Publikujte trénovací experiment (ne prediktivní experiment) jako webovou službu. Tento krok v Azure Machine Learning studio, stejně jako jste se vystavit prediktivní experiment jako webové služby v předchozím scénáři.
2. Pomocí aktivity dávkového spuštění studia Azure Machine Learning studio k vyvolání webové služby pro trénovací experiment. V podstatě můžete použít Azure Machine Learning studio batch execution aktivity vyvolat školení webové služby a vyhodnocování webové služby.

Po dokončení rekvalifikace aktualizujte webovou službu hodnocení (prediktivní experiment vystavený jako webová služba) s nově trénovaným modelem pomocí **Azure Machine Learning studio Update Resource Activity**. Podrobnosti najdete v článku Aktualizace modelů pomocí článku [Aktualizace aktivity prostředků.](data-factory-azure-ml-update-resource-activity.md)

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Vyvolání webové služby pomocí aktivity dávkového spuštění
Azure Data Factory slouží k orchestraci přesunu a zpracování dat a následnému provádění dávkového spuštění pomocí Azure Machine Learning. Zde jsou kroky nejvyšší úrovně:

1. Vytvořte propojenou službu Azure Machine Learning. Potřebujete následující hodnoty:

   1. **Požadavek identifikátoru URI** pro rozhraní API pro spuštění dávky. Identifikátor URI požadavku můžete najít kliknutím na odkaz **SPUŠTĚNÍ DÁVKY** na stránce webových služeb.
   2. **Klíč rozhraní API** pro publikovanou webovou službu Azure Machine Learning. Klíč rozhraní API můžete najít kliknutím na webovou službu, kterou jste publikovali.
   3. Použijte aktivitu **AzureMLBatchExecution.**

      ![Řídicí panel strojového učení](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identifikátor URI dávky](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář: Experimenty pomocí vstupů a výstupů webové služby, které odkazují na data v úložišti objektů blob Azure
V tomto scénáři Azure Machine Learning webové služby provede předpovědi pomocí dat ze souboru v úložišti objektů blob Azure a ukládá výsledky předpověď v úložišti objektů blob. Následující JSON definuje kanál datové továrny s aktivitou AzureMLBatchExecution. Aktivita má datovou sadu **DecisionTreeInputBlob** jako vstup a **DecisionTreeResultBlob** jako výstup. **DecisionTreeInputBlob** je předán jako vstup do webové služby pomocí **vlastnosti webServiceInput** JSON. **DecisionTreeResultBlob** je předán jako výstup webové služby pomocí **vlastnosti webServiceOutputs** JSON.

> [!IMPORTANT]
> Pokud webová služba trvá více vstupů, použijte vlastnost **webServiceInputs** namísto použití **webServiceInput**. Viz [webová služba vyžaduje více vstupů](#web-service-requires-multiple-inputs) části příklad použití vlastnosti webServiceInputs.
>
> Datové sady, na které odkazují vlastnosti **webServiceInput**/**webServiceInputs** a **webServiceOutputs** (v **typeProperties**) musí být také zahrnuty do vstupů a **výstupů** **Aktivity** .
>
> V experimentu studia Azure Machine Learning studio vstupní a výstupní porty webové služby a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvy v experimentech. Můžete zobrazit datové části ukázkového požadavku na stránce nápovědy dávkového spuštění pro koncový bod studia Azure Machine Learning a ověřit očekávané mapování.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Pouze vstupy a výstupy aktivity AzureMLBatchExecution mohou být předány jako parametry webové službě. Například ve výše uvedeném fragmentu JSON, DecisionTreeInputBlob je vstup pro Aktivitu AzureMLBatchExecution, která je předána jako vstup do webové služby prostřednictvím parametru webServiceInput.
>
>

### <a name="example"></a>Příklad
Tento příklad používá Azure Storage k uložení vstupních i výstupních dat.

Doporučujeme projít sestavení [první ho potrubí s datovou továrnou][adf-build-1st-pipeline] kurz před procházením tohoto příkladu. Pomocí Editoru datových toků můžete v tomto příkladu vytvářet artefakty datové továrny (propojené služby, datové sady, kanál).

1. Vytvořte **propojenou službu** pro azure **storage**. Pokud jsou vstupní a výstupní soubory v různých účtech úložiště, potřebujete dvě propojené služby. Zde je příklad JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Vytvořte **vstupní** **datovou sadu**Azure Data Factory . Na rozdíl od některých jiných datových sad Data Factory musí tyto datové sady obsahovat hodnoty **folderPath** i **fileName.** Dělení můžete použít k tomu, aby každé spuštění dávky (každý řez dat) zpracovat nebo vytvořit jedinečné vstupní a výstupní soubory. Možná budete muset zahrnout některé upstream aktivity transformovat vstup do formátu souboru CSV a umístit jej do účtu úložiště pro každý řez. V takovém případě by nezahrnovala **externí** a **externínastavení dat** uvedené v následujícím příkladu a vaše DecisionTreeInputBlob by výstupní datovou sadu jiné aktivity.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    Vstupní soubor CSV musí mít řádek záhlaví sloupce. Pokud používáte **kopírovat aktivitu** k vytvoření nebo přesunutí csv do úložiště objektů blob, měli byste nastavit vlastnost jímky **blobWriterAddHeader** na **true**. Například:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Pokud soubor CSV nemá řádek záhlaví, může se zobrazit následující chyba: **Chyba v aktivitě: Chyba při čtení řetězce. Neočekávaný token: StartObject. Cesta '', řádek 1, pozice 1**.
3. Vytvořte **výstupní** **datovou sadu**Azure Data Factory . Tento příklad používá dělení k vytvoření jedinečné výstupní cesty pro každé spuštění řezu. Bez dělení by aktivita přepsat soubor.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Vytvořte **propojenou službu** typu: **AzureMLLinkedService**, která poskytuje klíč rozhraní API a adresu URL spuštění dávky modelu.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Nakonec vytvořte kanál obsahující **aktivitu AzureMLBatchExecution.** Za běhu pipeline provádí následující kroky:

   1. Získá umístění vstupního souboru ze vstupních datových sad.
   2. Vyvolá rozhraní API pro spuštění dávky Azure Machine Learning.
   3. Zkopíruje výstup dávkového spuštění do objektu blob uvedeného ve výstupní datové sadě.

      > [!NOTE]
      > AzureMLBatchExecution aktivita může mít nula nebo více vstupů a jeden nebo více výstupů.
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Datum **zahájení** i **ukončení** musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **Koncový** čas je volitelný. Pokud nezadáte hodnotu **pro koncovou** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

      > [!NOTE]
      > Zadání vstupu pro aktivitu AzureMLBatchExecution je volitelné.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář: Experimenty používající moduly čtečky a zapisovače k odkazování na data v různých úložištích
Dalším běžným scénářem při vytváření experimentů studia Azure Machine Learning je použití modulů Reader a Writer. Modul čtečky se používá k načtení dat do experimentu a modul zapisovače je pro ukládání dat z experimentů. Podrobnosti o modulech pro čtení a zapisování naleznete v tématech [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) v knihovně MSDN.

Při použití modulů čtečky a zapisovače je vhodné použít parametr webové služby pro každou vlastnost těchto modulů čtečky a zapisovače. Tyto webové parametry umožňují konfigurovat hodnoty za běhu. Můžete například vytvořit experiment s modulem čtečky, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby chcete povolit uživatelům webové služby zadat další Azure SQL Server s názvem YYY.database.windows.net. Chcete-li povolit konfiguraci této hodnoty, můžete použít parametr webové služby.

> [!NOTE]
> Vstup a výstup webové služby se liší od parametrů webové služby. V prvním scénáři jste viděli, jak lze zadat vstup a výstup pro webovou službu studio Azure Machine Learning. V tomto scénáři předáte parametry pro webovou službu, které odpovídají vlastnostem modulů pro čtení a zápis.
>
>

Podívejme se na scénář pro použití parametrů webové služby. Máte nasazenou webovou službu Azure Machine Learning, která používá modul čtečky ke čtení dat z jednoho ze zdrojů dat podporovaných Azure Machine Learning (například Azure SQL Database). Po spuštění dávky se výsledky zapisují pomocí modulu Writer (Azure SQL Database).  V experimentech nejsou definovány žádné vstupy a výstupy webové služby. V takovém případě doporučujeme nakonfigurovat příslušné parametry webové služby pro moduly čtečky a zapisovače. Tato konfigurace umožňuje nakonfigurovat moduly čtečky a zapisovače při použití aktivity AzureMLBatchExecution. Parametry webové služby zadáte v části **globalParameters** v aktivitě JSON následujícím způsobem.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Funkce data [factory](data-factory-functions-variables.md) můžete také použít při předávání hodnot pro parametry webové služby, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby rozlišují malá a velká písmena, takže se ujistěte, že názvy zadané v aktivitě JSON odpovídají názvům vystaveným webovou službou.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Použití modulu Reader ke čtení dat z více souborů v objektu Blob Azure
Kanály velkých objemů dat s aktivitami, jako je Pig a Hive, mohou vytvářet jeden nebo více výstupních souborů bez rozšíření. Pokud například zadáte externí tabulku Hive, data pro externí tabulku Hive mohou být uložena v úložišti objektů blob Azure s následujícím názvem 000000_0. Modul čtečky můžete použít v experimentu ke čtení více souborů a použít je pro předpovědi.

Při použití modulu čtečky v experimentu Azure Machine Learning, můžete zadat Objekt blob Azure jako vstup. Soubory v úložišti objektů blob Azure mohou být výstupní soubory (příklad: 000000_0), které jsou vytvářeny skriptem Pig a Hive spuštěným na HDInsight. Modul čtečky umožňuje číst soubory (bez rozšíření) konfigurací **cesty ke kontejneru, adresáře/objektu blob**. **Cesta ke kontejneru** odkazuje na kontejner a **adresář/objekt blob** odkazuje na složku, která obsahuje soubory, jak je znázorněno na následujícím obrázku. Hvězdička, která je, ) \* **určuje, že všechny soubory v kontejneru/složce (to znamená data/aggregateddata/year=2014/month-6/\*)** jsou čteny jako součást experimentu.

![Vlastnosti objektů blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Příklad
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Kanál s aktivitou AzureMLBatchExecution s parametry webové služby

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

Ve výše uvedeném příkladu JSON:

* Nasazená webová služba Azure Machine Learning používá modul čtečky a zapisovače ke čtení a zápisu dat z nebo do databáze Azure SQL. Tato webová služba zpřístupňuje následující čtyři parametry: název databázového serveru, název databáze, název uživatelského účtu serveru a heslo uživatelského účtu serveru.
* Datum **zahájení** i **ukončení** musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **Koncový** čas je volitelný. Pokud nezadáte hodnotu **pro koncovou** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

### <a name="other-scenarios"></a>Další scénáře
#### <a name="web-service-requires-multiple-inputs"></a>Webová služba vyžaduje více vstupů
Pokud webová služba trvá více vstupů, použijte vlastnost **webServiceInputs** namísto použití **webServiceInput**. Datové sady, na které odkazuje **webServiceInputs,** musí být také zahrnuty do **vstupů aktivity**.

V experimentu studia Azure Machine Learning studio vstupní a výstupní porty webové služby a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvy v experimentech. Můžete zobrazit datové části ukázkového požadavku na stránce nápovědy dávkového spuštění pro koncový bod studia Azure Machine Learning a ověřit očekávané mapování.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Webová služba nevyžaduje vstup
Azure Machine Learning studio dávkové spuštění webové služby lze spustit všechny pracovní postupy, například R nebo Python skripty, které nemusí vyžadovat žádné vstupy. Nebo experiment může být nakonfigurován s modulem Reader, který nezveřejňuje žádné GlobalParameters. V takovém případě AzureMLBatchExecution Activity by být nakonfigurován takto:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webová služba nevyžaduje vstup a výstup
Webová služba dávkového spuštění studia Azure Machine Learning nemusí mít nakonfigurovaný žádný výstup webové služby. V tomto příkladu neexistuje žádný vstup nebo výstup webové služby ani žádné GlobalParameters nakonfigurovány. Stále je výstup nakonfigurován na samotnou aktivitu, ale není uveden jako webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webová služba používá čtečky a autory a aktivita se spustí pouze v případě, že byly úspěšné jiné aktivity.
Azure Machine Learning studio webové služby čtečky a moduly pro zapisovatel může být nakonfigurované pro spuštění s nebo bez globalparameters. Můžete však chtít vložit volání služby do kanálu, který používá závislosti datové sady k vyvolání služby pouze v případě, že některé zpracování nadřazené hodu bylo dokončeno. Můžete také aktivovat některé další akce po dokončení dávkového spuštění pomocí tohoto přístupu. V takovém případě můžete vyjádřit závislosti pomocí vstupů a výstupů aktivity, aniž byste je pojmenovali jako vstupy nebo výstupy webové služby.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**Stánek s jídlem** jsou:

* Pokud koncový bod experimentu používá webServiceInput: je reprezentován datovou sadou objektů blob a je součástí vstupů aktivit y a vlastnosti webServiceInput. V opačném případě je vlastnost webServiceInput vynechána.
* Pokud koncový bod experimentu používá webServiceOutput(s): jsou reprezentovány datovými sadami objektů blob a jsou zahrnuty ve výstupech aktivity a ve vlastnosti webServiceOutputs. Výstupy aktivity a webServiceOutputs jsou mapovány názvem každého výstupu v experimentu. V opačném případě je vlastnost webServiceOutputs vynechána.
* Pokud koncový bod experimentu zpřístupňuje globalParameter(s), jsou uvedeny v aktivitě globalParameters vlastnost jako klíč, dvojice hodnot. V opačném případě je vlastnost globalParameters vynechána. Klíče rozlišují malá a velká písmena. V hodnotách lze použít [funkce Azure Data Factory.](data-factory-functions-variables.md)
* Další datové sady mohou být zahrnuty do aktivity vstupy a výstupy vlastnosti, aniž by odkazoval se na activity typeProperties. Tyto datové sady řídí provádění pomocí závislostí řezů, ale jinak jsou ignorovány aktivitou AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizace modelů pomocí aktualizace aktivity zdrojů
Po dokončení rekvalifikace aktualizujte webovou službu hodnocení (prediktivní experiment vystavený jako webová služba) s nově trénovaným modelem pomocí **Azure Machine Learning studio Update Resource Activity**. Podrobnosti najdete v článku Aktualizace modelů pomocí článku [Aktualizace aktivity prostředků.](data-factory-azure-ml-update-resource-activity.md)

### <a name="reader-and-writer-modules"></a>Moduly pro čtení a zapisování
Běžným scénářem pro použití parametrů webové služby je použití azure sql čtenáři a zapisovači. Modul čtečky se používá k načtení dat do experimentu ze služeb správy dat mimo Azure Machine Learning Studio. Modul zapisovače je ukládání dat z experimentů do služeb správy dat mimo Azure Machine Learning Studio.

Podrobnosti o Azure Blob/Azure SQL reader/writer najdete v tématu [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN. Příklad v předchozí části používá Azure Blob reader a Azure Blob writer. Tato část popisuje použití Azure SQL reader a Azure SQL writer.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Otázka:** Mám více souborů, které jsou generovány mé velké datové kanály. Můžu použít aktivitu AzureMLBatchExecution k práci na všech souborech?

**Odpověď:** Ano. Podrobnosti najdete **v části Použití modulu Reader ke čtení dat z více souborů v části Azure Blob.**

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Aktivita dávkového bodování studia Azure Machine Learning studio
Pokud používáte **aktivitu AzureMLBatchScoring** k integraci s Azure Machine Learning, doporučujeme použít nejnovější aktivitu **AzureMLBatchExecution.**

Aktivita AzureMLBatchExecution se zavádí v srpnu 2015 vydání Azure SDK a Azure PowerShell.

Pokud chcete pokračovat v používání aktivity AzureMLBatchScoring, pokračujte ve čtení v této části.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Aktivita dávkového vyhodnocování studia Azure Machine Learning pomocí azure storage pro vstup a výstup

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parametry webové služby
Chcete-li zadat hodnoty parametrů webové služby, přidejte oddíl **typeProperties** do oddílu **AzureMLBatchScoringActivity** v kanálu JSON, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Funkce data [factory](data-factory-functions-variables.md) můžete také použít při předávání hodnot pro parametry webové služby, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby rozlišují malá a velká písmena, takže se ujistěte, že názvy zadané v aktivitě JSON odpovídají názvům vystaveným webovou službou.
>
>

## <a name="see-also"></a>Viz také
* [Blogový příspěvek Azure: Začínáme s Azure Data Factory a Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
