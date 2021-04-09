---
title: Přesunout data – Správa dat bránu
description: Nastavte bránu dat pro přesun dat mezi místním prostředím a cloudem. K přesunu dat použijte Správa dat bránu v Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380283"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [kopírování dat mezi místním prostředím a cloudem pomocí Data Factory](../tutorial-hybrid-copy-powershell.md).

Tento článek obsahuje přehled integrace dat mezi místními úložišti dat a úložišti cloudových dat pomocí Data Factory. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) a dalších konceptů základní koncepce Data Factory: datové [sady](data-factory-create-datasets.md) a [kanály](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brána správy dat
Aby bylo možné přesouvat data do a z místního úložiště dat, je nutné na místní počítač nainstalovat bránu Správa dat. Bránu můžete nainstalovat na stejný počítač jako úložiště dat nebo na jiný počítač, pokud se brána může připojit k úložišti dat.

> [!IMPORTANT]
> Podrobnosti o Správa dat bráně najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) .

Následující návod ukazuje, jak vytvořit datovou továrnu s kanálem, který přesouvá data z místní databáze **SQL Server** do úložiště objektů BLOB v Azure. Jako součást návodu nainstalujete a nakonfigurujete ve svém počítači Bránu správy dat.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Návod: kopírování místních dat do cloudu
V tomto návodu provedete následující kroky:

