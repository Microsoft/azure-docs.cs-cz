---
title: Vytváření prediktivních datových kanálů pomocí Azure Data Factory
description: Popisuje, jak vytvořit vytváření prediktivních kanálů pomocí Azure Data Factory a Azure Machine Learning Studio (Classic).
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 481b801d481f32ef84279be2d8bd6089670a01b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496515"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí Azure Machine Learning Studio (Classic) a Azure Data Factory

> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md)
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Úvod
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí strojového učení v Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasický)
[Azure Machine Learning Studio (Classic)](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje sestavovat, testovat a nasazovat řešení prediktivní analýzy. Z hlediska vysoké úrovně je to provedeno ve třech krocích:

1. **Vytvořte školicí experiment**. Tento krok provedete pomocí Azure Machine Learning Studio (Classic). Studio (Classic) je vizuální vývojové prostředí pro týmovou spolupráci, které slouží k výuce a testování modelu prediktivní analýzy pomocí školicích dat.
2. **Převeďte ji na prediktivní experiment**. Jakmile se model vyškole pomocí stávajících dat a jste připraveni ho použít k vyhodnocení nových dat, připravujete a zjednodušte experimenty pro bodování.
3. **Nasaďte ji jako webovou službu**. Experiment bodování můžete publikovat jako webovou službu Azure. Pomocí tohoto koncového bodu webové služby můžete odesílat data do svého modelu a výsledky přijetí výsledků předpovědi z tohoto modelu.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje **Přesun** a **transformaci** dat. Pomocí Azure Data Factory můžete vytvářet řešení pro integraci dat, která mohou ingestovat data z různých úložišť dat, transformovat a zpracovávat je, a výsledná data publikovat do úložišť dat.

Služba Data Factory umožňuje vytvářet datové kanály, které přesouvají a transformují data, a následně tyto kanály spouštět podle zadaného plánu (každou hodinu, denně, týdně atd.). Poskytuje také bohaté vizualizace pro zobrazení rodokmenu a závislostí mezi vašimi datovými kanály a monitorování všech datových kanálů z jednoho jednotného zobrazení pro jednodušší identifikaci problémů a nastavení výstrah monitorování.

Přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a [Sestavte své první](data-factory-build-your-first-pipeline.md) články o kanálu, abyste mohli rychle začít používat službu Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory a Machine Learning Studio (Classic) společně
Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu [Azure Machine Learning Studio (Classic)][azure-machine-learning] pro prediktivní analýzy. Pomocí **aktivity dávkového spuštění** v kanálu Azure Data Factory můžete vyvolat webovou službu studia (Classic), která předpovědi data ve službě Batch. Podrobnosti najdete v části vyvolání webové služby Azure Machine Learning Studio (Classic) pomocí části aktivita spuštění dávky.

Prediktivní modely v studiu bodování studia (Classic) se v průběhu času musí přenášet pomocí nových vstupních datových sad. Model studia (Classic) můžete přeškolit z Data Factory kanálu pomocí následujících kroků:

1. Publikujte experimenty školení (ne prediktivní experiment) jako webovou službu. Tento krok provedete v nástroji Studio (Classic) jako při zveřejnění prediktivního experimentu jako webové služby v předchozím scénáři.
2. K vyvolání webové služby pro experimentální experimenty použijte aktivitu spustit v rámci dávkového zpracování studia (Classic). V podstatě můžete použít aktivitu spustit dávku Studio (Classic) k vyvolání webové služby pro cvičení webové služby a bodování.

Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity aktualizace prostředku Azure Machine Learning Studio (Classic)**. Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](data-factory-azure-ml-update-resource-activity.md) .

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Vyvolání webové služby pomocí aktivity spuštění služby Batch
Azure Data Factory slouží k orchestraci přesunu a zpracování dat a následnému provádění dávek pomocí studia (Classic). Tady je postup nejvyšší úrovně:

