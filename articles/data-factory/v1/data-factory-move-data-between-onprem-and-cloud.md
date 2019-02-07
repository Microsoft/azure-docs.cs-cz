---
title: Přesun dat – Brána pro správu dat | Dokumentace Microsoftu
description: Nastavte bránu data gateway pro přesun dat mezi místním prostředím a cloudem. Přesunutí dat pomocí brány správy dat ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 77eaa6a642e02206eac319b76666bed8ae1fd165
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822421"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kopírovat data mezi místním a cloudovým pomocí služby Data Factory](../tutorial-hybrid-copy-powershell.md).

Tento článek obsahuje přehled integrace dat mezi úložišti dat s místními a cloudovými úložišti dat pomocí služby Data Factory. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článku a další články data factory základní koncepty: [datových sad](data-factory-create-datasets.md) a [kanály](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brána správy dat
Brána správy dat je nutné nainstalovat na váš místní počítač umožňují přesouvá data z úložiště dat v místním. Brána lze nainstalovat ve stejném počítači jako úložiště dat nebo na jiném počítači, tak dlouho, dokud brána lze připojit k úložišti dat.

> [!IMPORTANT]
> Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti o brána správy dat. 

Následující návod ukazuje, jak vytvořit datovou továrnu s kanálem, který přesouvá data z místní **systému SQL Server** databáze do Azure blob storage. Jako součást návodu nainstalujete a nakonfigurujete ve svém počítači Bránu správy dat.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Postupy: kopírování místních dat do cloudu
V tomto návodu provedete následující kroky: 

1. Vytvoření datové továrny
2. Vytvořte bránu pro správu data. 
3. Vytvoření propojených služeb pro úložiště dat zdroje a jímky.
4. Vytvoření datové sady, které představují vstupní a výstupní data.
5. Vytvoření kanálu s aktivitou kopírování pro přesun dat

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz
Před zahájením tohoto návodu, musíte mít splněné následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Zobrazit [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) , kde najdete podrobnosti.
* **Účet úložiště Azure**. Použijete jako úložiště objektů blob **cíl/jímka** úložiště dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření.
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako **zdrojové** úložiště dat. 

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku použijete Azure portal k vytvoření instance služby Azure Data Factory s názvem **ADFTutorialOnPremDF**.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek**, klikněte na tlačítko **inteligence a analýza**a klikněte na tlačítko **služby Data Factory**.

   ![Nový -> Datová továrna](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. V **nová datová továrna** zadejte **ADFTutorialOnPremDF** pro název.

    ![Přidat k úvodnímu panelu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chybová zpráva: **Název objektu pro vytváření dat "ADFTutorialOnPremDF" není k dispozici**, změňte název datové továrny (například yournameADFTutorialOnPremDF) a zkuste to znovu. Tento název používejte místo ADFTutorialOnPremDF při provádění zbývající kroky v tomto kurzu.
   >
   > Název datové továrny se může zaregistrovat jako **DNS** název v budoucnu takže pak bude veřejně viditelný.
   >
   >
4. Vyberte **předplatné Azure**, ve kterém chcete objekt pro vytváření dat vytvořit.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou. Pro tento kurz vytvořte skupinu prostředků s názvem: **ADFTutorialResourceGroup**.
6. Klikněte na tlačítko **vytvořit** na **nová datová továrna** stránky.

   > [!IMPORTANT]
   > Chcete-li vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného a skupiny prostředků.
   >
   >
7. Po vytvoření se zobrazí **služby Data Factory** stránce, jak je znázorněno na následujícím obrázku:

   ![Data Factory domovské stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Vytvoření brány
1. V **služby Data Factory** klikněte na **vytvořit a nasadit** dlaždice **Editor** služby data factory.

    ![Dlaždice Vytvořit a nasadit](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. V editoru služby Data Factory, klikněte na tlačítko **... Další** na panelu nástrojů a pak klikněte na tlačítko **novou bránu data gateway**. Alternativně je můžete kliknout pravým tlačítkem **brány Data Gateways** stromové zobrazení, a klikněte na **novou bránu data gateway**.

   ![Novou bránu data gateway na panelu nástrojů](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. V **vytvořit** zadejte **adftutorialgateway** pro **název**a klikněte na tlačítko **OK**.     

    ![Vytvoření brány stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > V tomto podrobném návodu vytvoření logické brány s pouze jedním uzlem (místní počítač Windows). Brána správy dat můžete horizontálně tím, že přidružíte více místních počítačů s bránou. Můžete škálovat nahoru zvýšením počtu data přesun úloh, které můžou běžet souběžně na uzlu. Tato funkce je také k dispozici pro logické brány se jeden uzel. Zobrazit [brána správy dat škálování ve službě Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) , kde najdete podrobnosti.  
4. V **konfigurovat** klikněte na **přímo na tento počítač nainstalovat**. Tato akce stáhne instalační balíček pro bránu, nainstaluje, konfiguruje a zaregistruje brány na počítači.  

   > [!NOTE]
   > Použijte Internet Explorer nebo Microsoft ClickOnce kompatibilní webový prohlížeč.
   >
   > Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
   >
   > Totéž proveďte pro Firefox (instalace doplňku). Klikněte na tlačítko **otevřít nabídku** tlačítko na panelu nástrojů (**tři vodorovné čáry** v pravém horním rohu), klikněte na tlačítko **doplňky**, dejte hledat klíčové slovo "ClickOnce", vyberte jednu z Rozšíření ClickOnce a nainstalujte ho.    
   >
   >

    ![Gateway – stránka konfigurace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Tímto způsobem je nejjednodušší způsob (jedním kliknutím) stáhnout, nainstalovat, konfigurace a registrace brány v jednom jednoho kroku. Zobrazí se **Microsoft Data Management Gateway Configuration Manager** aplikace je nainstalována v počítači. Můžete také najít spustitelný soubor **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Můžete také stáhnout a nainstalovat bránu ručně pomocí odkazů na této stránce a zaregistrujte ho pomocí klíče je znázorněno **nový klíč** textového pole.

    Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti o bráně.

   > [!NOTE]
   > Musíte být správcem v místním počítači nainstalovat a nakonfigurovat bránu správy dat úspěšně. Můžete přidat další uživatele **Data Management Gateway Users** místní skupiny Windows. Členové této skupiny můžete použít nástroje Správce konfigurace brány správy dat ke konfiguraci brány.
   >
   >
5. Počkejte několik minut nebo počkejte, až se zobrazí následující zpráva upozornění:

    ![Úspěšná instalace brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Spuštění **Data Management Gateway Configuration Manager** aplikace ve vašem počítači. V **hledání** okno, zadejte **brána správy dat** pro přístup k tohoto nástroje. Můžete také najít spustitelný soubor **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Správce konfigurace brány](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Zkontrolujte, jestli se `adftutorialgateway is connected to the cloud service` zprávy. Na stavovém řádku zobrazí dolní **připojená ke cloudové službě** spolu s **zelená značka zaškrtnutí**.

    Na **Domů** kartu, můžete také provést následující operace:

   * **Zaregistrujte** bránu s klíčem z portálu Azure portal pomocí tlačítka registru.
   * **Zastavit** Data Management Gateway hostitele Service spuštěná na počítači brány.
   * **Naplánovat aktualizace** nainstalovaný v určitém čase dne.
   * Zobrazit, když brána byla **poslední aktualizace**.
   * Zadejte dobu, ve kterém můžete nainstalovat aktualizace brány.
8. Přepněte na kartu **Nastavení**. Certifikát uvedený v **certifikát** oddíl se používá k šifrování/dešifrování přihlašovacích údajů pro místní úložiště dat, které jste zadali na portálu. (volitelné) Klikněte na tlačítko **změnu** místo toho použít vlastní certifikát. Ve výchozím nastavení brána používá certifikát, který je automaticky vygenerován pomocí služby Data Factory.

    ![Konfigurace certifikátu brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Můžete také provést následující akce na **nastavení** kartu:

   * Zobrazení nebo export certifikátu používanému službou Brána.
   * Změňte koncový bod HTTPS používá brána.    
   * Nastavte proxy server HTTP pro bránu.     
9. (volitelné) Přepněte **diagnostiky** kartě **zapnout podrobné protokolování** možnost, pokud chcete zapnout podrobné protokolování, můžete použít k řešení potíží s bránou. Informace o protokolování najdete v **Prohlížeč událostí** pod **protokoly aplikací a služeb** -> **brána správy dat** uzlu.

    ![Karta Diagnostika](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Můžete také provést následující akce v **diagnostiky** kartu:

   * Použití **Test připojení** část s místními zdroji dat pomocí brány.
   * Klikněte na tlačítko **zobrazit protokoly** chcete podívat na protokol brány správy dat v okně prohlížeče událostí.
   * Klikněte na tlačítko **odeslat protokoly** k nahrání souboru zip s protokoly posledních sedmi dnů společnosti Microsoft, aby se usnadnilo řešení vašich potíží.
10. Na **diagnostiky** kartě **Test připojení** vyberte **SqlServer** pro typ úložiště dat, zadejte název databázového serveru, název databáze, Zadejte typ ověřování, zadejte uživatelské jméno a heslo a klikněte na tlačítko **testování** k otestování, jestli je brána lze připojit k databázi.
11. Přepnout do webového prohlížeče a v **webu Azure portal**, klikněte na tlačítko **OK** na **konfigurovat** stránky a pak na **novou bránu data gateway** stránky.
12. Měli byste vidět **adftutorialgateway** pod **brány Data Gateways** ve stromovém zobrazení na levé straně.  Pokud na něj klikne, měli byste vidět přidružené JSON.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby: **AzureStorageLinkedService** a **SqlServerLinkedService**. **SqlServerLinkedService** odkazy k místní databázi SQL serveru a **AzureStorageLinkedService** propojená služba propojuje úložiště objektů blob v Azure s datovou továrnou. Vytvoření kanálu později v tomto názorném postupu, který kopíruje data z místní databáze SQL serveru do úložiště objektů blob v Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Přidat propojenou službu k místní databázi SQL serveru
1. V **editoru služby Data Factory**, klikněte na tlačítko **nové datové úložiště** na panelu nástrojů a vyberte **systému SQL Server**.

   ![Nové propojené služby SQL serveru](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. V **JSON editor** na pravé straně, proveďte následující kroky:

   1. Pro **název brány**, zadejte **adftutorialgateway**.    
   2. V **connectionString**, proveďte následující kroky:    

      1. Pro **servername**, zadejte název serveru, který je hostitelem databáze serveru SQL Server.
      2. Pro **databasename**, zadejte název databáze.
      3. Klikněte na tlačítko **šifrovat** tlačítko na panelu nástrojů. Zobrazí se aplikace Správce přihlašovacích údajů.

         ![Aplikace Správce přihlašovacích údajů](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. V **nastavení přihlašovacích údajů** dialogové okno, zadejte typ ověřování, uživatelské jméno a heslo a klikněte na tlačítko **OK**. Pokud je připojení úspěšné, zašifrované přihlašovací údaje jsou uložené v kódu JSON a dialog se zavře.
      5. Zavřete kartu prohlížeče prázdné, pokud není uzavřený automaticky spustit dialogové okno a potom na kartě pomocí webu Azure portal.

         Na počítači brány, tyto přihlašovací údaje jsou **šifrované** pomocí certifikátu, který služba Data Factory vlastní. Pokud chcete použít certifikát, který je přidružený k brána správy dat místo toho, bezpečně najdete v nastavení přihlašovacích údajů.    
   3. Klikněte na tlačítko **nasadit** na panelu příkazů pro nasazení SQL serveru propojená služba. Měli byste vidět propojené služby ve stromovém zobrazení.

      ![Propojené služby SQL serveru ve stromovém zobrazení](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Přidat propojené služby pro účet úložiště Azure
1. V **editoru služby Data Factory**, klikněte na tlačítko **nové datové úložiště** na panelu příkazů a klikněte na **služby Azure storage**.
2. Zadejte název svého účtu Azure storage **název účtu**.
3. Zadejte klíč účtu úložiště Azure pro **klíč účtu**.
4. Klikněte na tlačítko **nasadit** k nasazení **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady, které představují vstupní a výstupní data pro operaci kopírování (místní databáze SQL Serveru => úložiště objektů blob v Azure). Před vytvořením datových sad proveďte následující kroky (podrobný postup je uvedený pod seznamem):

* V databázi SQL Serveru, kterou jste do datové továrny přidali jako propojenou službu, vytvořte tabulku **emp** a vložte do ní několik ukázkových záznamů.
* V účtu služby Azure Blob Storage, který jste do datové továrny přidali jako propojenou službu, vytvořte kontejner objektů blob **adftutorial**.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Příprava místního SQL serveru pro tento kurz
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

1. V **Data Factory Editoru** klikněte na **... Další**, klikněte na tlačítko **nová datová sada** na panelu příkazů a klikněte na **tabulku SQL serveru**.
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

   * **typ** je nastavena na **SqlServerTable**.
   * **tableName** je nastavena na **emp**.
   * **Vlastnost linkedServiceName** je nastavena na **SqlServerLinkedService** (měl tuto propojenou službu jste vytvořili dříve v tomto názorném postupu.).
   * U vstupní datové sady, který není generován jiný kanál ve službě Azure Data Factory, musíte nastavit **externí** k **true**. Označuje, že se že vstupní data vytvořená externí do služby Azure Data Factory. Volitelně můžete zadat všechny zásady externí data pomocí **externalData** prvek **zásady** části.    

   Zobrazit [přesun dat do a z SQL serveru](data-factory-sqlserver-connector.md) podrobnosti o vlastnostech JSON.
3. Klikněte na tlačítko **nasadit** na panelu příkazů pro nasazení datové sady.  

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady

1. V **editoru služby Data Factory**, klikněte na tlačítko **nová datová sada** na panelu příkazů a klikněte na **úložiště objektů Blob v Azure**.
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

   * **typ** je nastavena na **AzureBlob**.
   * **Vlastnost linkedServiceName** je nastavena na **AzureStorageLinkedService** (měl tuto propojenou službu jste vytvořili v kroku 2).
   * **folderPath** je nastavena na **adftutorial/outfromonpremdf** kde outfromonpremdf je složka v kontejneru adftutorial. Vytvořte **adftutorial** kontejner, pokud ještě neexistuje.
   * Vlastnost **availability** je nastavená na **hourly** (**frequency** je nastavená na **hour** a **interval** je nastavená na **1**).  Služba Data Factory každou hodinu vygeneruje řez výstupních dat **emp** tabulky ve službě Azure SQL Database.

   Pokud nezadáte **fileName** pro **výstupní tabulky**, generované soubory v **folderPath** jsou pojmenovány v následujícím formátu: Data. <Guid>.txt (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

    Zobrazit [přesun dat do a z úložiště objektů Blob v Azure](data-factory-azure-blob-connector.md) podrobnosti o vlastnostech JSON.
3. Klikněte na tlačítko **nasadit** na panelu příkazů pro nasazení datové sady. Ověřte, že oba datové sady ve stromovém zobrazení.  

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte **kanálu** s jedním **aktivity kopírování** , která používá **EmpOnPremSQLTable** jako vstup a **OutputBlobTable** jako výstup.

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

   * V části aktivit je jenom aktivity jehož **typ** je nastavena na **kopírování**.
   * **Vstup** aktivity je nastavený na **EmpOnPremSQLTable** a **výstup** aktivity je nastavený na **OutputBlobTable**.
   * V **typeProperties** části **SqlSource** je zadán jako **typ zdroje** a ** BlobSink ** je zadán jako **jímky typu**.
   * Příkaz jazyka SQL `select * from emp` je určená pro **sqlReaderQuery** vlastnost **SqlSource**.

   Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](http://en.wikipedia.org/wiki/ISO_8601). Příklad: 2014-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme.

   Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Chcete-li kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9/9/9999**.

   Definujete dobu trvání ve kterém se zpracovávají datové řezy podle **dostupnosti** vlastnosti, které byly definovány pro každou datovou sadu Azure Data Factory.

   V příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.        
3. Klikněte na tlačítko **nasadit** na panelu příkazů a nasaďte datovou sadu (tabulka je obdélníková datová sada). Potvrďte, že kanál se zobrazí v zobrazení stromu v části **kanály** uzlu.  
4. Nyní, klikněte na tlačítko **X** dvakrát na zavřete stránku chcete vrátit zpět do **služby Data Factory** stránku **ADFTutorialOnPremDF**.

**Blahopřejeme!** Úspěšně jste vytvořili datovou továrnu Azure, propojené služby, datové sady a kanál a naplánovali jste kanál.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Zobrazit objektu pro vytváření dat v zobrazení diagramu
1. V **webu Azure portal**, klikněte na tlačítko **Diagram** dlaždici na domovské stránce **ADFTutorialOnPremDF** služby data factory. :

    ![Diagram odkaz](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Zobrazený diagram by měl vypadat přibližně jako na následujícím obrázku:

    ![Zobrazení diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Můžete přiblížení, oddálení, zvětšení na 100 % zvětšení přizpůsobit, automatické umísťování kanálů a datové sady a zobrazení informací o rodokmenu (zvýrazní nadřazené a podřízené položky vybraných položek).  Dvakrát klikněte na objekt (vstupní a výstupní datovou sadu nebo kanál) můžete zobrazit vlastnosti.

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku budete pomocí webu Azure Portal monitorovat, co se děje v objektu pro vytváření dat Azure. K monitorování datových sad a kanálů můžete také použít rutiny PowerShellu. Podrobnosti o monitorování najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).

1. V diagramu, klikněte dvakrát na **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Všimněte si, že všechny řezy dat nahoru v **připravené** stavu, protože doba trvání kanálu (čas zahájení koncového času) je v minulosti. Je také vzhledem k tomu, že jste tam vložili data v databázi serveru SQL Server a je pořád existuje. Potvrďte, že žádné řezy nezobrazují v **problémové řezy** oblast v dolní části. Chcete-li zobrazit všechny řezy, klikněte na tlačítko **zobrazit další** v dolní části Seznam řezů.
3. Teď v **datových sad** klikněte na **OutputBlobTable**.

    ![Kolekce obsahuje nějaké řezy OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klikněte na libovolný datový řez ze seznamu a měla by se zobrazit **datový řez** stránky. Zobrazí se spuštění aktivit pro řez. Zobrazí se obvykle spouštěny pouze jednu aktivitu.  

    ![Okno datový řez](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Není-li řez ve stavu **Připraveno**, zobrazí se v seznamu **Upstreamové datové řezy, které nejsou připraveny** upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu.
5. Klikněte na tlačítko **spuštění aktivit** ze seznamu v dolní části zobrazíte **podrobnosti o spuštění aktivit**.

   ![Stránka Podrobnosti o spuštění aktivit](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobrazí informace, jako je propustnost, doba trvání a bránu sloužící k přenosu dat.
6. Klikněte na tlačítko **X** zavřete všechny stránky dokud
7. vrátit zpět na domovskou stránku pro **ADFTutorialOnPremDF**.
8. (volitelné) Klikněte na tlačítko **kanály**, klikněte na tlačítko **ADFTutorialOnPremDF**a přejít k podrobnostem vstupní tabulky (**spotřebovaná**) nebo výstupní datové sady (**vyprodukováno**).
9. Pomocí nástrojů, jako [Microsoft Storage Explorer](http://storageexplorer.com/) k ověření, že soubor/blob se vytvoří pro každou hodinu.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Další postup
* Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti o brána správy dat.
* Zobrazit [kopírování dat z objektů Blob v Azure do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Další informace o tom, jak používat aktivitu kopírování pro přesun dat ze zdrojového úložiště dat do úložiště dat jímky.
