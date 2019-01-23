---
title: Odeslat data do datového skladu služby Event Hubs – Event Grid
description: Popisuje, jak migrovat data do SQL Data Warehouse pomocí Azure Event Grid a Event Hubs. Funkce Azure používá k načtení zachytávací soubor.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: 6e8ca9d3a7fbdf1926ac642ac60a37d298af0129
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476849"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Kurz: Streamování velkých objemů dat do datového skladu
Azure [služby Event Grid](overview.md) je služba inteligentního směrování událostí, která umožňuje reagovat na upozornění (události) z aplikací a služeb. Může například aktivovat funkci Azure Functions pro zpracování dat služby Event Hubs, která byla zachycena do služby Azure Blob storage nebo Azure Data Lake Storage a migraci dat do jiných úložišť dat. To [ukázka integrace Event Hubs a služby Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) se dozvíte, jak pomocí služby Event Hubs s využitím služby Event Grid můžou hladce migrovat zachycená data Event Hubs ze služby blob storage do SQL Data Warehouse.

![Přehled aplikace](media/event-grid-event-hubs-integration/overview.png)

Tento diagram znázorňuje pracovní postup řešení, které vytváříte v tomto kurzu: 

1. Data odeslaná do centra událostí Azure je zachycena ve službě Azure blob storage.
2. Po dokončení sběr dat vygeneruje událost a odeslané do služby Azure event grid. 
3. Služby event grid předá tato data událostí k aplikaci Azure function app.
4. Aplikace function app používá adresu URL objektu blob v datech událostí k načtení objektu blob z úložiště. 
5. Aplikace function app migruje data objektů blob do služby Azure SQL data warehouse. 

V tomto článku proveďte následující kroky:

