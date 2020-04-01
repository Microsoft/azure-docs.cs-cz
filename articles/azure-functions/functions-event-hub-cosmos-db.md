---
title: 'Kurz: Použití funkcí Java s Azure Cosmos DB a Event Hubs'
description: Tento kurz ukazuje, jak využívat události z event hubů k aktualizaci v Azure Cosmos DB pomocí funkce napsané v Jazyce Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425319"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Kurz: Vytvoření funkce v Javě s aktivační událostí centra událostí a výstupní vazbou Azure Cosmos DB

Tento kurz ukazuje, jak používat funkce Azure k vytvoření funkce Java, která analyzuje nepřetržitý proud dat o teplotě a tlaku. Události centra událostí, které představují hodnoty senzorů, aktivují funkci. Funkce zpracuje data událostí a pak přidá položky stavu do databáze Azure Cosmos DB.

V tomto kurzu budete:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte prostředky Azure pomocí rozhraní příkazového příkazu Azure.
> * Vytvořte a otestujte funkce jazyka Java, které s těmito prostředky interagují.
> * Nasaďte své funkce do Azure a monitorujte je pomocí Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, musíte mít nainstalované následující:

* [Java Developer Kit](https://aka.ms/azure-jdks) verze 8
* [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější
* [Azure CLI,](/cli/azure/install-azure-cli) pokud nechcete používat Cloud Shell
* [Základní nástroje Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) verze 2.6.666 nebo vyšší

> [!IMPORTANT]
> Proměnná `JAVA_HOME` prostředí musí být nastavena na umístění instalace JDK k dokončení tohoto kurzu.

Pokud dáváte přednost použití kódu pro tento kurz přímo, naleznete [java functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) ukázkový úložiště.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V tomto kurzu budete potřebovat tyto prostředky:

* Skupina prostředků obsahující ostatní prostředky
* Obor názvů Centra událostí, centrum událostí a pravidlo autorizace
* Účet Cosmos DB, databáze a kolekce
* Aplikace pro funkce a účet úložiště pro její hostování

V následujících částech se ukazuje, jak vytvořit tyto prostředky pomocí azure cli.

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Pokud nepoužíváte Cloud Shell, budete muset použít Azure CLI místně pro přístup ke svému účtu. Pomocí `az login` příkazu z výzvy Bash spusťte přihlašovací prostředí založené na prohlížeči. Pokud máte přístup k více než jednomu předplatnému Azure, nastavte výchozí nastavení s `az account set --subscription` následovaným ID předplatného.

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Dále vytvořte některé proměnné prostředí pro názvy a umístění prostředků, které vytvoříte. Použijte následující příkazy a `<value>` nahraďte zástupné symboly hodnotami podle vašeho výběru. Hodnoty by měly odpovídat [pravidlům pojmenování a omezením pro prostředky Azure](/azure/architecture/best-practices/resource-naming). Pro `LOCATION` proměnnou použijte jednu z `az functionapp list-consumption-locations` hodnot vytvořených příkazem.

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

Zbytek tohoto kurzu používá tyto proměnné. Uvědomte si, že tyto proměnné přetrvávají pouze po dobu trvání aktuální relace Azure CLI nebo Cloud Shell. Pokud použijete jiné okno místního terminálu nebo časový čas relace prostředí Cloud Shell, budete muset tyto příkazy znovu spustit.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure používá skupiny prostředků ke shromažďování všech souvisejících prostředků ve vašem účtu. Tímto způsobem je můžete zobrazit jako jednotku a odstranit je jediným příkazem, když s nimi skončíte.

K vytvoření skupiny prostředků použijte následující příkaz:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Dále vytvořte obor názvů Centra událostí Azure, centrum událostí a pravidlo autorizace pomocí následujících příkazů:

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

Obor názvů Event Hubs obsahuje centrum skutečných událostí a jeho autorizační pravidlo. Autorizační pravidlo umožňuje vašim funkcím odesílat zprávy do centra a naslouchat příslušným událostem. Jedna funkce odesílá zprávy, které představují telemetrická data. Jiná funkce naslouchá událostem, analyzuje data událostí a ukládá výsledky v Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

Dále vytvořte účet, databázi a kolekci Azure Cosmos DB pomocí následujících příkazů:

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

Hodnota `partition-key-path` rozdělí data na základě `temperatureStatus` hodnoty každé položky. Klíč oddílu umožňuje Cosmos DB zvýšit výkon rozdělením dat do různých podmnožiny, které lze přistupovat nezávisle.

### <a name="create-a-storage-account-and-function-app"></a>Vytvoření účtu úložiště a aplikace funkcí

Dále vytvořte účet Azure Storage, který je vyžadován funkcemi Azure, a pak vytvořte aplikaci funkcí. Použijte následující příkazy:

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

Když `az functionapp create` příkaz vytvoří aplikaci funkce, vytvoří také prostředek Application Insights se stejným názvem. Aplikace funkce se automaticky nakonfiguruje s nastavením s názvem, `APPINSIGHTS_INSTRUMENTATIONKEY` které ji připojuje k Application Insights. Telemetrii aplikací můžete zobrazit po nasazení funkcí do Azure, jak je popsáno dále v tomto kurzu.

## <a name="configure-your-function-app"></a>Konfigurace aplikace funkcí

Vaše aplikace funkce bude muset přistupovat k ostatním prostředkům, aby fungovaly správně. V následujících částech se ukazuje, jak nakonfigurovat aplikaci funkcí tak, aby mohla běžet v místním počítači.

### <a name="retrieve-resource-connection-strings"></a>Načíst připojovací řetězce prostředků

Pomocí následujících příkazů načtěte připojovací řetězce úložiště, centra událostí a cosmos DB a uložte je do proměnných prostředí:

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

Tyto proměnné jsou nastaveny na hodnoty načtené z příkazů Azure CLI. Každý příkaz používá dotaz JMESPath k extrahování připojovacího řetězce z vrácené datové části JSON. Připojovací řetězce jsou `echo` také zobrazeny pomocí, takže můžete potvrdit, že byly úspěšně načteny.

### <a name="update-your-function-app-settings"></a>Aktualizace nastavení aplikace funkcí

Dále pomocí následujícího příkazu přeneste hodnoty připojovacího řetězce do nastavení aplikace v účtu Azure Functions:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Vaše prostředky Azure byly teď vytvořeny a nakonfigurovány tak, aby správně spolupracovaly.

## <a name="create-and-test-your-functions"></a>Vytvoření a testování funkcí

Dále vytvoříte projekt v místním počítači, přidáte kód Java a otestujete ho. Budete používat příkazy, které fungují s pluginem Azure Functions plugin pro Maven a nástroje Azure Functions Core Tools. Vaše funkce budou spuštěny místně, ale budou používat cloudové prostředky, které jste vytvořili. Po získání funkce pracovat místně, můžete použít Maven nasadit do cloudu a sledovat vaše data a analýzy hromadí.

Pokud jste k vytvoření prostředků použili Cloud Shell, nebudete místně připojeni k Azure. V takovém případě `az login` pomocí příkazu spusťte proces přihlášení na základě prohlížeče. Pak v případě potřeby nastavte `az account set --subscription` výchozí předplatné s následovaným ID předplatného. Nakonec spusťte následující příkazy a znovu vytvořte některé proměnné prostředí v místním počítači. Nahraďte `<value>` zástupné symboly stejnými hodnotami, které jste použili dříve.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Vytvoření projektu místních funkcí

Pomocí následujícího příkazu Maven vytvořte projekt funkcí a přidejte požadované závislosti.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Tento příkaz generuje několik `telemetry-functions` souborů uvnitř složky:

* Soubor `pom.xml` pro použití s Maven
* Soubor `local.settings.json` pro uložení nastavení aplikace pro místní testování
* Soubor, `host.json` který umožňuje rozšíření Azure Functions Bundle, požadované pro cosmos DB výstupní vazby ve funkci analýzy dat
* Soubor, `Function.java` který obsahuje výchozí implementaci funkce
* Několik testovacích souborů, které tento kurz nepotřebuje

Chcete-li se vyhnout chybám kompilace, budete muset odstranit testovací soubory. Spuštěním následujících příkazů přejděte do nové složky projektu a odstraňte testovací složku:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Načtení nastavení aplikace pro funkce pro místní použití

Pro místní testování bude projekt funkce potřebovat připojovací řetězce, které jste přidali do aplikace funkce v Azure dříve v tomto kurzu. Použijte následující příkaz Nástroje jádra funkcí Azure, který načte všechna nastavení aplikace `local.settings.json` funkcí uložená v cloudu a přidá je do souboru:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Přidat kód Java

Dále otevřete `Function.java` soubor a nahraďte obsah následujícím kódem.

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

Jak můžete vidět, tento soubor `generateSensorData` obsahuje `processSensorData`dvě funkce a . Funkce `generateSensorData` simuluje snímač, který odesílá hodnoty teploty a tlaku do centra událostí. Aktivační událost časovače spustí funkci každých 10 sekund a výstupní vazba centra událostí odešle vrácenou hodnotu do centra událostí.

Když centrum událostí obdrží zprávu, vygeneruje událost. Funkce `processSensorData` se spustí, když obdrží událost. Potom zpracuje data událostí a použije výstupní vazbu Azure Cosmos DB k odeslání výsledků do Azure Cosmos DB.

Data používaná těmito funkcemi jsou `TelemetryItem`uložena pomocí třídy s názvem , kterou budete muset implementovat. Vytvořte nový `TelemetryItem.java` soubor volaný `Function.java` ve stejném umístění jako a přidejte následující kód:

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

Teď můžete vytvářet a spouštět funkce místně a zobrazit data se zobrazí ve vašem Azure Cosmos DB.

K vytvoření a spuštění funkcí použijte následující příkazy Maven:

```bash
mvn clean package
mvn azure-functions:run
```

Po některých zprávách sestavení a spuštění se zobrazí výstup podobný následujícímu příkladu pro každé spuštění funkcí:

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

Pak můžete přejít na [portál Azure](https://portal.azure.com) a přejít na svůj účet Azure Cosmos DB. Vyberte **Průzkumník dat**, rozbalte **možnost TelemetryInfo**a pak vyberte **Položky,** chcete-li data zobrazit při doručení.

![Průzkumník dat Cosmos DB](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Nasazení do Azure a zobrazení telemetrie aplikací

Nakonec můžete nasadit aplikaci do Azure a ověřit, že i nadále fungovat stejným způsobem to udělal místně.

Nasazení projektu do Azure pomocí následujícího příkazu:

```bash
mvn azure-functions:deploy
```

Vaše funkce teď běží v Azure a nadále akumulovat data ve vašem Azure Cosmos DB. Nawebu Na webu Azure Portal můžete zobrazit aplikaci nasazených funkcí a telemetrii aplikací prostřednictvím připojeného prostředku Application Insights, jak je znázorněno na následujících snímcích obrazovky:

**Stream živých metrik:**

![Datový proud živých metrik Přehledy aplikací](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Výkon:**

![Okno Výkon přehledů aplikací](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už s prostředky Azure vytvořenými v tomto kurzu skončili, můžete je pomocí následujícího příkazu odstranit:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit funkci Azure, která zpracovává události centra událostí a aktualizuje Cosmos DB. Další informace najdete v [průvodci vývojářem Azure Functions Java](/azure/azure-functions/functions-reference-java). Informace o použitých poznámkách najdete v odkazu [com.microsoft.azure.functions.anotation.](/java/api/com.microsoft.azure.functions.annotation)

Tento kurz používá proměnné prostředí a nastavení aplikace k ukládání tajných kódů, jako jsou připojovací řetězce. Informace o ukládání těchto tajných kódů v azure key vault, najdete [v tématu použití key vault odkazy pro služby App Service a Azure Functions](/azure/app-service/app-service-key-vault-references).

Dále se dozvíte, jak používat CI/CD Azure Pipelines pro automatizované nasazení:

> [!div class="nextstepaction"]
> [Vytváření a nasazování Javy do funkcí Azure](/azure/devops/pipelines/ecosystems/java-function)
