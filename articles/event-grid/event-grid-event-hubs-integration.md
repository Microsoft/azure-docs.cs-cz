---
title: Integrace služeb Azure Event Grid a Event Hubs
description: Článek popisuje, jak používat služby Azure Event Grid a Event Hubs k migraci dat do služby SQL Data Warehouse.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: aad7a24d8b0e0bc74815cad3604db1cc21a6db96
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163222"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Streamování velkých objemů dat do datového skladu

Azure [Event Grid](overview.md) je inteligentní služba směrování událostí, se kterou můžete reagovat na oznámení aplikací a služeb. Může například aktivovat funkci Azure, která zpracuje data Event Hubs zachycená v úložišti objektů blob v Azure nebo službě Data Lake Store a provede migraci těchto dat do dalších úložišť dat. Tato [ukázka Zachytávání Event Hubs a služby Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) ukazuje, jak pomocí Zachytávání Event Hubs a služby Event Grid bezproblémově migrovat data Event Hubs z úložiště objektů blob do služby SQL Data Warehouse.

![Přehled aplikace](media/event-grid-event-hubs-integration/overview.png)

Při odeslání dat do centra událostí si Capture přetáhne data ze streamu a vygeneruje objekty blob úložiště s daty ve formátu Avro. Když Capture vygeneruje objekt blob, aktivuje událost. Event Grid distribuuje data události mezi odběratele. V tomto případě se data události odešlou koncovému bodu Azure Functions. Součástí dat události je i cesta k vygenerovanému objektu blob. Funkce tuto adresu URL použije k načtení souboru a jeho odeslání do datového skladu.

V tomto článku:

* Nasadíte následující infrastrukturu:
  * Centrum událostí s povolenou službou Capture
  * Účet úložiště pro soubory z Capture
  * Plán služby Azure App Service pro hostování aplikace Function App
  * Aplikace Function App na zpracování události
  * SQL Server na hostování datového skladu
  * SQL Data Warehouse na uložení migrovaných dat
* Vytvoříte tabulku v datovém skladu
* Přidáte kód do aplikace funkcí
* Přihlásíte se k odběru události
* Spustíte aplikaci, která posílá data do centra událostí
* Zobrazíte migrovaná data v datovém skladu

## <a name="about-the-event-data"></a>O datech události

Event Grid distribuuje data události mezi odběratele. V následujícím příkladu vidíte data události pro vytvoření souboru Capture. Všimněte si zejména vlastnosti `fileUrl` objektu `data`. Aplikace funkcí získá tuto hodnotu a použije ji k načtení souboru Capture.

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

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Visual studio 2017 verze 15.3.2 nebo vyšší](https://www.visualstudio.com/vs/) se sadami funkcí pro vývoj desktopových aplikací pomocí .NET, vývoj pro Azure, vývoj pro ASP.NET a web, vývoj aplikací Node.js a pro vývoj v jazyce Python.
* [Ukázkový projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo), který jste si stáhli do svého počítače.

## <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury

Kvůli zjednodušení tohoto článku použijeme k nasazení potřebné infrastruktury šablonu Resource Manageru. Pokud si chcete prohlédnout nasazené prostředky, prohlédněte si [šablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json).

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Po zobrazení výzvy zadejte heslo.

## <a name="create-a-table-in-sql-data-warehouse"></a>Vytvoření tabulky ve službě SQL Data Warehouse

Do datového skladu přidejte tabulku skriptem [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Ke spuštění skriptu použijte Visual Studio nebo editor dotazů na portálu.

Skript ke spuštění:

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

## <a name="publish-the-azure-functions-app"></a>Publikování aplikace Azure Functions

1. Otevřete [ukázkový projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) v sadě Visual Studio 2017 (verze 15.3.2 nebo vyšší).

1. V průzkumníku řešení klikněte pravým tlačítkem na **FunctionEGDWDumper** a vyberte **Publikovat**.

   ![Publikování aplikace funkcí](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Vyberte **Azure Function App** a pak vyberte **Vybrat existující**. Vyberte **Publikovat**.

   ![Cílová aplikace funkcí](media/event-grid-event-hubs-integration/pick-target.png)

1. Vyberte aplikaci funkcí, kterou jste nasadili prostřednictvím šablony. Vyberte **OK**.

   ![Výběr aplikace funkcí](media/event-grid-event-hubs-integration/select-function-app.png)

1. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

   ![Výběr publikování](media/event-grid-event-hubs-integration/select-publish.png)

Po publikování funkce můžete začít událost odebírat.

## <a name="subscribe-to-the-event"></a>Přihlásíte se k odběru události

1. Přejděte na [Azure Portal](https://portal.azure.com/). Vyberte skupinu prostředků a aplikaci funkcí.

   ![Zobrazení aplikace funkcí](media/event-grid-event-hubs-integration/view-function-app.png)

1. Vyberte funkci.

   ![Výběr funkce](media/event-grid-event-hubs-integration/select-function.png)

1. Vyberte **Přidat předplatné Event Gridu**.

   ![Přidat předplatné](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Pojmenujte předplatné Event Gridu. Jako typ události použijte **Obory názvů služby Event Hubs**. Zadejte hodnoty pro výběr instance oboru názvů služby Event Hubs. Jako zadanou hodnotu nechte koncový bod odběratele. Vyberte **Vytvořit**.

   ![Vytvoření odběru](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Spuštění aplikace, která generuje data

Nastavili jste centrum událostí, datový sklad SQL, aplikaci Azure Function App a odběr událostí. Řešení je připravené migrovat data z centra událostí do datového skladu. Před spuštěním aplikace, která generuje data z centra událostí, je potřeba nakonfigurovat několik hodnot.

1. Na portálu vyberte obor názvů centra události. Vyberte **Připojovací řetězce**.

   ![Výběr připojovacích řetězců](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Vyberte **RootManageSharedAccessKey**.

   ![Výběr klíče](media/event-grid-event-hubs-integration/show-root-key.png)

3. Zkopírujte **připojovací řetězec – primární klíč**.

   ![Kopírování klíče](media/event-grid-event-hubs-integration/copy-key.png)

4. Vraťte se do projektu Visual Studio. V projektu WindTurbineDataGenerator otevřete **program.cs**.

5. Nahraďte dvě konstantní hodnoty. Zkopírovanou hodnotu použijte místo **EventHubConnectionString**. Jako název centra událostí použijte **hubdatamigration**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení. Spusťte aplikaci WindTurbineGenerator.exe. Po pár minutách zadejte dotaz do tabulky v datovém skladu a zkontrolujte, že obsahuje migrovaná data.

## <a name="next-steps"></a>Další kroky

* Pokud chcete poznat rozdíly ve službách Azure pro zasílání zpráv, přečtěte si článek [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Úvod do služby Event Hubs Capture najdete v článku o [povolení služby Event Hubs Capture na webu Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Další informace o nastavení a spuštění ukázky najdete v článku s [ukázkou služeb Event Hubs Capture a Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).