1. Vytvořte propojenou službu Azure Machine Learning Studio (Classic). Potřebujete následující hodnoty:

   1. **Identifikátor URI žádosti** pro rozhraní API pro spuštění dávky Identifikátor URI žádosti najdete kliknutím na odkaz na **spuštění dávky** na stránce webové služby.
   2. **Klíč rozhraní API** pro publikovanou webovou službu Studio (Classic) Klíč rozhraní API můžete najít kliknutím na webovou službu, kterou jste publikovali.
   3. Použijte aktivitu **AzureMLBatchExecution** .

      ![Řídicí panel Machine Learning Studio (klasický)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identifikátor URI dávky](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář: experimenty s využitím vstupů a výstupů webové služby, které odkazují na data v Azure Blob Storage
V tomto scénáři zpřístupňuje webová služba Studio (Classic) předpovědi pomocí dat ze souboru v úložišti objektů BLOB v Azure a ukládá výsledky předpovědi do úložiště objektů BLOB. Následující JSON definuje Data Factory kanál s aktivitou AzureMLBatchExecution. Aktivita má datovou sadu **DecisionTreeInputBlob** jako vstup a **DecisionTreeResultBlob** jako výstup. **DecisionTreeInputBlob** je předán jako vstup do webové služby pomocí vlastnosti **webServiceInput** JSON. **DecisionTreeResultBlob** se předává jako výstup do webové služby pomocí vlastnosti **webServiceOutputs** JSON.

> [!IMPORTANT]
> Pokud webová služba používá více vstupů, použijte namísto použití **webServiceInput** vlastnost **webServiceInputs** . Viz část [Webová služba vyžaduje více vstupů](#web-service-requires-multiple-inputs) pro příklad použití vlastnosti webServiceInputs.
>
> Datové sady, na které odkazují vlastnosti **webServiceInput** / **webServiceInputs** a **webServiceOutputs** (v **typeProperties**), musí být zahrnuté i do **vstupů** a **výstupů** aktivit.
>
> V experimentu studia (Classic), vstupní a výstupní porty webové služby a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které použijete pro nastavení webServiceInputs, webServiceOutputs a globalParameters, se musí přesně shodovat s názvy v experimentech. Pokud chcete ověřit očekávané mapování, můžete zobrazit ukázkovou datovou část požadavku na stránce s nápovědu pro spuštění dávky pro koncový bod studia (Classic).
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
> Jako parametry webové služby lze předat pouze vstupy a výstupy aktivity AzureMLBatchExecution. Například ve výše uvedeném fragmentu kódu JSON je DecisionTreeInputBlob jako vstup aktivity AzureMLBatchExecution, která se předává jako vstup do webové služby prostřednictvím parametru webServiceInput.
>
>

### <a name="example"></a>Příklad
Tento příklad používá Azure Storage k uložení vstupních i výstupních dat.

Před přechodem do tohoto příkladu doporučujeme projít si [sestavení prvního kanálu pomocí Data Factory][adf-build-1st-pipeline] kurzu. Pomocí editoru Data Factory můžete v tomto příkladu vytvořit Data Factory artefakty (propojené služby, datové sady, kanál).

1. Vytvořte **propojenou službu** pro vaši **Azure Storage**. Pokud jsou vstupní a výstupní soubory v různých účtech úložiště, potřebujete dvě propojené služby. Tady je příklad JSON:

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
2. Vytvořte **vstupní** Azure Data Factory **datovou sadu**. Na rozdíl od některých jiných Data Factory datových sad musí tyto datové sady obsahovat hodnoty **FolderPath** i **filename** . Pomocí dělení můžete způsobit, že každé spuštění dávky (každý datový řez) zpracuje nebo vytváří jedinečné vstupní a výstupní soubory. Možná budete muset zahrnout některé nadřazené aktivity pro transformaci vstupu do formátu souboru CSV a umístit ho do účtu úložiště pro každý řez. V takovém případě nebudete zahrnovat nastavení **externích** a **externalData** , která jsou uvedená v následujícím příkladu, a váš DecisionTreeInputBlob by byl výstupní datovou sadou jiné aktivity.

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

    Vstupní soubor CSV musí obsahovat řádek záhlaví sloupce. Pokud používáte **aktivitu kopírování** k vytvoření nebo přesunutí sdíleného svazku clusteru do úložiště objektů blob, měli byste nastavit vlastnost jímky **blobWriterAddHeader** na **hodnotu true**. Příklad:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Pokud soubor CSV neobsahuje řádek záhlaví, může se zobrazit následující chyba: **Chyba v aktivitě: Chyba při čtení řetězce. Neočekávaný token: metodě StartObject. Cesta ' ', řádek 1, pozice 1**.
3. Vytvořte **výstupní** Azure Data Factory **datovou sadu**. Tento příklad používá dělení k vytvoření jedinečné výstupní cesty pro každé spuštění řezu. Bez dělení by aktivita mohla soubor přepsat.

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
4. Vytvořte **propojenou službu** typu: **AzureMLLinkedService**, která poskytuje adresu URL pro spuštění klíče rozhraní API a dávky modelu.

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
5. Nakonec vytvořte kanál obsahující aktivitu **AzureMLBatchExecution** . Kanál za běhu provede následující kroky:

   1. Získá umístění vstupního souboru ze vstupních datových sad.
   2. Vyvolá rozhraní API pro spuštění sady Studio (Classic).
   3. Zkopíruje výstup spuštění dávky do objektu BLOB daného ve výstupní datové sadě.

      > [!NOTE]
      > Aktivita AzureMLBatchExecution může mít nula nebo více vstupů a jeden nebo více výstupů.
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

      **Počáteční** i **koncové** hodnoty data a času musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **Koncový** čas je nepovinný. Pokud nezadáte hodnotu vlastnosti **End** , vypočítá se jako "**Start + 48 hodiny".** Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](/previous-versions/azure/dn835050(v=azure.100)) (Referenční příručka skriptování JSON).

      > [!NOTE]
      > Zadání vstupu pro aktivitu AzureMLBatchExecution je volitelné.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář: experimenty pomocí modulů pro čtení a zápis, které odkazují na data v různých úložištích
Dalším běžným scénářem při vytváření experimentů studia (Classic) je použití modulů pro čtení a zápis. Modul čtečky se používá k načtení dat do experimentu a modul zapisovače ukládá data z experimentů. Podrobnosti o modulech čtenářů a zapisovači najdete v tématu témata [čtenářů](/azure/machine-learning/studio-module-reference/import-data) a [zapisovače](/azure/machine-learning/studio-module-reference/export-data) v knihovně MSDN.

Při použití modulů pro čtení a zápis je vhodné použít parametr webové služby pro každou vlastnost těchto modulů pro čtení a zápis. Tyto webové parametry umožňují konfigurovat hodnoty za běhu. Můžete například vytvořit experiment s modulem čtenář, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby budete chtít povolit uživatelům webové služby, aby určili jiný logický SQL Server s názvem YYY.database.windows.net. Tuto hodnotu můžete nakonfigurovat pomocí parametru webové služby.

> [!NOTE]
> Vstup a výstup webové služby se liší od parametrů webové služby. V prvním scénáři jste viděli, jak lze zadat vstup a výstup pro webovou službu studia (Classic). V tomto scénáři předáte parametry pro webovou službu, která odpovídá vlastnostem modulů pro čtení a zápis.
>
>

Pojďme se podívat na scénář použití parametrů webové služby. Máte nasazenou webovou službu studia (Classic), která používá modul čtenáře ke čtení dat z jednoho zdroje dat podporovaného nástrojem Studio (Classic) (například: Azure SQL Database). Po provedení dávky se výsledky zapisují pomocí modulu zapisovače (Azure SQL Database).  V experimentech nejsou definovány vstupy a výstupy webové služby. V takovém případě doporučujeme nakonfigurovat parametry relevantní webové služby pro moduly pro čtení a zápis. Tato konfigurace umožňuje, aby byly moduly pro čtení a zápis nastaveny při použití aktivity AzureMLBatchExecution. Parametry webové služby v části **globalParameters** v kódu JSON aktivity určíte následujícím způsobem.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

V rámci předávání hodnot pro parametry webové služby můžete také použít [funkci Data Factory Functions](data-factory-functions-variables.md) , jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> V parametrech webové služby se rozlišují velká a malá písmena, proto zajistěte, aby názvy zadané v kódu JSON aktivity odpovídaly těm, které jsou zpřístupněny webovou službou.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Použití modulu čtenáře ke čtení dat z více souborů v objektu blob Azure
Kanály velkých objemů dat s aktivitami, jako je například prase a podregistr, mohou způsobit jeden nebo více výstupních souborů bez rozšíření. Když například zadáte externí tabulku podregistru, data pro tabulku externího podregistru můžete uložit do Azure Blob Storage s následujícím názvem 000000_0. Modul čtečky můžete použít ve experimentu ke čtení více souborů a jejich použití pro předpovědi.

Při použití modulu Reader v experimentu (klasickém) studia můžete jako vstup zadat objekt blob Azure. Soubory v úložišti objektů BLOB v Azure můžou být výstupní soubory (například: 000000_0), které jsou vyráběny pomocí nástroje pro vepřové soubory a skripty v HDInsight spuštěné v HDInsight. Modul čtečky umožňuje číst soubory (bez rozšíření) konfigurací **cesty ke kontejneru, adresáři nebo objektu BLOB**. **Cesta ke kontejneru** odkazuje na kontejner a **adresář nebo objekt BLOB** odkazuje na složku, která obsahuje soubory, jak je znázorněno na následujícím obrázku. Hvězdička, která je, \* ) **Určuje, že všechny soubory v kontejneru/složce (tj. data/aggregateddata/Year = 2014/month-6/ \* )** jsou čteny jako součást experimentu.

