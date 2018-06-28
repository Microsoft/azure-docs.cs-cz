---
title: Vytvoření kanálů prediktivní dat pomocí Azure Data Factory | Microsoft Docs
description: Popisuje postup vytvoření vytvořit prediktivní kanály pomocí Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 62712a201e8f9717f3e0215c4c29130d3bddcdf2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050207"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Vytvořit prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita Hive](data-factory-hive-activity.md) 
> * [Pig aktivity](data-factory-pig-activity.md)
> * [Činnost MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Spark aktivity](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivity rozhraní .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Úvod
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [transformace dat pomocí machine learning v datové továrně](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umožňuje vytvářet, testovat a nasazovat řešení prediktivní analýzy. Z vysoké úrovně hlediska se provádí v tři kroky:

1. **Vytvoření experimentu školení**. Tento krok se provádí pomocí nástroje Azure ML Studio. ML studio se spolupráce vizuální vývojové prostředí, které používáte a natrénuje a otestuje model prediktivní analýzy pomocí Cvičná data.
2. **Převést na prediktivní experiment**. Jakmile model byl vyzkoušen s existujícími daty a budete chtít použít skóre pro nová data, připravte a zjednodušit experimentu pro vyhodnocování.
3. **Nasadit jako webovou službu**. Vyhodnocování experimentu můžete publikovat jako Azure webové služby. Můžete posílat data do modelu přes tento koncový bod webové služby a přijímat výsledek předpovědi u tabulátorů modelu.  

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje **přesun** a **transformaci** dat. Můžete vytvořit data integrace řešení pomocí Azure Data Factory můžete načítání dat z různých úložišť dat, proces nebo transformace dat a publikovat Výsledná data do úložiště data.

Služba Data Factory umožňuje vytvářet datové kanály, které přesouvají a transformují data, a následně tyto kanály spouštět podle zadaného plánu (každou hodinu, denně, týdně atd.). Poskytuje také bohaté vizualizace pro zobrazení rodokmenu a závislostí mezi vašimi datovými kanály a monitorování všech datových kanálů z jednoho jednotného zobrazení pro jednodušší identifikaci problémů a nastavení výstrah monitorování.

V tématu [Úvod do Azure Data Factory](data-factory-introduction.md) a [sestavit svůj první kanál](data-factory-build-your-first-pipeline.md) články rychle začít se službou Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Objekt pro vytváření dat a strojové učení společně
Azure Data Factory můžete snadno vytvořit kanály, které používají publikovaný [Azure Machine Learning] [ azure-machine-learning] webová služba pro prediktivní analýzy. Pomocí **aktivita provedení dávky** v kanál služby Azure Data Factory můžete vyvolat webové služby Azure ML provádět předpovědi na datech v dávce. V tématu [vyvolání webové služby Azure ML pomocí aktivita provedení dávky](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) podrobnosti.

V průběhu času prediktivní modely v Azure ML vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Model Azure ML z objektu pro vytváření dat kanál můžete přeučování provedením následujících kroků:

1. Publikujte výukový experiment (ne prediktivní experiment) jako webovou službu. Tento krok v Azure ML Studio můžete udělat, jako jste to udělali vystavit prediktivní experiment jako webovou službu v předchozím scénáři.
2. Použijte aktivita provedení dávky Azure ML k vyvolání webové služby pro výukový experiment. V podstatě můžete aktivita provedení dávky Azure ML k vyvolání webové služby školení a vyhodnocování webové služby.

Po dokončení práce s retraining, aktualizovat vyhodnocování webové služby (prediktivní experiment zveřejněné jako webovou službu) s nově trénovaného modelu pomocí **aktivita prostředku aktualizace Azure ML**. V tématu [aktualizace modelů pomocí aktivita prostředku aktualizace](data-factory-azure-ml-update-resource-activity.md) článku.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Vyvolání webové služby pomocí aktivita provedení dávky
Pomocí Azure Data Factory k orchestraci přesouvání dat a zpracování a poté proveďte dávkového spuštění pomocí Azure Machine Learning. Tady jsou kroky nejvyšší úrovně:

1. Vytvoření služby Azure Machine Learning propojený. Je třeba následující hodnoty:

   1. **Identifikátor URI požadavku je** pro spuštění dávky rozhraní API. Kliknutím můžete najít identifikátor URI požadavku **BATCH EXECUTION** odkaz na webové stránce služby.
   2. **Klíč rozhraní API** pro publikované Azure Machine Learning webové služby. Kliknutím na webovou službu, kterou jste publikovali, můžete najít klíč rozhraní API.
   3. Použití **AzureMLBatchExecution** aktivity.

      ![Strojového učení řídicí panel](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch identifikátor URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scénář: Experimenty pomocí webové služby vstupy/výstupy odkazující na data v Azure Blob Storage
V tomto scénáři Azure Machine Learning webovou službu díky předpovědi pomocí dat ze souboru v Azure blob storage a ukládá výsledky předpovědi ve službě blob storage. Následující kód JSON určuje objekt pro vytváření dat kanál s AzureMLBatchExecution aktivitu. Datová sada má aktivita **DecisionTreeInputBlob** jako vstup a **DecisionTreeResultBlob** jako výstup. **DecisionTreeInputBlob** předaným jako vstup k webové službě pomocí pomocí **webServiceInput** vlastnost JSON. **DecisionTreeResultBlob** předaný jako výstup webové služby s použitím **webServiceOutputs** vlastnost JSON.  

> [!IMPORTANT]
> Pokud webová služba přijímá více vstupů, použijte **webServiceInputs** vlastnost místo použití **webServiceInput**. Najdete v článku [webová služba vyžaduje více vstupů](#web-service-requires-multiple-inputs) části příklad pomocí vlastnosti webServiceInputs.
>
> Datové sady, které odkazují **webServiceInput**/**webServiceInputs** a **webServiceOutputs** vlastnosti (v  **rámci typeProperties**) musí být i součástí aktivity **vstupy** a **výstupy**.
>
> V experimentu Azure ML vstup webové služby a porty výstup a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které můžete použít pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně shodovat názvy v experimenty. Datová část požadavku ukázka můžete zobrazit na stránce pomůže spuštění dávky pro koncový bod služby Azure ML ověření očekávané mapování.
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
> Pouze vstupy a výstupy aktivity AzureMLBatchExecution lze předat jako parametry webové služby. Například ve výše uvedeném fragmentu JSON je DecisionTreeInputBlob vstup AzureMLBatchExecution aktivity, která se předá jako vstup k webové službě pomocí parametru webServiceInput.   
>
>

### <a name="example"></a>Příklad:
Tento příklad používá úložiště Azure pro uložení vstupní a výstupní data.

Doporučujeme projít si [sestavit svůj první kanál pomocí služby Data Factory] [ adf-build-1st-pipeline] kurz před projít tento příklad. Pomocí editoru služby Data Factory pro vytvoření artefaktů služby Data Factory (propojené služby, datové sady, kanálu) v tomto příkladu.   

1. Vytvoření **propojená služba** pro vaše **Azure Storage**. Pokud jsou vstupní a výstupní soubory v jiným účtům úložiště, je třeba dvě propojené služby. Tady je příklad JSON:

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
2. Vytvořte **vstupní** Azure Data Factory **datovou sadu**. Na rozdíl od některých jiných objekt pro vytváření dat datové sady, musí obsahovat tyto datové sady obě **folderPath** a **fileName** hodnoty. Vytváření oddílů můžete způsobit, že každé spuštění dávky (každý datový řez) ke zpracování nebo vytvořit jedinečný vstupní a výstupní soubory. Budete muset zahrnují některé nadřízené činnosti k transformaci vstup do souboru ve formátu CSV a jeho následné uložení do účtu úložiště pro každý řez. V takovém případě by zahrnovat **externí** a **externalData** nastavení zobrazené v následujícím příkladu a vaše DecisionTreeInputBlob by výstupní datovou sadu jiné aktivitě.

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

    Soubor vstupní csv musí obsahovat řádek záhlaví sloupců. Pokud používáte **aktivity kopírování** k vytvoření nebo přesunutí sdíleném svazku clusteru do úložiště objektů blob, měli nastavit vlastnost jímky **blobWriterAddHeader** k **true**. Příklad:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Pokud soubor csv nemá řádek záhlaví, mohou se zobrazit následující chyby: **Chyba v aktivitě: Chyba při čtení řetězec. Neočekávaný token: StartObject. Cesta %, řádek 1, 1 umístit**.
3. Vytvořte **výstup** Azure Data Factory **datovou sadu**. Tento příklad používá k vytvoření jedinečné výstupní cesta pro každé spuštění řezu rozdělení do oddílů. Bez oddílů, aktivity by došlo k přepsání souboru.

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
4. Vytvoření **propojená služba** typu: **AzureMLLinkedService**, poskytuje klíč rozhraní API a modelu adresa URL pro spuštění dávky.

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
5. Nakonec vytvořte kanálu obsahující **AzureMLBatchExecution** aktivity. V době běhu kanálu provede následující kroky:

   1. Získá umístění souboru vstupního souboru z vaší vstupní datové sady.
   2. Vyvolá provedení dávky Azure Machine Learning API
   3. Zkopíruje výstupu spuštění dávky do objektu blob v výstupní datovou sadu.

      > [!NOTE]
      > Aktivita AzureMLBatchExecution může mít v počtu nula či více vstupů a výstupů jeden nebo více.
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

      Obě **spustit** a **end** času musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **End** čas je volitelné. Pokud nezadáte hodnotu **end** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

      > [!NOTE]
      > Zadání vstupu pro AzureMLBatchExecution aktivita je volitelné.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scénář: Experimenty, používání modulů Reader/Writer odkazovat na data v různých úložných
Další z typických možností při vytváření experimenty Azure ML je použití modulů pro čtení a zápis. Modul čtečky se používá k načtení dat do experimentu a modul zapisovače k uložení dat z experimentů. Podrobnosti o čtení a zápis modulech najdete v tématu [čtečky](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [zapisovače](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN.     

Pokud používáte moduly čtení a zápis, je vhodné použít parametr webové služby pro každou vlastnost tyto moduly čtečky nebo zapisovač. Tyto parametry webové umožňují konfiguraci hodnot, které za běhu. Můžete například vytvořit experimentu s čtečky modul, který používá Azure SQL Database: XXX.database.windows.net. Po nasazení webové služby, které chcete povolit příjemcům webové služby, zadejte jiný Server SQL Azure volá YYY.database.windows.net. Parametr webové služby můžete povolit tuto hodnotu nakonfigurovat.

> [!NOTE]
> Webové služby vstup a výstup se liší od parametry webové služby. V prvního scénáře jste viděli, jak vstup a výstup lze zadat pro Azure ML webové služby. V tomto scénáři předat parametry webové služby, které odpovídají vlastnosti modulů čtečky nebo zapisovač.
>
>

Podívejme se na scénář použití parametry webové služby. Máte nasazené webové službě Azure Machine Learning, která používá modul čtečky číst data z jednoho zdroje dat podporované rozhraním Azure Machine Learning (například: Azure SQL Database). Po provedení dávkového spuštění výsledky jsou zapsány pomocí zapisovače modulu (Azure SQL Database).  Žádné webové služby vstupy a výstupy jsou definovány v experimenty. V takovém případě doporučujeme nakonfigurovat parametry relevantní webové služby pro moduly čtení a zápis. Tato konfigurace umožňuje čtečky nebo zapisovač moduly nakonfigurovat při použití AzureMLBatchExecution aktivity. Zadejte parametry webové služby v **globalParameters** část v kódu JSON aktivity následujícím způsobem.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Můžete také použít [funkce objektu pro vytváření dat](data-factory-functions-variables.md) v předávání hodnot pro Web služby parametry, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby je malá a velká písmena, takže shodné názvy, které zadáte v aktivitě JSON těm, které jsou vystavené webovou službu.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Použití modulu Reader číst data z více souborů v Azure Blob
Velké objemy dat kanálů s aktivity, například Pig a Hive můžete vytvořit jeden nebo více výstupních souborů bez přípony. Například když zadáte externí tabulku Hive, data pro externí tabulku Hive mohou být uložena v úložišti objektů blob v Azure s následující 000000_0 název. Můžete použít modul čtečky v experimentu číst několik souborů a použít je k předpovědi.

Při použití modulu reader v experimentu Azure Machine Learning, můžete zadat objektů Blob v Azure jako vstup. Soubory v úložišti objektů blob Azure může být výstupní soubory (Příklad: 000000_0), vznikají pomocí funkcí Pig a Hive skript spuštěný v HDInsight. Modul čtečky umožňuje čtení souborů (žádné) tak, že nakonfigurujete **cestu na kontejner, adresáře nebo objekt blob**. **Cesta ke kontejneru** odkazuje na kontejner a **adresáře nebo objekt blob** ukazuje na složku, která obsahuje soubory, jak je znázorněno na následujícím obrázku. Hvězdička tedy \*) **Určuje, že všechny soubory v kontejneru nebo složku (to znamená, data, aggregateddata a rok 2014/měsíc-6 = /\*)** se čtou v rámci experimentu.

![Azure Blob vlastnosti](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Příklad:
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Kanál s aktivitou AzureMLBatchExecution s parametry webové služby

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
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

V tomto příkladu JSON:

* Nasazené služby Azure Machine Learning Web používá čtečka čipových karet a modul zapisovače pro čtení a zápis dat z/do Azure SQL Database. Tato webová služba zveřejňuje následující čtyři parametry: název serveru, název databáze, název serveru uživatelského účtu a heslo uživatelského účtu serveru databáze.  
* Obě **spustit** a **end** času musí být v [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. **End** čas je volitelné. Pokud nezadáte hodnotu **end** vlastnost, vypočítá se jako "**start + 48 hodin.**" Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**. Podrobné informace o vlastnostech JSON najdete v tématu [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Referenční příručka skriptování JSON).

### <a name="other-scenarios"></a>Další scénáře
#### <a name="web-service-requires-multiple-inputs"></a>Webová služba vyžaduje více vstupů
Pokud webová služba přijímá více vstupů, použijte **webServiceInputs** vlastnost místo použití **webServiceInput**. Datové sady, které odkazují **webServiceInputs** musí také obsahovat aktivity **vstupy**.

V experimentu Azure ML vstup webové služby a porty výstup a globální parametry mají výchozí názvy ("input1", "input2"), které můžete přizpůsobit. Názvy, které můžete použít pro webServiceInputs, webServiceOutputs a globalParameters nastavení musí přesně shodovat názvy v experimenty. Datová část požadavku ukázka můžete zobrazit na stránce pomůže spuštění dávky pro koncový bod služby Azure ML ověření očekávané mapování.

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

#### <a name="web-service-does-not-require-an-input"></a>Webová služba nevyžaduje žádný vstup
Azure ML dávky spuštění webové služby slouží ke spouštění všech pracovních postupů pro R nebo Python příklad skripty, které ale nemusí vyžadovat všechny vstupy. Nebo můžou být nakonfigurované experimentu s čtečky modul, který nevystavuje žádné GlobalParameters. V takovém případě AzureMLBatchExecution aktivity by být nakonfigurovány takto:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webová služba nevyžaduje vstupu a výstupu
Služba webové provedení dávky Azure ML nemusí mít žádný výstup webové služby nakonfigurované. V tomto příkladu není žádná webová služba vstup nebo výstup, ani nejsou nakonfigurované žádné GlobalParameters. Stále je nakonfigurované na aktivity samotný výstup, ale není zadané jako webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Webové služby používá čtečky a zapisovače a aktivita běží jenom v případě, že ostatní aktivity proběhlo úspěšně.
Azure ML web service čtení a zápis moduly může být nakonfigurována pro spuštění s nebo bez jakékoli GlobalParameters. Můžete však vložit volání služby kanál, který používá datovou sadu závislosti vyvolat službu jenom v případě, že některé nadřazeného zpracování byla dokončena. Můžete ji spustit taky některých jiných akcí po dokončení provádění batch tento přístup. V takovém případě lze vyjádřit pomocí aktivity vstupy a výstupy, bez pojmenování některý z nich jako webová služba vstupů nebo výstupů závislosti.

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

* Pokud váš koncový bod experiment používá webServiceInput: to je reprezentována datovou sadu objektu blob a je součástí vstupy aktivity a vlastnost webServiceInput. Vlastnost webServiceInput, jinak je vynechán.
* Pokud váš koncový bod experiment používá webServiceOutput(s): se jsou reprezentované pomocí datové sady objektu blob a jsou zahrnuty do výstupů aktivity a ve vlastnosti webServiceOutputs. Výstup aktivity a webServiceOutputs jsou namapované podle názvu každého výstupu v experimentu. Vlastnost webServiceOutputs, jinak je vynechán.
* Pokud váš koncový bod experimentu zpřístupní globalParameter(s), jsou uvedené ve vlastnosti globalParameters aktivitu jako páry klíčů, hodnota. Vlastnost globalParameters, jinak je vynechán. U klíčů se malá a velká písmena. [Azure Data Factory funkce](data-factory-functions-variables.md) je možné použít hodnoty.
* Další datové sady může být součástí vstupy a výstupy vlastnosti aktivity, aniž by se na ně odkazovat v rámci typeProperties aktivity. Tyto datové sady řídí provádění pomocí závislosti řezu ale aktivitou AzureMLBatchExecution jinak ignorují.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizace modelů pomocí aktivita prostředku aktualizace
Po dokončení práce s retraining, aktualizovat vyhodnocování webové služby (prediktivní experiment zveřejněné jako webovou službu) s nově trénovaného modelu pomocí **aktivita prostředku aktualizace Azure ML**. V tématu [aktualizace modelů pomocí aktivita prostředku aktualizace](data-factory-azure-ml-update-resource-activity.md) článku.

### <a name="reader-and-writer-modules"></a>Čtečka a zapisovače moduly
Obvyklým scénářem pro používání parametry webové služby je používání Azure SQL čtení a zápis. Modul čtečky slouží k načtení dat do experimentu z datových služeb správy mimo Azure Machine Learning Studio. Zapisovač modulu je k uložení dat z experimentů do služby pro data mimo Azure Machine Learning Studio.  

Podrobnosti o čtečky nebo zápis Azure Blob nebo Azure SQL najdete v tématu [čtečky](https://msdn.microsoft.com/library/azure/dn905997.aspx) a [zapisovače](https://msdn.microsoft.com/library/azure/dn905984.aspx) témata v knihovně MSDN. Ukázka v předchozím oddílu používala objektů Blob v Azure čtení a zápis objektů Blob v Azure. Tato část popisuje používání Azure SQL čtení a zápis Azure SQL.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Otázka:** mám více souborů, které jsou generovány nástrojem Moje velkých datových kanálů. Můžete použít aktivitu AzureMLBatchExecution pracovat na všechny soubory?

**Odpověď:** Ano. Najdete v článku **pomocí modul čtečky číst data z více souborů v Azure Blob** podrobnosti.

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML dávkové vyhodnocování aktivity
Pokud používáte **AzureMLBatchScoring** aktivity k integraci s Azure Machine Learning, doporučujeme používat nejnovější **AzureMLBatchExecution** aktivity.

Aktivita AzureMLBatchExecution byla zavedená v srpnu 2015 verzi sady Azure SDK a prostředí Azure PowerShell.

Pokud chcete pokračovat v používání AzureMLBatchScoring aktivity, pokračujte v načítání prostřednictvím této části.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Použití služby Azure Storage pro vstupní a výstupní Azure aktivity ML dávkového vyhodnocování

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
Zadejte hodnoty pro parametry webové služby, přidat **rámci typeProperties** části k **AzureMLBatchScoringActivty** část v kódu JSON kanálu, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Můžete také použít [funkce objektu pro vytváření dat](data-factory-functions-variables.md) v předávání hodnot pro Web služby parametry, jak je znázorněno v následujícím příkladu:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry webové služby je malá a velká písmena, takže shodné názvy, které zadáte v aktivitě JSON těm, které jsou vystavené webovou službu.
>
>

## <a name="see-also"></a>Viz také
* [Azure příspěvku na blogu: Začínáme s Azure Data Factory a Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
