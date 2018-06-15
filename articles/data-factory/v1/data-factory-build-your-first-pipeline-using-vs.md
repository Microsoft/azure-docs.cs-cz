---
title: Sestavení prvního objektu pro vytváření dat (Visual Studio) | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte pomocí sady Visual Studio ukázkový kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7398c0c9-7a03-4628-94b3-f2aaef4a72c5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ca8d5a26205e65668d46be6b558d596ab066c5f8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404867"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Kurz: Vytvoření datové továrny pomocí sady Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory verze 2](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu se dozvíte, jak vytvořit datovou továrnu Azure pomocí sady Visual Studio. Vytvoříte projektu sady Visual Studio pomocí šablony projektu Data Factory, budete definovat entity Data Factory (propojené služby, datové sady a kanál) ve formátu JSON a potom budete tyto entity publikovat/nasazovat do cloudu. 

Kanál v tomto kurzu má jednu aktivitu: **aktivitu HDInsight Hive**. Tato aktivita spouští skript Hive v clusteru Azure HDInsight, který transformuje vstupní data pro vytvoření výstupních dat. Spuštění kanálu je naplánované jednou za měsíc mezi zadaným počátečním a koncovým časem. 

> [!NOTE]
> Tento kurz neukazuje, jak kopírovat data pomocí Azure Data Factory. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace najdete v tématu [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Názorný postup: Vytvoření a publikování entit Data Factory
V rámci tohoto názorného postupu provedete tyto kroky:

1. Vytvořte dvě propojené služby: **AzureStorageLinkedService1** a **HDInsightOnDemandLinkedService1**. 
   
    V tomto kurzu jsou vstupní i výstupní data pro aktivitu Hive ve stejné službě Azure Blob Storage. Ke zpracování existujících vstupních dat a vytvoření výstupních dat můžete použít cluster HDInsight na vyžádání. Cluster HDInsight na vyžádání za vás automaticky vytvoří služba Azure Data Factory za běhu, když jsou vstupní data připravená k zpracování. Musíte propojit úložiště dat nebo výpočetní služby k datové továrně pro vytváření dat tak, aby se k nim služba Data Factory mohla připojit za běhu. Proto propojíte účet Azure Storage k datové továrně pomocí AzureStorageLinkedService1 a připojíte cluster HDInsight na vyžádání pomocí HDInsightOnDemandLinkedService1. Při publikování je potřeba zadat název datové továrny, která se má vytvořit, nebo název existující datové továrny.  
2. Vytvořte dvě datové sady, **InputDataset** a **OutputDataset**, které představují vstupní a výstupní data uložená ve službě Azure Blob Storage. 
   
    Tyto definice datové sady odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku. Jako InputDataset zadejte kontejner objektů blob (adfgetstarted) a složku (inptutdata), která obsahuje objekt blob se vstupními daty. Jako OutputDataset zadejte kontejner objektů blob (adfgetstarted) a složku (partitioneddata), do které se ukládají výstupní data. Zadáte také další vlastnosti, jako jsou například struktura, dostupnost a zásady.
3. Vytvořte kanál s názvem **MyFirstPipeline**. 
  
    V tomto názorném postupu má kanál má jednu aktivitu: **aktivitu HDInsight Hive**. Tato aktivita spouští skript Hive v clusteru HDInsight na vyžádání, který transformuje vstupní data pro vytvoření výstupních dat. Další informace o aktivitě Hive najdete v tématu [Aktivita Hive](data-factory-hive-activity.md). 
4. Vytvořte datovou továrnu s názvem **DataFactoryUsingVS**. Nasaďte objekt pro vytváření dat a všechny entity služby Data Factory (propojené služby, tabulky a kanál).
5. Po publikování použijte okna na webu Azure Portal a aplikaci Monitorování a správa k monitorování kanálu. 
  
### <a name="prerequisites"></a>Požadavky
1. Přečtěte si článek [Přehled kurzu](data-factory-build-your-first-pipeline.md) a proveďte **nutné** kroky. Pokud chcete přepnout na tento článek, můžete taky v rozevíracím seznamu v horní části okna vybrat možnost **Přehled a požadavky**. Po dokončení požadavků přepněte zpátky na tento článek výběrem možnosti **Visual Studio** v rozevíracím seznamu.
2. Chcete-li vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného a skupiny prostředků.  
3. Na počítači musíte mít nainstalované tyto položky:
   * Visual Studio 2013 nebo Visual Studio 2015.
   * Stáhněte si sadu Azure SDK pro Visual Studio 2013 nebo Visual Studio 2015. Přejděte na [stránku položek ke stažení pro Azure](https://azure.microsoft.com/downloads/) a klikněte na **VS 2013** nebo **VS 2015** v části **.NET**.
   * Stáhněte si nejnovější modul plug-in Azure Data Factory pro Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) nebo [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Modul plug-in můžete taky aktualizovat, a to pomocí tohoto postupu: V nabídce klikněte na **Nástroje** -> **Rozšíření a aktualizace** -> **Online** -> **Galerie sady Visual Studio** -> **Microsoft Azure Data Factory Tools for Visual Studio** (Nástroje Microsoft Azure Data Factory pro Visual Studio) -> **Aktualizovat**.

Nyní použijeme sadu Visual Studio k vytvoření objektu pro vytváření dat Azure.

### <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio
1. Spusťte **Visual Studio 2013** nebo **Visual Studio 2015**. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**. Mělo by se zobrazit dialogové okno **Nový projekt**.  
2. V dialogovém okně **Nový projekt** vyberte šablonu **DataFactory** a klikněte na **Empty Data Factory Project** (Prázdný projekt Data Factory).   

    ![Dialogové okno Nový projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Zadejte **název** projektu, **umístění** a název **řešení** a klikněte na **OK**.

    ![Průzkumník řešení](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby, **Azure Storage** a **HDInsight na vyžádání**. 

Propojená služba Azure Storage propojuje účet úložiště Azure k datové továrně tím, že poskytuje informace o připojení. Služba Data Factory používá připojovací řetězec z nastavení propojené služby pro připojení k úložišti Azure za běhu. V tomto úložišti jsou uložená vstupní a výstupní data pro kanál a soubor skriptu Hive, který používá aktivita Hive. 

S využitím propojené služby HDInsight na vyžádání se cluster HDInsight automaticky vytvoří za běhu, když jsou vstupní data připravená k zpracování. Až cluster dokončí zpracování, po určité zadané době nečinnosti se odstraní. 

> [!NOTE]
> Datovou továrnu vytvoříte zadáním názvu a nastavení v době publikování řešení Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **Propojené služby**, přejděte na **Přidat** a klikněte na **Nová položka**.      
2. V dialogovém okně **Přidat novou položku** vyberte v seznamu možnost **Azure Storage Linked Service** (Propojená služba Azure Storage) a klikněte na **Přidat**.
    ![Propojená služba Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Hodnoty `<accountname>` a `<accountkey>` nahraďte názvem účtu služby Azure Storage a jeho klíčem. Chcete-li zjistit, jak získat přístupový klíč k úložišti, přečtěte si informace o zobrazení, kopírování a opětovném vygenerování přístupových klíčů k úložišti v tématu [Správa účtu úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
    ![Propojená služba Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Uložte soubor **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Vytvoření propojené služby Azure HDInsight
1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Propojené služby**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. Vyberte **HDInsight On Demand Linked Service** a klikněte na **Přidat**.
3. Nahraďte kód **JSON** následujícím kódem JSON:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

    Vlastnost | Popis
    -------- | ----------- 
    ClusterSize | Určuje velikost clusteru HDInsight Hadoop.
    TimeToLive | Určuje dobu nečinnosti před odstraněním clusteru HDInsight.
    linkedServiceName | Určuje účet úložiště, který se používá k ukládání protokolů generovaných clusterem HDInsight Hadoop. 

    > [!IMPORTANT]
    > Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (linkedServiceName). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když se zpracuje určitý řez, pokud neexistuje aktivní cluster (timeToLive). Po dokončení zpracování se cluster automaticky odstraní.
    > 
    > Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).

    Další informace o vlastnostech JSON najdete v tématu [Propojené služby Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Uložte soubor **HDInsightOnDemandLinkedService1.json**.

### <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data pro zpracování Hive. Tyto datové sady odkazují na službu **AzureStorageLinkedService1**, kterou už jste v tomto kurzu vytvořili. Propojená služba odkazuje na účet služby Azure Storage a datové sady určují kontejner, složku a název souboru v úložišti, který obsahuje vstupní a výstupní data.   

#### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady
1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Tabulky**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V seznamu vyberte **Azure Blob**, změňte název souboru na **InputDataSet.json** a klikněte na **Přidat**.
3. Nahraďte kód **JSON** v editoru následujícím fragmentem kódu JSON:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Tento fragment kódu JSON definuje datovou sadu s názvem **AzureBlobInput**, která představuje vstupní data pro aktivitu Hive v kanálu. Určíte, že vstupní data se nacházejí v kontejneru objektů blob s názvem `adfgetstarted` ve složce `inputdata`.

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON:

    Vlastnost | Popis |
    -------- | ----------- |
    type |Vlastnost type je nastavená na hodnotu **AzureBlob**, protože se data nachází ve službě Azure Blob Storage.
    linkedServiceName | Odkazuje na službu AzureStorageLinkedService1, kterou jste vytvořili předtím.
    fileName |Tato vlastnost je nepovinná. Pokud ji vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto případě se zpracovává jenom soubor input.log.
    type | Soubory protokolů jsou v textovém formátu, takže použijeme hodnotu TextFormat. |
    columnDelimiter | Sloupce v souborech protokolu jsou oddělené znakem čárky (`,`).
    frequency/interval | Frekvence je nastavená na hodnotu Month (Měsíc) a interval je 1, takže vstupní řezy jsou dostupné jednou za měsíc.
    external | Pokud vstupní data pro tuto aktivitu nevygeneroval kanál, je tato vlastnost nastavená na hodnotu true. Tato vlastnost se určuje jenom pro vstupní datové sady. U vstupní datové sady první aktivity ji vždycky nastavte na hodnotu true.
4. Uložte soubor **InputDataset.json**.

#### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady
Teď vytvoříte výstupní datovou sadu, která bude představovat výstupní data ve službě Azure Blob Storage.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Tabulky**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V seznamu vyberte **Azure Blob**, změňte název souboru na **OutputDataSet.json** a klikněte na **Přidat**.
3. Nahraďte kód **JSON** v editoru následujícím kódem JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    Tento fragment kódu JSON definuje datovou sadu s názvem **AzureBlobOutput**, která představuje výstupní data vytvořená aktivitou Hive v kanálu. Určíte, že výstupní data vytvořená aktivitou Hive se nacházejí v kontejneru objektů blob s názvem `adfgetstarted` ve složce `partitioneddata`. 
    
    Oddíl **availability** určuje, že se výstupní sada vytváří jednou měsíčně. Výstupní datovou sadu řídí plán kanálu. Kanál se spouští každý měsíc mezi zadaným počátečním a koncovým časem. 

    Popisy těchto vlastností najdete v části **Vytvoření vstupní datové sady**. U výstupní datové sady nenajdete vlastnost external, protože datovou sadu vytváří kanál.
4. Uložte soubor **OutputDataset.json**.

### <a name="create-pipeline"></a>Vytvoření kanálu
Zatím jste vytvořili propojenou službu Azure Storage a vstupní a výstupní datovou sadu. Teď vytvoříte kanál s aktivitou **HDInsightHive**. **Vstup** aktivity Hive je nastavený na **AzureBlobInput** a **výstup aktivity** je nastavený na **AzureBlobOutput**. Řez vstupní datové sady je dostupný každý měsíc (frekvence: Měsíc, interval: 1). Výstupní datová sada se taky vytváří každý měsíc. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem myši na **Kanály**, přejděte na **Přidat** a klikněte na **Nová položka**.
2. V seznamu vyberte **Hive Transformation Pipeline** (Kanál transformace Hive) a klikněte na **Přidat**.
3. Nahraďte kód **JSON** tímto fragmentem kódu:

    > [!IMPORTANT]
    > Nahraďte `<storageaccountname>` názvem vašeho účtu úložiště.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Nahraďte `<storageaccountname>` názvem vašeho účtu úložiště.

    Fragment JSON definuje kanál, který se skládá z jediné aktivity (aktivita Hive). Tato aktivita spouští skript Hive pro zpracování vstupních dat v clusteru HDInsight na vyžádání s cílem vytvořit výstupní data. V oddílu aktivit JSON kanálu uvidíte jenom jednu aktivitu s nastaveným typem **HDInsightHive**. 

    Ve vlastnostech type, které jsou specifické pro aktivitu HDInsight Hive, zadáte, kterou propojenou službu Azure Storage má soubor skriptu Hive, cestu k tomuto souboru skriptu a parametry pro něj. 

    Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService) a ve složce `script` v kontejneru `adfgetstarted`.

    Oddíl `defines` určuje nastavení běhového prostředí, které se předá skriptu Hive jako konfigurační hodnoty Hive (např. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`).

    Vlastnosti kanálu **start** a **end** určují období aktivity kanálu. Nakonfigurovali jste vytváření datové sady jednou měsíčně. Proto taky kanál vytvoří jenom jeden řez (protože měsíc je pro počáteční a koncové datum stejný).

    V kódu JSON aktivity určujete, že má skript Hive běžet ve výpočetní službě určené vlastností **linkedServiceName**, tedy **HDInsightOnDemandLinkedService**.
4. Uložte soubor **HiveActivity1.json**.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Přidání souborů partitionweblogs.hql a input.log jako závislosti
1. Klikněte pravým tlačítkem myši na **Závislosti** v okně **Průzkumník řešení**, přejděte na **Přidat** a klikněte na **Existující položka**.  
2. Přejděte do složky **C:\ADFGettingStarted**, vyberte soubory **partitionweblogs.hql** a **input.log** a klikněte na **Přidat**. Tyto dva soubory jste vytvořili v rámci požadavků uvedených v článku [Přehled kurzu](data-factory-build-your-first-pipeline.md).

Až řešení v dalším kroku publikujete, soubor **partitionweblogs.hql** se nahraje do složky **script** v kontejneru objektů blob `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publikování/nasazení entit služby Data Factory
V tomto kroku publikujete entity služby Data Factory (propojené služby, datové sady a kanál) ve vašem projektu do služby Azure Data Factory. Během publikování zadáte název vaší datové továrny. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na požadovaný projekt a poté klikněte na **Publikovat**.
2. Pokud se zobrazí dialogové okno **Přihlásit se pomocí účtu Microsoft**, zadejte přihlašovací údaje k účtu s předplatným Azure a klikněte na **Přihlásit**.
3. Mělo by se zobrazit následující dialogové okno:

   ![Dialogové okno publikování](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Na stránce **Konfigurace datové továrny** proveďte následující kroky:

    ![Publikování – nová nastavení datové továrny](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Vyberte možnost **Create New Data Factory** (Vytvořit nový objekt pro vytváření dat).
   2. Zadejte jedinečný **název** objektu pro vytváření dat. Příklad: **DataFactoryUsingVS09152016**. Název musí být globálně jedinečný.
   3. V poli **Předplatné** vyberte správné předplatné. 
        > [!IMPORTANT]
        > Pokud nevidíte žádné předplatné, ujistěte se, že jste přihlášeni pomocí účtu, který je správce nebo spolusprávce předplatného.
   4. Vyberte **skupinu prostředků** pro objekt pro vytváření dat, který se má vytvořit.
   5. Vyberte **oblast** pro objekt pro vytváření dat.
   6. Kliknutím na **Další** přejděte na stránku **Publish Items** (Publikovat položky). (Pokud je tlačítko **Další** neaktivní, opusťte pole Název stisknutím klávesy **TAB**.)

    > [!IMPORTANT]
    > Pokud se při publikování zobrazí chyba typu **Název objektu pro vytváření dat FirstDataFactoryUsingVS není dostupný**, název změňte (třeba na vaše_jméno_FirstDataFactoryUsingVS). V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.   
1. Na stránce **Publish Items** (Publikovat položky) zkontrolujte, jestli jsou vybrané všechny entity služby Data Factory, a kliknutím na **Další** přejděte na stránku **Souhrn**.

    ![Stránka publikování položek](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Zkontrolujte souhrn a klikněte na **Další**. Spustí se proces nasazení a zobrazí se **Stav nasazení**.

    ![Stránka souhrnu](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Na stránce **Stav nasazení** byste měli vidět stav procesu nasazení. Až se nasazení dokončí, klikněte na Dokončit.

Všimněte si těchto důležitých bodů:

- Pokud se zobrazí chyba **Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory**, proveďte některý z těchto kroků a znovu zkuste publikovat:
    - V prostředí Azure PowerShell zaregistrujte zprostředkovatele služby Data Factory pomocí následujícího příkazu.
        ```PowerShell   
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Spuštěním následujícího příkazu si můžete ověřit, jestli je zprostředkovatel služby Data Factory zaregistrovaný.

        ```PowerShell
        Get-AzureRmResourceProvider
        ```
    - Přihlaste se na web [Azure Portal](https://portal.azure.com) pomocí předplatného Azure a přejděte do okna Objekt pro vytváření dat nebo na webu Azure Portal vytvořte objekt pro vytváření dat. Zprostředkovatel se při takovém postupu zaregistruje automaticky.
- Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název DNS, takže pak bude veřejně viditelný.
- Chcete-li vytvářet instance služby Data Factory, musíte být správce nebo spolusprávce předplatného Azure.

### <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku monitorujete kanál pomocí zobrazení diagramu datové továrny. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Monitorování kanálu pomocí Zobrazení diagramu
1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) a proveďte následující kroky:
   1. Klikněte na **Další služby** a poté na **Objekty pro vytváření dat**.
       
        ![Procházet -> Datové továrny](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Ze seznamu datových továren vyberte název své datové továrny (příklad: **DataFactoryUsingVS09152016**).
   
       ![Výběr objektu pro vytváření dat](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Na domovské stránce objektu pro vytváření dat klikněte na **Diagram**.

    ![Dlaždice Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. V zobrazení diagramu uvidíte přehled kanálů a datové sady použité v tomto kurzu.

    ![Zobrazení diagramu](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Pokud chcete zobrazit všechny aktivity v kanálu, klikněte na kanál v diagramu pravým tlačítkem myši a potom klikněte na Otevřít kanál.

    ![Nabídka Otevřít kanál](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Zkontrolujte, jestli je v kanálu vidět aktivita HDInsightHive.

    ![Zobrazení Otevřít kanál](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Pokud se chcete vrátit do předchozího zobrazení, klikněte v nabídce navigace s popisem cesty v horní části na **Objekt pro vytváření dat**.
6. V **zobrazení diagramu** dvakrát klikněte na datovou sadu **AzureBlobInput**. Zkontrolujte, jestli je řez ve stavu **Připraveno**. Než se řez zobrazí ve stavu Připraveno, může to několik minut trvat. Pokud se to do nějaké doby nestane, zkontrolujte, jestli je vstupní soubor (input.log) umístěný ve správném kontejneru (`adfgetstarted`) a složce (`inputdata`). Zkontrolujte taky, že vlastnost **external** vstupní datové sady je nastavená na hodnotu **true**. 

   ![Vstupní řez ve stavu Připraveno](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Kliknutím na tlačítko **X** zavřete okno **AzureBlobInput**.
8. V **zobrazení diagramu** dvakrát klikněte na datovou sadu **AzureBlobOutput**. Zobrazí se řez, který se právě zpracovává.

   ![Datová sada](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Po dokončení zpracování bude řez ve stavu **Připraveno**.

   > [!IMPORTANT]
   > Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá (přibližně 20 minut). Proto počítejte s tím, že zpracování řezu kanálem bude trvat **přibližně 30 minut**.  
   
    ![Datová sada](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Pokud je řez ve stavu **Připraveno**, zkontrolujte, jestli se ve složce `partitioneddata` v kontejneru `adfgetstarted` ve službě Blob Storage nachází výstupní data.  

    ![Výstupní data](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Kliknutím na řez otevřete okno **Datový řez** s podrobnostmi o řezu.

    ![Podrobnosti o datovém řezu](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Kliknutím na spuštění aktivity v **seznamu spuštění aktivit** zobrazíte podrobnosti o spuštění aktivity (v našem scénáři aktivity Hivu) v okně **Podrobnosti o spuštění aktivit**. 
  
    ![Podrobnosti o spuštění aktivit](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    V souborech protokolů můžete zobrazit provedený dotaz Hivu s informacemi o jeho stavu. Tyto protokoly jsou užitečné při řešení potíží.  

Pokyny k monitorování kanálu a datových sad, které jste vytvořili v tomto kurzu, pomocí webu Azure Portal najdete v článku [Monitorování datových sad a kanálu](data-factory-monitor-manage-pipelines.md).

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorování kanálu pomocí aplikace pro monitorování a správu
K monitorování kanálů můžete také použít aplikaci pro monitorování a správu. Podrobnosti o použití této aplikace najdete v tématu [Monitorování a správa kanálů služby Azure Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md).

1. Klikněte na dlaždici Monitorování a správa.

    ![Dlaždice Monitorování a správa](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Měla by se zobrazit aplikace pro monitorování a správu. Změňte hodnoty **Čas spuštění** a **Čas ukončení** tak, aby odpovídaly času spuštění (01.04.2016 00:00) a ukončení (02.04.2016 00:00) vašeho kanálu, a klikněte na **Použít**.

    ![Aplikace pro monitorování a správu](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Pokud chcete zobrazit podrobnosti o okně aktivity, vyberte ho v seznamu **Okna aktivit**.
    ![Podrobnosti o okně aktivity](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Po úspěšném zpracování řezu se vstupní soubor odstraní. Pokud tedy chcete spustit řez znovu nebo si znovu projít tento kurz, načtěte vstupní soubor (input.log) do složky `inputdata` v kontejneru `adfgetstarted`.

### <a name="additional-notes"></a>Další poznámky
- Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu Hivu HDInsight pro spuštění skriptu Hive, který umožňuje transformovat vstupní data. Všechny zdroje a jímky podporované aktivitou kopírování najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Seznam výpočetních služeb podporovaných službou Data Factory najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md).
- Propojené služby propojují úložiště dat nebo výpočetní služby s objektem pro vytváření dat Azure. Všechny zdroje a jímky podporované aktivitou kopírování najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Seznam výpočetních služeb podporovaných službou Data Factory a [aktivity transformace](data-factory-data-transformation-activities.md), které se v nich dají spustit, najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md).
- Podrobné informace o vlastnostech JSON použitých v definici propojené služby Azure Storage najdete v tématu [Přesun dat do a z Azure Blobu](data-factory-azure-blob-connector.md#azure-storage-linked-service).
- Místo clusteru HDInsight na vyžádání můžete použít také vlastní cluster HDInsight. Podrobnosti najdete v tématu [Propojené výpočetní služby](data-factory-compute-linked-services.md).
-  Pomocí výše uvedeného kódu JSON služba Data Factory vytvoří cluster HDInsight **se systémem Linux** za vás. Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
- Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (linkedServiceName). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když se zpracuje určitý řez, pokud neexistuje aktivní cluster (timeToLive). Po dokončení zpracování se cluster automaticky odstraní.
    
    Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů se řídí vzorem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).
- V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita nevytváří žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. 
- Tento kurz neukazuje, jak kopírovat data pomocí Azure Data Factory. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>Zobrazení objektů pro vytváření dat pomocí Průzkumníka serveru
1. V sadě **Visual Studio** klikněte v nabídce na **Zobrazit** a potom klikněte na **Průzkumník serveru**.
2. V okně Průzkumníka serveru rozbalte položku **Azure** a potom **Data Factory**. Pokud se zobrazí text **Přihlásit se k Visual Studiu**, zadejte **účet** přidružený k vašemu předplatnému Azure a klikněte na **Pokračovat**. Zadejte **heslo** a klikněte na **Přihlásit**. Visual Studio se pokusí získat informace o všech objektech pro vytváření dat Azure v rámci vašeho předplatného. Stav této operace se zobrazuje v okně **Data Factory Task List** (Seznam úkolů služby Data Factory).

    ![Průzkumník serveru](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Kliknutím na objekt pro vytváření dat pravým tlačítkem a výběrem **Export Data Factory to New Project** (Exportovat objekt pro vytváření dat do nového projektu) můžete vytvořit projekt sady Visual Studio založený na existujícím objektu pro vytváření dat.

    ![Export objektu pro vytváření dat](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

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
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili objekt pro zpracování dat Azure, který zpracovává data pomocí skriptu Hive v clusteru HDInsight Hadoop. Pomocí editoru služby Data Factory na webu Azure Portal jste provedli tyto kroky:  

1. Vytvořili jste **objekt pro vytváření dat** Azure.
2. Vytvořili jste dvě **propojené služby**:
   1. Propojená služba **Azure Storage** spojuje úložiště objektů blob Azure, které obsahuje vstupní/výstupní soubory, s objektem pro vytváření dat.
   2. Propojená služba na vyžádání **Azure HDInsight** spojuje cluster na vyžádání HDInsight Hadoop s objektem pro vytváření dat. Služba Azure Data Factory vytvoří cluster HDInsight Hadoop ve chvíli, kdy je potřeba zpracovat vstupní data a vygenerovat výstupní data.
3. Vytvořili jste dvě **datové sady**, které popisují vstupní a výstupní data aktivity HDInsight Hive v kanálu.
4. Vytvořili jste **kanál** s aktivitou **HDInsight Hive**.  

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili kanál s aktivitou transformace (aktivita HDInsight), která v clusteru HDInsight na vyžádání spouští skript Hive. Pokud chcete zjistit, jak pomocí aktivity kopírování zkopírovat data z Azure Blob do Azure SQL, projděte si článek [Kurz: Kopírování dat z Azure Blob do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md). 


## <a name="see-also"></a>Viz také
| Téma | Popis |
|:--- |:--- |
| [Kanály](data-factory-create-pipelines.md) |Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a porozumět tomu, jak se dají ve vaší situaci nebo firmě použít k sestavení pracovních postupů založených na datech. |
| [Datové sady](data-factory-create-datasets.md) |Tento článek vám pomůže pochopit datové sady ve službě Azure Data Factory. |
| [Aktivity transformace dat](data-factory-data-transformation-activities.md) |Tento článek obsahuje seznam aktivit transformace dat (třeba transformaci HDInsight Hive, kterou jste použili v tomto kurzu) podporovaných službou Azure Data Factory. |
| [Plánování a provádění](data-factory-scheduling-and-execution.md) |Tento článek vysvětluje aspekty plánování a provádění aplikačního modelu služby Azure Data Factory. |
| [Monitorování a správa kanálů pomocí monitorovací aplikace](data-factory-monitor-manage-app.md) |Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí aplikace pro monitorování a správu. |
