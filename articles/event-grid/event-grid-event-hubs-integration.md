---
title: 'Kurz: Odesílání dat centra událostí do datového skladu – mřížka událostí'
description: 'Kurz: Popisuje, jak používat Azure Event Grid a Event Hubs k migraci dat do datového skladu SQL. Používá funkci Azure k načtení souboru sběru.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718185"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Kurz: Streamování velkých dat do datového skladu
Azure [Event Grid](overview.md) je inteligentní služba směrování událostí, která umožňuje reagovat na oznámení (události) z aplikací a služeb. Například může aktivovat funkci Azure pro zpracování dat centra událostí, která byla zachycena do úložiště objektů blob Azure nebo Azure Data Lake Storage a migrovat data do jiných úložišť dat. Tato [ukázka centra událostí a mřížky událostí](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) ukazuje, jak pomocí event hubů s event gridem bezproblémově migrovat zachycená data event hubů z úložiště objektů blob do datového skladu SQL.

![Přehled aplikace](media/event-grid-event-hubs-integration/overview.png)

Tento diagram znázorňuje pracovní postup řešení, které vytvoříte v tomto kurzu: 

1. Data odeslaná do centra událostí Azure se zachycují v úložišti objektů blob Azure.
2. Po dokončení sběru dat se vygeneruje událost a odešle se do sítě událostí Azure. 
3. Mřížka událostí předá tato data události do aplikace funkce Azure.
4. Aplikace funkce používá adresu URL objektu blob v datech události k načtení objektu blob z úložiště. 
5. Aplikace funkce migruje data objektu blob do datového skladu Azure SQL. 

V tomto článku provedete následující kroky:

