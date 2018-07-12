---
title: 'Kurz: Vytvoření kanálu Azure Data Factory pro kopírování dat (portál Azure Portal) | Dokumentace Microsoftu'
description: V tomto kurzu pomocí portálu Azure Portal vytvoříte kanál Azure Data Factory s aktivitou kopírování pro kopírování dat z úložiště objektů blob v Azure do databáze Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09cad6b76b2fdefe1163530e5437427faf29d72f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299917"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Kurz: Použití portálu Azure Portal k vytvoření kanálu Data Factory pro kopírování dat 
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

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 

V tomto článku se naučíte, jak použít portál [Azure Portal](https://portal.azure.com) k vytvoření datové továrny s kanálem, který kopíruje data z úložiště objektů blob v Azure do databáze SQL v Azure. Pokud s Azure Data Factory začínáte, přečtěte si článek [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto kurzem začnete.   

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky
Než se do tohoto kurzu pustíte, dokončete požadované kroky uvedené v [požadavcích kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="steps"></a>Kroky
Zde jsou kroky, které provedete v rámci tohoto kurzu:

1. Vytvořte **datovou továrnu** Azure. V tomto kroku vytvoříte datovou továrnu s názvem ADFTutorialDataFactory. 
2. V této datové továrně vytvořte **propojené služby**. V tomto kroku vytvoříte dvě propojené služby typu: Azure Storage a Azure SQL Database. 
    
    Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. V rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) jste vytvořili kontejner a nahráli data do tohoto účtu úložiště.   

    Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku SQL.   
3. Vytvořte v datové továrně vstupní a výstupní **datové sady**.  
    
    Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob určuje kontejner a složku obsahující vstupní data.  

    Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.
4. Vytvořte v datové továrně **kanál**. V tomto kroku pomocí aktivity kopírování vytvoříte kanál.   
    
    Aktivita kopírování kopíruje data z objektu blob v úložišti objektů blob v Azure do tabulky v databázi Azure SQL. Aktivitu kopírování můžete v kanálu použít ke kopírování dat z jakéhokoli podporovaného zdroje do jakéhokoli podporovaného cíle. Seznam podporovaných úložišť dat najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitorujte kanál. V tomto kroku budete **monitorovat** řezy vstupních a výstupních datových sad pomocí portálu Azure Portal. 

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
> [!IMPORTANT]
> Pokud jste tak ještě neučinili, dokončete [požadavky kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu HDInsight Hive pro spuštění skriptu Hive, který umožňuje transformovat vstupní data na výstupní data produktu. V tomto kroku začneme vytvořením objektu pro vytváření dat.

1. Po přihlášení k webu [Azure Portal](https://portal.azure.com/) klikněte v levé nabídce na **Vytvořit prostředek**, pak na **Data a analýzy** a pak klikněte na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. V okně **Nový objekt pro vytváření dat**:
   
   1. Do pole **Název** zadejte **ADFTutorialDataFactory**. 
      
         ![Okno Nový objekt pro vytváření dat](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název objektu pro vytváření dat (třeba na váš_název_ADFTutorialDataFactory) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Název objektu pro vytváření dat není k dispozici](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
   3. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
      
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
          Některé kroky v tomto kurzu vychází z předpokladu, že pro skupinu prostředků použijete název **ADFTutorialResourceGroup**. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../../azure-resource-manager/resource-group-overview.md).  
   4. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu jsou uvedené pouze oblasti podporované službou Data Factory.
   5. Zaškrtněte **Připnout na řídicí panel**.     
   6. Klikněte na možnost **Vytvořit**.
      
      > [!IMPORTANT]
      > Chcete-li vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného a skupiny prostředků.
      > 
      > Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.                
      > 
      > 
3. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Po vytvoření se zobrazí okno **Objekt pro vytváření dat**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu nebudete používat žádnou výpočetní službu jako je Azure HDInsight nebo Azure Data Lake Analytics. Budete používat dvě úložiště dat typu Azure Storage (zdroj) a Azure SQL Database (cíl). 

Vytvoříte tedy dvě propojené služby s názvem AzureStorageLinkedService a AzureSqlLinkedService typu: AzureStorage a AzureSqlDatabase.  

Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. Tento účet úložiště je ten, ve kterém jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili kontejner a nahráli do něj data.   

Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp.  

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V tomto kroku propojíte se svou datovou továrnou účet úložiště Azure. V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage.  

1. V okně **Data Factory** klikněte na dlaždici **Vytvořit a nasadit**.
   
   ![Dlaždice Vytvořit a nasadit](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Zobrazí se **Data Factory Editor**, jak je vidět na následujícím obrázku: 

    ![Data Factory Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. V editoru klikněte na panelu nástrojů na tlačítko **Nové datové úložiště** a z rozevírací nabídky vyberte **Úložiště Azure**. V pravém podokně by se měla zobrazit šablona JSON pro vytvoření propojené služby Azure Storage. 
   
    ![Tlačítko Nové datové úložiště v editoru](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Hodnoty `<accountname>` a `<accountkey>` nahraďte názvem účtu služby Azure Storage a jeho klíčem. 
   
    ![Editor – Blob Storage – JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Na panelu nástrojů klikněte na **Nasadit**. Nyní byste měli v zobrazení stromu vidět nasazenou službu **AzureStorageLinkedService**. 
   
    ![Editor – Blob Storage – nasazení](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Další informace o vlastnostech JSON použitých v definici propojené služby najdete v článku[Konektor služby Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Vytvoření propojené služby pro Azure SQL Database
V tomto kroku se svým objektem pro vytváření dat propojíte svou databázi SQL Azure. V tomto oddílu zadáte název serveru Azure SQL, název databáze, uživatelské jméno a heslo. 

1. V **editoru služby Data Factory** klikněte na panelu nástrojů na tlačítko **Nové úložiště dat** a z rozevírací nabídky vyberte **Azure SQL Database**. V pravém podokně by se měla zobrazit šablona JSON pro vytvoření propojené služby Azure SQL.
2. Hodnoty `<servername>`, `<databasename>`, `<username>@<servername>` a `<password>` nahraďte názvy serveru Azure SQL, databáze, uživatelského účtu a heslem. 
3. Službu **AzureSqlLinkedService** vytvoříte a nasadíte kliknutím na **Nasadit** na panelu nástrojů.
4. Zkontrolujte, jestli se služba **AzureSqlLinkedService** objevila v zobrazení stromu v části **Propojené služby**.  

    Další informace o těchto vlastnostech JSON najdete v článku [Konektor služby Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Vytvoření datových sad
V předchozím kroku jste vytvořili propojené služby, abyste propojili účet úložiště Azure a Azure SQL Database s datovou továrnou. V tomto kroku nadefinujete dvě datové sady s názvem InputDataset a OutputDataset, které představují vstupní a výstupní data uložená v úložištích dat, na která odkazují služby AzureStorageLinkedService a AzureSqlLinkedService.

Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob (InputDataset) určuje kontejner a složku obsahující vstupní data.  

Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL (OutputDataset) určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob. 

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
V tomto kroku vytvoříte datovou sadu s názvem InputDataset, která odkazuje na soubor blob (emp.txt) v kořenové složce kontejneru objektů blob (adftutorial), který se nachází ve službě Azure Storage reprezentované propojenou službou AzureStorageLinkedService. Pokud neurčíte hodnotu fileName (nebo ji přeskočíte), data ze všech objektů blob ve vstupní složce se zkopírují do cíle. V tomto kurzu určíte hodnotu fileName. 

1. V **editoru** služby Data Factory klikněte na **... Další**, klikněte na **Nová datová sada** a v rozevíracím seznamu klikněte na **Azure Blob Storage**. 
   
    ![Nabídka Nová datová sada](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Nahraďte kód JSON v pravém podokně následujícím fragmentem kódu JSON: 
   
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
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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
3. Datovou sadu **InputDataset** vytvoříte a nasadíte kliknutím na **Nasadit** na panelu nástrojů. Zkontrolujte, jestli se datová sada **InputDataset** objevila v zobrazení stromu.

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady
Propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. Výstupní datová sada tabulky SQL (OutputDataset), kterou v tomto kroku vytvoříte, určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.

1. V **editoru** služby Data Factory klikněte na **... Další**, klikněte na **Nová datová sada** a v rozevíracím seznamu klikněte na **Azure SQL**. 
2. Nahraďte kód JSON v pravém podokně následujícím fragmentem kódu JSON:

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
    | type | Vlastnost type je nastavena na hodnotu **AzureSqlTable**, protože data se kopírují do tabulky v databázi Azure SQL. |
    | linkedServiceName | Odkazuje na službu **AzureSqlLinkedService**, kterou jste vytvořili předtím. |
    | tableName | Určuje **tabulku**, do které se kopírují data. | 
    | frequency/interval | Frekvence je nastavená na hodnotu **Hour** (hodina) a interval je **1**, což znamená, že výstupní řezy se tvoří **každou hodinu** mezi časy spuštění a ukončení, ne před nebo po této době.  |

    V tabulce emp v databázi jsou k dispozici tři sloupce – **ID**, **FirstName** a **LastName**. ID je sloupec identity, takže je zde třeba zadat pouze položky **FirstName** (Jméno) a **LastName** (Příjmení).

    Další informace o těchto vlastnostech JSON najdete v článku [konektor Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).
3. Datovou sadu **OutputDataset** vytvoříte a nasadíte kliknutím na **Nasadit** na panelu nástrojů. Zkontrolujte, jestli se datová sada **OutputDataset** objevila v zobrazení stromu v části **Datové sady**. 

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s **aktivitou kopírování**, která používá **InputDataset** jako vstup a **OutputDataset** jako výstup.

Výstupní datové sady v současné době řídí plán. V tomto kurzu je výstupní datová sada nakonfigurovaná tak, aby vytvářela řez jednou za hodinu. Kanál má čas spuštění a čas ukončení nastavený jeden den od sebe, což je 24 hodin. Proto kanál vytvoří 24 řezů výstupní datové sady. 

1. V **editoru** služby Data Factory klikněte na **... Další** a poté na **Nový kanál**. Případně můžete ve stromovém zobrazení kliknout pravým tlačítkem na **anály** a pak kliknout na **Nový kanál**.
2. Nahraďte kód JSON v pravém podokně následujícím fragmentem kódu JSON: 

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
    - Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2016-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme. Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Pokud chcete kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9999-09-09**.
     
    V předchozím příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.

    Popisy vlastností JSON použitých v definici kanálu najdete v článku [Vytvoření kanálů](data-factory-create-pipelines.md). Popisy vlastností JSON použitých v definici aktivity kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md). Popisy vlastností JSON podporovaných zdrojem BlobSource najdete v článku [Konektor Azure Blob](data-factory-azure-blob-connector.md). Popisy vlastností JSON podporovaných jímkou SqlSink najdete v článku [Konektor Azure SQL Database](data-factory-azure-sql-connector.md).
3. Kanál **ADFTutorialPipeline** vytvoříte a nasadíte kliknutím na **Nasadit** na panelu nástrojů. Zkontrolujte, jestli se kanál objevil v zobrazení stromu. 
4. Teď zavřete okno **Editor** kliknutím na **X**. Chcete-li zobrazit domovskou stránku **objektu pro vytváření dat** pro **ADFTutorialDataFactory**, znovu klikněte na **X**.

**Blahopřejeme!** Úspěšně jste vytvořili datovou továrnu Azure s kanálem, který kopíruje data z úložiště objektů blob v Azure do databáze Azure SQL. 


## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku budete pomocí webu Azure Portal monitorovat, co se děje v objektu pro vytváření dat Azure.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorování kanálu pomocí aplikace pro monitorování a správu
Následující kroky ukazují, jak v datové továrně monitorovat kanály pomocí aplikace pro monitorování a správu: 

1. Na domovské stránce svého objektu pro vytváření dat klikněte na dlaždici **Monitorování a správa**.
   
    ![Dlaždice Monitorování a správa](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Na samostatné kartě by se měla zobrazit **aplikace pro monitorování a správu**. 

    > [!NOTE]
    > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, proveďte jeden z následujících kroků: zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) nebo vytvořte výjimku pro **login.microsoftonline.com** a potom zkuste aplikaci znovu otevřít.

    ![Aplikace pro monitorování a správu](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Změňte hodnoty **Čas spuštění** a **Čas ukončení** tak, aby zahrnovaly časy spuštění (11. 5. 2017) a ukončení (12. 5. 2017) vašeho kanálu, potom klikněte na **Použít**.       
3. Na seznamu v prostředním podokně se zobrazí **okna aktivit** spojená s každou hodinou mezi časem spuštění a časem ukončení. 
4. Pokud chcete zobrazit podrobnosti o okně aktivity, vyberte ho v seznamu **Okna aktivit**. 
    ![Podrobnosti o okně aktivity](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    V Průzkumníku okna aktivity napravo uvidíte, že řezy do aktuálního času UTC (20:12) jsou všechny zpracované (v zelené barvě). Řezy v časech 20:00–21:00, 21:00–22:00, 22:00–23:00 a 23:00–00:00 ještě zpracované nebyly.

    Oddíl **Pokusy** v pravém podokně poskytuje informace o spuštění aktivit pro datové řezy. V případě, že došlo k chybě, poskytuje o ní podrobnosti. Například pokud vstupní složka nebo kontejner neexistuje a zpracování řezu se nezdaří, zobrazí chybová zpráva s oznámením, že kontejner nebo složka neexistuje.

    ![Pokusy spuštění aktivit](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Spusťte **SQL Server Management Studio**, připojte se ke službě Azure SQL Database a ověřte, že se řádky vložily do tabulky **emp** v databázi.
    
    ![Výsledky dotazu SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Podrobnosti o použití této aplikace najdete v tématu [Monitorování a správa kanálů služby Azure Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md).

### <a name="monitor-pipeline-using-diagram-view"></a>Monitorování kanálu pomocí Zobrazení diagramu
Datové kanály můžete také monitorovat pomocí zobrazení diagramu.  

1. V okně **Objekt pro vytváření dat** klikněte na **Diagram**.
   
    ![Okno objekt pro vytváření dat – dlaždice Diagram](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Zobrazený diagram by měl vypadat přibližně jako na následujícím obrázku: 
   
    ![Zobrazení diagramu](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. V zobrazení diagramu si dvojitým kliknutím na **InputDataset** zobrazíte řezy pro datovou sadu.  
   
    ![Datové sady s vybranou sadou InputDataset](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Kliknutím na odkaz **Zobrazit více** zobrazíte všechny datové řezy. Uvidíte 24 hodinových řezů mezi časem spuštění a ukončení. 
   
    ![Všechny vstupní datové řezy](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Všimněte si, že všechny datové řezy až do aktuálního času UTC jsou ve stavu **Připraveno**, protože soubor **emp.txt** celou dobu existuje v kontejneru objektů blob: **adftutorial\input**. Řezy budoucích časů ještě ve stavu připraveno nejsou. Potvrďte, že se žádné řezy nezobrazují v části **Řezy, které v poslední době selhaly** dole.
6. Pozavírejte tato okna, dokud neuvidíte zobrazení diagramu nebo si zobrazení diagramu zobrazte posunutím vlevo. Potom dvakrát klikněte na **OutputDataset**. 
8. Pokud chcete zobrazit všechny řezy, klikněte na odkaz **Zobrazit více** v okně **tabulky** pro **OutputDataset**.

    ![okno datové řezy](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Všimněte si, že všechny řezy až do aktuálního času UTC se přesunuly ze stavu **Čeká na provedení** => **Probíhá** ==> **Připraveno**. Ve výchozím nastavení se řezy z minulosti (před aktuálním časem) zpracovávají od nejnovějšího po nejstarší. Například pokud je aktuální čas UTC 20:12, řez pro dobu 19:00–20:00 se zpracuje před řezem z doby 18:00–19:00. Ve výchozím nastavení se řez z doby 20:00–21:00 zpracuje na konci časového intervalu, což je po 21:00.  
10. Klikněte na libovolný datový řez ze seznamu. Mělo by se zobrazit okno **Datový řez**. Část dat spojená s oknem aktivity se nazývá řez. Řez může tvořit jeden soubor nebo více souborů.  
    
     ![okno datový řez](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Není-li řez ve stavu **Připraveno**, zobrazí se v seznamu **Upstreamové datové řezy, které nejsou připraveny** upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu.
11. V okně **DATOVÝ ŘEZ**byste měli v seznamu dole vidět všechna spuštění aktivit. Kliknutím na **spuštění aktivit** zobrazíte okno **Podrobnosti o spuštění aktivit**. 
    
    ![Podrobnosti o spuštění aktivit](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    V tomto okně vidíte, jak dlouho kopírování trvalo, jaká je propustnost, kolik bajtů dat se přečetlo a zapsalo, počáteční čas spuštění, koncový čas spuštění atd.  
12. Klikejte na tlačítko **X**, dokud nezavřete všechna okna a nevrátíte se zpátky do domovského okna pro **ADFTutorialDataFactory**.
13. (volitelné) pokud chcete zobrazit okna, která jste viděli u předchozích kroků, klikněte na dlaždici **Datové sady** nebo dlaždici **Kanály**. 
14. Spusťte **SQL Server Management Studio**, připojte se ke službě Azure SQL Database a ověřte, že se řádky vložily do tabulky **emp** v databázi.
    
    ![Výsledky dotazu SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro vytváření dat Azure pro zkopírování dat z objektu blob Azure do Azure SQL Database. Použili jste web Azure Portal k vytvoření objektu pro vytváření dat, propojených služeb, datových sad a kanálu. Zde jsou základní kroky, které jste v tomto kurzu provedli:  

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste **propojené služby**:
   1. Propojená služba **Azure Storage** připojující účet úložiště Azure, který obsahuje vstupní data.     
   2. Propojená služba **Azure SQL** připojující službu Azure SQL Database, která obsahuje výstupní data. 
3. Vytvořili jste **datové sady**, které popisují vstupní data a výstupní data pro kanály.
4. Vytvořili jste **kanál** s **aktivitou kopírování**, která má jako zdroj **BlobSource** a jako jímku **SqlSink**.  

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat.