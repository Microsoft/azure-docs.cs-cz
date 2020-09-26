---
title: 'Kurz: odeslání Event Hubs dat do datového skladu – Event Grid'
description: 'Kurz: popisuje, jak pomocí Azure Event Grid a Event Hubs migrovat data do služby Azure synapse Analytics. K načtení digitalizačního souboru používá funkci Azure Functions.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 64d4b9769e1a228294bd7d8741f6f4b1260fb0dd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270543"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Kurz: streamování velkých objemů dat do datového skladu
Azure [Event Grid](overview.md) je inteligentní služba Směrování událostí, která umožňuje reagovat na oznámení (události) z aplikací a služeb. Například může aktivovat funkci Azure Functions pro zpracování Event Hubs dat zachycených do úložiště objektů BLOB v Azure nebo v Azure Data Lake Storage a migrovat data do jiných úložišť dat. V [ukázce Event Hubs a Event Grid Integration Sample](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) se dozvíte, jak používat Event Hubs se Event Grid k bezproblémové migraci zachycených Event Hubs dat z úložiště objektů blob do služby Azure synapse Analytics (dříve SQL Data Warehouse).

![Přehled aplikace](media/event-grid-event-hubs-integration/overview.png)

Tento diagram znázorňuje pracovní postup řešení, které sestavíte v tomto kurzu: 

1. Data odesílaná do centra událostí Azure jsou zachycena v úložišti objektů BLOB v Azure.
2. Po dokončení zachycení dat se vygeneruje událost a pošle se do služby Azure Event Grid. 
3. Event Grid přepošle tato data události do aplikace funkce Azure.
4. Aplikace Function App používá k načtení objektu BLOB z úložiště adresu URL objektu BLOB v datech události. 
5. Aplikace Function App migruje data objektů blob do služby Azure synapse Analytics. 

V tomto článku proveďte následující kroky:

> [!div class="checklist"]
> * Použití šablony Azure Resource Manager k nasazení infrastruktury: centra událostí, účet úložiště, aplikace Function App, synapse Analytics.
> * Vytvořte tabulku v datovém skladu.
> * Přidejte kód do aplikace Function App.
> * Přihlaste se k odběru události. 
> * Spusťte aplikaci, která odesílá data do centra událostí.
> * Zobrazení migrovaných dat v datovém skladu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
* [Visual studio 2019](https://www.visualstudio.com/vs/) s pracovními postupy pro: vývoj desktopových aplikací pro .NET, vývoj pro Azure, vývoj pro ASP.NET a vývoj webů Node.js, vývoj a vývoj v Pythonu
* Stáhněte si [vzorový projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) do svého počítače.

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury
V tomto kroku nasadíte požadovanou infrastrukturu pomocí [šablony Správce prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Při nasazení šablony se vytvoří následující prostředky:

* Centrum událostí s povolenou funkcí Capture
* Účet úložiště pro zachycené soubory. 
* Plán služby App Service pro hostování aplikace Function App
* Aplikace Function App na zpracování události
* SQL Server na hostování datového skladu
* Azure synapse Analytics pro ukládání migrovaných dat

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Spustit Azure Cloud Shell v Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. V horní části vyberte tlačítko **Cloud Shell** .

    ![portál Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. V dolní části prohlížeče se zobrazí Cloud Shell otevřít.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Pokud se v Cloud Shell zobrazí možnost výběru mezi **bash** a **PowerShellem**, vyberte **bash**. 
5. Pokud Cloud Shell používáte poprvé, vytvořte účet úložiště tak, že vyberete **vytvořit úložiště**. Azure Cloud Shell vyžaduje účet úložiště Azure pro ukládání některých souborů. 

    ![Snímek obrazovky, který zobrazuje dialogové okno nemáte připojené k úložišti, s vybraným tlačítkem vytvořit úložiště](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Počkejte, dokud se Cloud Shell neinicializuje. 

    ![Vytvoření úložiště pro Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Použití Azure CLI

1. Vytvořte skupinu prostředků Azure spuštěním následujícího příkazu CLI: 
    1. Zkopírujte následující příkaz a vložte ho do okna Cloud Shell. Pokud chcete, změňte název a umístění skupiny prostředků.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Stiskněte klávesu **ENTER**. 

        Tady je příklad:
    
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
2. Spusťte následující příkaz CLI a nasaďte všechny prostředky uvedené v předchozí části (centrum událostí, účet úložiště, aplikace Functions, Azure synapse Analytics): 
    1. Zkopírujte příkaz a vložte ho do okna Cloud Shell. Alternativně můžete chtít zkopírovat nebo vložit do editoru podle svého výběru, nastavit hodnoty a potom zkopírovat příkaz do Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název oboru názvů centra událostí. 
        3. Název centra událostí Hodnotu můžete ponechat tak, jak je (hubdatamigration).
        4. Název systému SQL Server.
        5. Jméno uživatele a heslo SQL 
        6. Název pro Azure synapse Analytics
        7. Název účtu úložiště 
        8. Název aplikace Function App 
    3.  Stisknutím klávesy **ENTER** v okně Cloud Shell spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte spoustu prostředků. V důsledku příkazu se ujistěte, že nedošlo k žádným chybám. 
    

### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. V Azure Cloud Shell přepněte do režimu PowerShell. V levém horním rohu Azure Cloud Shell vyberte šipku dolů a vyberte **PowerShell**.

    ![Přepnout do PowerShellu](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure: 
    1. Zkopírujte následující příkaz a vložte ho do okna Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Zadejte název **skupiny prostředků**.
    3. Stiskněte ENTER. 
3. Nasaďte všechny prostředky uvedené v předchozí části (centrum událostí, účet úložiště, aplikace Functions, Azure synapse Analytics) spuštěním následujícího příkazu:
    1. Zkopírujte příkaz a vložte ho do okna Cloud Shell. Alternativně můžete chtít zkopírovat nebo vložit do editoru podle svého výběru, nastavit hodnoty a potom zkopírovat příkaz do Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Zadejte hodnoty pro následující entity:
        1. Název skupiny prostředků, kterou jste vytvořili dříve.
        2. Název oboru názvů centra událostí. 
        3. Název centra událostí Hodnotu můžete ponechat tak, jak je (hubdatamigration).
        4. Název systému SQL Server.
        5. Jméno uživatele a heslo SQL 
        6. Název pro Azure synapse Analytics
        7. Název účtu úložiště 
        8. Název aplikace Function App 
    3.  Stisknutím klávesy **ENTER** v okně Cloud Shell spusťte příkaz. Tento proces může chvíli trvat, protože vytváříte spoustu prostředků. V důsledku příkazu se ujistěte, že nedošlo k žádným chybám. 

### <a name="close-the-cloud-shell"></a>Zavřít Cloud Shell 
Kliknutím na tlačítko **Cloud Shell** na portálu (nebo) na tlačítko **X** v pravém horním rohu okna Cloud Shell zavřete Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Ověření vytvoření prostředků

1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** . 
2. Seznam skupin prostředků vyfiltrujte tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
3. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků.](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Ověřte, že se ve skupině prostředků zobrazují tyto prostředky:

    ![Prostředky ve skupině prostředků](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Vytvoření tabulky ve službě Azure synapse Analytics
Vytvořte tabulku v datovém skladu spuštěním skriptu [CreateDataWarehouseTable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) . Chcete-li spustit skript, můžete použít aplikaci Visual Studio nebo Editor dotazů na portálu. Následující kroky ukazují, jak používat Editor dotazů: 

1. V seznamu prostředků ve skupině prostředků vyberte svůj **synapse fond SQL (datový sklad)**. 
2. Na stránce Azure synapse Analytics v levé nabídce vyberte **Editor dotazů (Preview)** . 

    ![Stránka analýzy Azure synapse](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Zadejte jméno **uživatele** a **heslo** pro systém SQL Server a vyberte **OK**. Možná budete muset přidat IP adresu klienta do brány firewall pro úspěšné přihlášení k systému SQL Server. 

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
5. Nechejte tuto kartu nebo okno otevřené, abyste mohli ověřit, že se data vytvoří na konci kurzu. 

### <a name="update-the-function-runtime-version"></a>Aktualizace verze modulu runtime funkce

1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** .
2. Vyberte skupinu prostředků, ve které aplikace Function App existuje. 
3. V seznamu prostředků ve skupině prostředků vyberte Function App typu **App Service** .
4. V části **Nastavení** v nabídce vlevo vyberte **Konfigurace** . 
5. Přepněte na kartu **nastavení modulu runtime funkce** v pravém podokně. 
5. Aktualizujte **verzi modulu runtime** na **~ 3**. 

    ![Verze modulu runtime funkce aktualizace](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Publikování aplikace Azure Functions

1. Spusťte Visual Studio.
2. Otevřete řešení **EventHubsCaptureEventGridDemo. sln** , které jste stáhli z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) jako součást požadavků.
3. V průzkumníku řešení klikněte pravým tlačítkem na **FunctionEGDWDumper** a vyberte **Publikovat**.

   ![Publikování aplikace funkcí](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Pokud se zobrazí následující obrazovka, vyberte **Spustit**. 

   ![Snímek obrazovky, který zobrazuje Visual studia s tlačítkem Start v oddílu Publish.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. V dialogovém okně **publikovat** vyberte **Azure** pro **cíl**a vyberte **Další**. 

   ![Tlačítko pro spuštění publikování](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Vyberte **Azure Function App (Windows)** a vyberte **Další**. 

   ![Výběr služby Azure Function App – Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. Na kartě **instance funkcí** vyberte své předplatné Azure, rozbalte skupinu prostředků, vyberte aplikace Function App a pak vyberte **Dokončit**. Pokud jste to ještě neudělali, musíte se přihlásit k účtu Azure. 

   ![Výběr aplikace Function App](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. V části **závislosti služby** vyberte **Konfigurovat**.
9. Na stránce **Konfigurace závislosti** vyberte účet úložiště, který jste vytvořili dříve, a pak vyberte **Další**. 
10. Ponechte nastavení název a hodnota připojovacího řetězce a vyberte **Další**.
11. Zrušte zaškrtnutí možnosti **úložiště tajných klíčů** a pak vyberte **Dokončit**.  
8. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

   ![Výběr publikování](media/event-grid-event-hubs-integration/select-publish.png)

Po publikování funkce můžete začít událost odebírat.

## <a name="subscribe-to-the-event"></a>Přihlásíte se k odběru události

1. Na nové kartě nebo v novém okně webového prohlížeče přejděte na [Azure Portal](https://portal.azure.com).
2. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** . 
3. Seznam skupin prostředků vyfiltrujte tak, že do vyhledávacího pole zadáte název vaší skupiny prostředků. 
4. V seznamu vyberte skupinu prostředků.

    ![Vyberte skupinu prostředků.](media/event-grid-event-hubs-integration/select-resource-group.png)
4. V seznamu prostředků ve skupině prostředků vyberte plán App Service (ne App Service). 
5. Na stránce plán App Service v nabídce vlevo vyberte **aplikace** a vyberte aplikace Function App. 

    ![Výběr aplikace Functions](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Rozbalte aplikaci Function App, rozbalte položku funkce a potom vyberte svou funkci. 
7. Na panelu nástrojů vyberte **přidat Event Grid předplatné** . 

    ![Výběr funkce Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Na stránce **vytvořit Event Grid předplatné** proveďte následující akce: 
    1. Na stránce **Podrobnosti odběru události** zadejte název předplatného (například: captureEventSub) a vyberte **vytvořit**. 
    2. V části **Podrobnosti o tématu** proveďte následující akce:
        1. Pro **typy témat**vyberte **Event Hubs obory názvů** . 
        2. Vyberte své předplatné Azure.
        2. Vyberte skupinu prostředků Azure.
        3. Vyberte svůj obor názvů Event Hubs.
    3. V části **typy událostí** potvrďte, že je vybraný **zachytávací soubor** , který se má **filtrovat na typy událostí**. 
    4. V části **Podrobnosti o koncovém bodu** potvrďte, že **Typ koncového bodu** je nastavený na **funkci Azure Functions** , a **koncový bod** je nastavený na funkci Azure Functions. 
    
        ![Vytvoření předplatného Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Spuštění aplikace, která generuje data
Dokončili jste nastavení centra událostí, Azure synapse Analytics, aplikace Azure functions a odběru událostí. Před spuštěním aplikace, která generuje data z centra událostí, je potřeba nakonfigurovat několik hodnot.

1. V Azure Portal přejděte do skupiny prostředků stejně jako dříve. 
2. Vyberte obor názvů Event Hubs.
3. Na stránce **Event Hubs obor názvů** vyberte v levé nabídce **zásady sdíleného přístupu** .
4. V seznamu zásad vyberte **RootManageSharedAccessKey** . 
5. Vyberte tlačítko Kopírovat vedle textového pole **připojovací řetězec – primární klíč** . 

    ![Připojovací řetězec pro obor názvů centra událostí](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Vraťte se do řešení sady Visual Studio. 
2. V projektu WindTurbineDataGenerator otevřete soubor **program.cs**.
5. Nahraďte dvě konstantní hodnoty. Zkopírovanou hodnotu použijte místo **EventHubConnectionString**. Jako název centra událostí použijte **hubdatamigration**. Pokud jste pro centrum událostí použili jiný název, zadejte tento název. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení. Spusťte aplikaci **WindTurbineGenerator.exe** . 
7. Po pár minutách zadejte dotaz do tabulky v datovém skladu a zkontrolujte, že obsahuje migrovaná data.

    ![Výsledky dotazu](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Data události generovaná centrem událostí
Event Grid distribuuje data události mezi odběratele. Následující příklad ukazuje data události vygenerovaná při zachycení datových proudů prostřednictvím centra událostí v objektu BLOB. Zejména Všimněte si, že `fileUrl` vlastnost v `data` objektu odkazuje na objekt BLOB v úložišti. Aplikace Function App používá tuto adresu URL k načtení souboru objektu BLOB se zachycenými daty.

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
