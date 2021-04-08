---
title: 'Kurz: vytvoření kanálu pro přesouvání dat pomocí Azure PowerShell '
description: V tomto kurzu vytvoříte kanál služby Azure Data Factory s aktivitou kopírování pomocí Azure PowerShellu.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 54c296ed8013b9962de9487cfec3e2568c03e738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377036"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Kurz: Vytvoření kanálu Data Factory pro přesouvání dat pomocí Azure PowerShellu
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-powershell.md). 

V tomto článku se naučíte, jak pomocí PowerShellu vytvořit datovou továrnu s kanálem, který kopíruje data z úložiště objektů BLOB v Azure do Azure SQL Database. Pokud s Azure Data Factory začínáte, přečtěte si článek [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto kurzem začnete.   

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [aktivity přesunu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Tento článek nepopisuje všechny rutiny služby Data Factory. Úplnou dokumentaci k těmto rutinám najdete v článku [Referenční informace o rutinách služby Data Factory](/powershell/module/az.datafactory).
> 
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Dokončete požadované kroky uvedené v [požadavcích kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Nainstalujte **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="steps"></a>Postup
Zde jsou kroky, které provedete v rámci tohoto kurzu:

1. Vytvořte **datovou továrnu** Azure. V tomto kroku vytvoříte datovou továrnu s názvem ADFTutorialDataFactoryPSH. 
1. V této datové továrně vytvořte **propojené služby**. V tomto kroku vytvoříte dvě propojené služby typu: Azure Storage a Azure SQL Database. 

    Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jste vytvořili kontejner a nahráli data do tohoto účtu úložiště.   

    AzureSqlLinkedService odkazy Azure SQL Database na datovou továrnu. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku SQL.   
1. Vytvořte v datové továrně vstupní a výstupní **datové sady**.  

    Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob určuje kontejner a složku obsahující vstupní data.  

    Podobně Azure SQL Database propojená služba Určuje připojovací řetězec, který Data Factory služba používá v době běhu pro připojení k vaší databázi. A výstupní datová sada tabulky SQL určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.
1. Vytvořte v datové továrně **kanál**. V tomto kroku pomocí aktivity kopírování vytvoříte kanál.   

    Aktivita kopírování kopíruje data z objektu BLOB v úložišti objektů BLOB v Azure do tabulky v Azure SQL Database. Aktivitu kopírování můžete v kanálu použít ke kopírování dat z jakéhokoli podporovaného zdroje do jakéhokoli podporovaného cíle. Seznam podporovaných úložišť dat najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
1. Monitorujte kanál. V tomto kroku budete **monitorovat** řezy vstupních a výstupních datových sad pomocí PowerShellu.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
> [!IMPORTANT]
> Pokud jste tak ještě neučinili, dokončete [požadavky kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu HDInsight Hive pro spuštění skriptu Hive, který umožňuje transformovat vstupní data na výstupní data produktu. V tomto kroku začneme vytvořením objektu pro vytváření dat.

1. Spusťte **PowerShell**. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu.

    Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal:

    ```powershell
    Connect-AzAccount
    ```   

    Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```powershell
    Get-AzSubscription
    ```

    Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Nahraďte **&lt; NameOfAzureSubscription** &gt; názvem vašeho předplatného Azure:

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup** :

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Některé kroky v tomto kurzu vychází z předpokladu, že používáte skupinu prostředků s názvem **ADFTutorialResourceGroup**. Pokud máte jinou skupinu prostředků, použijte ji v postupech v tomto kurzu místo skupiny ADFTutorialResourceGroup.
1. Spuštěním rutiny **New-AzDataFactory** Vytvořte datovou továrnu s názvem **ADFTutorialDataFactoryPSH**:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Tento název už nejspíš není volný. Vytvořte proto jedinečný název datové továrny přidáním předpony nebo přípony (například: ADFTutorialDataFactoryPSH05152017) a spusťte příkaz znovu.  

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název (třeba na váš_název_ADFTutorialDataFactoryPSH). Při provádění postupů v tomto kurzu potom tento název používejte místo názvu ADFTutorialFactoryPSH. Informace o artefaktech služby Data Factory najdete v tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md).

    ```
    Data factory name "ADFTutorialDataFactoryPSH" is not available
    ```
* Instance služby Data Factory můžete vytvářet jenom tehdy, když jste přispěvatelem nebo správcem předplatného Azure.
* Název datové továrny se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.
* Mohla se zobrazit tato chyba: **Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory.** Proveďte jednu z následujících a zkuste publikování znovu:

  * Spuštěním následujícího příkazu v prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Spuštěním následujícího příkazu ověřte, zda je zprostředkovatel služby Data Factory zaregistrovaný:

    ```powershell
    Get-AzResourceProvider
    ```
  * Přihlaste se pomocí předplatného Azure k webu [Azure Portal](https://portal.azure.com). Přejděte do okna Data Factory, nebo vytvořte datovou továrnu na webu Azure Portal. Zprostředkovatel se při takovém postupu zaregistruje automaticky.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu nebudete používat žádnou výpočetní službu jako je Azure HDInsight nebo Azure Data Lake Analytics. Budete používat dvě úložiště dat typu Azure Storage (zdroj) a Azure SQL Database (cíl). 

Vytvoříte tedy dvě propojené služby s názvem AzureStorageLinkedService a AzureSqlLinkedService typu: AzureStorage a AzureSqlDatabase.  

Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. Tento účet úložiště je ten, ve kterém jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili kontejner a nahráli do něj data.   

AzureSqlLinkedService odkazy Azure SQL Database na datovou továrnu. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Vytvoření propojené služby pro účet úložiště Azure
V tomto kroku propojíte se svou datovou továrnou účet úložiště Azure.

1. Vytvořte soubor JSON s názvem **AzureStorageLinkedService.json** ve složce **C:\ADFGetStartedPSH** s následujícím obsahem: (pokud ještě neexistuje, složku ADFGetStartedPSH vytvořte).

    > [!IMPORTANT]
    > Než soubor uložíte, položky &lt;accountname&gt; a &lt;accountkey&gt; nahraďte názvem svého účtu Azure Storage a jeho klíčem. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. V prostředí **Azure PowerShell** přejděte do složky **ADFGetStartedPSH**.
1. Spuštěním rutiny **New-AzDataFactoryLinkedService** vytvořte propojenou službu: **AzureStorageLinkedService**. Tato rutina a další rutiny služby Data Factory používané v tomto kurzu vyžadují, abyste zadali hodnoty parametrů **ResourceGroupName** a **DataFactoryName**. Alternativně můžete předat objekt DataFactory vrácený rutinou New-AzDataFactory bez zadání ResourceGroupName a DataFactory při každém spuštění rutiny. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Zde je ukázkový výstup:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Druhým způsobem vytvoření této propojené služby je zadání názvu skupiny prostředků a názvu datové továrny místo zadání objektu DataFactory.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-azure-sql-database"></a>Vytvořte propojenou službu pro Azure SQL Database
V tomto kroku propojíte Azure SQL Database s datovou továrnou.

1. Ve složce C:\ADFGetStartedPSH vytvořte soubor JSON s názvem AzureSqlLinkedService.json s následujícím obsahem:

    > [!IMPORTANT]
    > Položku &lt; servername &gt; , &lt; DatabaseName &gt; , a heslo nahraďte názvem serveru &lt; username@servername &gt; &lt; &gt; , databáze, uživatelského účtu a heslem.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Spuštěním následujícího příkazu vytvořte propojenou službu:

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

    Zde je ukázkový výstup:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Potvrďte, že nastavení **Povolit přístup ke službám Azure** je pro váš server zapnuté. Chcete-li to ověřit a zapnout ho, proveďte následující kroky:

    1. Přihlášení k [Azure Portal](https://portal.azure.com)
    1. Vlevo klikněte na možnost **Další služby** a potom na **Servery SQL** v kategorii **DATABÁZE**.
    1. Ze seznamu serverů SQL vyberte svůj server.
    1. V okně Server SQL klikněte na odkaz **Zobrazit nastavení brány firewall**.
    1. V okně **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.
    1. Na panelu nástrojů klikněte na tlačítko **Uložit**. 

## <a name="create-datasets"></a>Vytvoření datových sad
V předchozím kroku jste vytvořili propojené služby, které propojí váš Azure Storage účet a Azure SQL Database k vašemu objektu pro vytváření dat. V tomto kroku nadefinujete dvě datové sady s názvem InputDataset a OutputDataset, které představují vstupní a výstupní data uložená v úložištích dat, na která odkazují služby AzureStorageLinkedService a AzureSqlLinkedService.

Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob (InputDataset) určuje kontejner a složku obsahující vstupní data.  

Podobně Azure SQL Database propojená služba Určuje připojovací řetězec, který Data Factory služba používá v době běhu pro připojení k vaší databázi. A výstupní datová sada tabulky SQL (OutputDataset) určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob. 

### <a name="create-an-input-dataset"></a>Vytvoření vstupní datové sady
V tomto kroku vytvoříte datovou sadu s názvem InputDataset, která odkazuje na soubor blob (emp.txt) v kořenové složce kontejneru objektů blob (adftutorial), který se nachází ve službě Azure Storage reprezentované propojenou službou AzureStorageLinkedService. Pokud neurčíte hodnotu fileName (nebo ji přeskočíte), data ze všech objektů blob ve vstupní složce se zkopírují do cíle. V tomto kurzu určíte hodnotu fileName.  

1. Ve složce **C:\ADFGetStartedPSH** vytvořte soubor JSON s názvem **InputDataset.json** s následujícím obsahem:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

    | Vlastnost | Popis |
    |:--- |:--- |
    | typ | Vlastnost type je nastavená na hodnotu **AzureBlob**, protože se data nachází ve službě Azure Blob Storage. |
    | linkedServiceName | Odkazuje na službu **AzureStorageLinkedService**, kterou jste vytvořili předtím. |
    | folderPath | Určuje **kontejner** objektů BLOB a **složku** , která obsahuje vstupní objekty blob. V tomto kurzu je adftutorial kontejnerem objektů blob a složka je kořenová složka. | 
    | fileName | Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto kurzu má fileName hodnotu **emp.txt**, takže se zpracuje pouze tento soubor. |
    | format -> type |Vstupní soubor je v textovém formátu, takže použijeme **TextFormat**. |
    | columnDelimiter | Sloupce ve vstupním souboru jsou oddělené **znakem čárky (`,`)**. |
    | frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, takže vstupní řezy jsou dostupné **každou hodinu**. Jinými slovy služba Data Factory každou hodinu vyhledá vstupní data v kořenové složce kontejneru objektů blob (**adftutorial**), který jste zadali. Vyhledává data v rámci kanálu mezi časy spuštění a ukončení, ne před nebo po této době.  |
    | external | Pokud data nevygeneroval tento kanál, je tato vlastnost nastavená na hodnotu **true**. Vstupní data v tomto kurzu jsou v souboru emp.txt, který není generován tímto kanálem, proto jsme tuto vlastnost nastavili na hodnotu true. |

    Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
1. Spuštěním následujícího příkazu vytvořte datovou sadu služby Data Factory.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Zde je ukázkový výstup:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
V této části kroku vytvoříte výstupní datovou sadu s názvem **OutputDataset**. Tato datová sada odkazuje na tabulku SQL v Azure SQL Database reprezentované **AzureSqlLinkedService**. 

1. Ve složce **C:\ADFGetStartedPSH** vytvořte soubor JSON s názvem **OutputDataset.json** s následujícím obsahem:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

    | Vlastnost | Popis |
    |:--- |:--- |
    | typ | Vlastnost Type je nastavená na **AzureSqlTable** , protože data se zkopírují do tabulky v Azure SQL Database. |
    | linkedServiceName | Odkazuje na službu **AzureSqlLinkedService**, kterou jste vytvořili předtím. |
    | tableName | Určuje **tabulku**, do které se kopírují data. | 
    | frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, což znamená, že výstupní řezy se tvoří **každou hodinu** mezi časy spuštění a ukončení, ne před nebo po této době.  |

    V tabulce emp v databázi jsou k dispozici tři sloupce – **ID**, **FirstName** a **LastName**. ID je sloupec identity, takže je zde třeba zadat pouze položky **FirstName** (Jméno) a **LastName** (Příjmení).

    Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).
1. Spuštěním následujícího příkazu vytvořte datovou sadu datové továrny.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Zde je ukázkový výstup:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s **aktivitou kopírování**, která používá **InputDataset** jako vstup a **OutputDataset** jako výstup.

Výstupní datové sady v současné době řídí plán. V tomto kurzu je výstupní datová sada nakonfigurovaná tak, aby vytvářela řez jednou za hodinu. Kanál má čas spuštění a čas ukončení nastavený jeden den od sebe, což je 24 hodin. Proto kanál vytvoří 24 řezů výstupní datové sady. 

1. Ve složce **C:\ADFGetStartedPSH** vytvořte soubor JSON s názvem **ADFTutorialPipeline.json** s následujícím obsahem:

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Je třeba počítat s následujícím:

   - V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md). V řešeních služby Data Factory můžete také použít [aktivity transformace dat](data-factory-data-transformation-activities.md).
   - Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. 
   - V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. Úplný seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky najdete v tématu [podporovaných úložištích dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kliknutím na odkaz v tabulce se dozvíte, jak použít konkrétní podporovaná úložiště dat jako zdroj/jímku.  

     Nahraďte hodnotu vlastnosti **start** aktuálním dnem a **end** následujícím dnem. Můžete zadat jenom část data a přeskočit část času. Například „2016-02-03“ je ekvivalentní hodnotě „2016-02-03T00:00:00Z“.

     Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2016-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme. 

     Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**.

     V předchozím příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.

     Popisy vlastností JSON použitých v definici kanálu najdete v článku [Vytvoření kanálů](data-factory-create-pipelines.md). Popisy vlastností JSON použitých v definici aktivity kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md). Popisy vlastností JSON podporovaných zdrojem BlobSource najdete v článku [Konektor Azure Blob](data-factory-azure-blob-connector.md). Popisy vlastností JSON podporovaných jímkou SqlSink najdete v článku [Konektor Azure SQL Database](data-factory-azure-sql-connector.md).
1. Spuštěním následujícího příkazu vytvořte tabulku datové továrny.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Zde je ukázkový výstup: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Blahopřejeme!** Úspěšně jste vytvořili objekt pro vytváření dat Azure s kanálem, který kopíruje data z úložiště objektů BLOB v Azure do Azure SQL Database. 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu
V tomto kroku použijete Azure PowerShell k monitorování toho, co se chystá v Azure Data Factory.

1. Nahraďte hodnotu &lt; DataFactory &gt; názvem vaší datové továrny a spusťte příkaz **Get-AzDataFactory** a přiřaďte výstup k proměnné $DF.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Například:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```

    Potom vytisknutím obsahu $df zobrazte následující výstup: 

    ```
    PS C:\ADFGetStartedPSH> $df

    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Spuštěním rutiny **Get-AzDataFactorySlice** získáte podrobnosti o všech řezech **OutputDataset**, což je výstupní datová sada kanálu.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Toto nastavení by mělo odpovídat hodnotě **Start** v kódu JSON kanálu. Měli byste vidět 24 řezů, pro každou hodinu od 12:00 aktuálního dne do 12:00 následujícího dne jeden.

   Tady jsou tři ukázkové řezy z výstupu: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Spuštěním rutiny **Get-AzDataFactoryRun** získáte podrobnosti o spuštění aktivit pro **konkrétní** řez. Zkopírujte hodnoty data a času z výstupu předchozího příkazu, abyste určili hodnotu parametru StartDateTime. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Zde je ukázkový výstup: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Úplnou dokumentaci o rutinách služby Data Factory najdete v článku [Referenční informace o rutinách služby Data Factory](/powershell/module/az.datafactory).

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro vytváření dat Azure ke zkopírování dat z objektu blob Azure do Azure SQL Database. PowerShell jste použili k vytvoření objektu pro vytváření dat, propojených služeb, datových sad a kanálu. Zde jsou základní kroky, které jste v tomto kurzu provedli:  

1. Vytvořili jste **objekt pro vytváření dat** Azure.
1. Vytvořili jste **propojené služby**:

   a. Propojená služba **Azure Storage** propojí váš účet úložiště Azure, který obsahuje vstupní data.     
   b. Propojená služba **Azure SQL** připojující službu SQL Database, která obsahuje výstupní data.
1. Vytvořili jste **datové sady**, které popisují vstupní data a výstupní data pro kanály.
1. Vytvořili jste **kanál** s **aktivitou kopírování**, která má jako zdroj **BlobSource** a jako jímku **SqlSink**.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste jako zdrojové úložiště dat použili službu Azure Blob Storage a v rámci operace kopírování Azure SQL Database jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat. 

