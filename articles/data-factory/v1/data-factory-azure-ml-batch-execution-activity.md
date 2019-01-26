---
title: Vytvořit prediktivní datové kanály pomocí Azure Data Factory | Dokumentace Microsoftu
description: Popisuje, jak vytvořit vytváření prediktivních kanálů pomocí služby Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c280a1f7e060ab7637e8d0b2484951f72b58a89c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081190"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita hivu](data-factory-hive-activity.md)
> * [Aktivita pig](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Spark](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Úvod
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí machine learningu v Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat řešení prediktivní analýzy. Z vyšší úrovně pohledu se provádí ve třech krocích:

1. **Vytvořit výukový experiment**. Tento krok můžete provést pomocí Azure Machine Learning studio. Azure Machine Learning studio je spolupráce na vývoji visual prostředí, který používáte pro trénování a testování modelu prediktivní analýzy pomocí trénovací data.
2. **Převeďte jej na prediktivní experiment**. Jakmile vyškolila modelu s existujícími daty a budete chtít použít ke stanovení skóre pro nová data, Příprava a zefektivnit experimentu pro vyhodnocení.
3. **Ho nasadit jako webovou službu**. Hodnoticí experimentu můžete publikovat jako webová služba Azure. Může odesílat data do modelu přes tento koncový bod webové služby a zobrazí výsledky předpovědí z modelu.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje **přesun** a **transformaci** dat. Řešení pro integraci dat pomocí Azure Data Factory, která mohou ingestovat data z různých zdrojů dat, transformovat a zpracovávat data a výsledná data publikovat do úložišť dat, můžete vytvořit.

Služba Data Factory umožňuje vytvářet datové kanály, které přesouvají a transformují data, a následně tyto kanály spouštět podle zadaného plánu (každou hodinu, denně, týdně atd.). Poskytuje také bohaté vizualizace pro zobrazení rodokmenu a závislostí mezi vašimi datovými kanály a monitorování všech datových kanálů z jednoho jednotného zobrazení pro jednodušší identifikaci problémů a nastavení výstrah monitorování.

Zobrazit [Úvod do služby Azure Data Factory](data-factory-introduction.md) a [sestavit svůj první kanál](data-factory-build-your-first-pipeline.md) články rychle začít se službou Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Objekt pro vytváření dat a strojové učení najednou
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikování [Azure Machine Learning] [ azure-machine-learning] webová služba pro prediktivní analýzy. Použití **aktivita provedení dávky služby** v kanálu Azure Data Factory, můžete vyvolat webové služby Azure Machine Learning studio k vytvoření predikcí data ve službě batch. Zobrazit [vyvolání Azure Machine Learning studio webové služby pomocí aktivita provedení dávky služby](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) podrobné informace.

V průběhu času prediktivních modelů v Azure Machine Learning studio hodnocení experimentů musí být retrained pomocí nové vstupní datové sady. Model Azure Machine Learning studio z kanálu Data Factory mohou uchovávat provedením následujících kroků:

1. Publikujte jako webovou službu výukového experimentu (ne prediktivní experiment). Tento krok v aplikaci Azure Machine Learning studio udělat, jako jste to udělali vystavit prediktivní experiment jako webové služby v předchozím scénáři.
2. Pomocí Azure Machine Learning studio aktivita provedení dávky služby k vyvolání webové služby pro výukového experimentu. V podstatě můžete použít Azure Machine Learning studio aktivita provedení dávky k vyvolání webové služby školení a webová služba pro vyhodnocení.

Jakmile budete hotovi s přetrénování aktualizovat hodnoticí webové služby (prediktivní experiment jako webové služby) s nově trénovaného modelu s použitím **aktivita prostředku aktualizace Azure Machine Learning studio**. Zobrazit [aktualizace modelů pomocí aktivity aktualizace prostředku](data-factory-azure-ml-update-resource-activity.md) , kde najdete podrobnosti.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Vyvolání webové služby pomocí aktivita provedení dávky služby
Použití Azure Data Factory k orchestraci přesouvání a zpracování dat a pak proveďte spuštění služby batch pomocí Azure Machine Learning. Tady jsou kroky nejvyšší úrovně:

1. Vytvoření služby Azure Machine Learning propojený. Budete potřebovat následující hodnoty:

   1. **Identifikátor URI žádosti** pro rozhraní API pro spuštění dávky. Identifikátor URI požadavku můžete najít kliknutím **BATCH EXECUTION** odkaz na webové stránce služby.
   2. **Klíč rozhraní API** publikované Azure Machine Learning webové služby. Klíč rozhraní API můžete najít kliknutím webová služba, kterou jste publikovali.
   3. Použití **AzureMLBatchExecution** aktivity.

      ![Ve službě Machine Learning řídicí panel](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identifikátor URI služby batch](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář: Experimentů využívajících webové služby vstupů nebo výstupů, které odkazují na data ve službě Azure Blob Storage
V tomto scénáři službu Azure Machine Learning Web vytváří předpovědi pomocí dat ze souboru ve službě Azure blob storage a uchovává výsledky předpovědí ve službě blob storage. Následující kód JSON definuje kanál služby Data Factory s aktivitou AzureMLBatchExecution. Datové sady má aktivita **DecisionTreeInputBlob** jako vstup a **DecisionTreeResultBlob** jako výstup. **DecisionTreeInputBlob** je předán jako vstup do webové služby s použitím **webServiceInput** vlastnost JSON. **DecisionTreeResultBlob** je předán jako výstup webové služby s použitím **webServiceOutputs** vlastnost JSON.

> [!IMPORTANT]
> Pokud webová služba přijímá více vstupů, použijte **webServiceInputs** vlastnosti namísto použití **webServiceInput**. Najdete v článku [webová služba vyžaduje více vstupů](#web-service-requires-multiple-inputs) oddílu příklad použití vlastnosti webServiceInputs.
>
> Datové sady, které odkazují **webServiceInput**/**webServiceInputs** a **webServiceOutputs** vlastnosti (v  **typeProperties**) musí být i součástí aktivity **vstupy** a **výstupy**.
>
> V Azure Machine Learning studio experimentu webové služby vstupní a výstupní porty a globální parametry mají výchozí názvy ("vstup1", "vstup2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvům v experimenty. Na stránce nápovědy spouštění dávek pro koncový bod Azure Machine Learning studio můžete ověřit očekávané mapování můžete zobrazit ukázkovou datovou část požadavku.
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
> Pouze vstupů a výstupů aktivity AzureMLBatchExecution lze předat jako parametry webové služby. Například ve výše uvedeném fragmentu JSON DecisionTreeInputBlob je vstupní hodnota pro AzureMLBatchExecution aktivity, které je předáno jako vstup do webové služby prostřednictvím parametru webServiceInput.
>
>

### <a name="example"></a>Příklad:
Tento příklad používá Azure Storage k uložení vstupní a výstupní data.

Doporučujeme vám projít si [sestavit svůj první kanál pomocí služby Data Factory] [ adf-build-1st-pipeline] kurz před provedením tohoto příkladu. Pomocí editoru služby Data Factory k vytvoření artefaktů služby Data Factory (propojené služby, datové sady, kanál) v tomto příkladu.

1. Vytvoření **propojená služba** pro vaše **služby Azure Storage**. Pokud vstupní a výstupní soubory jsou v různých účtů úložiště, budete potřebovat dvě propojené služby. Tady je příklad JSON:

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
2. Vytvořte **vstupní** Azure Data Factory **datovou sadu**. Na rozdíl od některé jiné služby Data Factory datové sady, musí obsahovat tyto datové sady i **folderPath** a **fileName** hodnoty. Dělení můžete způsobit, že každé spuštění služby batch (každý datový řez) ke zpracování nebo vytvořit jedinečný vstupní a výstupní soubory. Budete muset zahrnout některé upstreamovou aktivitou transformace vstup do souboru ve formátu CSV a umístěte ho do účtu úložiště pro každý řez. V takovém případě nezahrnul **externí** a **externalData** nastavení uvedená v následujícím příkladu a vaše DecisionTreeInputBlob by výstupní datové sady jiné aktivity.

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

    Soubor vstupní csv musí obsahovat řádek záhlaví sloupců. Pokud používáte **aktivity kopírování** Pokud chcete vytvořit nebo přesunout sdíleného svazku clusteru do úložiště objektů blob, musí nastavit vlastnost jímky **blobWriterAddHeader** k **true**. Příklad:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Pokud soubor csv neobsahuje záhlaví, může se zobrazit následující chyba: **Chyba v aktivitě: Chyba čtení řetězce. Neočekávaný token: Metodě StartObject. Cesta ", řádek 1, pozice 1**.
3. Vytvořte **výstup** Azure Data Factory **datovou sadu**. Tento příklad používá k vytvoření jedinečné výstupní cesta pro každé spuštění řezu dělení. Bez oddílů, by soubor přepsat aktivity.

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
4. Vytvoření **propojená služba** typu: **AzureMLLinkedService**nabízí klíč rozhraní API a modelu adresa URL pro spuštění dávky.

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
5. Nakonec vytvořit kanál, který obsahuje **AzureMLBatchExecution** aktivity. Za běhu kanál provádí následující kroky:

   1. Získá umístění výstupního souboru ze vstupní datové sady.
   2. Vyvolá spuštění dávky Azure Machine Learning API
   3. Zkopíruje výstup spuštění služby batch do objektu blob podle výstupní datové sady.

      > [!NOTE]
      > Aktivita AzureMLBatchExecution může mít nula nebo více vstupů a výstupů jeden nebo více.
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

      Obě **start** a **end** času musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41Z. **End** čas je volitelné. Pokud nezadáte hodnotu **end** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

      > [!NOTE]
      > Zadání vstupu pro AzureMLBatchExecution aktivita je volitelné.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář: Používání modulů čtení/zápis odkazovat na data v různých úložných experimentů
Další z typických možností při vytváření experimenty Azure Machine Learning studio je použití modulů čtečky a zapisovače. Modul čtečky slouží k načtení dat do experimentu a zapisovače modulu je k uložení dat z experimentů. Podrobnosti o čtečky a zapisovače modulech najdete v tématu [čtečky](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [zapisovače](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN.

Pokud používáte moduly čtečky a zapisovače, je vhodné použít parametr webové služby pro každou vlastnost tyto moduly čtení/zápis. Tyto parametry webové umožňují nakonfigurovat hodnoty za běhu. Například můžete například vytvořit experiment s čtečky modulu, který používá službu Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby, které chcete povolit uživatelům webové služby, zadejte jiný Server SQL Azure volá YYY.database.windows.net. Parametr webové služby můžete povolit tuto hodnotu a nakonfigurovat.

> [!NOTE]
> Webové služby vstup a výstup se liší od parametrů webové služby. V první scénář jste viděli, jak vstup a výstup se dá nastavit pro Azure Machine Learning studio webové služby. V tomto scénáři předat parametry webové služby, které odpovídají vlastnosti čtení/zápis modulů.
>
>

Podívejme se na scénář použití parametrů webové služby. Máte nasazené webové služby Azure Machine Learning, který používá modul čtečky číst data z jednoho zdroje dat podporované službou Azure Machine Learning (například: Azure SQL Database). Po provedení příkazu batch se provádí, výsledky jsou zapsány pomocí zapisovače modulu (Azure SQL Database).  Žádné webové služby vstupy a výstupy jsou definovány v experimenty. V takovém případě doporučujeme nakonfigurovat parametry příslušné webové služby pro moduly čtečky a zapisovače. Tato konfigurace umožňuje čtení/zápis moduly, které nakonfigurují při použití AzureMLBatchExecution aktivity. Zadejte parametry webové služby v **globalParameters** části v kódu JSON aktivity následujícím způsobem.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Můžete také použít [funkce Data Factory](data-factory-functions-variables.md) v předávání hodnot pro Web služby parametry, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby jsou malá a velká písmena, zajistěte proto, že názvy, které zadáte v aktivitě JSON odpovídají těm, které jsou vystavené webové služby.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Použití modulu Reader na čtení dat z více souborů v Azure Blob
Velké objemy dat kanály s aktivitami, jako je například Pig a Hive můžete vytvořit jednu nebo více výstupních souborů bez přípony. Například když zadáte externí tabulky Hive, data pro externí tabulky Hive můžete uložená ve službě Azure blob storage s následujícím názvem 000000_0. Můžete použít modul čtečky v jednom experimentu ke čtení více souborů a jejich použití pro předpovědi.

Pokud používáte modul čtečky v jednom experimentu Azure Machine Learning, můžete zadat objektů Blob v Azure jako vstup. Soubory v úložišti objektů blob v Azure může být výstupní soubory (Příklad: 000000_0), který vytváří skript Pigu a Hivu spuštěný v HDInsight. Modul čtečky umožňuje čtení souborů (bez přípony) tím, že nakonfigurujete **cestu do kontejneru, adresáře nebo objekt blob**. **Cesta ke kontejneru** odkazuje na kontejner a **adresář nebo objekt blob** odkazuje na složku, která obsahuje soubory, jak je znázorněno na následujícím obrázku. Hvězdička to znamená, \*) **Určuje, že všechny soubory ve složce/kontejneru (tedy data/aggregateddata/rok = 2014/měsíc – 6 /\*)** se číst jako část testu.

![Azure vlastnosti objektu Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Příklad:
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

V předchozím příkladu JSON:

* V nasazované službě Azure Machine Learning Web používá čtečky a zapisovače modulu pro čtení a zápis dat z/do Azure SQL Database. Tato webová služba poskytuje následující čtyři parametry:  Název databázového serveru, název databáze, název serveru uživatelského účtu a heslo uživatelského účtu serveru.
* Obě **start** a **end** času musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41Z. **End** čas je volitelné. Pokud nezadáte hodnotu **end** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

### <a name="other-scenarios"></a>Další scénáře
#### <a name="web-service-requires-multiple-inputs"></a>Webová služba vyžaduje více vstupů
Pokud webová služba přijímá více vstupů, použijte **webServiceInputs** vlastnosti namísto použití **webServiceInput**. Datové sady, které odkazují **webServiceInputs** musí také obsahovat aktivity **vstupy**.

V Azure Machine Learning studio experimentu webové služby vstupní a výstupní porty a globální parametry mají výchozí názvy ("vstup1", "vstup2"), které můžete přizpůsobit. Názvy, které používáte pro webServiceInputs webServiceOutputs a globalParameters nastavení musí přesně odpovídat názvům v experimenty. Na stránce nápovědy spouštění dávek pro koncový bod Azure Machine Learning studio můžete ověřit očekávané mapování můžete zobrazit ukázkovou datovou část požadavku.

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

#### <a name="web-service-does-not-require-an-input"></a>Webová služba nevyžaduje, aby vstup
Azure Machine Learning studio batch spuštění webové služby je možné ke spouštění všech pracovních postupů, R nebo Python příklad skripty, které nevyžadují žádné vstupy. Nebo experimentu může být nakonfigurovaný pomocí čtečky modulu, který nevystavuje žádné GlobalParameters. V takovém případě by být aktivita AzureMLBatchExecution nakonfigurovány takto:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webová služba nevyžaduje, aby vstup/výstup
Služba Azure Machine Learning studio batch execution webové nemusí mít žádný výstup webová služba nakonfigurována. V tomto příkladu není žádná webová služba vstup nebo výstup, ani jsou nakonfigurované všechny GlobalParameters. Stále je výstupní hodnota nakonfigurovaná přímo na aktivitu, ale není zadána jako webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webové služby používá čtečky a zapisovače a aktivita spustí jenom v případě, že dalšími aktivitami proběhlo úspěšně
Azure Machine Learning studio webové služby čtečky a zapisovače moduly můžou být nakonfigurované ke spuštění s nebo bez jakékoli GlobalParameters. Můžete však vložit volání mezi službami v kanálu, který používá závislosti datových sad se vyvolat službu pouze v případě, že některé nadřazeného zpracování bylo dokončeno. Můžete také aktivovat nějakou jinou akci po dokončení spuštění dávky tento přístup. V takovém případě můžete vyjádřit závislosti pomocí aktivity vstupy a výstupy, aniž byste je pojmenovali jako webovou službu vstupy nebo výstupy.

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

**Takeaways** jsou:

* Pokud váš koncový bod experiment používá webServiceInput: je reprezentována datovou sadou objektů blob a je součástí vstupech do aktivity a vlastnost webServiceInput. V opačném případě je vynechána vlastnost webServiceInput.
* Pokud váš koncový bod experiment používá webServiceOutput(s): jsou reprezentované prostřednictvím datových sad objektů blob a jsou zahrnuty do výstupů aktivity a ve vlastnosti webServiceOutputs. Výstup aktivity a webServiceOutputs mapovaly podle názvu každého výstup v experimentu. V opačném případě je vynechána vlastnost webServiceOutputs.
* Pokud váš koncový bod experiment zpřístupní globalParameter(s), jsou uvedeny ve vlastnosti globalParameters aktivity jako dvojice klíč. V opačném případě je vynechána vlastnost globalParameters. Klíče jsou malá a velká písmena. [Funkce Azure Data Factory](data-factory-functions-variables.md) může sloužit jako hodnoty.
* Další datové sady může být součástí s použitím vlastnosti vstupů a výstupů aktivity bez na kterou se odkazuje v rámci typeProperties aktivity. Tyto datové sady určují provádění pomocí vlastnosti závislosti řezu ale ignorují. v opačném případě AzureMLBatchExecution aktivita.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizace modelů pomocí aktivity aktualizace prostředku
Jakmile budete hotovi s přetrénování aktualizovat hodnoticí webové služby (prediktivní experiment jako webové služby) s nově trénovaného modelu s použitím **aktivita prostředku aktualizace Azure Machine Learning studio**. Zobrazit [aktualizace modelů pomocí aktivity aktualizace prostředku](data-factory-azure-ml-update-resource-activity.md) , kde najdete podrobnosti.

### <a name="reader-and-writer-modules"></a>Čtečky a zapisovače moduly
Běžný scénář použití parametrů webové služby je použití Azure SQL čtečky a zapisovače. Modul čtečky slouží k načtení dat do experimentu ze služeb správy dat. mimo Azure Machine Learning Studio. Modul zapisovače je k uložení dat z experimentů do služeb správy dat. mimo Azure Machine Learning Studio.

Podrobnosti o čtení/zápis Azure Blob nebo Azure SQL najdete v tématu [čtečky](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [zapisovače](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN. V příkladu v předchozí části používá, objektů Blob v Azure čtečky a zapisovače objektů Blob v Azure. Tato část popisuje použití Azure SQL čtečky a zapisovače Azure SQL.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**DOTAZ:** Mám několik souborů, které se vygenerovaly Moje kanály velkých objemů dat. Můžete použít aktivitu AzureMLBatchExecution pro práci se soubory?

**ODPOVĚĎ:** Ano. Zobrazit **pomocí modulu Reader na čtení dat z více souborů v Azure Blob** podrobné informace.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning studio dávkové bodování aktivity
Pokud používáte **AzureMLBatchScoring** aktivity můžete integrovat s Azure Machine Learning, doporučujeme používat nejnovější **AzureMLBatchExecution** aktivity.

Aktivita AzureMLBatchExecution byla zavedená v srpen 2015 verzi sady Azure SDK a Azure Powershellu.

Pokud chcete pokračovat v používání AzureMLBatchScoring aktivity, pokračujte ve čtení této části.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning studio dávkové bodování aktivity pomocí služby Azure Storage pro vstup/výstup

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
Chcete-li zadat hodnoty parametrů webové služby, přidejte **typeProperties** části **AzureMLBatchScoringActivity** části v kódu JSON kanálu, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Můžete také použít [funkce Data Factory](data-factory-functions-variables.md) v předávání hodnot pro Web služby parametry, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby jsou malá a velká písmena, zajistěte proto, že názvy, které zadáte v aktivitě JSON odpovídají těm, které jsou vystavené webové služby.
>
>

## <a name="see-also"></a>Viz také
* [Příspěvku na blogu Azure: Začínáme s Azure Data Factory a Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