![Vlastnosti objektu BLOB v Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Příklad
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Kanál s aktivitou AzureMLBatchExecution s parametry webové služby

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
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

* Nasazená webová služba Studio (Classic) používá ke čtení a zápisu dat z/do Azure SQL Database modul pro čtení a zápis. Tato webová služba zpřístupňuje následující čtyři parametry: název databázového serveru, název databáze, název uživatelského účtu serveru a heslo uživatelského účtu serveru.
* **Počáteční** i **koncové** hodnoty data a času musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **Koncový** čas je nepovinný. Pokud nezadáte hodnotu vlastnosti **End** , vypočítá se jako "**Start + 48 hodiny".** Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](/previous-versions/azure/dn835050(v=azure.100)) (Referenční příručka skriptování JSON).

### <a name="other-scenarios"></a>Další scénáře
#### <a name="web-service-requires-multiple-inputs"></a>Webová služba vyžaduje více vstupů.
Pokud webová služba používá více vstupů, použijte namísto použití **webServiceInput** vlastnost **webServiceInputs** . Datové sady, na které odkazuje **webServiceInputs** , musí být zahrnuté i do **vstupů** aktivity.

V Azure Machine Learning Studio (klasickém) experimentu mají vstupní a výstupní porty webové služby a globální parametry výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které použijete pro nastavení webServiceInputs, webServiceOutputs a globalParameters, se musí přesně shodovat s názvy v experimentech. Pokud chcete ověřit očekávané mapování, můžete zobrazit ukázkovou datovou část požadavku na stránce s nápovědu pro spuštění dávky pro koncový bod studia (Classic).

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

#### <a name="web-service-does-not-require-an-input"></a>Webová služba nevyžaduje vstup.
Webové služby dávkového spouštění Azure Machine Learning Studio (Classic) se dají použít ke spouštění libovolných pracovních postupů, například skriptů R nebo Pythonu, které nemusí vyžadovat žádné vstupy. Nebo je možné experiment nakonfigurovat s modulem čtenář, který nevystavuje žádné GlobalParameters. V takovém případě se aktivita AzureMLBatchExecution nakonfiguruje takto:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webová služba nevyžaduje vstup/výstup.
Webová služba batch execution Azure Machine Learning Studio (Classic) nemusí mít nakonfigurovaný žádný výstup webové služby. V tomto příkladu není k dispozici žádný vstup nebo výstup webové služby, ani není nakonfigurován žádný GlobalParameters. U samotné aktivity je stále výstup nakonfigurovaný, ale není zadaný jako webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webová služba používá čtečky a zapisovače a aktivita se spouští pouze v případě, že byly úspěšně dokončeny jiné aktivity.
Moduly pro čtení a modul pro zápis webové služby Azure Machine Learning Studio (Classic) můžou být nakonfigurované tak, aby se spouštěly s GlobalParameters nebo bez něj. Můžete však chtít vložit volání služby do kanálu, který používá závislosti datových sad k vyvolání služby pouze v případě, že bylo dokončeno některé z nadřazených zpracování. Můžete také aktivovat jinou akci po dokončení dávkového zpracování pomocí tohoto přístupu. V takovém případě můžete vyjádřit závislosti pomocí vstupů a výstupů aktivit, aniž byste je museli pojmenovat jako vstupy a výstupy webové služby.

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

