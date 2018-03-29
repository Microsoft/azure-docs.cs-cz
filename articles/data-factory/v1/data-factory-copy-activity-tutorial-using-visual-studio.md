---
title: 'Kurz: Vytvoření kanálu s aktivitou kopírování pomocí sady Visual Studio | Dokumentace Microsoftu'
description: V tomto kurzu vytvoříte kanál služby Azure Data Factory s aktivitou kopírování pomocí sady Visual Studio.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c25bd85c362e7055109e0a2a178a417ef110899a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Kurz: Vytvoření kanálu s aktivitou kopírování pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [kurz aktivity kopírování v dokumentaci verze 2](../quickstart-create-data-factory-dot-net.md). 

V tomto článku se naučíte, jak používat Microsoft Visual Studio, abyste vytvořili datovou továrnu s kanálem, který kopíruje data z úložiště objektů blob v Azure do databáze SQL v Azure. Pokud s Azure Data Factory začínáte, přečtěte si článek [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto kurzem začnete.   

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky
1. Přečtěte si článek [Přehled kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a proveďte **nutné** kroky.       
2. Chcete-li vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) na úrovni předplatného a skupiny prostředků.
3. Na počítači musíte mít nainstalované tyto položky: 
   * Visual Studio 2013 nebo Visual Studio 2015.
   * Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
   * Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Modul plug-in můžete taky aktualizovat, a to pomocí tohoto postupu: V nabídce klikněte na **Nástroje** -> **Rozšíření a aktualizace** -> **Online** -> **Galerie sady Visual Studio** -> **Microsoft Azure Data Factory Tools for Visual Studio** (Nástroje Microsoft Azure Data Factory pro Visual Studio) -> **Aktualizovat**.

## <a name="steps"></a>Kroky
Zde jsou kroky, které provedete v rámci tohoto kurzu:

1. V této datové továrně vytvořte **propojené služby**. V tomto kroku vytvoříte dvě propojené služby typu: Azure Storage a Azure SQL Database. 
    
    Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jste vytvořili kontejner a nahráli data do tohoto účtu úložiště.   

    Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku SQL.     
2. Vytvořte v datové továrně vstupní a výstupní **datové sady**.  
    
    Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob určuje kontejner a složku obsahující vstupní data.  

    Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.
3. Vytvořte v datové továrně **kanál**. V tomto kroku pomocí aktivity kopírování vytvoříte kanál.   
    
    Aktivita kopírování kopíruje data z objektu blob v úložišti objektů blob v Azure do tabulky v databázi Azure SQL. Aktivitu kopírování můžete v kanálu použít ke kopírování dat z jakéhokoli podporovaného zdroje do jakéhokoli podporovaného cíle. Seznam podporovaných úložišť dat najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
4. Vytvořte **datovou továrnu** Azure, když nasazujete entity služby Data Factory (propojené služby, datové sady / tabulky a kanály). 

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio
1. Spusťte **Visual Studio 2015**. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**. Mělo by se zobrazit dialogové okno **Nový projekt**.  
2. V dialogovém okně **Nový projekt** vyberte šablonu **DataFactory** a klikněte na **Empty Data Factory Project** (Prázdný projekt Data Factory).  
   
    ![Dialogové okno Nový projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Zadejte název projektu, umístění pro řešení a název tohoto řešení a potom klikněte na **OK**.
   
    ![Průzkumník řešení](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu nebudete používat žádnou výpočetní službu jako je Azure HDInsight nebo Azure Data Lake Analytics. Budete používat dvě úložiště dat typu Azure Storage (zdroj) a Azure SQL Database (cíl). 

Proto vytvoříte dvě propojené služby typu: AzureStorage a AzureSqlDatabase.  

Propojená služba AzureStorage propojí váš účet služby Azure Storage s datovou továrnou. Tento účet úložiště je ten, ve kterém jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili kontejner a nahráli do něj data.   

Propojená služba Azure SQL propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp.

Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. Všechny zdroje a jímky podporované aktivitou kopírování najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Seznam výpočetních služeb podporovaných službou Data Factory najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md). V tomto kurzu žádné výpočetní služby nepoužijete. 

### <a name="create-the-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Propojené služby**, přejděte na **Přidat** a klikněte na **Nová položka**.      
2. V dialogovém okně **Přidat novou položku** vyberte v seznamu možnost **Azure Storage Linked Service** (Propojená služba Azure Storage) a klikněte na **Přidat**. 
   
    ![Nová propojená služba](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Hodnoty `<accountname>` a `<accountkey>`* nahraďte názvem účtu služby Azure Storage a jeho klíčem. 
   
    ![Propojená služba Azure Storage](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Uložte soubor **AzureStorageLinkedService1.json**.

    Další informace o vlastnostech JSON použitých v definici propojené služby najdete v článku[Konektor služby Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Vytvoření propojené služby Azure SQL
1. V **Průzkumníku řešení** znovu klikněte pravým tlačítkem myši na uzel **Propojené služby**, přejděte na **Přidat** a klikněte na **Nová položka**. 
2. Tentokrát vyberte **Propojená služba Azure SQL** a klikněte na **Přidat**. 
3. V souboru **AzureSqlLinkedService1.json** nahraďte hodnoty `<servername>`, `<databasename>`, `<username@servername>` a `<password>` názvy svého serveru Azure SQL, databáze, uživatelského účtu a heslem.    
4. Uložte soubor **AzureSqlLinkedService1.json**. 
    
    Další informace o těchto vlastnostech JSON najdete v článku [Konektor služby Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).


## <a name="create-datasets"></a>Vytvoření datových sad
V předchozím kroku jste vytvořili propojené služby, abyste propojili účet úložiště Azure a Azure SQL Database s datovou továrnou. V tomto kroku nadefinujete dvě datové sady s názvem InputDataset a OutputDataset, které představují vstupní a výstupní data uložená v úložištích dat, na která odkazují služby AzureStorageLinkedService1 a AzureSqlLinkedService1.

Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob (InputDataset) určuje kontejner a složku obsahující vstupní data.  

Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL (OutputDataset) určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob. 

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
V tomto kroku vytvoříte datovou sadu s názvem InputDataset, která odkazuje na soubor blob (emp.txt) v kořenové složce kontejneru objektů blob (adftutorial), který se nachází ve službě Azure Storage reprezentované propojenou službou AzureStorageLinkedService1. Pokud neurčíte hodnotu fileName (nebo ji přeskočíte), data ze všech objektů blob ve vstupní složce se zkopírují do cíle. V tomto kurzu určíte hodnotu fileName. 

Zde raději použijte termín „tabulky“ než „datové sady“. Tabulka je obdélníková datová sada a je jediným typem datové sady, který je nyní podporovaný. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Tabulky**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V dialogovém okně **Přidat novou položku** vyberte v seznamu možnost **Azure Blob** a klikněte na **Přidat**.   
3. Nahraďte text JSON následujícím textem a uložte soubor **AzureBlobLocation1.json**. 

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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
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
    | type | Vlastnost type je nastavená na hodnotu **AzureBlob**, protože se data nachází ve službě Azure Blob Storage. |
    | linkedServiceName | Odkazuje na službu **AzureStorageLinkedService**, kterou jste vytvořili předtím. |
    | folderPath | Určuje **kontejner** objektů blob a **složku** obsahující vstupní objekty blob. V tomto kurzu je adftutorial kontejnerem objektů blob a složka je kořenová složka. | 
    | fileName | Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto kurzu má fileName hodnotu **emp.txt**, takže se zpracuje pouze tento soubor. |
    | format -> type |Vstupní soubor je v textovém formátu, takže použijeme **TextFormat**. |
    | columnDelimiter | Sloupce ve vstupním souboru jsou oddělené **znakem čárky (`,`)**. |
    | frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, takže vstupní řezy jsou dostupné **každou hodinu**. Jinými slovy služba Data Factory každou hodinu vyhledá vstupní data v kořenové složce kontejneru objektů blob (**adftutorial**), který jste zadali. Vyhledává data v rámci kanálu mezi časy spuštění a ukončení, ne před nebo po této době.  |
    | external | Pokud data nevygeneroval tento kanál, je tato vlastnost nastavená na hodnotu **true**. Vstupní data v tomto kurzu jsou v souboru emp.txt, který není generován tímto kanálem, proto jsme tuto vlastnost nastavili na hodnotu true. |

    Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).   

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady
V tomto kroku vytvoříte výstupní datovou sadu s názvem **OutputDataset**. Tato datová sada odkazuje na tabulku SQL ve službě Azure SQL Database, kterou reprezentuje **AzureSqlLinkedService1**. 

1. V **Průzkumníku řešení** klikněte opět pravým tlačítkem myši na **Tabulky**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V dialogovém okně **Přidat novou položku** vyberte **Azure SQL** a klikněte na **Přidat**. 
3. Nahraďte text JSON následujícím textem JSON a uložte soubor **AzureSqlTableLocation1.json**.

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
       "linkedServiceName": "AzureSqlLinkedService1",
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
    | type | Vlastnost type je nastavena na hodnotu **AzureSqlTable**, protože data se kopírují do tabulky v databázi Azure SQL. |
    | linkedServiceName | Odkazuje na službu **AzureSqlLinkedService**, kterou jste vytvořili předtím. |
    | tableName | Určuje **tabulku**, do které se kopírují data. | 
    | frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, což znamená, že výstupní řezy se tvoří **každou hodinu** mezi časy spuštění a ukončení, ne před nebo po této době.  |

    V tabulce emp v databázi jsou k dispozici tři sloupce – **ID**, **FirstName** a **LastName**. ID je sloupec identity, takže je zde třeba zadat pouze položky **FirstName** (Jméno) a **LastName** (Příjmení).

    Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s **aktivitou kopírování**, která používá **InputDataset** jako vstup a **OutputDataset** jako výstup.

Výstupní datové sady v současné době řídí plán. V tomto kurzu je výstupní datová sada nakonfigurovaná tak, aby vytvářela řez jednou za hodinu. Kanál má čas spuštění a čas ukončení nastavený jeden den od sebe, což je 24 hodin. Proto kanál vytvoří 24 řezů výstupní datové sady. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Kanály**, přejděte na **Přidat** a klikněte na **Nová položka**.  
2. V dialogovém okně **Přidat novou položku** vyberte **Copy Data Pipeline** (Kanál kopírování dat) a klikněte na **Přidat**. 
3. Nahraďte text JSON následujícím textem JSON a uložte soubor **CopyActivity1.json**.

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
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
    - V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md). V řešeních služby Data Factory můžete také použít [aktivity transformace dat](data-factory-data-transformation-activities.md).
    - Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. 
    - V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. Úplný seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky najdete v tématu [podporovaných úložištích dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kliknutím na odkaz v tabulce se dozvíte, jak použít konkrétní podporovaná úložiště dat jako zdroj/jímku.  
     
    Nahraďte hodnotu vlastnosti **start** aktuálním dnem a **end** následujícím dnem. Můžete zadat jenom část data a přeskočit část času. Například „2016-02-03“ je ekvivalentní hodnotě „2016-02-03T00:00:00Z“.
     
    Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2016-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme. 
     
    Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**.
     
    V předchozím příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.

    Popisy vlastností JSON použitých v definici kanálu najdete v článku [Vytvoření kanálů](data-factory-create-pipelines.md). Popisy vlastností JSON použitých v definici aktivity kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md). Popisy vlastností JSON podporovaných zdrojem BlobSource najdete v článku [Konektor Azure Blob](data-factory-azure-blob-connector.md). Popisy vlastností JSON podporovaných jímkou SqlSink najdete v článku [Konektor Azure SQL Database](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Publikování/nasazení entit služby Data Factory
V tomto kroku publikujete entity služby Data Factory (propojené služby, datové sady a kanál), které jste vytvořili dříve. Také zadáte název nově vytvořeného objektu pro vytváření dat, do kterého se tyto entity načtou.  

1. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**. 
2. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
3. Mělo by se zobrazit následující dialogové okno:
   
   ![Dialogové okno publikování](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na stránce Konfigurace objektu pro vytváření dat proveďte následující kroky: 
   
   1. Vyberte možnost **Create New Data Factory** (Vytvořit nový objekt pro vytváření dat).
   2. Zadejte **VSTutorialFactory** jako **Název**.  
      
      > [!IMPORTANT]
      > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se při publikování zobrazí chyba týkající se názvu objektu pro vytváření dat, změňte název objektu pro vytváření dat (třeba na váš_název_VSTutorialFactory) a zkuste publikování znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.        
      > 
      > 
   3. V poli **Předplatné** vyberte své předplatné Azure.
      
      > [!IMPORTANT]
      > Pokud nevidíte žádné předplatné, ujistěte se, že jste přihlášeni pomocí účtu, který je správce nebo spolusprávce předplatného.  
      > 
      > 
   4. Vyberte **skupinu prostředků** pro objekt pro vytváření dat, který se má vytvořit. 
   5. Vyberte **oblast** pro objekt pro vytváření dat. V rozevíracím seznamu jsou uvedené pouze oblasti podporované službou Data Factory.
   6. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky).
      
       ![Stránka konfigurace služby Data Factory](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.
   
   ![Stránka publikování položek](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.
   
   ![Stránka souhrnu publikování](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.
 
   ![Stránka stavu nasazení](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Je třeba počítat s následujícím: 

* Pokud se zobrazí chyba „Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory“, proveďte některý z těchto kroků a znovu zkuste název publikovat: 
  
  * V prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory pomocí následujícího příkazu. 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Spuštěním následujícího příkazu si můžete ověřit, jestli je zprostředkovatel služby Data Factory zaregistrovaný. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Přihlaste se na web [Azure Portal ](https://portal.azure.com) pomocí předplatného Azure a přejděte do okna Objekt pro vytváření dat nebo na webu Azure Portal vytvořte objekt pro vytváření dat. Zprostředkovatel se při takovém postupu zaregistruje automaticky.
* Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.

> [!IMPORTANT]
> Chcete-li vytvářet instance služby Data Factory, musíte být správce nebo spolusprávce předplatného Azure.

## <a name="monitor-pipeline"></a>Monitorování kanálu
Přejděte na domovskou stránku své datové továrny:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte v levé nabídce na **Další služby** a poté na **Datové továrny**.

    ![Procházet -> Datové továrny](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Začněte zadávat název své datové továrny.

    ![Název datové továrny](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Klikněte na svou datovou továrnu v seznamu výsledků, abyste si zobrazili domovskou stránku datové továrny.

    ![Domovská stránka objektu pro vytváření dat](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Postupujte podle pokynů v tématu [Monitorování datových sad a kanálu](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) k monitorování kanálu a datových sad, které jste vytvořili v tomto kurzu. V současné době Visual Studio monitorování kanálů Data Factory nepodporuje. 

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro vytváření dat Azure pro zkopírování dat z objektu blob Azure do Azure SQL Database. Visual Studio jste použili k vytvoření objektu pro vytváření dat, propojených služeb, datových sad a kanálu. Zde jsou základní kroky, které jste v tomto kurzu provedli:  

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste **propojené služby**:
   1. Propojená služba **Azure Storage** připojující účet úložiště Azure, který obsahuje vstupní data.     
   2. Propojená služba **Azure SQL** připojující službu Azure SQL Database, která obsahuje výstupní data. 
3. Vytvořili jste **datové sady**, které popisují vstupní data a výstupní data pro kanály.
4. Vytvořili jste **kanál** s **aktivitou kopírování**, která má jako zdroj **BlobSource** a jako jímku **SqlSink**. 

Informace o tom, jak používat aktivitu HDInsight Hive pomocí clusteru Azure HDInsight najdete v tématu popisujícím [kurz vytvoření prvního kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md). 

## <a name="view-all-data-factories-in-server-explorer"></a>Zobrazení všech datových továren v Průzkumníku serveru
Tato část popisuje, jak použít Průzkumník serveru ve Visual Studiu k zobrazení všech datových továren v předplatném Azure a jak vytvořit ve Visual Studiu projekt na základě existující datové továrny. 

1. V sadě **Visual Studio** klikněte v nabídce na **Zobrazit** a potom klikněte na **Průzkumník serveru**.
2. V okně Průzkumníka serveru rozbalte položku **Azure** a potom **Data Factory**. Pokud se zobrazí text **Přihlásit se k Visual Studiu**, zadejte **účet** přidružený k vašemu předplatnému Azure a klikněte na **Pokračovat**. Zadejte **heslo** a klikněte na **Přihlásit**. Visual Studio se pokusí získat informace o všech objektech pro vytváření dat Azure v rámci vašeho předplatného. Stav této operace se zobrazuje v okně **Data Factory Task List** (Seznam úkolů služby Data Factory).

    ![Průzkumník serveru](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Vytvoření projektu ve Visual Studiu pro existující datovou továrnu

- Kliknutím na datovou továrnu pravým tlačítkem v Průzkumníku serveru a výběrem **Export Data Factory to New Project** (Exportovat továrnu dat do nového projektu) vytvoříte projekt sady Visual Studio založený na existující datové továrně.

    ![Export objektu pro vytváření dat do projektu VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Aktualizace nástrojů služby Data Factory pro Visual Studio
Chcete-li aktualizovat nástroje služby Azure Data Factory pro Visual Studio, proveďte následující kroky:

1. V nabídce klikněte na **Nástroje** a vyberte **Rozšíření a aktualizace**. 
2. V levém podokně vyberte **Aktualizace** a vyberte **Galerie sady Visual Studio**.
3. Vyberte možnost **Azure Data Factory tools for Visual Studio** (Nástroje služby Azure Data Factory pro Visual Studio) a klikněte na **Aktualizovat**. Pokud se tato položka nezobrazí, znamená to, že máte nejnovější verzi těchto nástrojů. 

## <a name="use-configuration-files"></a>Použití konfiguračních souborů
Pomocí konfiguračních souborů v sadě Visual Studio můžete pro různá prostředí nakonfigurovat různé vlastnosti propojených služeb / tabulek / kanálů.

Podívejte se na následující definici JSON pro propojenou službu Azure Storage. U položky **connectionString** můžete určit jiné hodnoty vlastností accountname a accountkey pro různá prostředí (vývojové/testovací/produkční), do kterých nasazujete entity služby Data Factory. Provedete to tak, že pro každé prostředí použijete samostatný konfigurační soubor.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Přidání konfiguračního souboru
Následující postup umožňuje přidat pro každé prostředí jiný konfigurační soubor:   

1. V řešení v sadě Visual Studio klikněte pravým tlačítkem na svůj projekt Data Factory, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V seznamu nainstalovaných šablon vlevo vyberte šablonu **Config**, vyberte možnost **Konfigurační soubor**, zadejte jeho **název** a klikněte na **Přidat**.

    ![Přidání konfiguračního souboru](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Přidejte parametry konfigurace a jejich hodnoty v následujícím formátu:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Tento příklad umožňuje nakonfigurovat vlastnost connectionString propojené služby Azure Storage a propojené služby Azure SQL. Všimněte si, že syntaxe pro určení názvu je [JsonPath](http://goessner.net/articles/JsonPath/).   

    Pokud má kód JSON vlastnost s polem hodnot, jak je znázorněno v následujícím kódu:  

    ```json
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
    ```

    Nakonfigurujte vlastnosti tak, jak je znázorněno v následujícím konfiguračním souboru (použijte indexování počítané od nuly):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Názvy vlastností s mezerami
Pokud název vlastnosti obsahuje mezery, použijte hranaté závorky, jak ukazuje následující příklad (název databázového serveru):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Nasazení řešení pomocí konfigurace
Když v sadě VS publikujete entity služby Azure Data Factory, můžete určit, jakou konfiguraci chcete pro danou operaci publikování použít.

Pokud chcete publikovat entity v projektu Azure Data Factory pomocí konfiguračního souboru, postupujte takto:   

1. Klikněte pravým tlačítkem na projekt Data Factory a kliknutím na **Publikovat** zobrazte dialogové okno **Publish Items** (Publikovat položky).
2. Vyberte existující objekt pro vytváření dat nebo zadejte hodnoty pro vytvoření objektu pro vytváření dat na stránce **Configure data factory** (Konfigurace objektu pro vytváření dat) a klikněte na **Další**.   
3. Na stránce **Publish Items** (Publikovat položky) se zobrazí rozevírací seznam s dostupnými konfiguracemi pro pole **Select Deployment Config** (Výběr konfigurace nasazení).

    ![Výběr konfiguračního souboru](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Vyberte **konfigurační soubor**, který chcete použít, a klikněte na **Další**.
5. Ujistěte se, že se na stránce **Souhrn** zobrazil název souboru JSON, a klikněte na **Další**.
6. Po dokončení operace nasazení klikněte na **Dokončit**.

Při nasazení se hodnoty z konfiguračního souboru použijí k nastavení hodnot vlastností v souborech JSON před samotným nasazením entit do služby Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Použití Azure Key Vault
Není vhodné a často je to proti zásadám zabezpečení ukládat citlivá data, jako jsou například připojovací řetězce, do úložišti kódu. V ukázce [zabezpečeného publikování ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) na Githubu získáte další informace o ukládání citlivých informací v Azure Key Vault a jejich používání při publikování entit služby Data Factory. Rozšíření zabezpečeného publikování pro Visual Studio umožňuje ukládat tajné klíče v Key Vault a v konfiguracích propojených služeb a nasazení uvádět pouze odkazy. Tyto odkazy se při publikování entit služby Data Factory do Azure vyhodnotí. Tyto soubory pak lze uložit do úložiště zdrojového kódu bez vystavení jakýchkoli tajných kódů.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat.