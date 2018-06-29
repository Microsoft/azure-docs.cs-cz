---
title: Přesun dat – Brána pro správu dat | Microsoft Docs
description: Nastaví bránu dat pro přesun dat mezi místními a cloudem. Přesunutí dat pomocí brány pro správu dat v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1d19cf6ecc9f2bedb6ceaf6312b247670d965b84
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048545"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Přesun dat mezi místní zdroje a cloudu s Brána pro správu dat
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [kopírovat data mezi místními a cloudu pomocí služby Data Factory](../tutorial-hybrid-copy-powershell.md).

Tento článek obsahuje přehled dat integrace mezi místním úložištím dat a cloudové úložiště dat pomocí služby Data Factory. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek a další data factory základní koncepty články: [datové sady](data-factory-create-datasets.md) a [kanály](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brána správy dat
Brána pro správu dat je nutné nainstalovat na místním počítači povolit přesunutí dat z úložiště služby místní data. Brána můžete tak dlouho, dokud brána se může připojit k úložišti dat nainstalovaná na stejném počítači jako úložiště dat nebo na jiný počítač.

> [!IMPORTANT]
> V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) podrobnosti o Brána pro správu dat najdete v článku. 

Následující postup ukazuje, jak vytvořit objekt pro vytváření dat s kanál, který přesouvá data z místního **systému SQL Server** databáze do Azure blob storage. Jako součást návodu nainstalujete a nakonfigurujete ve svém počítači Bránu správy dat.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Návod: kopírování místně dat do cloudu
V tomto názorném postupu proveďte následující kroky: 