> [!div class="checklist"]
> * K nasazení infrastruktury použijte šablonu Azure Resource Manageru: centrum událostí, účet úložiště, aplikaci funkcí, datový sklad SQL.
> * Vytvořte tabulku v datovém skladu.
> * Přidejte kód do aplikace funkce.
> * Přihlaste se k odběru akce. 
> * Spusťte aplikaci, která odesílá data do centra událostí.
> * Zobrazení migrovaných dat v datovém skladu

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* [Visual studio 2019](https://www.visualstudio.com/vs/) s úlohami pro: vývoj desktopů .NET, vývoj Azure, ASP.NET a vývoj webu, vývoj Node.js a vývoj Pythonu.
* Stáhněte [ukázkový projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) do počítače.

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury
V tomto kroku nasadíte požadovanou infrastrukturu pomocí [šablony Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Při nasazení šablony se vytvoří následující prostředky:

* Centrum událostí s povolenou funkcí Zachycení.
* Účet úložiště pro zachycené soubory. 
* Plán služby App pro hostování aplikace funkce
* Aplikace Function App na zpracování události
* SQL Server na hostování datového skladu
* SQL Data Warehouse na uložení migrovaných dat

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Spuštění Azure Cloud Shellu na webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Nahoře vyberte tlačítko **Cloud Shell.**

    ![portál Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. V dolní části prohlížeče se zobrazí prostředí Cloud Shell.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Pokud se v prostředí Cloud Shell zobrazí možnost vybrat mezi **bashem** a **PowerShellem**, vyberte **Bash**. 
5. Pokud používáte cloudové prostředí poprvé, vytvořte účet úložiště výběrem **možnosti Vytvořit úložiště**. Azure Cloud Shell vyžaduje účet úložiště Azure pro ukládání některých souborů. 

    ![Vytvoření úložiště pro cloudshell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Počkejte, až se prostředí Cloud Shell inicializuje. 

    ![Vytvoření úložiště pro cloudshell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Použití Azure CLI

1. Vytvořte skupinu prostředků Azure spuštěním následujícího příkazu příkazu příkazu příkazu: 
    1. Zkopírujte a vložte následující příkaz do okna Cloud Shellu

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Zadejte název **skupiny prostředků.**
    2. Stiskněte **klávesu ENTER**. 

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
2. Nasadit všechny prostředky uvedené v předchozí části (centrum událostí, účet úložiště, aplikace functions, datový sklad SQL) spuštěním následujícího příkazu příkazu příkazu příkazu příkazu:Deploy all the resources mentiond in the previous section (event hub, storage account, functions app, SQL data warehouse) by running the following CLI command: 
    1. Zkopírujte a vložte příkaz do okna Cloud Shell. Případně můžete chtít zkopírovat nebo vložit do editoru podle vašeho výběru, nastavit hodnoty a pak zkopírovat příkaz do prostředí Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název oboru názvů centra událostí. 
        3. Název centra událostí. Hodnotu můžete ponechat tak, jak je (hubdatamigration).
        4. Název serveru SQL.
        5. Jméno uživatele SQL a heslo. 
        6. Název datového skladu SQL
        7. Název účtu úložiště. 
        8. Název aplikace funkce. 
    3.  Stisknutím **klávesy ENTER** v okně prostředí Cloud Spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte spoustu prostředků. V důsledku příkazu se ujistěte, že nedošlo k žádným chybám. 
    

### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. V Azure Cloud Shell přepněte do režimu PowerShell. Vyberte šipku dolů v levém horním rohu Azure Cloud Shell u a vyberte **PowerShell**.

    ![Přepnutí do PowerShellu](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Vytvořte skupinu prostředků Azure spuštěním následujícího příkazu: 
    1. Zkopírujte a vložte následující příkaz do okna Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Zadejte název **skupiny prostředků**.
    3. Stiskněte ENTER. 
3. Nasadit všechny prostředky uvedené v předchozí části (centrum událostí, účet úložiště, aplikace functions, datový sklad SQL) spuštěním následujícího příkazu:
    1. Zkopírujte a vložte příkaz do okna Cloud Shell. Případně můžete chtít zkopírovat nebo vložit do editoru podle vašeho výběru, nastavit hodnoty a pak zkopírovat příkaz do prostředí Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název oboru názvů centra událostí. 
        3. Název centra událostí. Hodnotu můžete ponechat tak, jak je (hubdatamigration).
        4. Název serveru SQL.
        5. Jméno uživatele SQL a heslo. 
        6. Název datového skladu SQL
        7. Název účtu úložiště. 
        8. Název aplikace funkce. 
    3.  Stisknutím **klávesy ENTER** v okně prostředí Cloud Spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte spoustu prostředků. V důsledku příkazu se ujistěte, že nedošlo k žádným chybám. 

### <a name="close-the-cloud-shell"></a>Zavření prostředí cloudu 
Zavřete prostředí cloud u tlačítka **Cloud Shell** na portálu (nebo) x tlačítko v pravém horním rohu okna Cloud Shell. **X** 

### <a name="verify-that-the-resources-are-created"></a>Ověření, zda jsou prostředky vytvořeny

1. Na webu Azure Portal vyberte **skupiny prostředků** v levé nabídce. 
2. Seznam skupin prostředků můžete filtrovat zadáním názvu skupiny prostředků do vyhledávacího pole. 
3. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků.](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Zkontrolujte, zda se ve skupině prostředků zobrazují následující prostředky:

    ![Prostředky ve skupině prostředků](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Vytvoření tabulky ve službě SQL Data Warehouse
Vytvořte tabulku v datovém skladu spuštěním skriptu [CreateDataWarehouseTable.sql.](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) Chcete-li spustit skript, můžete použít Visual Studio nebo Editor dotazů na portálu. Následující kroky ukazují, jak používat Editor dotazů: 

1. V seznamu prostředků ve skupině prostředků vyberte datový sklad SQL. 
2. Na stránce datového skladu SQL vyberte v levé nabídce **editor dotazů (náhled).** 

    ![Stránka datového skladu SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Zadejte jméno **uživatele** a **heslo** pro sql server a vyberte **OK**. 

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
5. Ponechte tuto kartu nebo okno otevřené, abyste mohli ověřit, zda jsou data vytvořena na konci kurzu. 


## <a name="publish-the-azure-functions-app"></a>Publikování aplikace Azure Functions

1. Spusťte Visual Studio.
2. Otevřete řešení **EventHubsCaptureEventGridDemo.sln,** které jste stáhli z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) jako součást předpokladů.
3. V průzkumníku řešení klikněte pravým tlačítkem na **FunctionEGDWDumper** a vyberte **Publikovat**.

   ![Publikování aplikace funkcí](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Pokud se zobrazí následující obrazovka, vyberte **Spustit**. 

   ![Tlačítko Spustit publikování](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na stránce **Vybrat cílovou** publikování vyberte možnost **Vybrat existující** a vyberte **Vytvořit profil**. 

   ![Výběr cíle publikování](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na stránce Služba Aplikace vyberte **předplatné Azure**, vyberte **aplikaci funkcí** ve skupině prostředků a vyberte **OK**. 

   ![Stránka Služby aplikace](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

   ![Výběr publikování](media/event-grid-event-hubs-integration/select-publish.png)

Po publikování funkce můžete začít událost odebírat.

## <a name="subscribe-to-the-event"></a>Přihlásíte se k odběru události

1. V nové kartě nebo v novém okně webového prohlížeče přejděte na [portál Azure](https://portal.azure.com).
2. Na webu Azure Portal vyberte **skupiny prostředků** v levé nabídce. 
3. Seznam skupin prostředků můžete filtrovat zadáním názvu skupiny prostředků do vyhledávacího pole. 
4. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků.](media/event-grid-event-hubs-integration/select-resource-group.png)
4. V seznamu vyberte plán Služby aplikací. 
5. Na stránce Plán služby App Service vyberte **Aplikace** v levé nabídce a vyberte aplikaci funkce. 

    ![Výběr aplikace funkce](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Rozbalte aplikaci funkcí, rozbalte funkce a vyberte svou funkci. 

    ![Výběr funkce Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Na panelu nástrojů vyberte **Přidat odběr mřížky událostí.** 
8. Na stránce **Vytvořit odběr mřížky událostí** proveďte následující akce: 
    1. V části **PODROBNOSTI O TÉMATU** proveďte následující akce:
        1. Vyberte své předplatné Azure.
        2. Vyberte skupinu prostředků Azure.
        3. Vyberte obor názvů Event Hubs.
    2. Na stránce **PODROBNOSTI ODBĚRU UDÁLOSTI** zadejte název předplatného (například captureEventSub) a vyberte **Vytvořit**. 

        ![Vytvořit předplatné sítě událostí](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Spuštění aplikace, která generuje data
Nastavili jste centrum událostí, datový sklad SQL, aplikaci Azure Function App a odběr událostí. Před spuštěním aplikace, která generuje data z centra událostí, je potřeba nakonfigurovat několik hodnot.

1. Na webu Azure Portal přejděte do skupiny prostředků stejně jako dříve. 
2. Vyberte obor názvů Event Hubs.
3. Na stránce **Obor názvů centra eventhubů** vyberte v levé nabídce **zásady sdíleného přístupu.**
4. V seznamu zásad vyberte **RootManageSharedAccessKey.** 
5. Vyberte tlačítko kopírování vedle textového pole **Připojovací řetězec primární klíče.** 

    ![Připojovací řetězec pro obor názvů centra událostí](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Vraťte se k řešení sady Visual Studio. 
2. V projektu WindTurbineDataGenerator otevřete soubor **program.cs**.
5. Nahraďte dvě konstantní hodnoty. Zkopírovanou hodnotu použijte místo **EventHubConnectionString**. Jako název centra událostí použijte **hubdatamigration**. Pokud jste pro centrum událostí použili jiný název, zadejte tento název. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení. Spusťte aplikaci **WindTurbineGenerator.exe.** 
7. Po pár minutách zadejte dotaz do tabulky v datovém skladu a zkontrolujte, že obsahuje migrovaná data.

    ![Výsledky dotazu](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Data událostí vygenerovaná centrem událostí
Event Grid distribuuje data události mezi odběratele. Následující příklad ukazuje data událostí generovaná při streamování dat prostřednictvím centra událostí zachycených v objektu blob. Všimněte si `fileUrl` zejména vlastnost `data` v objektu odkazuje na objekt blob v úložišti. Aplikace funkce používá tuto adresu URL k načtení souboru objektů blob se zachycenými daty.

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


## <a name="next-steps"></a>Další kroky

* Pokud chcete poznat rozdíly ve službách Azure pro zasílání zpráv, přečtěte si článek [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Úvod do služby Event Hubs Capture najdete v článku o [povolení služby Event Hubs Capture na webu Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Další informace o nastavení a spuštění ukázky najdete v článku s [ukázkou služeb Event Hubs Capture a Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
