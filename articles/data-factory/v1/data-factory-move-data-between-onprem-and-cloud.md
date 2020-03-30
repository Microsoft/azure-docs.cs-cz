---
title: Přesunout data – brána pro správu dat
description: Nastavte bránu dat pro přesun dat mezi místním a cloudem. K přesunu dat použijte bránu pro správu dat v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982200"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma kopírování dat mezi místním a cloudem pomocí data factory](../tutorial-hybrid-copy-powershell.md).

Tento článek poskytuje přehled integrace dat mezi místními úložišti dat a cloudovými úložišti dat pomocí data factory. Vychází z článku [Aktivity přesunu dat](data-factory-data-movement-activities.md) a dalších konceptů jádra datové továrny: [datové sady](data-factory-create-datasets.md) a [kanály](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brána správy dat
Chcete-li povolit přesun dat do nebo z místního úložiště dat, musíte do místního počítače nainstalovat bránu pro správu dat. Bránu lze nainstalovat na stejný počítač jako úložiště dat nebo na jiný počítač, pokud se brána může připojit k úložišti dat.

> [!IMPORTANT]
> Podrobnosti o bráně pro správu dat najdete v článku [Brána pro správu](data-factory-data-management-gateway.md) dat.

Následující návod ukazuje, jak vytvořit továrnu dat s kanálem, který přesouvá data z místní databáze **SQL Serveru** do úložiště objektů blob Azure. Jako součást návodu nainstalujete a nakonfigurujete ve svém počítači Bránu správy dat.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Návod: kopírování místních dat do cloudu
V tomto návodu provedete následující kroky:

1. Vytvoření datové továrny
2. Vytvořte bránu pro správu dat.
3. Vytvořte propojené služby pro úložiště dat zdrojového a jímanového skladu.
4. Vytvořte datové sady představující vstupní a výstupní data.
5. Vytvoření kanálu s aktivitou kopírování pro přesun dat

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro výuku
Než začnete tento návod, musíte mít následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Podrobnosti najdete v článku [o bezplatné zkušební verzi.](https://azure.microsoft.com/pricing/free-trial/)
* **Účet úložiště Azure**. Úložiště objektů blob se používá jako úložiště dat **cílového a cílového úložiště** v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
* **SQL Server**. V tomto kurzu použijete místní databázi SQL Serveru jako **zdrojové** úložiště dat.

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku použijete portál Azure k vytvoření instance Azure Data Factory s názvem **ADFTutorialOnPremDF**.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte **na Vytvořit prostředek**, klikněte na Intelligence + **analytics**a potom klikněte na **Data Factory**.

   ![Nový -> Objekt pro vytváření dat](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na stránce **Nová továrna dat** zadejte **adftutorialonpremdf** pro název.

    ![Přidat do startboardu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: **Název factory dat "ADFTutorialOnPremDF" není k dispozici**, změňte název datové továrny (například yournameADFTutorialOnPremDF) a zkuste vytvořit znovu. Použijte tento název místo ADFTutorialOnPremDF při provádění zbývajících kroků v tomto kurzu.
   >
   > Název datové továrny může být v budoucnu registrován jako název **DNS** a tím se stane veřejně viditelným.
   >
   >
4. Vyberte **předplatné Azure**, ve kterém chcete objekt pro vytváření dat vytvořit.
5. Vyberte existující **skupinu prostředků** nebo vytvořte novou. Pro účely tohoto kurzu vytvořte skupinu prostředků s **názvem: ADFTutorialResourceGroup**.
6. Na stránce **Nová továrna na nová data** klikněte na **Vytvořit.**

   > [!IMPORTANT]
   > Chcete-li vytvořit instance data factory, musíte být členem role [přispěvatele datové továrny](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.
   >
   >
7. Po dokončení vytvoření se zobrazí stránka **Data Factory,** jak je znázorněno na následujícím obrázku:

   ![Domovská stránka datové továrny](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Vytvořit bránu
1. Na stránce **Data Factory** klikněte na Author **and deploy** tile a spusťte **Editor** pro datovou továrnu.

    ![Dlaždice Vytvořit a nasadit](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. V Editoru datových tove je klikněte na **... Další informace** na panelu nástrojů a potom klepněte na tlačítko **Nová brána dat**. Případně můžete ve stromovém zobrazení kliknout pravým tlačítkem myši na **datové brány** a kliknout na **Nová brána dat**.

   ![Nová brána dat na panelu nástrojů](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na stránce **Vytvořit** zadejte **název** **adftutorialgateway** a klepněte na tlačítko **OK**.     

    ![Vytvořit stránku brány](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > V tomto návodu vytvoříte logickou bránu pouze s jedním uzlem (místní počítač se systémem Windows). Bránu pro správu dat můžete škálovat tak, že k ní přisuzujete více místních počítačů. Můžete vertikálně navýšit kapacitu zvýšením počtu úloh přesunu dat, které lze spustit souběžně na uzlu. Tato funkce je také k dispozici pro logickou bránu s jedním uzlem. Podrobnosti najdete [v článku Škálování brány pro správu dat v](data-factory-data-management-gateway-high-availability-scalability.md) Azure Data Factory.  
4. Na stránce **Konfigurovat** klepněte na tlačítko **Instalovat přímo v tomto počítači**. Tato akce stáhne instalační balíček brány, nainstaluje, nakonfiguruje a zaregistruje bránu v počítači.  

   > [!NOTE]
   > Použijte aplikaci Internet Explorer nebo webový prohlížeč kompatibilní s aplikací Microsoft ClickOnce.
   >
   > Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
   >
   > Udělejte totéž pro Firefox (nainstalujte doplněk). Na panelu nástrojů klepněte na tlačítko **Otevřít nabídku** **(tři vodorovné čáry** v pravém horním rohu), klikněte **na Doplňky**, vyhledejte klíčové slovo ClickOnce, zvolte jedno z rozšíření ClickOnce a nainstalujte je.    
   >
   >

    ![Brána – konfigurace stránky](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Tímto způsobem je nejjednodušší způsob (jedním kliknutím) stáhnout, nainstalovat, nakonfigurovat a zaregistrovat bránu v jednom kroku. V počítači je nainstalována aplikace **Microsoft Data Management Gateway Configuration Manager.** Spustitelný soubor **ConfigManager.exe** naleznete také ve složce: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Bránu můžete také stáhnout a nainstalovat ručně pomocí odkazů na této stránce a zaregistrovat ji pomocí klávesy zobrazené v textovém poli **NOVÝ KLÍČ.**

    Všechny podrobnosti o bráně najdete v článku [Brána pro správu dat.](data-factory-data-management-gateway.md)

   > [!NOTE]
   > Chcete-li úspěšně nainstalovat a nakonfigurovat bránu pro správu dat, musíte být správcem v místním počítači. Do místní skupiny Windows mohou přidat další uživatele **uživatelé brány pro správu dat.** Členové této skupiny mohou ke konfiguraci brány použít nástroj Správce konfigurace brány brány pro správu dat.
   >
   >
5. Počkejte několik minut nebo počkejte, až se zobrazí následující zpráva s oznámením:

    ![Instalace brány byla úspěšná.](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Spusťte v počítači aplikaci **Správce konfigurace brány pro správu dat.** V okně **Hledat** zadejte **Bránu pro správu dat,** abyste měli přístup k tomuto nástroji. Spustitelný soubor **ConfigManager.exe** naleznete také ve složce: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Správce konfigurace brány](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Potvrďte, `adftutorialgateway is connected to the cloud service` že se vám zobrazí zpráva. Stavový řádek v dolní části zobrazuje **Připojeno ke cloudové službě** spolu se **zeleným zaškrtnutím**.

    Na kartě **Domů** můžete provést také následující operace:

   * **Zaregistrujte** bránu s klíčem z portálu Azure pomocí tlačítka Registrovat.
   * **Zastavte** hostitelskou službu brány pro správu dat spuštěnou v počítači brány.
   * **Naplánujte aktualizace,** které mají být nainstalovány v určitou denní dobu.
   * Zobrazení, kdy byla brána **naposledy aktualizována**.
   * Určete čas, kdy lze nainstalovat aktualizaci brány.
8. Přepněte na kartu **Nastavení.** Certifikát zadaný v části **Certifikát** se používá k šifrování nebo dešifrování pověření pro místní úložiště dat, které zadáte na portálu. (nepovinné) Chcete-li místo toho použít vlastní certifikát, klepněte na **tlačítko Změnit.** Ve výchozím nastavení používá brána certifikát, který je automaticky generován službou Data Factory.

    ![Konfigurace certifikátu brány](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Na kartě **Nastavení** můžete také provést následující akce:

   * Zobrazení nebo export certifikátu používaného bránou.
   * Změňte koncový bod HTTPS používaný bránou.    
   * Nastavte proxy HTTP, který bude brána používat.     
9. (nepovinné) Přepněte na kartu **Diagnostika,** zaškrtněte možnost **Povolit podrobné protokolování,** pokud chcete povolit podrobné protokolování, které můžete použít k řešení problémů s bránou. Informace o protokolování naleznete v **Prohlížeči událostí** v uzlu **Aplikace a protokoly protokolů** -> **dat.**

    ![Karta Diagnostika](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Na kartě **Diagnostika** můžete také provést následující akce:

   * Použijte část **Testovací připojení** k místnímu zdroji dat pomocí brány.
   * Kliknutím na **Zobrazit protokoly** zobrazíte protokol brány pro správu dat v okně Prohlížeče událostí.
   * Chcete-li společnosti Microsoft nahrát soubor zip s protokoly za posledních sedm dní, klepněte na tlačítko **Odeslat protokoly,** abyste usnadnili řešení potíží.
10. Na kartě **Diagnostika vyberte** v části **Testovat připojení** **sqlserver** pro typ úložiště dat, zadejte název databázového serveru, název databáze, zadejte typ ověřování, zadejte uživatelské jméno a heslo a klepnutím na **tlačítko Testovat** otestujte, zda se brána může připojit k databázi.
11. Přepněte do webového prohlížeče a na **webu Azure Portal**klikněte na stránce **Konfigurace** a potom na stránce **Nová brána dat** na OK. **OK**
12. Měli byste vidět **adftutorialgateway** v části **datové brány** ve stromovém zobrazení na levé straně.  Pokud na něj klepnete, měli byste vidět přidružené JSON.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby: **AzureStorageLinkedService** a **SqlServerLinkedService**. **Služba SqlServerLinkedService** propojuje místní databázi serveru SQL Server a propojenou službu **AzureStorageLinkedService** propojuje úložiště objektů blob Azure s toto datové továrně. V tomto návodu vytvoříte kanál, který zkopíruje data z místní databáze SERVERU SQL Server do úložiště objektů blob Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Přidání propojené služby do místní databáze serveru SQL Server
1. V **Editoru datové továrny**klepněte na panelu nástrojů na **nový sklad dat** a vyberte příkaz SQL **Server**.

   ![Nová propojená služba SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. V **editoru JSON** vpravo proveďte následující kroky:

   1. Pro **název gateway**zadejte **adftutorialgateway**.    
   2. V **connectionString**, postupujte takto:    

      1. Název **serveru**zadejte název serveru, který je hostitelem databáze serveru SQL Server.
      2. Název **databáze**zadejte název databáze.
      3. Na panelu nástrojů klepněte na tlačítko **Šifrovat.** Zobrazí se aplikace Správce přihlašovacích údajů.

         ![Aplikace Správce přihlašovacích údajů](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. V dialogovém okně **Nastavení pověření** zadejte typ ověřování, uživatelské jméno a heslo a klepněte na tlačítko **OK**. Pokud je připojení úspěšné, šifrovaná pověření jsou uložena v JSON a dialogové okno se zavře.
      5. Zavřete prázdnou kartu prohlížeče, která spustila dialogové okno, pokud není automaticky uzavřeno, a vraťte se na kartu s portálem Azure.

         V počítači brány jsou tato pověření **šifrována** pomocí certifikátu, který vlastní služba Data Factory. Pokud chcete místo toho použít certifikát, který je přidružený k bráně pro správu dat, přečtěte si informace o bezpečném nastavení přihlašovacích údajů.    
   3. Chcete-li na panelu příkazů nasadit propojenou službu SQL Server, klepněte na tlačítko **Nasadit** na panelu příkazů. Měly by se zobrazit propojené služby ve stromovém zobrazení.

      ![Propojená služba SQL Server ve stromovém zobrazení](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Přidání propojené služby pro účet úložiště Azure
1. V **Editoru datových toků**klikněte na panelu příkazů na **Nové úložiště dat** a klikněte na Azure **Storage**.
2. Zadejte název účtu úložiště Azure pro **název účtu**.
3. Zadejte klíč pro svůj účet úložiště Azure pro **klíč účtu**.
4. Kliknutím na **Nasadit** nasaďte **službu AzureStorageLinkedService**.

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady, které představují vstupní a výstupní data pro operaci kopírování (místní databáze SQL Serveru => úložiště objektů blob v Azure). Před vytvořením datových sad proveďte následující kroky (podrobný postup je uvedený pod seznamem):

* V databázi SQL Serveru, kterou jste do datové továrny přidali jako propojenou službu, vytvořte tabulku **emp** a vložte do ní několik ukázkových záznamů.
* V účtu služby Azure Blob Storage, který jste do datové továrny přidali jako propojenou službu, vytvořte kontejner objektů blob **adftutorial**.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Příprava místního serveru SQL Server na kurz
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

1. V **Editoru datových toveje**klikněte na **... Více**, klepněte na tlačítko **Nová datová sada** na panelu příkazů a klepněte na **položku Tabulka serveru SQL Server**.
2. Nahraďte json v pravém podokně následujícím textem:

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
   * **tableName** je nastavena na **emp**.
   * **linkedServiceName** je nastavena na **SqlServerLinkedService** (jste vytvořili tuto propojenou službu dříve v tomto návodu.).
   * Pro vstupní datovou sadu, která není generována jiným kanálem v Azure Data Factory, musíte nastavit **externí** na **true**. Označuje vstupní data vytvořená externí službou Azure Data Factory. Volitelně můžete zadat všechny zásady externích dat pomocí prvku **externalData** v části **Zásady.**    

   Podrobnosti o vlastnostech JSON najdete v tématu [Přesun dat do nebo z serveru SQL Server.](data-factory-sqlserver-connector.md)
3. Kliknutím na **Nasadit** na panelu příkazů nasadíte datovou sadu.  

### <a name="create-output-dataset"></a>Vytvoření výstupní datové sady

1. V **Editoru datových toků**klikněte na panelu příkazů na **Novou datovou sadu** a klikněte na **Azure Blob storage**.
2. Nahraďte json v pravém podokně následujícím textem:

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
   * **linkedServiceName** je nastavena na **AzureStorageLinkedService** (jste vytvořili tuto propojenou službu v kroku 2).
   * **folderPath** je **nastavena na adftutorial/outfromonpremdf** kde outfromonpremdf je složka v kontejneru adftutorial. Vytvořte kontejner **adftutorial,** pokud ještě neexistuje.
   * Vlastnost **availability** je nastavená na **hourly** (**frequency** je nastavená na **hour** a **interval** je nastavená na **1**).  Služba Data Factory generuje výstupní řez dat každou hodinu v tabulce **EMP** v azure sql database.

   Pokud nezadáte **název souboru** pro **výstupní tabulku**, budou generované soubory ve **složcePath** pojmenovány v následujícím formátu: `Data.<Guid>.txt` (například: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

    Podrobnosti o vlastnostech JSON najdete v tématu [Přesun dat do/z úložiště objektů blob Azure.](data-factory-azure-blob-connector.md)
3. Kliknutím na **Nasadit** na panelu příkazů nasadíte datovou sadu. Potvrďte, že se obě datové sady zobrazují ve stromovém zobrazení.  

## <a name="create-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte **kanál** s jednou **aktivitou kopírování,** která používá **EmpOnPremSQLTable** jako vstup a **OutputBlobTable** jako výstup.

1. V Editoru datových toveje klikněte na **... Další**a klepněte na tlačítko **Nový kanál**.
2. Nahraďte json v pravém podokně následujícím textem:    

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

   * V části aktivity je pouze aktivita, jejíž **typ** je nastaven na **Kopírovat**.
   * **Vstup** pro aktivitu je nastaven na **EmpOnPremSQLTable** a **výstup** pro aktivitu je nastaven na **OutputBlobTable**.
   * V části **typeProperties** je **SqlSource** určen jako **typ zdroje** a **objekt BlobSink** je určen jako typ **jímky**.
   * Dotaz `select * from emp` SQL je určen pro vlastnost **sqlReaderQuery** **aplikace SqlSource**.

   Počáteční a koncové hodnoty data a času musí být ve [formátu ISO](https://en.wikipedia.org/wiki/ISO_8601). Například: 2014-10-14T16:32:41Z. Čas hodnoty **end** je nepovinný, ale my ho v tomto kurzu použijeme.

   Pokud nezadáte hodnotu vlastnosti **end**, vypočítá se jako „**start + 48 hodin**“. Chcete-li kanál spouštět bez omezení, zadejte vlastnosti **end** hodnotu **9/9/9999**.

   Definujete dobu trvání, ve které jsou řezy dat zpracovány na základě vlastností **dostupnosti,** které byly definovány pro každou datovou sadu Azure Data Factory.

   V příkladu je 24 datových řezů, protože se vytvářejí každou hodinu.        
3. Kliknutím na **Nasadit** na panelu příkazů nasaďte datovou sadu (tabulka je obdélníková datová sada). Zkontrolujte, zda se kanál zobrazí ve stromovém zobrazení v části Uzel **Potrubí.**  
4. Nyní klepněte na tlačítko **X** dvakrát zavřete stránku se vrátit na stránku **Data Factory** pro **ADFTutorialOnPremDF**.

**Blahopřejeme!** Úspěšně jste vytvořili datovou továrnu Azure, propojené služby, datové sady a kanál a naplánovali kanál.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Zobrazit objektu pro vytváření dat v zobrazení diagramu
1. Na **webu Azure Portal**klikněte na dlaždici **Diagram** na domovské stránce datové továrny **ADFTutorialOnPremDF.** :

    ![Odkaz na diagram](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Zobrazený diagram by měl vypadat přibližně jako na následujícím obrázku:

    ![Zobrazení diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Můžete přiblížit, oddálit, zvětšit na 100 %, přiblížit, přizpůsobit, automaticky umístit kanály a datové sady a zobrazit informace o původu (zvýrazní položky vybraných položek pod proudem a pod ním).  Můžete poklepat na objekt (vstupní a výstupní datovou sadu nebo kanál) a zobrazit jeho vlastnosti.

## <a name="monitor-pipeline"></a>Monitorování kanálu
V tomto kroku budete pomocí webu Azure Portal monitorovat, co se děje v objektu pro vytváření dat Azure. K monitorování datových sad a kanálů můžete také použít rutiny PowerShellu. Podrobnosti o monitorování naleznete v [tématu Sledování a správa kanálů](data-factory-monitor-manage-pipelines.md).

1. V diagramu poklepejte na **položku EmpOnPremSQLTable**.  

    ![Řezy EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Všimněte si, že všechny řezy dat nahoru jsou ve stavu **Připraveno,** protože doba trvání kanálu (čas zahájení do konce) je v minulosti. Je to také proto, že jste vložili data v databázi SERVERU SQL A je tam po celou dobu. Zkontrolujte, zda se v části **Problémové řezy** v dolní části nezobrazují žádné řezy. Pokud chcete zobrazit všechny řezy, klikněte dole v seznamu řezů na **Zobrazit** víc.
3. Nyní na stránce **Datové sady** klepněte na **položku OutputBlobTable**.

    ![OputputBlobTable řezy](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klikněte na libovolný řez dat ze seznamu a měla by se zobrazit stránka **Řez dat.** Zobrazí se spuštění aktivity pro řez. Obvykle se zobrazí pouze jedna aktivita.  

    ![Datový řez](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Není-li řez ve stavu **Připraveno**, zobrazí se v seznamu **Upstreamové datové řezy, které nejsou připraveny** upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu.
5. Kliknutím na **aktivitu spuštěnou** ze seznamu v dolní části **zobrazíte podrobnosti spuštění aktivity**.

   ![Stránka Podrobnosti spuštění aktivity](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobrazí se informace, jako je propustnost, doba trvání a brána používaná k přenosu dat.
6. Klepnutím na **tlačítko X** zavřete všechny stránky, dokud
7. vrátit se na domovskou stránku **adftutorialonpremdf**.
8. (nepovinné) Klepněte na **položku Kanály**, klepněte na **položku ADFTutorialOnPremDF**a projděte vstupní tabulky (**Spotřebované)** nebo výstupní datové sady (**Vyrobeno**).
9. Pomocí nástrojů, jako je [Microsoft Storage Explorer,](https://storageexplorer.com/) ověřte, zda je pro každou hodinu vytvořen objekt blob/soubor.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Další kroky
* V článku [Brána pro správu dat](data-factory-data-management-gateway.md) najdete všechny podrobnosti o bráně pro správu dat.
* Najdete [v tématu Kopírování dat z objektu Blob Azure do Azure SQL,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kde se dozvíte, jak pomocí aktivity kopírování přesunout data ze zdrojového úložiště dat do úložiště dat jímky.
