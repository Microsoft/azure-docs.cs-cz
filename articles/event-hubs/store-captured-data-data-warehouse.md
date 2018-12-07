---
title: Migrace dat služby Azure Event Hubs do služby SQL Data Warehouse | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí funkce Azure aktivované Event Gridem zachytávat data z centra událostí do služby SQL Data Warehouse.
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 12/06/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 56701f039ae2de6d136a2a06fbeb9e99359a0029
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017061"
---
# <a name="migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Migrace do SQL Data Warehouse s využitím služby Event Grid a Azure Functions zachycená data Event Hubs

[Zachytávání](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) služby Event Hubs představuje nejjednodušší způsob, jak automaticky doručovat streamovaná data ve službě Event Hubs do služby Azure Blob Storage nebo Azure Data Lake Store. Následně můžete tato data zpracovávat a doručovat do libovolných dalších cílových úložišť, například do služby SQL Data Warehouse nebo Cosmos DB. V tomto kurzu se dozvíte, jak pomocí funkce Azure aktivované [Event Gridem](https://docs.microsoft.com/azure/event-grid/overview) zachytávat data z centra událostí do služby SQL Data Warehouse.

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Nejprve vytvoříte centrum událostí s povolenou funkcí **Zachytávání** a jako cíl nastavíte službu Azure Blob Storage. Data generovaná aplikací WindTurbineGenerator se streamují do centra událostí a automaticky se zachytávají do služby Azure Storage jako soubory Avro. 
*   Pak vytvoříte předplatné Azure Event Gridu s oborem názvů služby Event Hubs jako zdrojem a koncovým bodem funkce Azure jako cílem.
*   Vždy, když funkce Zachytávání služby Event Hubs doručí soubor Avro do objektu blob služby Azure Storage, oznámí Event Grid funkci Azure identifikátor URI objektu blob. Funkce pak provede migraci dat z objektu blob do služby SQL Data Warehouse.

V tomto kurzu provedete následující akce: 

> [!div class="checklist"]
> * Nasazení infrastruktury
> * Publikování kódu do aplikace Functions
> * Vytvoření odběru Event Gridu v aplikaci Functions
> * Streamování ukázkových dat do centra událostí 
> * Ověření zachycených dat ve službě SQL Data Warehouse

## <a name="prerequisites"></a>Požadavky

- [Sada Visual studio 2017 verze 15.3.2 nebo novější](https://www.visualstudio.com/vs/). Při instalaci nezapomeňte nainstalovat následující sady funkcí: Vývoj desktopových aplikací .NET, Vývoj pro Azure, Vývoj pro ASP.NET a web, Vývoj v Node.js a Vývoj v Pythonu.
- Stáhněte si [ukázku z Gitu](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo). Ukázkové řešení obsahuje následující komponenty:
    - *WindTurbineDataGenerator* – Jednoduchý vydavatel odesílající ukázková data větrné turbíny do centra událostí s povolenou funkcí Zachytávání.
    - *FunctionDWDumper* – Funkce Azure, která přijímá oznámení Event Gridu při zachycení souboru Avro do objektu blob služby Azure Storage. Přijme cestu URI objektu blob, načte jeho obsah a odešle tato data do služby SQL Data Warehouse.

### <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury
Pomocí Azure PowerShellu nebo Azure CLI nasaďte infrastrukturu potřebnou pro tento kurz s využitím této [šablony Azure Resource Manageru](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Tato šablona vytvoří následující prostředky:

-   Centrum událostí s povolenou funkci Zachytávání
-   Účet úložiště pro zachycená data událostí
-   Plán služby Azure App Service pro hostování aplikace Functions
-   Aplikace funkcí pro zpracování zachycených souborů událostí
-   SQL Server pro hostování služby Data Warehouse
-   SQL Data Warehouse na uložení migrovaných dat

Následující části obsahují příkazy Azure CLI a Azure PowerShellu pro nasazení požadované infrastruktury pro tento kurz. Před spuštěním příkazů aktualizujte názvy následujících objektů: 

- Skupina prostředků Azure 
- Oblast pro skupinu prostředků
- Obor názvů služby Event Hubs
- Centrum událostí
- Server SQL Azure
- Uživatel SQL (a heslo)
- Databáze SQL Azure
- Úložiště Azure 
- Aplikace Azure Functions

Vytvoření všech artefaktů Azure těmito skripty nějakou dobu trvá. Než budete pokračovat, počkejte na dokončení skriptu. Pokud nasazení z nějakého důvodu selže, odstraňte skupinu prostředků, opravte nahlášený problém a spusťte příkaz znovu. 

#### <a name="azure-cli"></a>Azure CLI
Pokud chcete šablonu nasadit pomocí Azure CLI, použijte následující příkazy:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Pokud chcete šablonu nasadit pomocí PowerShellu, použijte následující příkazy:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Vytvoření tabulky ve službě SQL Data Warehouse 
Vytvořte ve své službě SQL Data Warehouse tabulku spuštěním skriptu [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) v sadě [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), aplikaci [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) nebo Editoru dotazů na portálu. 

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

## <a name="publish-code-to-the-functions-app"></a>Publikování kódu do aplikace Functions

1. Otevřete řešení *EventHubsCaptureEventGridDemo.sln* v sadě Visual Studio 2017 (verze 15.3.2 nebo novější). 

1. V průzkumníku řešení klikněte pravým tlačítkem na *FunctionEGDWDumper* a vyberte **Publikovat**.

   ![Publikování aplikace funkcí](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Vyberte **Azure Function App** a pak vyberte **Vybrat existující**. Vyberte **Publikovat**.

   ![Cílová aplikace funkcí](./media/store-captured-data-data-warehouse/pick-target.png)

1. Vyberte aplikaci funkcí, kterou jste nasadili prostřednictvím šablony. Vyberte **OK**.

   ![Výběr aplikace funkcí](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Jakmile Visual Studio nakonfiguruje profil, vyberte **Publikovat**.

   ![Výběr publikování](./media/store-captured-data-data-warehouse/select-publish.png)

Po publikování funkce můžete začít odebírat událost zachycení ze služby Event Hub.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Vytvoření odběru Event Gridu v aplikaci Functions
 
1. Přejděte na [Azure Portal](https://portal.azure.com/). Vyberte skupinu prostředků a aplikaci funkcí.

   ![Zobrazení aplikace funkcí](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Vyberte funkci.

   ![Výběr funkce](./media/store-captured-data-data-warehouse/select-function.png)

1. Vyberte **Přidat předplatné Event Gridu**.

   ![Přidat předplatné](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Pojmenujte předplatné Event Gridu. Jako typ události použijte **Obory názvů služby Event Hubs**. Zadejte hodnoty pro výběr instance oboru názvů služby Event Hubs. Jako zadanou hodnotu nechte koncový bod odběratele. Vyberte **Vytvořit**.

   ![Vytvořit odběr](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generování ukázkových dat  
Teď máte nastavené centrum událostí, službu SQL Data Warehouse, aplikaci Azure Function App a odběr Event Gridu. Po aktualizaci připojovacího řetězce a názvu vašeho centra událostí ve zdrojovém kódu můžete spustit aplikaci WindTurbineDataGenerator.exe, která bude generovat datové streamy do centra událostí. 

1. Na portálu vyberte obor názvů centra události. Vyberte **Připojovací řetězce**.

   ![Výběr připojovacích řetězců](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Vyberte **RootManageSharedAccessKey**.

   ![Výběr klíče](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Zkopírujte **připojovací řetězec – primární klíč**.

   ![Kopírování klíče](./media/store-captured-data-data-warehouse/copy-key.png)

4. Vraťte se do projektu Visual Studio. V projektu *WindTurbineDataGenerator* otevřete soubor *program.cs*.

5. Aktualizujte hodnoty **EventHubConnectionString** a **EventHubName** na připojovací řetězec a název vašeho centra událostí. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení a pak spusťte aplikaci WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Ověření zachycených dat ve službě Data Warehouse
Po několika minutách zadejte dotaz do tabulku ve službě SQL Data Warehouse. Zjistíte, že se data generovaná aplikací WindTurbineDataGenerator streamovala do vašeho centra událostí, zachytila se do kontejneru Azure Storage a pak se pomocí funkce Azure migrovala do tabulky SQL Data Warehouse.  

## <a name="next-steps"></a>Další postup 
Pomocí kombinace datového skladu s výkonnými nástroji pro vizualizaci dat můžete získat užitečné přehledy.

V tomto článku se dozvíte, jak používat [Power BI se službou SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi).