1. Vytvoření datové továrny
2. Vytvořte bránu pro správu dat.
3. Vytvořte propojené služby pro úložiště dat pro zdroj a jímku.
4. Vytvořte datové sady, které reprezentují vstupní a výstupní data.
5. Vytvoření kanálu s aktivitou kopírování pro přesun dat

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro tento kurz
Před zahájením tohoto Názorného postupu musíte mít následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Podrobnosti najdete v článku [bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/) .
* **Účet Azure Storage**. V tomto kurzu použijete úložiště objektů BLOB jako **cílové úložiště nebo** úložiště dat jímky. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
* **SQL Server**. V tomto kurzu použijete databázi SQL Server jako **zdrojové** úložiště dat.

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku použijete Azure Portal k vytvoření instance Azure Data Factory s názvem **ADFTutorialOnPremDF**.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Klikněte na **vytvořit prostředek**, klikněte na **Intelligence + Analytics** a pak klikněte na **Data Factory**.

   ![Nový -> Objekt pro vytváření dat](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na stránce **Nová datová továrna** jako název zadejte **ADFTutorialOnPremDF** .

    ![Přidat do úvodní panel](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **název objektu pro vytváření dat "ADFTutorialOnPremDF" není k dispozici**, změňte název objektu pro vytváření dat (třeba na yournameADFTutorialOnPremDF) a zkuste to znovu. Použijte tento název místo ADFTutorialOnPremDF při provádění zbývajících kroků v tomto kurzu.
   >
   > Název objektu pro vytváření dat se může v budoucnu zaregistrovat jako název **DNS** , takže bude veřejně viditelný.
   >
   >
4. Vyberte **předplatné Azure**, ve kterém chcete objekt pro vytváření dat vytvořit.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou. V tomto kurzu vytvořte skupinu prostředků s názvem: **ADFTutorialResourceGroup**.
6. Na stránce **Nová datová továrna** klikněte na **vytvořit** .

   > [!IMPORTANT]
   > Chcete-li vytvořit instance Data Factory, musíte být členem role [přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.
   >
   >
7. Po vytvoření se zobrazí stránka **Data Factory** , jak je znázorněno na následujícím obrázku:

   ![Data Factory domovskou stránku](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Vytvořit bránu
1. Na stránce **Data Factory** klikněte na dlaždici **vytvořit a nasadit** , abyste spustili **Editor** pro objekt pro vytváření dat.

    ![Dlaždice Vytvořit a nasadit](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. V editoru Data Factory klikněte na **... Další** na panelu nástrojů a pak klikněte na **Nová brána dat**. Případně můžete kliknout pravým tlačítkem na **datové brány** ve stromovém zobrazení a kliknout na **Nová brána dat**.

   ![Nová brána dat na panelu nástrojů](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na stránce **vytvořit** zadejte **adftutorialgateway** pro **název** a klikněte na **OK**.     

    ![Stránka vytvořit bránu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > V tomto návodu vytvoříte logickou bránu jenom s jedním uzlem (místní počítač s Windows). Bránu pro správu dat můžete škálovat tak, že přidružíte několik místních počítačů k bráně. Horizontální navýšení kapacity můžete zvýšit tak, že zvýšíte počet úloh přesunu dat, které můžou běžet souběžně na uzlu. Tato funkce je k dispozici také pro logickou bránu s jedním uzlem. Podrobnosti najdete [v tématu škálování brány pro správu dat v Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) článku.  
4. Na stránce **Konfigurace** klikněte na možnost **instalovat přímo na tento počítač**. Tato akce stáhne instalační balíček pro bránu, nainstaluje, nakonfiguruje a zaregistruje bránu v počítači.  

   > [!NOTE]
   > Použijte Internet Explorer nebo webový prohlížeč kompatibilní s Microsoft ClickOnce.
   >
   > Pokud používáte Chrome, klikněte na [Web Store pro Chrome](https://chrome.google.com/webstore/), vyhledejte klíčové slovo ClickOnce, vyberte jedno z rozšíření ClickOnce a nainstalujte je.
   >
   > To samé udělejte pro Firefox (instalovat doplněk). Klikněte na tlačítko **otevřít nabídku** na panelu nástrojů (**tři horizontální čáry** v pravém horním rohu), klikněte na **Doplňky**, vyhledejte klíčové slovo ClickOnce, vyberte jedno z rozšíření ClickOnce a nainstalujte je.    
   >
   >

    ![Brána – konfigurace stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Tímto způsobem nejsnadnější způsob (jedním kliknutím) stáhnout, nainstalovat, nakonfigurovat a zaregistrovat bránu v jediném kroku. Můžete vidět, že se v počítači nainstaluje aplikace **Microsoft Správa dat Gateway Configuration Manager** . Spustitelný **ConfigManager.exe** lze také najít ve složce: **C:\Program Files\Microsoft Správa dat Gateway\2.0\Shared**.

    Bránu můžete také ručně stáhnout a nainstalovat pomocí odkazů na této stránce a zaregistrovat ji pomocí klíče uvedeného v textovém poli **nový klíč** .

    Všechny podrobnosti o bráně najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) .

   > [!NOTE]
   > Abyste mohli úspěšně nainstalovat a nakonfigurovat bránu Správa dat, musíte být správcem místního počítače. Další uživatele můžete přidat do místní skupiny Windows **Správa dat uživatelé brány** . Členové této skupiny můžou ke konfiguraci brány použít nástroj Správa dat brány Configuration Manager.
   >
   >
5. Počkejte pár minut nebo počkejte, až se zobrazí následující zpráva oznámení:

    ![Instalace brány byla úspěšná.](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Na svém počítači spusťte aplikaci **Správa dat Configuration Manager brány** . V okně **hledání** zadejte pro přístup k tomuto nástroji **Správa dat bránu** . Spustitelný **ConfigManager.exe** lze také najít ve složce: **C:\Program Files\Microsoft Správa dat Gateway\2.0\Shared**

    ![Configuration Manager brány](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Potvrďte, že se zobrazí `adftutorialgateway is connected to the cloud service` zpráva. Stavový řádek dole zobrazuje **připojení ke cloudové službě** spolu se **zeleným znakem zaškrtnutí**.

    Na kartě **Domů** můžete také provádět následující operace:

   * Pomocí tlačítka registrovat **Zaregistrujte** bránu s klíčem z Azure Portal.
   * **Zastavte** službu hostitel brány Správa dat spuštěnou na počítači brány.
   * **Naplánujte aktualizace** tak, aby se nainstalovaly v určitou dobu daného dne.
   * Zobrazit čas **Poslední aktualizace** brány
   * Zadejte čas, kdy se dá nainstalovat aktualizace brány.
8. Přepněte na kartu **Nastavení** . Certifikát zadaný v části **certifikát** slouží k šifrování/dešifrování přihlašovacích údajů pro místní úložiště dat, které zadáte na portálu. volitelné Pokud místo toho chcete použít vlastní certifikát, klikněte na **změnit** . Ve výchozím nastavení brána používá certifikát, který je automaticky generovaný službou Data Factory.

    ![Konfigurace certifikátu brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Na kartě **Nastavení** můžete provést také následující akce:

   * Zobrazit nebo exportovat certifikát používaný bránou.
   * Změňte koncový bod HTTPS používaný bránou.    
   * Nastavte proxy server HTTP, který bude brána používat.     
9. volitelné Přepněte na kartu **Diagnostika** , zaškrtněte možnost **Povolit podrobné protokolování** , pokud chcete povolit podrobné protokolování, které můžete použít k řešení potíží s bránou. Informace o protokolování najdete v **Prohlížeč událostí** v části **protokoly aplikací a služeb**  ->  **Správa dat uzel Brána** .

    ![Karta Diagnostika](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Na kartě **Diagnostika** můžete také provést následující akce:

   * Použijte k místnímu zdroji dat pomocí brány oddíl **test Connection** .
   * Kliknutím na **Zobrazit protokoly** zobrazíte protokol Správa dat brány v okně Prohlížeč událostí.
   * Kliknutím na **Odeslat protokoly** nahrajte soubor zip s protokoly posledních sedmi dnů do Microsoftu, aby se usnadnilo řešení potíží s problémy.
10. Na kartě **Diagnostika** v části **Test připojení** vyberte **SQLServer** pro typ úložiště dat, zadejte název databázového serveru, název databáze, zadejte typ ověřování, zadejte uživatelské jméno a heslo a klikněte na tlačítko **test** , chcete-li ověřit, zda se brána může připojit k databázi.
11. Přepněte do webového prohlížeče a v **Azure Portal** klikněte na tlačítko **OK** na stránce **Konfigurace** a potom na stránce **Nová brána dat** .
12. Ve stromovém zobrazení na levé straně byste měli vidět **adftutorialgateway** v části **brány dat** .  Pokud na něj kliknete, měl by se zobrazit přidružený kód JSON.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby: **AzureStorageLinkedService** a **SqlServerLinkedService**. **SqlServerLinkedService** odkazuje na databázi SQL Server a propojená služba **AzureStorageLinkedService** propojuje úložiště objektů BLOB v Azure s datovou továrnou. Kanál vytvoříte později v tomto návodu, který kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Přidání propojené služby do databáze SQL Server
1. V **editoru Data Factory** klikněte na panelu nástrojů na **nové úložiště dat** a vyberte **SQL Server**.

   ![Nová propojená služba SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. V **editoru JSON** vpravo proveďte následující kroky:

   1. Pro **bránu** zadejte **adftutorialgateway**.    
   2. V **připojovacím řetězci** proveďte následující kroky:    

      1. Jako název **serveru** zadejte název serveru, který je hostitelem databáze SQL Server.
      2. Pro **DatabaseName** zadejte název databáze.
      3. Klikněte na tlačítko **šifrování** na panelu nástrojů. Zobrazí se aplikace Správce přihlašovacích údajů.

         ![Aplikace Správce přihlašovacích údajů](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. V dialogovém okně **nastavení pověření** zadejte typ ověřování, uživatelské jméno a heslo a klikněte na tlačítko **OK**. Pokud je připojení úspěšné, šifrované přihlašovací údaje se uloží do formátu JSON a dialogové okno se zavře.
      5. Zavřete prázdnou kartu prohlížeče, která otevře dialogové okno, pokud není automaticky zavřeno a návrat na kartu s Azure Portal.

         Na počítači brány se tyto přihlašovací údaje **šifrují** pomocí certifikátu, který služba Data Factory vlastní. Pokud místo toho chcete použít certifikát, který je přidružený k bráně Správa dat, přečtěte si téma Nastavení přihlašovacích údajů bezpečně.    
   3. Pokud chcete nasadit propojenou službu SQL Server, klikněte na panelu příkazů na **nasadit** . Propojená služba by se měla zobrazit ve stromovém zobrazení.

      ![SQL Server propojená služba ve stromovém zobrazení](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Přidání propojené služby pro účet úložiště Azure
1. V **editoru Data Factory** klikněte na panelu příkazů na **nové úložiště dat** a pak klikněte na **Azure Storage**.
2. Jako **název účtu** zadejte název svého účtu úložiště Azure.
3. Zadejte klíč účtu úložiště Azure pro **klíč účtu**.
4. Kliknutím na **nasadit** nasaďte rozhraní **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady, které představují vstupní a výstupní data pro operaci kopírování (místní databáze SQL Serveru => úložiště objektů blob v Azure). Před vytvořením datových sad proveďte následující kroky (podrobný postup je uvedený pod seznamem):

* V databázi SQL Serveru, kterou jste do datové továrny přidali jako propojenou službu, vytvořte tabulku **emp** a vložte do ní několik ukázkových záznamů.
* V účtu služby Azure Blob Storage, který jste do datové továrny přidali jako propojenou službu, vytvořte kontejner objektů blob **adftutorial**.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Příprava místních SQL Server pro tento kurz
1. V databázi, kterou jste zadali pro službu SQL Server Linked Service (**SqlServerLinkedService**), vytvořte tabulku **EMP** v databázi pomocí následujícího skriptu SQL.

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

1. V **editoru Data Factory** klikněte na **... Další**, na panelu příkazů klikněte na **Nová datová sada** a pak klikněte na **SQL Server Tabulka**.
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

   * **typ** je nastaven na **SQLServer**.
   * vlastnost **TableName** je nastavená na **EMP**.
   * **linkedServiceName** je nastavená na **SqlServerLinkedService** (Tato propojená služba byla dříve v tomto návodu vytvořená výše).
   * Pro vstupní datovou sadu, která není generována jiným kanálem v Azure Data Factory, je nutné nastavit **hodnotu** **External** na true. Všimněte si, že vstupní data jsou vyráběná externě pro službu Azure Data Factory. Volitelně můžete zadat všechny zásady externích dat pomocí elementu **externalData** v části **Policy (zásady** ).    

   Podrobnosti o vlastnostech JSON najdete v tématu [přesun dat do/z SQL Server](data-factory-sqlserver-connector.md) .
3. Datovou sadu nasadíte kliknutím na **nasadit** na panelu příkazů.  

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady

1. V **editoru Data Factory** klikněte na panelu příkazů na **Nová datová sada** a pak klikněte na **Azure Blob Storage**.
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

   * **typ** je nastavený na **azureblobu**.
   * **linkedServiceName** je nastavená na **AzureStorageLinkedService** (Tato propojená služba se vytvořila v kroku 2).
   * **FolderPath** je nastavená na **adftutorial/outfromonpremdf** , kde outfromonpremdf je složka v kontejneru adftutorial. Vytvořte kontejner **adftutorial** , pokud ještě neexistuje.
   * Vlastnost **availability** je nastavená na **hourly** (**frequency** je nastavená na **hour** a **interval** je nastavená na **1**).  Služba Data Factory v Azure SQL Database v tabulce **EMP** generuje výstupní datový řez každou hodinu.

   Pokud nezadáte **filename** pro **výstupní tabulku**, generované soubory v **FolderPath** se pojmenují v následujícím formátu: `Data.<Guid>.txt` (například:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Pokud chcete nastavit **folderPath** a **fileName** dynamicky podle času **SliceStart**, použijte vlastnost partitionedBy. V následujícím příkladu folderPath používá rok, měsíc a den z vlastnosti SliceStart (čas zahájení zpracování řezu) a fileName používá hodinu z vlastnosti SliceStart. Pokud například začne být řez vytvářen v době 2014-10-20T08:00:00, vlastnost folderName je nastavená na wikidatagateway/wikisampledataout/2014/10/20 a vlastnost fileName je nastavená na 08.csv.

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

    Podrobnosti o vlastnostech JSON najdete v tématu [přesun dat do a z Azure Blob Storage](data-factory-azure-blob-connector.md) .
3. Datovou sadu nasadíte kliknutím na **nasadit** na panelu příkazů. Ověřte, že se ve stromovém zobrazení zobrazuje jak datové sady.  

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte **kanál** s jednou **aktivitou kopírování** , která používá **EmpOnPremSQLTable** jako vstup a **OutputBlobTable** jako výstup.

1. V editoru Data Factory klikněte na **... A klikněte na** **Nový kanál**.
2. Nahraďte kód JSON v pravém podokně následujícím textem:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
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

   * V části aktivity je k dispozici pouze aktivita, jejíž **typ** je nastaven na **Kopírovat**.
   * **Vstup** aktivity je nastavený na **EmpOnPremSQLTable** a **výstup** aktivity je nastavený na **OutputBlobTable**.
   * V části **typeProperties** je jako typ **zdroje** zadáno **SqlSource** a jako **typ jímky** je zadáno **BlobSink** .
   * `select * from emp`Pro vlastnost **SqlReaderQuery** třídy **SQLSOURCE** je zadán dotaz SQL.

   Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme.

   Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Chcete-li kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9/9/9999**.

   Definujete dobu trvání zpracování datových řezů na základě vlastností **dostupnosti** , které byly definovány pro každou Azure Data Factory datovou sadu.

   V příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.        
3. Kliknutím na **nasadit** na panelu příkazů nasaďte datovou sadu (tabulka je obdélníková datová sada). Ověřte, že se kanál zobrazuje ve stromovém zobrazení pod uzlem **kanály** .  
4. Nyní kliknutím na **X** dvakrát zavřete stránku a vrátíte se na stránku **Data Factory** pro **ADFTutorialOnPremDF**.

**Blahopřejeme!** Úspěšně jste vytvořili objekt pro vytváření dat Azure, propojené služby, datové sady a kanál a naplánovali jste kanál.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Zobrazit objektu pro vytváření dat v zobrazení diagramu
1. V **Azure Portal** klikněte na dlaždici **diagram** na domovské stránce pro **ADFTutorialOnPremDF** Data Factory. :

    ![Odkaz na diagram](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Zobrazený diagram by měl vypadat přibližně jako na následujícím obrázku:

    ![Zobrazení diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Můžete přiblížit, oddálit, přiblížit 100%, přiblížit, automaticky umístit kanály a datové sady a zobrazit informace o počtu řádků (zvýrazní nadřazené a podřízené položky vybraných položek).  Poklikáním na objekt (vstupní/výstupní datová sada nebo kanál) můžete zobrazit vlastnosti.

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku použijete Azure Portal ke sledování toho, co se chystá v objektu pro vytváření dat Azure. K monitorování datových sad a kanálů můžete také použít rutiny PowerShellu. Podrobnosti o monitorování najdete v tématu [monitorování a Správa kanálů](data-factory-monitor-manage-pipelines.md).

1. V diagramu poklikejte na **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable řezy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Všimněte si, že všechny datové řezy jsou ve stavu **připraveno** , protože doba trvání kanálu (čas začátku do konce) je v minulosti. Je to také proto, že jste vložili data do databáze SQL Server a tato data jsou pokaždé. Potvrďte, že v dolní části **řezy problému** nejsou žádné řezy zobrazené. Chcete-li zobrazit všechny řezy, klikněte na tlačítko **Zobrazit další** v dolní části seznamu řezů.
3. Nyní na stránce **datové sady** klikněte na **OutputBlobTable**.

    ![OputputBlobTable řezy](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klikněte na libovolný datový řez ze seznamu a měli byste vidět stránku **datových řezů** . U řezu vidíte spuštění aktivit. Zobrazuje se obvykle jenom jedna spuštění aktivity.  

    ![Okno datového řezu](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Není-li řez ve stavu **Připraveno**, zobrazí se v seznamu **Upstreamové datové řezy, které nejsou připraveny** upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu.
5. Kliknutím na **spuštění aktivit** v seznamu dole zobrazíte **Podrobnosti o spuštění aktivit**.

   ![Stránka podrobností o spuštění aktivity](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobrazí se informace, jako je například propustnost, doba trvání a brána používaná k přenosu dat.
6. Kliknutím na **X** zavřete všechny stránky, dokud
7. Vraťte se na domovskou stránku pro **ADFTutorialOnPremDF**.
8. volitelné Klikněte na **kanály**, klikněte na **ADFTutorialOnPremDF** a Projděte si vstupní tabulky (**spotřebované**) nebo výstupní datové sady (**produkované**).
9. Pomocí nástrojů, jako je [Microsoft Průzkumník služby Storage](https://storageexplorer.com/) , ověřte, jestli je pro každou hodinu vytvořený objekt BLOB nebo soubor.

   ![Průzkumník služby Azure Storage](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Další kroky
* Všechny podrobnosti o Správa dat bráně najdete v článku o [Správa dat brány](data-factory-data-management-gateway.md) .
* Další informace o tom, jak pomocí aktivity kopírování přesunout data ze zdrojového úložiště dat do úložiště dat jímky, najdete v tématu [kopírování dat z objektu blob Azure do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