1. Vytvoření datové továrny
2. Brána pro správu dat vytvořte. 
3. Vytvoření propojených služeb pro úložiště dat zdroj a jímka.
4. Vytvořte datové sady, které představují vstupní a výstupní data.
5. Vytvoření kanálu s aktivitou kopírování pro přesun dat

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz
Než začnete Tento názorný postup, musíte mít následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Najdete v článku [bezplatné zkušební verze](http://azure.microsoft.com/pricing/free-trial/) článku.
* **Účet úložiště Azure**. Použít jako úložiště objektů blob **cílové/jímka** úložiště dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako **zdrojové** úložiště dat. 

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku použijete portál Azure k vytvoření instance objektu pro vytváření dat Azure s názvem **ADFTutorialOnPremDF**.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek**, klikněte na tlačítko **Intelligence + analýzy**a klikněte na tlačítko **Data Factory**.

   ![Nový -> Objekt pro vytváření dat](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. V **nový objekt pro vytváření dat** zadejte **ADFTutorialOnPremDF** pro název.

    ![Přidat na úvodní panel](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "ADFTutorialOnPremDF" není k dispozici**, změňte název objektu pro vytváření dat (například yournameADFTutorialOnPremDF) a zkuste to znovu. Tento název používejte místo ADFTutorialOnPremDF při provádění zbývající kroky v tomto kurzu.
   >
   > Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název **DNS** a tak se stát veřejně viditelným.
   >
   >
4. Vyberte **předplatné Azure**, ve kterém chcete objekt pro vytváření dat vytvořit.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou. Pro tento kurz, vytvořte skupinu prostředků s názvem: **ADFTutorialResourceGroup**.
6. Klikněte na tlačítko **vytvořit** na **nový objekt pro vytváření dat** stránky.

   > [!IMPORTANT]
   > Chcete-li vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného a skupiny prostředků.
   >
   >
7. Po dokončení vytvoření se zobrazí **Data Factory** stránky, jak je znázorněno na následujícím obrázku:

   ![Data Factory domovské stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Vytvoření brány
1. V **objekt pro vytváření dat** klikněte na **vytvořit a nasadit** dlaždici spustíte **Editor** služby data Factory.

    ![Dlaždice Vytvořit a nasadit](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. V editoru služby Data Factory, klikněte na tlačítko **... Další** na panelu nástrojů a pak klikněte na tlačítko **novou bránu dat**. Alternativně můžete můžete kliknout pravým tlačítkem **brány Data Gateways** v zobrazení stromu a klikněte na **novou bránu dat**.

   ![Nová brána dat na panelu nástrojů](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. V **vytvořit** zadejte **adftutorialgateway** pro **název**a klikněte na tlačítko **OK**.     

    ![Vytvoření brány stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > V tomto návodu vytvoříte logické brány se jenom jeden uzel (místní počítač Windows). Brána pro správu dat můžete škálovat tím, že přidružíte více místní počítače s bránou. Je možné škálovat až zvýšením počtu úloh přesun dat, které můžou běžet souběžně na uzlu. Tato funkce je také k dispozici pro logické brány se jeden uzel. V tématu [škálování Brána pro správu dat v Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) článku.  
4. V **konfigurace** klikněte na tlačítko **nainstalovat přímo na tomto počítači**. Tato akce stáhne instalační balíček brány, nainstaluje, konfiguruje a zaregistruje bránu v počítači.  

   > [!NOTE]
   > Použijte Internet Explorer nebo Microsoft ClickOnce kompatibilní webového prohlížeče.
   >
   > Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
   >
   > Totéž proveďte pro Firefox (instalace doplňku). Klikněte na tlačítko **otevřít nabídku** tlačítka na panelu nástrojů (**tři vodorovné čáry** v pravém horním rohu), klikněte na tlačítko **doplňky**, hledání with – klíčové slovo "ClickOnce", vyberte jednu z ClickOnce rozšíření a nainstalujte ji.    
   >
   >

    ![Brána - konfigurace stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Tento způsob je nejjednodušší (jedním kliknutím) stáhnout, nainstalovat, nakonfigurovat a zaregistrovat bránu v jednom kroku jeden. Můžete zobrazit **Správce konfigurace brány pro správu dat společnosti Microsoft** je aplikace nainstalována na váš počítač. Můžete také najít spustitelný soubor **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data správy Gateway\2.0\Shared**.

    Můžete také stáhnout a nainstalovat bránu ručně pomocí odkazů na této stránce a zaregistrovat ji pomocí klíče ukazuje **nový klíč** textové pole.

    V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) článku podrobné informace o bráně.

   > [!NOTE]
   > Musíte být správce v místním počítači k instalaci a konfiguraci brány pro správu dat úspěšně. Můžete přidat další uživatele na **Data Management Gateway Users** místní skupiny Windows. Členy této skupiny můžete použít nástroj Správce konfigurace brány pro správu dat. Pokud chcete konfigurovat bránu.
   >
   >
5. Počkejte několik minut, nebo počkejte, až se zobrazí následující zprávu oznámení:

    ![Úspěšná instalace brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Spusťte **Správce konfigurace brány pro správu dat** aplikace ve vašem počítači. V **vyhledávání** zadejte **Brána pro správu dat** pro přístup k tento nástroj. Můžete také najít spustitelný soubor **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data správy Gateway\2.0\Shared**

    ![Správce konfigurace brány](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Zkontrolujte, jestli `adftutorialgateway is connected to the cloud service` zprávy. Na stavovém řádku zobrazuje dolní **připojené ke cloudové službě** spolu s **zelená značka zaškrtnutí**.

    Na **Domů** kartě, můžete také provést následující operace:

   * **Zaregistrovat** bránu pomocí klíče z portálu Azure pomocí na tlačítko zaregistrovat.
   * **Zastavit** Data Management Gateway hostitele Service spuštěná na počítači brány.
   * **Naplánovat aktualizace** nainstalovat v určitém čase dne.
   * Zobrazení při brány **poslední aktualizace**.
   * Zadejte čas, kdy lze nainstalovat aktualizaci brány.
8. Přepněte na kartu **Nastavení**. Certifikát uvedený v **certifikát** části se používá k šifrování a dešifrování přihlašovací údaje pro místní datové úložiště, které jste zadali na portálu. (volitelné) Klikněte na tlačítko **změnu** místo toho použít vlastní certifikát. Ve výchozím nastavení brána používá certifikát, který je automaticky generuje služba Data Factory.

    ![Konfigurace certifikátu brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Můžete také provést následující akce na **nastavení** karty:

   * Zobrazit nebo exportovat certifikát používaný bránou.
   * Změňte koncový bod HTTPS používá bránu.    
   * Nastavení proxy serveru HTTP pro použití brány.     
9. (volitelné) Přepnout **diagnostiky** zkontrolujte **zapnout podrobné protokolování** možnost, pokud chcete povolit podrobné protokolování, který vám pomůže vyřešit jakékoliv problémy s bránou. Informace o protokolování naleznete v **Prohlížeč událostí** pod **protokoly aplikací a služeb** -> **Brána pro správu dat** uzlu.

    ![Karta Diagnostika](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Můžete také provádět následující akce v **diagnostiky** karty:

   * Použití **Test připojení** části místními zdroji dat pomocí brány.
   * Klikněte na tlačítko **zobrazit protokoly** zobrazíte protokol Brána pro správu dat v okně prohlížeče událostí.
   * Klikněte na tlačítko **odeslat protokoly** pro nahrání souboru zip s protokoly posledních sedmi dnů společnosti Microsoft, které usnadňují řešení potíží s vaší problémy.
10. Na **diagnostiky** ve **Test připojení** vyberte **SqlServer** pro typ úložiště dat, zadejte název databázového serveru, název databáze, Zadejte typ ověřování, zadejte uživatelské jméno a heslo a klikněte na tlačítko **testování** a otestovat, jestli brána se může připojit k databázi.
11. Přepněte do webového prohlížeče a v **portál Azure**, klikněte na tlačítko **OK** na **konfigurace** stránky a pak na **novou bránu dat** stránky.
12. Měli byste vidět **adftutorialgateway** pod **brány Data Gateways** ve stromovém zobrazení na levé straně.  Pokud kliknete na tlačítko ji, měli byste vidět přidružené JSON.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby: **AzureStorageLinkedService** a **SqlServerLinkedService**. **SqlServerLinkedService** odkazy místní databázi systému SQL Server a **AzureStorageLinkedService** propojená služba odkazuje na objekt pro vytváření dat úložišti objektů blob v Azure. Vytvoření kanálu dále v tomto návodu, který kopíruje data z místní databáze SQL serveru do úložiště objektů blob v Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Přidat propojené služby do místní databáze systému SQL Server
1. V **editoru služby Data Factory**, klikněte na tlačítko **nové datové úložiště** na panelu nástrojů a vyberte **systému SQL Server**.

   ![Nová SQL serveru propojená služba](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. V **JSON editor** na pravé straně, proveďte následující kroky:

   1. Pro **gatewayName**, zadejte **adftutorialgateway**.    
   2. V **connectionString**, proveďte následující kroky:    

      1. Pro **servername**, zadejte název serveru, který hostuje databázi systému SQL Server.
      2. Pro **databasename**, zadejte název databáze.
      3. Klikněte na tlačítko **šifrovat** tlačítka na panelu nástrojů. Zobrazí aplikace Správce přihlašovacích údajů.

         ![Pověření správce aplikace](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. V **nastavení pověření** dialogové okno, zadejte typ ověřování, uživatelské jméno a heslo a klikněte na tlačítko **OK**. Pokud je připojení úspěšné, zašifrované přihlašovací údaje jsou uložené v kódu JSON a zavření dialogového okna.
      5. Zavřete kartu prázdný prohlížeče, který spustí dialogové okno, pokud není uzavřený automaticky a vrátit na kartu pomocí portálu Azure.

         Na počítači s bránou tyto přihlašovací údaje jsou **šifrované** pomocí certifikátu, který vlastní službu Data Factory. Pokud chcete používat certifikát, který je přidružen Brána pro správu dat místo toho najdete v tématu [nastavit pověření bezpečně](#set-credentials-and-security).    
   3. Klikněte na tlačítko **nasadit** na panelu příkazů nasazení systému SQL Server propojené služby. Měli byste vidět propojené služby ve stromovém zobrazení.

      ![Služba SQL Server propojené ve stromovém zobrazení](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Přidat propojené služby pro účet úložiště Azure
1. V **editoru služby Data Factory**, klikněte na tlačítko **nové datové úložiště** na panelu příkazů a klikněte na **úložiště Azure**.
2. Zadejte název účtu úložiště Azure pro **název účtu**.
3. Zadejte klíč účtu úložiště Azure pro **klíč účtu**.
4. Klikněte na tlačítko **nasadit** k nasazení **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady, které představují vstupní a výstupní data pro operaci kopírování (místní databáze SQL Serveru => úložiště objektů blob v Azure). Před vytvořením datových sad proveďte následující kroky (podrobný postup je uvedený pod seznamem):

* V databázi SQL Serveru, kterou jste do datové továrny přidali jako propojenou službu, vytvořte tabulku **emp** a vložte do ní několik ukázkových záznamů.
* V účtu služby Azure Blob Storage, který jste do datové továrny přidali jako propojenou službu, vytvořte kontejner objektů blob **adftutorial**.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Příprava na místním serveru SQL pro tento kurz
1. V databázi, kterou jste zadali pro propojenou službu místního SQL Serveru (**SqlServerLinkedService**), pomocí následujícího skriptu SQL vytvořte tabulku **emp**.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Vložte do tabulky několik ukázkových hodnot:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Vytvoření vstupní datové sady

1. V **Data Factory Editoru** klikněte na **... Další**, klikněte na tlačítko **nová datová sada** na panelu příkazů a klikněte na **tabulky serveru SQL Server**.
2. Nahraďte kód JSON v pravém podokně následujícím textem:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
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
   Je třeba počítat s následujícím:

   * **typ** je nastaven na **SqlServerTable**.
   * **Název tabulky** je nastaven na **emp**.
   * **linkedServiceName** je nastaven na **SqlServerLinkedService** (jste vytvořili tuto propojenou službu dříve v tomto návodu.).
   * Pro vstupní datovou sadu, která není generované jiný kanál v Azure Data Factory, je potřeba nastavit **externí** k **true**. Ji označuje, že se vstupní data vytváří externí ke službě Azure Data Factory. Volitelně můžete zadat všechny zásady externích dat pomocí **externalData** element v **zásad** části.    

   V tématu [přesun dat do nebo ze systému SQL Server](data-factory-sqlserver-connector.md) podrobnosti o vlastnostech formátu JSON.
3. Klikněte na tlačítko **nasadit** na panelu příkazů na datovou sadu.  

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady

1. V **editoru služby Data Factory**, klikněte na tlačítko **nová datová sada** na panelu příkazů a klikněte na **úložiště objektů Azure Blob**.
2. Nahraďte kód JSON v pravém podokně následujícím textem:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Je třeba počítat s následujícím:

   * **typ** je nastaven na **AzureBlob**.
   * **linkedServiceName** je nastaven na **AzureStorageLinkedService** (tuto propojenou službu měl vytvořili v kroku 2).
   * **folderPath** je nastaven na **adftutorial/outfromonpremdf** kde outfromonpremdf je složka, v kontejneru adftutorial. Vytvořte **adftutorial** kontejner, pokud ještě neexistuje.
   * Vlastnost **availability** je nastavená na **hourly** (**frequency** je nastavená na **hour** a **interval** je nastavená na **1**).  Služba Data Factory generuje řez výstupních dat každou hodinu do **emp** tabulky v databázi SQL Azure.

   Pokud nezadáte **fileName** pro **výstupní tabulku**, generované soubory v **folderPath** jsou pojmenované v následujícím formátu: Data.<Guid>. TXT (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Chcete-li nastavit **folderPath** a **fileName** dynamicky podle **SliceStart** čas, použijte vlastnost partitionedBy. V následujícím příkladu folderPath používá rok, měsíc a den z vlastnosti SliceStart (čas zahájení zpracování řezu) a fileName používá hodinu z vlastnosti SliceStart. Pokud například začne být řez vytvářen v době 2014-10-20T08:00:00, vlastnost folderName je nastavená na wikidatagateway/wikisampledataout/2014/10/20 a vlastnost fileName je nastavená na 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    V tématu [přesun dat do/z Azure Blob Storage](data-factory-azure-blob-connector.md) podrobnosti o vlastnostech formátu JSON.
3. Klikněte na tlačítko **nasadit** na panelu příkazů na datovou sadu. Zkontrolujte, jestli obou datové sady ve stromovém zobrazení.  

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte **kanálu** s jedním **aktivity kopírování** používající **EmpOnPremSQLTable** jako vstup a **OutputBlobTable** jako výstup.

1. V editoru služby Data Factory, klikněte na tlačítko **... Další** a poté na **Nový kanál**.
2. Nahraďte kód JSON v pravém podokně následujícím textem:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
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
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Nahraďte hodnotu vlastnosti **start** aktuálním dnem a **end** následujícím dnem.
   >
   >

   Je třeba počítat s následujícím:

   * V části aktivit je jenom aktivita jejichž **typ** je nastaven na **kopie**.
   * **Vstup** aktivity je nastavený na **EmpOnPremSQLTable** a **výstup** aktivity je nastavený na **OutputBlobTable**.
   * V **rámci typeProperties** části **SqlSource** je zadán jako **typ zdroje** a ** BlobSink ** je zadán jako **typ jímky**.
   * Dotaz SQL `select * from emp` pro je zadána **sqlReaderQuery** vlastnost **SqlSource**.

   Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme.

   Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Chcete-li kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9/9/9999**.

   Definování doby trvání ve které se zpracovávají datové řezy podle **dostupnosti** vlastnosti, které byly definovány pro každé datové sady Azure Data Factory.

   V příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.        
3. Klikněte na tlačítko **nasadit** na panelu příkazů na datovou sadu (tabulka je obdélníková datová sada). Potvrďte, že kanálu se zobrazí v zobrazení stromu pod **kanály** uzlu.  
4. Nyní, klikněte na tlačítko **X** zavřete stránku a vrátit k tomu **Data Factory** stránky pro **ADFTutorialOnPremDF**.

**Blahopřejeme!** Úspěšně jste vytvořili objekt pro vytváření dat Azure, propojené služby, datové sady a kanál a naplánovali jste kanál.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Zobrazit objektu pro vytváření dat v zobrazení diagramu
1. V **portál Azure**, klikněte na tlačítko **Diagram** na domovské stránce dlaždici **ADFTutorialOnPremDF** služby data factory. :

    ![Diagram odkaz](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Zobrazený diagram by měl vypadat přibližně jako na následujícím obrázku:

    ![Zobrazení diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Můžete přiblížení, oddálení, zvětšení na 100 % zvětšení přizpůsobit, automatické umísťování kanálů a datové sady a zobrazení informací o rodokmenu (zvýrazní nadřazené a podřízené položky vybraných položek).  Dvakrát klikněte na objekt (vstupní a výstupní datové sady nebo kanál) můžete zobrazit vlastnosti.

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku budete pomocí webu Azure Portal monitorovat, co se děje v objektu pro vytváření dat Azure. K monitorování datových sad a kanálů můžete také použít rutiny PowerShellu. Podrobnosti o monitorování najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).

1. V diagramu, klikněte dvakrát na **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable řezy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Všimněte si, zda jsou všechny datové řezy až v **připraven** stavu, protože platnost kanálu (čas zahájení pro čas ukončení) je v minulosti. Je také protože vložení dat v databázi systému SQL Server a je existuje vždy. Potvrďte, že žádné řezy nezobrazují v **problémové řezy** v dolní části. Chcete-li zobrazit všechny řezy, klikněte na tlačítko **najdete v části více** v dolní části seznamu řezy.
3. Nyní v **datové sady** klikněte na tlačítko **OutputBlobTable**.

    ![OputputBlobTable řezy](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klikněte na libovolný datový řez ze seznamu a měli byste vidět **datový řez** stránky. Uvidíte, že aktivita spuštěna řez. Zobrazí jenom jedna aktivita obvykle spustit.  

    ![Okno datový řez](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Není-li řez ve stavu **Připraveno**, zobrazí se v seznamu **Upstreamové datové řezy, které nejsou připraveny** upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu.
5. Klikněte **aktivity při spuštění** ze seznamu dole zobrazíte **podrobnosti o spuštění aktivit**.

   ![Stránka Podrobnosti o spuštění aktivity](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobrazí se informace, jako je propustnost, doba trvání a používá k přenosu dat brány.
6. Klikněte na tlačítko **X** dokud zavřete všechny stránky
7. Návrat na domovskou stránku pro **ADFTutorialOnPremDF**.
8. (volitelné) Klikněte na tlačítko **kanály**, klikněte na tlačítko **ADFTutorialOnPremDF**a procházení vstupní tabulky (**Potřebováno**) nebo výstupní datové sady (**vyprodukováno**).
9. Pomocí nástrojů, jako [Microsoft Storage Explorer](http://storageexplorer.com/) k ověření, že objektu blob nebo soubor se vytvoří pro každou hodinu.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Další postup
* V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) všechny podrobnosti o Brána pro správu dat najdete v článku.
* V tématu [kopírování dat z objektu Blob Azure do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Další informace o tom, jak pomocí aktivity kopírování pro přesun dat ze zdrojového úložiště dat do úložiště dat jímky.
