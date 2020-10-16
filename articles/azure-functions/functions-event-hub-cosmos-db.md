---
title: 'Kurz: použití funkcí Java s Azure Cosmos DB a Event Hubs'
description: V tomto kurzu se dozvíte, jak zpracovávat události z Event Hubs, abyste mohli aktualizovat Azure Cosmos DB pomocí funkce napsané v jazyce Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 8ba15047d04d3af25518e8c55a50c55bcacf9037
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103930"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Kurz: vytvoření funkce v Java pomocí triggeru centra událostí a výstupní vazby Azure Cosmos DB

V tomto kurzu se dozvíte, jak pomocí Azure Functions vytvořit funkci jazyka Java, která analyzuje souvislý proud dat o teplotě a tlaku. Funkce události centra událostí, které reprezentují senzory, tuto funkci aktivovaly. Funkce zpracuje data události a poté přidá položky stavu do Azure Cosmos DB.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření a konfigurace prostředků Azure pomocí Azure CLI.
> * Vytváření a testování funkcí Java, které komunikují s těmito prostředky.
> * Nasaďte funkce do Azure a sledujte je pomocí Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu musíte mít nainstalované následující:

* [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) verze 8
* [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější
* [Azure CLI](/cli/azure/install-azure-cli) Pokud nechcete používat Cloud Shell
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) verze 2.6.666 nebo vyšší

> [!IMPORTANT]
> `JAVA_HOME`Aby se tento kurz dokončil, musí být proměnná prostředí nastavená na umístění instalace JDK.

