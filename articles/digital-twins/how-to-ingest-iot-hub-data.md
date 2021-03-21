---
title: Ingestování telemetrie z IoT Hubu
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ingestovat zprávy telemetrie zařízení z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3223a1c8e20d8b0caced5d940132c32fa0aba97c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149079"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingestování IoT Hub telemetrie do digitálních vláken Azure

Digitální vlákna Azure se řídí daty ze zařízení IoT a dalších zdrojů. Běžný zdroj dat zařízení, která se mají používat v rámci digitálních vláken Azure, je [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pro ingestování dat do digitálních vláken Azure je nastavení externího výpočetního prostředku, jako je například funkce, kterou provedete pomocí [Azure Functions](../azure-functions/functions-overview.md). Funkce přijme data a pomocí [rozhraní DigitalTwins API](/rest/api/digital-twins/dataplane/twins) nastaví vlastnosti nebo události telemetrie pro [digitální vlákna](concepts-twins-graph.md) . 

Tento postup popisuje, jak dokumentovat pomocí procesu vytváření funkce, která může ingestovat telemetrie z IoT Hub.

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat v tomto příkladu, budete muset nastavit následující prostředky jako požadavky:
* **Centrum IoT**. Pokyny najdete v části *vytvoření IoT Hub* [tohoto IoT Hub rychlé](../iot-hub/quickstart-send-telemetry-cli.md)spuštění.
* **Instance digitálního vlákna Azure** , která bude přijímat telemetrii zařízení. Pokyny najdete v tématu [*Postup: nastavení instance a ověřování digitálních vláken Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Ukázkový scénář telemetrie

Tento postup popisuje, jak odesílat zprávy z IoT Hub do digitálních vláken Azure pomocí funkce v Azure. K dispozici je celá řada možných konfigurací a vyhovujících strategií, které můžete použít pro posílání zpráv, ale příklad tohoto článku obsahuje následující části:
* Termostat zařízení v IoT Hub se známým ID zařízení
* Digitální vlákna představující zařízení s ID odpovídajícího

> [!NOTE]
> V tomto příkladu se používá jednoznačné ID mezi ID zařízení a odpovídajícím ID digitálního vlákna, ale je možné poskytnout propracovanější mapování ze zařízení na jeho dvojitou hodnotu (například s tabulkou mapování).

Pokaždé, když je událost telemetrie teploty odeslána zařízením termostatu, funkce zpracuje telemetrii a vlastnost *teploty* digitálního vlákna by měla aktualizovat. Tento scénář je popsaný v diagramu níže:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce v Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje." border="false":::

## <a name="add-a-model-and-twin"></a>Přidání modelu a digitálního dvojčete

V této části nastavíte [digitální dvojitou](concepts-twins-graph.md) hodnotu v digitálních proobjektech Azure, které budou představovat termostatické zařízení a budou se aktualizovat informacemi z IoT Hub.

Pro vytvoření vlákna typu termostatu budete muset nejprve nahrát termostatový [model](concepts-models.md) do instance, která popisuje vlastnosti termostatu a později se použije k vytvoření vlákna. 

Model vypadá takto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pokud chcete **Tento model nahrát do instance s dvojitou** silou, spusťte následující příkaz Azure CLI, který nahraje výše uvedený model jako vložený formát JSON. Můžete spustit příkaz v [Azure Cloud Shell](/cloud-shell/overview.md) v prohlížeči nebo na svém počítači, pokud máte rozhraní příkazového řádku [nainstalované místně](/cli/azure/install-azure-cli.md).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Pak budete muset **vytvořit jeden z těchto vláken pomocí tohoto modelu**. Pomocí následujícího příkazu vytvořte termostat s názvem **thermostat67** a nastavte 0,0 jako počáteční hodnotu teploty.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

>[!NOTE]
> Pokud používáte Cloud Shell v prostředí PowerShellu, může být nutné, aby se znaky uvozovek v vložených polích JSON vyhnuly jejich správné analýze. Tady jsou příkazy pro nahrání modelu a vytvoření vlákna s touto úpravou:
>
> Nahrát model:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```
>
> Vytvořit dvojitou dvojici:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Po úspěšném vytvoření vlákna by výstup CLI z příkazu vypadal přibližně takto:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Vytvoření funkce

V této části vytvoříte funkci Azure pro přístup k digitálním událostem Azure a budete moct aktualizovat vlákna na základě událostí telemetrie IoT, které obdrží. Pomocí následujících kroků vytvořte a publikujte funkci.

#### <a name="step-1-create-a-function-app-project"></a>Krok 1: vytvoření projektu Function App

Nejprve v aplikaci Visual Studio vytvořte nový projekt aplikace Function App. Pokyny k tomu, jak to provést, naleznete v části [**Vytvoření aplikace Function App v sadě Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

#### <a name="step-2-fill-in-function-code"></a>Krok 2: vyplnění kódu funkce

Přidejte do projektu následující balíčky:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Přejmenujte ukázkovou funkci *function1. cs* , kterou Visual Studio vygenerovalo s novým projektem na *IoTHubtoTwins. cs*. Nahraďte kód v souboru následujícím kódem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Uložte kód funkce.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3: publikování aplikace Function App do Azure

Publikujte projekt do aplikace Function App v Azure.

Pokyny k tomu, jak to provést, najdete v části [**publikování aplikace Function App do Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

#### <a name="step-4-configure-the-function-app"></a>Krok 4: Konfigurace aplikace Function App

Dále přiřaďte k této funkci **roli přístupu** a **nakonfigurujte nastavení aplikace** tak, aby měla přístup k instanci digitálních vláken Azure. Pokyny k tomu, jak to provést, najdete v části [**nastavení přístupu zabezpečení pro aplikaci Function App**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

## <a name="connect-your-function-to-iot-hub"></a>Připojte funkci k IoT Hub

V této části nastavíte funkci jako cíl události pro data zařízení služby IoT Hub. Tím se zajistí, že se data ze zařízení z termostatu v IoT Hub pošlou službě Azure Function za účelem zpracování.

V [Azure Portal](https://portal.azure.com/)přejděte na instanci IoT Hub, kterou jste vytvořili v části [*požadavky*](#prerequisites) . V části **události** Vytvořte předplatné pro vaši funkci.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Snímek obrazovky Azure Portal, který ukazuje přidání odběru události":::

Na stránce **vytvořit odběr události** vyplňte pole následujícím způsobem:
  1. Pro položku **název** vyberte libovolný název pro odběr události.
  2. V případě **schématu událostí** vyberte možnost _Event Grid schéma_.
  3. V **části název systémového tématu** vyberte libovolný požadovaný název.
  1. Pro **Filtr na typy událostí** vyberte zaškrtávací políčko _telemetrie zařízení_ a zrušte zaškrtnutí ostatních typů událostí.
  1. Jako **Typ koncového bodu** vyberte _Azure Function_.
  1. V případě **koncového bodu** použijte odkaz _Vybrat koncový bod_ a zvolte, kterou funkci Azure použít pro koncový bod.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Snímek obrazovky Azure Portal k vytvoření podrobností odběru události":::

Na stránce _Vybrat funkci Azure_ , která se otevře, ověřte nebo vyplňte následující podrobnosti.
 1. **Předplatné**: vaše předplatné Azure.
 2. **Skupina prostředků**: vaše skupina prostředků.
 3. **Function App**: název aplikace Function App.
 4. **Slot**: _Výroba_.
 5. **Funkce**: v rozevíracím seznamu vyberte funkci z předchozí *IoTHubtoTwins*.

Uložte podrobnosti pomocí tlačítka _potvrdit výběr_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Snímek obrazovky Azure Portal pro výběr funkce":::

Kliknutím na tlačítko _vytvořit_ Vytvořte odběr události.

## <a name="send-simulated-iot-data"></a>Odeslat Simulovaná data IoT

Pokud chcete otestovat novou funkci příchozího přenosu dat, použijte simulátor zařízení z [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Tento kurz se řídí ukázkovým projektem napsaným v jazyce C#. Vzorový kód je umístěný tady: [Azure Digital dokončí ukázky](/samples/azure-samples/digital-twins-samples/digital-twins-samples). V tomto úložišti budete používat projekt **DeviceSimulator** .

V tomto koncovém kurzu proveďte následující kroky:
1. [*Zaregistrujte simulované zařízení s IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurace a spuštění simulace*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Ověřit výsledky

Při současném spuštění simulátoru zařízení se změní hodnota teplota digitálního vlákna. V Azure CLI spuštěním následujícího příkazu zobrazte hodnotu teploty.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Výstup by měl obsahovat hodnotu teploty, například:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Chcete-li zobrazit změnu hodnoty, opakovaně spusťte příkaz dotazu výše.

## <a name="next-steps"></a>Další kroky

Přečtěte si o příchozím a odchozím přenosu dat pomocí digitálních vláken Azure:
* [*Koncepty: integrace s jinými službami*](concepts-integration.md)