**Poznatky** jsou:

* Pokud koncový bod experimentu používá webServiceInput: je reprezentován datovou sadou objektů BLOB a je zahrnutý ve vstupech aktivity a vlastnosti webServiceInput. V opačném případě je vlastnost webServiceInput vynechána.
* Pokud Váš koncový bod experiment používá webServiceOutput (e): jsou reprezentovány pomocí datových sad objektů BLOB a jsou součástí výstupů aktivity a ve vlastnosti webServiceOutputs. Výstupy aktivity a webServiceOutputs jsou mapovány podle názvu každého výstupu v experimentu. V opačném případě je vlastnost webServiceOutputs vynechána.
* Pokud koncový bod experimentu zveřejňuje globalParameter, jsou uvedeny v poli aktivita globalParameters Property jako klíčová dvojice hodnoty. V opačném případě je vlastnost globalParameters vynechána. U klíčů se rozlišují malá a velká písmena. V hodnotách se dají použít [funkce Azure Data Factory](data-factory-functions-variables.md) .
* Do vlastností vstupů a výstupů aktivit můžou být zahrnuté další datové sady, aniž by se na ně typePropertiesa aktivita. Tyto datové sady řídí spouštění pomocí závislostí řezů, ale jinak ignoruje aktivita AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizace modelů pomocí aktivity aktualizovat prostředek
Až budete s přeškolením hotovi, aktualizujte webovou službu bodování (prediktivní experiment vystavený jako webovou službu) s nově vyškolený model pomocí **aktivity aktualizace prostředku Azure Machine Learning Studio (Classic)**. Podrobnosti najdete v tématu [aktualizace modelů pomocí článku Aktualizace prostředku aktivity](data-factory-azure-ml-update-resource-activity.md) .

### <a name="reader-and-writer-modules"></a>Moduly pro čtení a zápis
Běžným scénářem použití parametrů webové služby je použití čteček a zapisovače Azure SQL. Modul čtečky se používá k načtení dat do experimentu ze služeb pro správu dat mimo Studio (Classic). Modul zapisovače ukládá data z experimentů do služeb pro správu dat mimo Studio (Classic).

Podrobnosti o službě Azure Blob/službě Azure SQL Reader a zapisovači najdete v tématech ke [čtenářům](/azure/machine-learning/studio-module-reference/import-data) a [zapisovači](/azure/machine-learning/studio-module-reference/export-data) v knihovně MSDN. Příklad v předchozím oddílu používá čtečku objektů blob Azure a zapisovač objektů BLOB v Azure. Tato část se zabývá používáním služby Azure SQL Reader a služby Azure SQL Writer.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Otázka:** Mám několik souborů generovaných kanály velkých objemů dat. Můžu aktivitu AzureMLBatchExecution použít pro práci na všech souborech?

**Odpověď:** Ano. Podrobnosti najdete v části **použití modulu čtenář k načtení dat z více souborů v objektu blob Azure** .

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Aktivita dávkového vyhodnocování Azure Machine Learning Studio (Classic)
Pokud používáte aktivitu **AzureMLBatchScoring** k integraci s Azure Machine Learning Studio (Classic), doporučujeme použít nejnovější aktivitu **AzureMLBatchExecution** .

Aktivita AzureMLBatchExecution se zavede do vydání sady Azure SDK ze srpna 2015 a Azure PowerShell.

Pokud chcete pokračovat v používání aktivity AzureMLBatchScoring, pokračujte v čtení v této části.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Aktivita dávkového vyhodnocování Azure Machine Learning Studio (Classic) pomocí Azure Storage pro vstup a výstup

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
Pokud chcete zadat hodnoty pro parametry webové služby, přidejte část **typeProperties** do části **AZUREMLBATCHSCORINGACTIVITY** v kanálu JSON kanálu, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
V rámci předávání hodnot pro parametry webové služby můžete také použít [funkci Data Factory Functions](data-factory-functions-variables.md) , jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> V parametrech webové služby se rozlišují velká a malá písmena, proto zajistěte, aby názvy zadané v kódu JSON aktivity odpovídaly těm, které jsou zpřístupněny webovou službou.
>
>

## <a name="see-also"></a>Viz také
* [Blogový příspěvek Azure: Začínáme s Azure Data Factory a Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/