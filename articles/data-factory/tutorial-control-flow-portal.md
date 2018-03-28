---
title: Větvení v kanálu Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak řídit tok dat v Azure Data Factory prostřednictvím větvení a řetězení aktivit.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 00b377b8ed7454c64d146a2de1867eca8ab1fb67
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Větvení a řetězení aktivit v kanálech Data Factory
V tomto kurzu vytvoříte kanál služby Data Factory, který prezentuje některé funkce řízení toku. Tento kanál provádí jednoduché kopírování z kontejneru ve službě Azure Blob Storage do jiného kontejneru ve stejném účtu úložiště. Pokud aktivita kopírování proběhne úspěšně, kanál odešle podrobnosti o úspěšném kopírování (jako je například množství zapsaných dat) v e-mailu informujícím o úspěchu. Pokud aktivita kopírování selže, kanál odešle podrobnosti o neúspěšném kopírování (jako je například chybová zpráva) v e-mailu informujícím o selhání. V rámci tohoto kurzu se dozvíte, jak předávat parametry.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Základní schéma tohoto scénáře: ![Přehled](media/tutorial-control-flow-portal/overview.png)

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu obsahujícího aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Využití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá Azure Portal. K interakci s Azure Data Factory můžete použít další mechanismy – podívejte se v obsahu na téma Šablony Rychlý start.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako **zdrojové** úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat **jímky**. Pokud Azure SQL Database nemáte, přečtěte si článek věnovaný [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.

### <a name="create-blob-table"></a>Vytvoření tabulky objektů blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho na disk jako soubor **input.txt**.

    ```
    John,Doe
    Jane,Doe
    ```
2. Pomocí nástroje, jako je například [Průzkumník služby Azure Storage](http://storageexplorer.com/), proveďte následující kroky: 
    1. Vytvořte kontejner **adfv2branch**.
    2. V kontejneru **adfv2branch** vytvořte **vstupní** složku.
    3. Nahrajte do kontejneru soubor **input.txt**.

## <a name="create-email-workflow-endpoints"></a>Vytvoření koncových bodů pracovního postupu pro e-maily
K aktivaci odesílání e-mailů z kanálu použijete [Logic Apps](../logic-apps/logic-apps-overview.md) pro definování pracovního postupu. Podrobnosti o vytvoření pracovního postupu Logic Apps najdete v tématu věnovaném [postupu vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu 
Vytvořte pracovní postup aplikace logiky s názvem `CopySuccessEmail`. Jako trigger tohoto pracovního postupu definujte `When an HTTP request is received` a potom přidejte akci `Office 365 Outlook – Send an email`.

![Pracovní postup pro e-maily s informací o úspěchu](media/tutorial-control-flow-portal/success-email-workflow.png)

Pro trigger požadavku zadejte do `Request Body JSON Schema` následující JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Požadavek v návrháři aplikace logiky by měl vypadat podobně jako na následujícím obrázku: 

![Návrhář aplikace logiky – požadavek](media/tutorial-control-flow-portal/logic-app-designer-request.png)

Pro akci **Odeslat e-mail** upravte, jak chcete e-mail naformátovat, a využijte přitom vlastnosti předané ve schématu JSON těla požadavku. Zde naleznete příklad:

![Návrhář aplikace logiky – akce odeslání e-mailu](media/tutorial-control-flow-portal/send-email-action-2.png)

Uložte pracovní postup. Poznamenejte si adresu URL žádosti HTTP Post pro pracovní postup pro e-maily s informací o neúspěchu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu 
Pomocí stejného postupu vytvořte další pracovní postup Logic Apps s názvem **CopyFailEmail**. Schéma `Request Body JSON schema` v triggeru požadavku je stejné. Změňte formát e-mailu, například `Subject`, tak, aby to odpovídalo neúspěchu. Zde naleznete příklad:

![Návrhář aplikace logiky – pracovní postup pro e-maily s informací o neúspěchu](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Uložte pracovní postup. Poznamenejte si adresu URL žádosti HTTP Post pro pracovní postup pro e-maily s informací o neúspěchu:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Teď byste měli mít dvě adresy URL pracovního postupu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-control-flow-portal/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-control-flow-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název objektu pro vytváření dat (třeba na váš_název_ADFTutorialDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
        Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.      
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Azure Data Factory.


## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s jednou aktivitou kopírování a dvěma aktivitami webu. K vytvoření kanálu použijete následující funkce:

- Parametry pro kanál, ke kterým přistupují datové sady. 
- Webová aktivita, která vyvolá pracovní postupy aplikace logiky pro odeslání e-mailů informujících o úspěchu nebo selhání. 
- Připojení jedné aktivity k jiné aktivitě (v případ úspěchu a selhání)
- Použití výstupu aktivity jako vstupu pro další aktivitu

1. Na stránce **Začínáme** uživatelského rozhraní služby Data Factory klikněte na dlaždici **Vytvořit kanál**.  

   ![Stránka Začínáme](./media/tutorial-control-flow-portal/get-started-page.png) 
3. V okně Vlastnosti kanálu přepněte na kartu **Parametry** a pomocí tlačítka **Nový** přidejte následující tři parametry typu řetězec: sourceBlobContainer, sinkBlobContainer a receiver. 

    - **sourceBlobContainer** – parametr kanálu využívaný zdrojovou datovou sadou objektů blob.
    - **sinkBlobContainer** – parametr kanálu využívaný datovou sadou objektů blob jímky.
    - **receiver** – tento parametr používají dvě aktivity webu v kanálu, které odesílají e-maily s oznámením o úspěchu nebo selhání příjemci, jehož e-mailová adresa je zadána tímto parametrem.

   ![Nabídka Nový kanál](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. Na panelu nástrojů **Aktivity** rozbalte **Tok dat** a přetáhněte aktivitu **Kopírování** na plochu návrháře kanálu. 

   ![Přetažení aktivity kopírování](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. V okně **Vlastnosti** aktivity **Kopírování** přepněte v dolní části na kartu **Zdroj** a klikněte na **+ Nový**. V tomto kroku vytvoříte zdrojovou datovou sadu pro aktivitu kopírování. 

   ![Zdrojová datová sada](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. V okně **Nová datová sada** vyberte **Azure Blob Storage** a klikněte na **Dokončit**. 

   ![Výběr služby Azure Blob Storage](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. Zobrazí se nová **karta** s názvem **AzureBlob1**. Změňte název datové sady na **SourceBlobDataset**.

   ![Obecná nastavení datové sady](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. V okně **Vlastnosti** přepněte na kartu **Připojení** a v části **Propojená služba** klikněte na Nová. V tomto kroku vytvoříte propojenou službu, která propojí váš účet služby Azure Storage s datovou továrnou. 
    
   ![Připojení k datové sadě – nová propojená služba](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **Název** zadejte **AzureStorageLinkedService**.
    2. Jako **Název účtu úložiště** vyberte svůj účet úložiště Azure.
    3. Klikněte na **Uložit**.

   ![Nová propojená služba Azure Storage](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Jako složku zadejte `@pipeline().parameters.sourceBlobContainer` a jako název souboru zadejte `emp.txt`. Parametr kanálu sourceBlobContainer použijete k nastavení cesty ke složce pro datovou sadu. 

    ![Nastavení zdrojové datové sady](./media/tutorial-control-flow-portal/source-dataset-settings.png)
13. Přepněte na kartu s **kanálu** (nebo) klikněte na kanál ve stromovém zobrazení. Ověřte, že jako **Zdrojová datová sada** je vybraná datová sada **SourceBlobDataset**. 

   ![Zdrojová datová sada](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)
13. V okně Vlastnosti přepněte na kartu **Jímka** a klikněte na **+ Nová** v části **Datová sada jímky**. V tomto kroku pro aktivitu kopírování vytvoříte datovou sadu jímky podobným způsobem, jako jste vytvořili zdrojovou datovou sadu. 

    ![Tlačítko Nová datová sada jímky](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. V okně **Nová datová sada** vyberte **Azure Blob Storage** a klikněte na **Dokončit**. 
15. Na stránce **Obecné** nastavení datové sady jako **Název** zadejte **SinkBlobDataset**.
16. Přepněte na kartu **Připojení** a proveďte následující kroky: 

    1. Jako **Propojená služba** vyberte **AzureStorageLinkedService**.
    2. Jako složku zadejte `@pipeline().parameters.sinkBlobContainer`.
    1. Jako název souboru zadejte `@CONCAT(pipeline().RunId, '.txt')`. Tento výraz jako název souboru používá ID aktuálního spuštění kanálu. Seznam podporovaných systémových proměnných a výrazů najdete v tématech [Systémové proměnné](control-flow-system-variables.md) a [Jazyk výrazů](control-flow-expression-language-functions.md).

        ![Nastavení datové sady jímky](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. V horní části přepněte na kartu **kanálu**. Na panelu nástrojů **Aktivity** rozbalte **Obecné** a přetáhněte aktivitu **Web** na plochu návrháře kanálu. Nastavte název aktivity na **SendSuccessEmailActivity**. Aktivita webu umožňuje volání libovolného koncového bodu REST. Další informace o této aktivitě najdete v tématu věnovaném [aktivitě webu](control-flow-web-activity.md). Tento kanál používá aktivitu webu pro volání pracovního postupu pro e-maily Logic Apps. 

   ![Přetažení první aktivity webu](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Z karty **Obecné** přepněte na kartu **Nastavení** a proveďte následující kroky: 
    1. Jako **Adresa URL** zadejte adresu URL pracovního postupu aplikace logiky, který odešle e-mail informující o úspěchu.  
    2. Jako **Metoda** vyberte **POST**. 
    3. V části **Záhlaví** klikněte na odkaz **+ Přidat záhlaví**. 
    4. Přidejte záhlaví **Content-Type** a nastavte ho na hodnotu **application/json**. 
    5. Jako **Text** zadejte následující kód JSON. 

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Text zprávy obsahuje následující vlastnosti:

        - Message – Předání hodnoty `@{activity('Copy1').output.dataWritten`. Má přístup k vlastnosti předchozí aktivity kopírování a předává hodnotu dataWritten. V případě neúspěchu předejte výstup chyby místo `@{activity('CopyBlobtoBlob').error.message`.
        - DataFactoryName – Předání hodnoty `@{pipeline().DataFactory}`. Toto je systémová proměnná, která umožňuje přístup k názvu odpovídající datové továrny. Seznam systémových proměnných najdete v článku [Systémové proměnné](control-flow-system-variables.md).
        - PipelineName – Předání hodnoty `@{pipeline().Pipeline}`. Toto je také systémová proměnná, která umožňuje přístup k názvu odpovídajícího kanálu. 
        - Receiver – Předání hodnoty @pipeline().parameters.receiver. Má přístup k parametrům kanálu.
    
        ![Nastavení první aktivity webu](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Připojte aktivitu **Kopírování** k aktivitě **Web** přetažením zeleného tlačítka vedle aktivity kopírování na aktivitu webu. 

    ![Připojení aktivity kopírování k první aktivitě webu](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Přetáhněte z panelu nástrojů Aktivity na plochu návrháře kanálu další aktivitu **Web** a nastavte její **název** na **SendFailureEmailActivity**.

    ![Název druhé aktivity webu](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Přepněte na kartu **Nastavení** a proveďte následující kroky:

    1. Jako **Adresa URL** zadejte adresu URL pracovního postupu aplikace logiky, který odešle e-mail informující o selhání.  
    2. Jako **Metoda** vyberte **POST**. 
    3. V části **Záhlaví** klikněte na odkaz **+ Přidat záhlaví**. 
    4. Přidejte záhlaví **Content-Type** a nastavte ho na hodnotu **application/json**. 
    5. Jako **Text** zadejte následující kód JSON. 

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Nastavení druhé aktivity webu](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. V návrháři kanálu vyberte aktivitu **Kopírování**, klikněte na tlačítko **+->** a vyberte **Chyba**.  

    ![Nastavení druhé aktivity webu](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Přetáhněte **červené** tlačítko vedle aktivity kopírování na druhou aktivitu webu **SendFailureEmailActivity**. Aktivity můžete přesunout tak, aby kanál vypadal podobně jako na následujícím obrázku: 

    ![Úplný kanál se všemi aktivitami](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Pokud chcete kanál ověřit, klikněte na tlačítko **Ověřit** na panelu nástrojů. Zavřete okno **Výstup ověření kanálu** kliknutím na tlačítko **>>**.

    ![Ověření kanálu](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Pokud chcete publikovat entity (datové sady, kanály atd.) do služby Data Factory, vyberte **Publikovat vše**. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**.

    ![Publikování](./media/tutorial-control-flow-portal/publish-button.png)
 
## <a name="trigger-a-pipeline-run-that-succeeds"></a>Aktivace spuštění kanálu, které proběhne úspěšně
1. Pokud chcete **aktivovat** spuštění kanálu, klikněte na **Aktivační událost** na panelu nástrojů a pak klikněte na **Aktivovat**. 

    ![Aktivace spuštění kanálu](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. V okně **Spuštění kanálu** proveďte následující kroky: 

    1. Jako hodnotu parametru **sourceBlobContainer** zadejte **adftutorial/adfv2branch/input**. 
    2. Jako hodnotu parametru **sinkBlobContainer** zadejte **adftutorial/adfv2branch/output**. 
    3. Zadejte **e-mailovou adresu** **příjemce**. 
    4. Klikněte na **Dokončit**.

        ![Parametry spuštění kanálu](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Monitorování úspěšného spuštění kanálu

1. Pokud chcete monitorovat spuštění kanálu, přepněte na kartu **Monitorování** na levé straně. Zobrazí se spuštění kanálu, které jste ručně aktivovali. Pomocí tlačítka **Aktualizovat** seznam aktualizujte. 
    
    ![Úspěšné spuštění kanálu](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Pokud chcete **zobrazit spuštění aktivit** související se spuštěním kanálu, klikněte na první odkaz ve sloupci **Akce**. Zpět na předchozí zobrazení můžete přepnout kliknutím na **Kanály** v horní části. Pomocí tlačítka **Aktualizovat** seznam aktualizujte. 

    ![Spuštění aktivit](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Aktivace spuštění kanálu, které selže
1. Vlevo přepněte na kartu **Upravit**. 
2. Pokud chcete **aktivovat** spuštění kanálu, klikněte na **Aktivační událost** na panelu nástrojů a pak klikněte na **Aktivovat**. 
3. V okně **Spuštění kanálu** proveďte následující kroky: 

    1. Jako hodnotu parametru **sourceBlobContainer** zadejte **adftutorial/dummy/input**. Ujistěte se, že v kontejneru adftutorial neexistuje složka dummy. 
    2. Jako hodnotu parametru **sinkBlobContainer** zadejte **adftutorial/dummy/output**. 
    3. Zadejte **e-mailovou adresu** **příjemce**. 
    4. Klikněte na **Dokončit**.

## <a name="monitor-the-failed-pipeline-run"></a>Monitorování neúspěšného spuštění kanálu

1. Pokud chcete monitorovat spuštění kanálu, přepněte na kartu **Monitorování** na levé straně. Zobrazí se spuštění kanálu, které jste ručně aktivovali. Pomocí tlačítka **Aktualizovat** seznam aktualizujte. 
    
    ![Neúspěšné spuštění kanálu](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Kliknutím na odkaz **Chyba** pro spuštění kanálu zobrazíte podrobnosti o chybě. 

    ![Chyba kanálu](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Pokud chcete **zobrazit spuštění aktivit** související se spuštěním kanálu, klikněte na první odkaz ve sloupci **Akce**. Pomocí tlačítka **Aktualizovat** seznam aktualizujte. Všimněte si, že aktivita kopírování v kanálu selhala. Aktivita webu úspěšně odeslala zadanému příjemci e-mail informující o chybě. 

    ![Spuštění aktivit](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Kliknutím na odkaz **Chyba** ve sloupci **Akce** zobrazte podrobnosti o chybě. 

    ![Chyba spuštění aktivit](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Využití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Teď můžete přejít k části Koncepty, která obsahuje další informace o Azure Data Factory.
> [!div class="nextstepaction"]
>[Kanály a aktivity](concepts-pipelines-activities.md)
