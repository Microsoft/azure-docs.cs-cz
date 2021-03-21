---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854158"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Přehled aplikace":::

Tento diagram znázorňuje pracovní postup řešení, které sestavíte v tomto kurzu: 

1. Data odesílaná do centra událostí Azure jsou zachycena v úložišti objektů BLOB v Azure.
2. Po dokončení zachycení dat se vygeneruje událost a pošle se do služby Azure Event Grid. 
3. Event Grid přepošle tato data události do aplikace funkce Azure.
4. Aplikace Function App používá k načtení objektu BLOB z úložiště adresu URL objektu BLOB v datech události. 
5. Aplikace Function App migruje data objektů blob do služby Azure synapse Analytics. 

V tomto článku proveďte následující kroky:

> [!div class="checklist"]
> - Nasazení požadované infrastruktury pro kurz
> - Publikování kódu do aplikace Functions
> - Vytvoření odběru Event Gridu 
> - Streamování ukázkových dat do Event Hubs
> - Ověření zachycených dat ve službě Azure synapse Analytics

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto kurzu potřebujete:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
- [Visual studio 2019](https://www.visualstudio.com/vs/) s pracovními postupy pro: vývoj desktopových aplikací pro .NET, vývoj pro Azure, vývoj pro ASP.NET a vývoj webů Node.js, vývoj a vývoj v Pythonu
- Stáhněte si [vzorový projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) do svého počítače.
    - WindTurbineDataGenerator – jednoduchý Vydavatel, který odesílá ukázková data turbínu do centra událostí s povoleným zachycením
    - FunctionDWDumper – Funkce Azure, která přijímá oznámení Event Gridu při zachycení souboru Avro do objektu blob služby Azure Storage. Obdrží cestu k identifikátoru URI objektu blob, přečte obsah a vloží tato data do služby Azure synapse Analytics (vyhrazený fond SQL).

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury
V tomto kroku nasadíte požadovanou infrastrukturu pomocí [šablony Správce prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Při nasazení šablony se vytvoří následující prostředky:

* Centrum událostí s povolenou funkcí Capture
* Účet úložiště pro zachycené soubory. 
* Plán služby App Service pro hostování aplikace Function App
* Aplikace Function App na zpracování události
* SQL Server na hostování datového skladu
* Azure synapse Analytics (vyhrazený fond SQL) pro ukládání migrovaných dat

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Nasazení infrastruktury pomocí Azure CLI

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. V horní části vyberte tlačítko **Cloud Shell** .

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure Portal":::
3. V dolní části prohlížeče se zobrazí Cloud Shell otevřít.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Pokud se v Cloud Shell zobrazí možnost výběru mezi **bash** a **PowerShellem**, vyberte **bash**. 
5. Pokud Cloud Shell používáte poprvé, vytvořte účet úložiště tak, že vyberete **vytvořit úložiště**. Azure Cloud Shell vyžaduje účet úložiště Azure pro ukládání některých souborů. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Vytvořit úložiště pro Cloud Shell":::
6. Počkejte, dokud se Cloud Shell neinicializuje. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inicializován":::
1. Vytvořte skupinu prostředků Azure spuštěním následujícího příkazu CLI: 
    1. Zkopírujte následující příkaz a vložte ho do okna Cloud Shell. Pokud chcete, změňte název a umístění skupiny prostředků.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Stiskněte klávesu **ENTER**. 

        Tady je příklad:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Spusťte následující příkaz CLI a nasaďte všechny prostředky uvedené v předchozí části (centrum událostí, účet úložiště, aplikace Functions, Azure synapse Analytics): 
    1. Zkopírujte příkaz a vložte ho do okna Cloud Shell. Alternativně můžete chtít zkopírovat nebo vložit do editoru podle svého výběru, nastavit hodnoty a potom zkopírovat příkaz do Cloud Shell. 

        > [!IMPORTANT]
        > Před spuštěním příkazu zadejte hodnoty pro následující entity: 
        > - Název skupiny prostředků, kterou jste vytvořili dříve.
        > - Název oboru názvů centra událostí. 
        > - Název centra událostí Hodnotu můžete ponechat tak, jak je (hubdatamigration).
        > - Název systému SQL Server.
        > - Jméno uživatele a heslo SQL 
        > - Název databáze.
        > - Název účtu úložiště 
        > - Název aplikace Function App 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Stisknutím klávesy **ENTER** v okně Cloud Shell spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte spoustu prostředků. V důsledku příkazu se ujistěte, že nedošlo k žádným chybám. 
1. Zavřete Cloud Shell tak, že v pravém horním rohu okna Cloud Shell kliknete na tlačítko **Cloud Shell** na portálu (nebo) na tlačítko **X** . 

### <a name="verify-that-the-resources-are-created"></a>Ověření vytvoření prostředků

1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** . 
2. Seznam skupin prostředků vyfiltrujte tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
3. V seznamu vyberte skupinu prostředků.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Vyberte skupinu prostředků.":::
4. Ověřte, že se ve skupině prostředků zobrazují tyto prostředky:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Prostředky ve skupině prostředků" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Vytvoření tabulky ve službě Azure synapse Analytics
Vytvořte tabulku v datovém skladu spuštěním skriptu [CreateDataWarehouseTable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) . Chcete-li spustit skript, můžete použít aplikaci Visual Studio nebo Editor dotazů na portálu. Následující kroky ukazují, jak používat Editor dotazů: 

1. V seznamu prostředků ve skupině prostředků vyberte **vyhrazený fond SQL**. 
2. Na stránce **vyhrazený fond SQL** v části **běžné úlohy** v levé nabídce vyberte **Editor dotazů (Preview)**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Stránka analýzy Azure synapse":::
2. Zadejte jméno **uživatele** a **heslo** pro systém SQL Server a vyberte **OK**. Pokud se zobrazí zpráva o tom, že klient bude mít přístup k systému SQL Server, postupujte podle následujících kroků:
    1. Vyberte odkaz: **nastavit bránu firewall serveru**. 
    2. Na stránce **nastavení brány firewall** vyberte **Přidat IP adresu klienta** na panelu nástrojů a pak na panelu nástrojů vyberte **Uložit** . 
    3. U zprávy o úspěchu vyberte **OK** .
    4. Přejděte zpět na **vyhrazenou stránku fondu SQL** a v nabídce vlevo vyberte **Editor dotazů (Preview)** . 
    5. Zadejte **uživatel** a **heslo** a pak vyberte **OK**. 
1. V okně dotazu zkopírujte a spusťte následující skript SQL: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Spustit dotaz SQL":::
5. Nechejte tuto kartu nebo okno otevřené, abyste mohli ověřit, že se data vytvoří na konci kurzu. 

### <a name="update-the-function-runtime-version"></a>Aktualizace verze modulu runtime funkce

1. Ve webovém prohlížeči otevřete jinou kartu a přejděte na [Azure Portal](https://portal.azure.com).
1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** .
1. Vyberte skupinu prostředků, ve které aplikace Function App existuje. 
1. V seznamu prostředků ve skupině prostředků vyberte **aplikace Function App** .
1. V části **Nastavení** v nabídce vlevo vyberte **Konfigurace** . 
1. Přepněte na kartu **nastavení modulu runtime funkce** v pravém podokně. 
1. Aktualizujte **verzi modulu runtime** na **~ 3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Verze modulu runtime funkce aktualizace":::
6. Na panelu nástrojů vyberte **Uložit**. 
1. V nabídce **Uložit změny** potvrďte výběr **pokračovat**. 

## <a name="publish-the-azure-functions-app"></a>Publikování aplikace Azure Functions

1. Spusťte Visual Studio.
2. Otevřete řešení **EventHubsCaptureEventGridDemo. sln** , které jste stáhli z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) jako součást požadavků. Můžete ji najít ve `/samples/e2e/EventHubsCaptureEventGridDemo` složce. 
3. V Průzkumník řešení klikněte pravým tlačítkem na projekt **FunctionEGDWDumper** a vyberte **publikovat**.
4. Pokud se zobrazí následující obrazovka, vyberte **Spustit**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Tlačítko Start v oddílu publikovat.":::
5. V dialogovém okně **publikovat** vyberte **Azure** pro **cíl** a vyberte **Další**. 
6. Vyberte **Azure Function App (Windows)** a vyberte **Další**.
7. Na kartě **instance funkcí** vyberte své předplatné Azure, rozbalte skupinu prostředků, vyberte aplikace Function App a pak vyberte **Dokončit**. Pokud jste to ještě neudělali, musíte se přihlásit k účtu Azure. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Výběr aplikace Function App":::
8. Na stránce **publikovat** v části **závislosti služby** vyberte **Konfigurovat** pro **úložiště**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Vyberte konfigurovat odkaz pro závislost služby úložiště.":::
1. Na stránce **Konfigurace závislosti** postupujte podle těchto kroků: 
    1. Vyberte **účet úložiště** , který jste vytvořili dříve, a pak vyberte **Další**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Výběr účtu úložiště":::
    10. Zadejte **název připojovacího řetězce** a v možnosti **Uložit připojovací řetězec** vyberte **None (žádné** ) a pak vyberte **Další**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Zadat název připojovacího řetězce":::      
    1. Zrušte zaškrtnutí políčka **soubor kódu jazyka C# a kódování** **tajných** kódů a pak vyberte **Dokončit**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Zkontrolovat souhrn změn":::
1. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Vybrat publikování":::
2. Na kartě, která má otevřenou stránku **Azure Functions** , vyberte v nabídce vlevo možnost  **funkce** . Ověřte, že se v seznamu zobrazuje funkce **EventGridTriggerMigrateData** . Pokud ho nevidíte, zkuste znovu publikovat ze sady Visual Studio a pak stránku aktualizovat na portálu. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Potvrdit vytvoření funkce":::    

Po publikování funkce můžete začít událost odebírat.

## <a name="subscribe-to-the-event"></a>Přihlásíte se k odběru události

1. Na nové kartě nebo v novém okně webového prohlížeče přejděte na [Azure Portal](https://portal.azure.com).
2. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** . 
3. Seznam skupin prostředků vyfiltrujte tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
4. V seznamu vyberte skupinu prostředků.
1. V seznamu prostředků vyberte **obor názvů Event Hubs** .
1. Na stránce **Event Hubs obor názvů** vyberte v nabídce vlevo možnost **události** a pak na panelu nástrojů vyberte **+ odběr události** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Přidat odkaz na odběr události na stránce události pro obor názvů Event Hubs":::
1. Na stránce **vytvořit odběr události** použijte následující postup:
    1. Zadejte název **odběru události**. 
    1. Zadejte název **systémového tématu**. Téma systému poskytuje koncový bod pro odesílatele, který odesílá události. Další informace najdete v tématu [Systémová témata](../articles/event-grid/system-topics.md) .
    1. Jako **Typ koncového bodu** vyberte **Azure Function**.
    1. V části **koncový bod** vyberte odkaz.
    1. Na stránce **Vybrat funkci Azure** postupujte podle těchto kroků, pokud nejsou automaticky vyplněné.
        1. Vyberte předplatné Azure, které obsahuje funkci Azure Functions. 
        1. Vyberte skupinu prostředků pro funkci. 
        1. Vyberte aplikaci Function App.
        1. Vyberte slot nasazení. 
        1. Vyberte funkci **EventGridTriggerMigrateData**. 
    1. Na stránce **Vybrat funkci Azure** vyberte **potvrdit výběr**.
    1. Pak zpátky na stránce **vytvořit odběr události** vyberte **vytvořit**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Vytvoření odběru událostí pomocí funkce" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Ověřte, že odběr události je vytvořený. Přepněte na kartu **odběry událostí** na stránce **události** pro obor názvů Event Hubs. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Potvrdit odběr události" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. V seznamu prostředků ve skupině prostředků vyberte plán App Service (ne App Service). 

## <a name="run-the-app-to-generate-data"></a>Spuštění aplikace, která generuje data
Dokončili jste nastavení centra událostí, vyhradu jste fond SQL (dřív SQL Data Warehouse), aplikaci funkcí Azure functions a odběr událostí. Před spuštěním aplikace, která generuje data z centra událostí, je potřeba nakonfigurovat několik hodnot.

1. V Azure Portal přejděte do skupiny prostředků stejně jako dříve. 
2. Vyberte obor názvů Event Hubs.
3. Na stránce **Event Hubs obor názvů** vyberte v levé nabídce **zásady sdíleného přístupu** .
4. V seznamu zásad vyberte **RootManageSharedAccessKey** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Stránka zásad sdíleného přístupu pro obor názvů Event Hubs":::    
1. Vyberte tlačítko Kopírovat vedle textového pole **připojovací řetězec – primární klíč** . 
1. Vraťte se do řešení sady Visual Studio. 
1. Klikněte pravým tlačítkem na projekt **WindTurbineDataGenerator** a vyberte **nastavit jako spouštěný projekt**. 
1. V projektu WindTurbineDataGenerator otevřete soubor **program.cs**.
1. Nahraďte `<EVENT HUBS NAMESPACE CONNECTION STRING>` připojovacím řetězcem, který jste zkopírovali z portálu. 
1. Nahraďte `<EVENT HUB NAME>` názvem centra událostí. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Sestavte řešení. Spusťte aplikaci **WindTurbineGenerator.exe** . 
7. Po několika minutách můžete na druhé kartě prohlížeče, kde máte okno dotazu otevřené, dotazovat se na tabulku v datovém skladu pro migrovaná data.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Výsledky dotazu](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Monitorování řešení
Tato část vám pomůže s monitorováním nebo řešením řešení potíží. 

### <a name="view-captured-data-in-the-storage-account"></a>Zobrazit zachycená data v účtu úložiště
1. Přejděte do skupiny prostředků a vyberte účet úložiště, který se používá pro zachytávání dat události. 
1. Na stránce **účet úložiště** vyberte v nabídce vlevo možnost **Průzkumník služby Storage (Preview**).
1. Rozbalte položku **kontejnery objektů BLOB** a vyberte možnost **windturbinecapture**. 
1. V pravém podokně otevřete složku s názvem stejné jako **Event Hubs obor názvů** . 
1. Otevřete složku s názvem stejné jako centrum událostí (**hubdatamigration**). 
1. Přejděte ke složkám a zobrazí se soubory AVRO. Tady je příklad:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Zachycený soubor v úložišti" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Ověřte, že Trigger Event Grid vyvolal funkci.
1. Přejděte do skupiny prostředků a vyberte aplikace Function App. 
1. V nabídce vlevo vyberte **funkce** .
1. V seznamu vyberte funkci **EventGridTriggerMigrateData** . 
1. Na stránce **funkce** vyberte v nabídce vlevo možnost **monitor** . 
1. Vyberte **Konfigurovat** a nakonfigurujte Application Insights pro zachycení protokolů vyvolání. 
1. Vytvořte nový prostředek **Application Insights** nebo použijte existující prostředek. 
1. Přejděte zpět na stránku **monitorování** pro danou funkci. 
1. Potvrďte, že je pořád spuštěná klientská aplikace (**WindTurbineDataGenerator**), která odesílá události. Pokud ne, spusťte aplikaci. 
1. Počkejte pár minut (5 minut nebo déle) a kliknutím na tlačítko **aktualizovat** Zobrazte vyvolání funkce.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Vyvolání funkcí":::
1. Chcete-li zobrazit podrobnosti, vyberte vyvolání.

    Event Grid distribuuje data události mezi odběratele. Následující příklad ukazuje data události vygenerovaná při zachycení datových proudů prostřednictvím centra událostí v objektu BLOB. Zejména Všimněte si, že `fileUrl` vlastnost v `data` objektu odkazuje na objekt BLOB v úložišti. Aplikace Function App používá tuto adresu URL k načtení souboru objektu BLOB se zachycenými daty.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Ověřte, že jsou data uložená ve vyhrazeném fondu SQL.
Na kartě prohlížeče, kde máte otevřené okno dotazu, proveďte dotaz na tabulku ve vyhrazeném fondu SQL pro migrovaná data.

![Výsledky dotazu](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