> [!div class="checklist"]
> * Pomocí šablony Azure Resource Manageru k nasazení infrastruktury: centra událostí, účet úložiště, aplikace function app, SQL data warehouse.
> * Vytvoření tabulky v datovém skladu.
> * Přidejte kód do aplikace function app.
> * K události registrovat. 
> * Spuštění aplikace, která odesílá data do centra událostí.
> * Zobrazte migrovaná data v datovém skladu.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual studio 2017 verze 15.3.2 nebo vyšší](https://www.visualstudio.com/vs/) se sadami funkcí pro vývoj desktopových aplikací pomocí .NET, vývoj pro Azure, vývoj pro ASP.NET a web, vývoj aplikací Node.js a pro vývoj v jazyce Python.
* Stáhněte si [EventHubsCaptureEventGridDemo ukázkový projekt](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) k vašemu počítači.

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury
V tomto kroku nasadíte požadovanou infrastrukturu s [šablony Resource Manageru](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Při nasazování šablony vytvoří následující prostředky:

* Centrum událostí s povolenou funkci Capture.
* Účet úložiště pro zachycené soubory. 
* Plán služby App service pro hostování aplikace function app
* Aplikace Function App na zpracování události
* SQL Server na hostování datového skladu
* SQL Data Warehouse na uložení migrovaných dat

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Spustit Azure Cloud Shell na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Vyberte **Cloud Shell** tlačítko v horní části.

    ![portál Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Uvidíte, že službě Cloud Shell se otevře v dolní části prohlížeče.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Ve službě Cloud Shell, pokud se zobrazí možnost výběru mezi **Bash** a **PowerShell**vyberte **Bash**. 
5. Pokud používáte Cloud Shell poprvé, vytvořit účet úložiště tak, že vyberete **vytvořit úložiště**. Azure Cloud Shell vyžaduje účet Azure storage k ukládání některé soubory. 

    ![Vytvoření úložiště cloud shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Počkejte, dokud se inicializuje službě Cloud Shell. 

    ![Vytvoření úložiště cloud shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Použití Azure CLI

1. Vytvořte skupinu prostředků Azure, spuštěním následujícího příkazu rozhraní příkazového řádku: 
    1. Zkopírujte a vložte následující příkaz do okna služby Cloud Shell

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Zadejte název **skupiny prostředků**
    2. Stiskněte **ENTER**. 

        Zde naleznete příklad:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Nasadíte všechny prostředky uvedené v předchozí části (centra událostí, účet úložiště, aplikace functions, SQL data warehouse) spuštěním následujícího příkazu rozhraní příkazového řádku: 
    1. Zkopírujte a vložte příkaz do okna služby Cloud Shell. Alternativně můžete kopírovat/vložit do editoru podle vašeho výběru, nastavte hodnoty a pak zkopírujte do Cloud Shellu příkaz. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název pro obor názvů centra událostí. 
        3. Název centra událostí. Hodnoty můžete ponechat beze (hubdatamigration).
        4. Název systému SQL Server.
        5. Název SQL uživatele a heslo. 
        6. Název pro SQL data warehouse
        7. Název účtu úložiště. 
        8. Název aplikace function app. 
    3.  Stisknutím klávesy **ENTER** do okna služby Cloud Shell a spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte mnoho prostředků. Ve výsledku příkazu Ujistěte se, že nebyly žádné chyby. 
    

### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ve službě Azure Cloud Shell přejděte do režimu Powershellu. Vyberte šipku dolů v levém horním rohu Azure Cloud Shell a vyberte **Powershellu**.

    ![Přepnout na prostředí PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure: 
    1. Zkopírujte a vložte do okna služby Cloud Shell následující příkaz.

        ```powershell
        New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Zadejte název **skupiny prostředků**.
    3. Stiskněte klávesu ENTER. 
3. Nasadíte všechny prostředky uvedené v předchozí části (centra událostí, účet úložiště, aplikace functions, SQL data warehouse) spuštěním následujícího příkazu:
    1. Zkopírujte a vložte příkaz do okna služby Cloud Shell. Alternativně můžete kopírovat/vložit do editoru podle vašeho výběru, nastavte hodnoty a pak zkopírujte do Cloud Shellu příkaz. 

        ```powershell
        New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název pro obor názvů centra událostí. 
        3. Název centra událostí. Hodnoty můžete ponechat beze (hubdatamigration).
        4. Název systému SQL Server.
        5. Název SQL uživatele a heslo. 
        6. Název pro SQL data warehouse
        7. Název účtu úložiště. 
        8. Název aplikace function app. 
    3.  Stisknutím klávesy **ENTER** do okna služby Cloud Shell a spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte mnoho prostředků. Ve výsledku příkazu Ujistěte se, že nebyly žádné chyby. 

### <a name="close-the-cloud-shell"></a>Zavřít Cloud Shell 
Zavřít cloud shell tak, že vyberete **Cloud Shell** tlačítko na portálu (nebo) **X** tlačítko v pravém horním rohu okna služby Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Ověřte, že se prostředky vytvoří

1. Na webu Azure Portal, vyberte **skupiny prostředků** v nabídce vlevo. 
2. Filtrovat seznam skupin prostředků tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
3. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Zkontrolujte, jestli vidíte následující prostředky ve skupině prostředků:

    ![Prostředky ve skupině prostředků](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Vytvoření tabulky ve službě SQL Data Warehouse
Vytvoření tabulky v datovém skladu spuštěním [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) skriptu. Spusťte skript, můžete použít Visual Studio nebo editoru dotazů na portálu. Následující kroky ukazují, jak pomocí editoru dotazů: 

1. V seznamu prostředků ve skupině prostředků vyberte služby SQL data warehouse. 
2. Na stránce SQL data warehouse vyberte **editor dotazů (preview)** v levé nabídce. 

    ![SQL data warehouse stránky](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Zadejte název **uživatele** a **heslo** pro SQL server a vyberte **OK**. 

    ![Ověřování přes server SQL](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. V okně dotazu zkopírujte a spusťte následující skript SQL: 

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

    ![Spustit dotaz SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Nechte tuto kartu nebo okno otevřené, aby mohli ověřit, že data se vytvoří na konci tohoto kurzu. 


## <a name="publish-the-azure-functions-app"></a>Publikování aplikace Azure Functions

1. Spusťte Visual Studio 2017. 
2. Otevřít **EventHubsCaptureEventGridDemo.sln** řešení, které jste stáhli ze služby [Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) jako součást požadavků.
3. V průzkumníku řešení klikněte pravým tlačítkem na **FunctionEGDWDumper** a vyberte **Publikovat**.

   ![Publikování aplikace funkcí](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Pokud se zobrazí následující obrazovka, vyberte **Start**. 

   ![Tlačítko Publikovat Start](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. V **vyberte cíl publikování** stránky, vyberte **vybrat existující** a vyberte možnost **vytvořit profil**. 

   ![Výběr cíle publikování](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na stránce služby App Service vyberte váš **předplatného Azure**, vyberte **aplikace function app** ve vaší skupině prostředků a vyberte **OK**. 

   ![Stránka služby App Service](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

   ![Výběr publikování](media/event-grid-event-hubs-integration/select-publish.png)

Po publikování funkce můžete začít událost odebírat.

## <a name="subscribe-to-the-event"></a>Přihlásíte se k odběru události

1. V nové kartě nebo v novém okně webového prohlížeče přejděte [webu Azure portal](https://portal.azure.com).
2. Na webu Azure Portal, vyberte **skupiny prostředků** v nabídce vlevo. 
3. Filtrovat seznam skupin prostředků tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
4. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků](media/event-grid-event-hubs-integration/select-resource-group.png)
4. V seznamu vyberte plán služby App Service. 
5. Na stránce plán služby App Service vyberte **aplikace** v levé nabídce a vyberte aplikaci function app. 

    ![Vyberte vaši aplikaci functions](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Rozbalte aplikaci function app, Rozbalit funkce a pak vyberte vaši funkci. 

    ![Výběr funkce Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Vyberte **přidat předplatné Event gridu** na panelu nástrojů. 
8. V **vytvořit odběr služby Event Grid** stránce, proveďte následující akce: 
    1. V **TÉMA obsahující podrobnosti** části, proveďte následující akce:
        1. Vyberte své předplatné Azure.
        2. Vyberte skupinu prostředků Azure.
        3. Vyberte obor názvů služby Event Hubs.
    2. V **podrobnosti PŘEDPLATNÉHO událostí** stránky, zadejte název předplatného (třeba: captureEventSub) a vyberte **vytvořit**. 

        ![Vytvořit odběr služby Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Spuštění aplikace, která generuje data
Nastavili jste centrum událostí, datový sklad SQL, aplikaci Azure Function App a odběr událostí. Před spuštěním aplikace, která generuje data z centra událostí, je potřeba nakonfigurovat několik hodnot.

1. Na webu Azure Portal přejděte do vaší skupiny prostředků, jako jste to udělali dříve. 
2. Vyberte obor názvů služby Event Hubs.
3. V **Event Hubs Namespace** stránce **zásady sdíleného přístupu** v nabídce vlevo.
4. Vyberte **RootManageSharedAccessKey** v seznamu zásad. 
5. Vyberte tlačítko kopírování vedle položky **připojovací řetězec – primární klíč** textového pole. 

    ![Připojovací řetězec pro obor názvů centra událostí](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Vraťte se do řešení sady Visual Studio. 
2. V projektu WindTurbineDataGenerator otevřete **program.cs**.
5. Nahraďte dvě konstantní hodnoty. Zkopírovanou hodnotu použijte místo **EventHubConnectionString**. Jako název centra událostí použijte **hubdatamigration**. Pokud jste použili jiný název centra událostí, zadejte tento název. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení. Spustit **WindTurbineGenerator.exe** aplikace. 
7. Po pár minutách zadejte dotaz do tabulky v datovém skladu a zkontrolujte, že obsahuje migrovaná data.

    ![Výsledky dotazu](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Data události vygenerovaná v Centru událostí
Event Grid distribuuje data události mezi odběratele. Následující příklad zobrazuje data událostí, které jsou generovány, pokud data prostřednictvím centra událostí se zaznamenávají do objektu BLOB. Zejména, Všimněte si, že `fileUrl` vlastnost `data` objektu odkazuje na objekt blob v úložišti. Aplikace function app používá tuto adresu URL k načtení souboru objektu blob s zachycená data.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Další postup

* Pokud chcete poznat rozdíly ve službách Azure pro zasílání zpráv, přečtěte si článek [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Úvod do služby Event Hubs Capture najdete v článku o [povolení služby Event Hubs Capture na webu Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Další informace o nastavení a spuštění ukázky najdete v článku s [ukázkou služeb Event Hubs Capture a Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).