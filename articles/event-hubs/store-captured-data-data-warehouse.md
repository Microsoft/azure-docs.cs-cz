---
title: 'Kurz: migrace dat událostí do Azure synapse Analytics – Azure Event Hubs'
description: 'Kurz: v tomto kurzu se dozvíte, jak zachytit data z centra událostí do služby Azure synapse Analytics pomocí funkce Azure aktivované službou Event Grid.'
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b2a35647422c91d6859e1889f906ae512ce41a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436608"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Kurz: migrace zachycených Event Hubs dat do služby Azure synapse Analytics pomocí Event Grid a Azure Functions

[Zachytávání](./event-hubs-capture-overview.md) služby Event Hubs představuje nejjednodušší způsob, jak automaticky doručovat streamovaná data ve službě Event Hubs do služby Azure Blob Storage nebo Azure Data Lake Store. Následně můžete zpracovávat a doručovat data do všech dalších cílů úložiště podle vaší volby, jako je Azure synapse Analytics nebo Cosmos DB. V tomto kurzu se naučíte zachytit data z centra událostí do služby Azure synapse Analytics pomocí funkce Azure aktivované v rámci služby [Event Grid](../event-grid/overview.md).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- Nejprve vytvoříte centrum událostí s povolenou funkcí **Zachytávání** a jako cíl nastavíte službu Azure Blob Storage. Data generovaná aplikací WindTurbineGenerator se streamují do centra událostí a automaticky se zachytávají do služby Azure Storage jako soubory Avro.
- Pak vytvoříte předplatné Azure Event Gridu s oborem názvů služby Event Hubs jako zdrojem a koncovým bodem funkce Azure jako cílem.
- Vždy, když funkce Zachytávání služby Event Hubs doručí soubor Avro do objektu blob služby Azure Storage, oznámí Event Grid funkci Azure identifikátor URI objektu blob. Funkce pak migruje data z objektu blob do služby Azure synapse Analytics.

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
>
> - Nasazení infrastruktury
> - Publikování kódu do aplikace Functions
> - Vytvoření odběru Event Gridu v aplikaci Functions
> - Streamování ukázkových dat do centra událostí
> - Ověření zachycených dat ve službě Azure synapse Analytics

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual studio 2019](https://www.visualstudio.com/vs/). Při instalaci nezapomeňte nainstalovat následující sady funkcí: Vývoj desktopových aplikací .NET, Vývoj pro Azure, Vývoj pro ASP.NET a web, Vývoj v Node.js a Vývoj v Pythonu.
- Stažení [ukázky Gitu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) ukázka řešení obsahuje následující součásti:

  - *WindTurbineDataGenerator* – Jednoduchý vydavatel odesílající ukázková data větrné turbíny do centra událostí s povolenou funkcí Zachytávání.
  - *FunctionDWDumper* – Funkce Azure, která přijímá oznámení Event Gridu při zachycení souboru Avro do objektu blob služby Azure Storage. Obdrží cestu k identifikátoru URI objektu blob, přečte obsah a vloží tato data do služby Azure synapse Analytics.

  Tato ukázka používá nejnovější balíček Azure. Messaging. EventHubs. Starou ukázku, která používá balíček Microsoft. Azure. EventHubs, můžete najít [tady](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

### <a name="deploy-the-infrastructure"></a>Nasazení infrastruktury

Pomocí Azure PowerShellu nebo Azure CLI nasaďte infrastrukturu potřebnou pro tento kurz s využitím této [šablony Azure Resource Manageru](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Tato šablona vytvoří následující prostředky:

- Centrum událostí s povolenou funkci Zachytávání
- Účet úložiště pro zachycená data událostí
- Plán služby Azure App Service pro hostování aplikace Functions
- Aplikace funkcí pro zpracování zachycených souborů událostí
- Logický server SQL pro hostování datového skladu
- Azure synapse Analytics pro ukládání migrovaných dat

Následující části obsahují příkazy Azure CLI a Azure PowerShellu pro nasazení požadované infrastruktury pro tento kurz. Před spuštěním příkazů aktualizujte názvy následujících objektů: 

- Skupina prostředků Azure 
- Oblast pro skupinu prostředků
- Obor názvů služby Event Hubs
- Centrum událostí
- Logický server SQL
- Uživatel SQL (a heslo)
- Databáze Azure SQL
- Azure Storage 
- Aplikace Azure Functions

Vytvoření všech artefaktů Azure těmito skripty nějakou dobu trvá. Než budete pokračovat, počkejte na dokončení skriptu. Pokud nasazení z nějakého důvodu selže, odstraňte skupinu prostředků, opravte nahlášený problém a spusťte příkaz znovu. 

#### <a name="azure-cli"></a>Azure CLI

Pokud chcete šablonu nasadit pomocí Azure CLI, použijte následující příkazy:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Pokud chcete šablonu nasadit pomocí PowerShellu, použijte následující příkazy:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-azure-synapse-analytics"></a>Vytvoření tabulky ve službě Azure synapse Analytics

Pomocí sady [Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md)nebo editoru dotazů na portálu vytvořte tabulku ve službě Azure synapse Analytics spuštěním skriptu [CreateDataWarehouseTable. SQL.](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 

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

1. Otevřete řešení *EventHubsCaptureEventGridDemo. sln* v aplikaci Visual Studio 2019.

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
 
1. Přejděte na web [Azure Portal](https://portal.azure.com/). Vyberte skupinu prostředků a aplikaci funkcí.

   ![Zobrazení aplikace funkcí](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Vyberte funkci.

   ![Výběr funkce](./media/store-captured-data-data-warehouse/select-function.png)

1. Vyberte **Přidat předplatné Event Gridu**.

   ![Přidat předplatné](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Pojmenujte předplatné Event Gridu. Jako typ události použijte **Obory názvů služby Event Hubs**. Zadejte hodnoty pro výběr instance oboru názvů služby Event Hubs. Jako zadanou hodnotu nechte koncový bod odběratele. Vyberte **Vytvořit**.

   ![Vytvoření odběru](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generování ukázkových dat  
Nyní jste nastavili centrum událostí, Azure synapse Analytics, Azure Function App a předplatné Event Grid. Po aktualizaci připojovacího řetězce a názvu vašeho centra událostí ve zdrojovém kódu můžete spustit aplikaci WindTurbineDataGenerator.exe, která bude generovat datové streamy do centra událostí. 

1. Na portálu vyberte obor názvů centra události. Vyberte **připojovací řetězce**.

   ![Výběr připojovacích řetězců](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Vyberte **RootManageSharedAccessKey**.

   ![Výběr klíče](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Kopírovat **připojovací řetězec – primární klíč**

   ![Kopírování klíče](./media/store-captured-data-data-warehouse/copy-key.png)

4. Vraťte se do projektu Visual Studio. V projektu *WindTurbineDataGenerator* otevřete soubor *program.cs*.

5. Aktualizujte hodnoty **EventHubConnectionString** a **EventHubName** na připojovací řetězec a název vašeho centra událostí. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Sestavte řešení a pak spusťte aplikaci WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Ověření zachycených dat ve službě Data Warehouse
Po několika minutách se dotazuje v tabulce ve službě Azure synapse Analytics. Všimněte si, že data generovaná službou WindTurbineDataGenerator byla streamovaná do vašeho centra událostí, zachycená do kontejneru Azure Storage a pak se migrují do tabulky Azure synapse Analytics pomocí funkce Azure Functions.  

## <a name="next-steps"></a>Další kroky 
Pomocí kombinace datového skladu s výkonnými nástroji pro vizualizaci dat můžete získat užitečné přehledy.

Tento článek ukazuje, jak používat [Power BI se službou Azure synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)