Pokud dáváte přednost použití kódu pro tento kurz přímo, přečtěte si ukázkové úložiště [Java-Functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V tomto kurzu budete potřebovat tyto prostředky:

* Skupina prostředků, která bude obsahovat další prostředky
* Obor názvů Event Hubs, centrum událostí a autorizační pravidlo
* Účet Cosmos DB, databáze a kolekce
* Aplikace Function App a účet úložiště, které se mají hostovat

V následujících částech se dozvíte, jak tyto prostředky vytvořit pomocí rozhraní příkazového řádku Azure CLI.

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Pokud nepoužíváte Cloud Shell, budete k přístupu k vašemu účtu muset použít Azure CLI místně. Pomocí `az login` příkazu z příkazového řádku bash spusťte prostředí pro přihlášení na základě prohlížeče. Pokud máte přístup k více než jednomu předplatnému Azure, nastavte výchozí nastavení `az account set --subscription` za následováním ID předplatného.

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Dále vytvořte některé proměnné prostředí pro názvy a umístění prostředků, které vytvoříte. Použijte následující příkazy a nahraďte `<value>` zástupné symboly hodnotami, které zvolíte. Hodnoty by měly odpovídat [pravidlům pojmenovávání a omezením pro prostředky Azure](/azure/architecture/best-practices/resource-naming). Pro `LOCATION` proměnnou použijte jednu z hodnot, které jsou vytvořeny `az functionapp list-consumption-locations` příkazem.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

Zbytek tohoto kurzu používá tyto proměnné. Mějte na paměti, že tyto proměnné přetrvávají jenom po dobu trvání aktuální relace Azure CLI nebo Cloud Shell. Tyto příkazy budete muset spustit znovu, pokud použijete jiné místní okno terminálu nebo vypršel časový limit relace Cloud Shell.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure používá skupiny prostředků ke shromáždění všech souvisejících prostředků ve vašem účtu. Tímto způsobem je můžete zobrazit jako jednotku a odstranit je jediným příkazem, když s nimi budete hotovi.

Pomocí následujícího příkazu vytvořte skupinu prostředků:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Dále vytvořte obor názvů Azure Event Hubs, centrum událostí a autorizační pravidlo pomocí následujících příkazů:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

Obor názvů Event Hubs obsahuje skutečné centrum událostí a jeho autorizační pravidlo. Autorizační pravidlo umožňuje vašim funkcím posílat zprávy do centra a naslouchat odpovídajícím událostem. Jedna funkce odesílá zprávy, které reprezentují data telemetrie. Jiná funkce naslouchá událostem, analyzuje data události a ukládá výsledky v Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

Dále vytvořte účet Azure Cosmos DB, databázi a kolekci pomocí následujících příkazů:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

`partition-key-path`Hodnota rozdělí data na základě `temperatureStatus` hodnoty každé položky. Klíč oddílu umožňuje Cosmos DB zvýšit výkon tím, že se data rozdělí na samostatné podmnožiny, ke kterým může přistupovat nezávisle.

### <a name="create-a-storage-account-and-function-app"></a>Vytvoření účtu úložiště a aplikace Function App

Dále vytvořte účet Azure Storage, který je vyžadován Azure Functions a pak vytvořte aplikaci Function App. Použijte následující příkazy:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Když `az functionapp create` příkaz vytvoří aplikaci Function App, vytvoří také prostředek Application Insights se stejným názvem. Aplikace Function App se automaticky nakonfiguruje s nastavením s názvem `APPINSIGHTS_INSTRUMENTATIONKEY` , které je připojuje k Application Insights. Telemetrii aplikace můžete zobrazit po nasazení funkcí do Azure, jak je popsáno dále v tomto kurzu.

## <a name="configure-your-function-app"></a>Konfigurace aplikace Function App

Vaše aplikace Function App bude potřebovat přístup k ostatním prostředkům, aby fungovala správně. V následujících částech se dozvíte, jak nakonfigurovat aplikaci Function App, aby mohla běžet na místním počítači.

### <a name="retrieve-resource-connection-strings"></a>Načtení připojovacích řetězců prostředků

Pomocí následujících příkazů načtěte úložiště, centrum událostí a Cosmos DB připojovací řetězce a uložte je do proměnných prostředí:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Tyto proměnné jsou nastavené na hodnoty načtené z příkazů Azure CLI. Každý příkaz používá dotaz JMESPath k extrakci připojovacího řetězce z vrácené datové části JSON. Připojovací řetězce se také zobrazují pomocí, `echo` abyste si ověřili, že byly úspěšně načteny.

### <a name="update-your-function-app-settings"></a>Aktualizovat nastavení aplikace Function App

Dále pomocí následujícího příkazu přeneste hodnoty připojovacího řetězce do nastavení aplikace ve vašem účtu Azure Functions:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Prostředky Azure se teď vytvořily a nakonfigurovali tak, aby správně fungovaly.

## <a name="create-and-test-your-functions"></a>Vytváření a testování funkcí

V dalším kroku vytvoříte projekt na svém místním počítači, přidáte kód Java a otestujete ho. Budete používat příkazy, které pracují s modulem plug-in Azure Functions pro Maven a Azure Functions Core Tools. Vaše funkce se spustí místně, ale budou používat cloudové prostředky, které jste vytvořili. Jakmile funkce fungují místně, můžete je pomocí Maven nasadit do cloudu a sledovat vaše data a analýzy.

Pokud jste k vytváření prostředků použili Cloud Shell, nebudete místně připojeni k Azure. V takovém případě pomocí `az login` příkazu spusťte proces přihlášení založený na prohlížeči. V případě potřeby nastavte výchozí předplatné s `az account set --subscription` následováním ID předplatného. Nakonec spuštěním následujících příkazů znovu vytvořte některé proměnné prostředí v místním počítači. Nahraďte `<value>` zástupné symboly stejnými hodnotami, které jste použili dříve.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

K vytvoření projektu functions a přidání požadovaných závislostí použijte následující příkaz Maven.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Tento příkaz vygeneruje ve složce několik souborů `telemetry-functions` :

* `pom.xml`Soubor pro použití s Maven
* `local.settings.json`Soubor pro uložení nastavení aplikace pro místní testování
* `host.json`Soubor, který povoluje sadu rozšíření Azure Functions požadovanou pro Cosmos DB výstupní vazby v rámci funkce analýzy dat
* `Function.java`Soubor, který obsahuje implementaci výchozí funkce
* Několik testovacích souborů, které tento kurz nevyžaduje

Aby nedocházelo k chybám při kompilaci, budete muset odstranit testovací soubory. Spusťte následující příkazy, abyste přešli do nové složky projektu a odstranili testovací složku:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Načíst nastavení aplikace Function App pro místní použití

Pro místní testování bude váš projekt funkcí potřebovat připojovací řetězce, které jste přidali do aplikace Function App v Azure dříve v tomto kurzu. Použijte následující příkaz Azure Functions Core Tools, který načte všechna nastavení aplikací funkcí uložených v cloudu a přidá je do `local.settings.json` souboru:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Přidat kód Java

Potom otevřete `Function.java` soubor a nahraďte jeho obsah následujícím kódem.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Jak vidíte, tento soubor obsahuje dvě funkce `generateSensorData` a `processSensorData` . `generateSensorData`Funkce simuluje senzor, který odesílá čtení teploty a tlaku do centra událostí. Aktivační událost časovače spouští funkci každých 10 sekund a výstupní vazba centra událostí odesílá vrácenou hodnotu do centra událostí.

Když centrum událostí přijme zprávu, vygeneruje událost. `processSensorData`Funkce se spustí, když přijme událost. Poté zpracuje data události a pomocí výstupní vazby Azure Cosmos DB odešle výsledky do Azure Cosmos DB.

Data, která jsou používána těmito funkcemi, jsou uložena pomocí třídy s názvem `TelemetryItem` , kterou budete muset implementovat. Vytvořte nový soubor s názvem `TelemetryItem.java` ve stejném umístění `Function.java` a přidejte následující kód:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Spuštění v místním prostředí

Nyní můžete vytvářet a spouštět funkce místně a zobrazovat data v Azure Cosmos DB.

Pro sestavování a spouštění funkcí použijte následující příkazy Maven:

```bash
mvn clean package
mvn azure-functions:run
```

Po některých zprávách o sestavení a spuštění se zobrazí výstup podobný následujícímu příkladu pro pokaždé, když se funkce spustí:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Pak můžete přejít na [Azure Portal](https://portal.azure.com) a přejít na účet Azure Cosmos DB. Vyberte **Průzkumník dat**, rozbalte **TelemetryInfo**a pak vyberte **položky** , které se po doručení zobrazí.

![Cosmos DB Průzkumník dat](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Nasazení do Azure a zobrazení telemetrie aplikací

Nakonec můžete aplikaci nasadit do Azure a ověřit, že i nadále funguje stejným způsobem jako v místním prostředí.

Nasaďte projekt do Azure pomocí následujícího příkazu:

```bash
mvn azure-functions:deploy
```

Vaše funkce se teď spouštějí v Azure a budou pokračovat ve shromažďování dat ve vašem Azure Cosmos DB. Nasazenou aplikaci Function App můžete zobrazit v Azure Portal a pomocí prostředku připojené Application Insights zobrazit telemetrii aplikace, jak je znázorněno na následujících snímcích obrazovky:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Předepsané**

![Okno výkonu Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už s prostředky Azure vytvořenými v tomto kurzu skončili, můžete je pomocí následujícího příkazu odstranit:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit funkci Azure, která zpracovává události centra událostí a aktualizuje Cosmos DB. Další informace najdete v příručce pro [vývojáře v jazyce Azure Functions Java](./functions-reference-java.md). Informace o použitých poznámkách naleznete v tématu [com. Microsoft. Azure. Functions. Annotation](/java/api/com.microsoft.azure.functions.annotation) reference.

Tento kurz používal proměnné prostředí a nastavení aplikace k ukládání tajných kódů, jako jsou připojovací řetězce. Informace o ukládání těchto tajných klíčů v Azure Key Vault najdete v tématu [použití Key Vaultch odkazů pro App Service a Azure Functions](../app-service/app-service-key-vault-references.md).

V dalším kroku se dozvíte, jak používat Azure Pipelines CI/CD pro automatizované nasazení:

> [!div class="nextstepaction"]
> [Sestavení a nasazení Java pro Azure Functions](/azure/devops/pipelines/ecosystems/java-function)