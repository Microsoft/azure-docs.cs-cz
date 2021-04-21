---
title: Integrace se službou Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit směrování událostí z digitálních vláken Azure na Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783768"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrace digitálních vláken Azure s Azure Time Series Insights

V tomto článku se dozvíte, jak integrovat digitální vlákna Azure pomocí [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Řešení popsané v tomto článku vám umožní shromáždit a analyzovat historické údaje o řešení IoT. Azure Digital Twins se výborně hodí k zasílání dat do Time Series Insights, protože umožňuje korelovat více streamů dat a standardizovat informace, než je pošle do Time Series Insights.

## <a name="prerequisites"></a>Požadavky

Než budete moct nastavit relaci s Time Series Insights, budete muset nastavit následující prostředky:
* **Centrum IoT**. Pokyny najdete v části [*vytvoření IoT Hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) v rychlém startu pro *odeslání telemetrie IoT Hub* .
* **Instance digitálního vlákna Azure**.
Pokyny najdete v tématu [*Postup: nastavení instance a ověřování digitálních vláken Azure*](./how-to-set-up-instance-portal.md).
* **Model a vlákna v instanci digitálních vláken Azure**.
K zobrazení dat v Time Series Insights budete muset několikrát aktualizovat informace o zdvojení. Pokyny najdete v článku [*Přidání modelu a vyzdvojení*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) článku postup: ingestování služby *IoT Hub* .

> [!TIP]
> V tomto článku se měnící hodnoty digitálních hodnot, které se zobrazují v Time Series Insights, aktualizují ručně pro jednoduchost. Pokud byste ale chtěli Tento článek dokončit se živými simulovanými daty, můžete nastavit funkci Azure, která aktualizuje digitální vlákna na základě událostí telemetrie IoT ze simulovaného zařízení. Pokyny najdete v tématu [*Postup:*](how-to-ingest-iot-hub-data.md)ingestování IoT Hub dat, včetně závěrečných kroků pro spuštění simulátoru zařízení a ověření, že tok dat funguje.
>
> Později vyhledejte další TIP, který vám ukáže, kde se má spustit simulátor zařízení a co vaše funkce Azure Functions automaticky aktualizují, místo odeslání ručních příkazů aktualizace digitálního vlákna.


## <a name="solution-architecture"></a>Architektura řešení

Pomocí níže uvedené cesty budete připojeni Time Series Insights k digitálním Vlákenám Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram služeb Azure v uceleném scénáři zvýrazňování Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Vytvoření oboru názvů centra událostí

Před vytvořením centra událostí můžete nejdřív vytvořit obor názvů centra událostí, který bude přijímat události z instance digitálního vlákna Azure. Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: vytvoření centra událostí pomocí Azure Portal*](../event-hubs/event-hubs-create.md). Pokud chcete zjistit, které oblasti podporují centra událostí, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Pokud se zobrazí chyba s oznámením `BadRequest: The specified service namespace is invalid.` , ujistěte se, že název, který jste zvolili pro váš obor názvů, splňuje požadavky na pojmenování popsané v tomto referenčním dokumentu: [vytvořit obor názvů](/rest/api/servicebus/create-namespace).

Pomocí tohoto oboru názvů centra událostí budete uchovávat dvě centra událostí, která jsou potřebná pro tento článek:

  1. **Vyzdvojení středu** – centrum událostí pro příjem událostí s dvojitou změnou
  2. **Centrum časových řad** – centrum událostí pro streamování událostí do Time Series Insights

Další části vás provedou vytvořením a konfigurací těchto Center v oboru názvů centra událostí.

## <a name="create-twins-hub"></a>Vytvořit centrum vláken

Prvním centrem událostí, které vytvoříte v tomto článku, je **centrum vláken**. Toto centrum událostí dostane z digitálních vláken Azure události s dvojitou změnou.
Chcete-li nastavit centrum vláken, proveďte následující kroky v této části:

1. Vytvoření centra vláken
2. Vytvoření autorizačního pravidla pro řízení oprávnění k centru
3. Vytvoření koncového bodu v digitálních událostech Azure, které pomocí autorizačního pravidla přistupují k centru
4. Vytvořte trasu v digitálních událostech Azure, které odesílají událost s dvojitou aktualizací do koncového bodu a propojených vláken.
5. Získání připojovacího řetězce centra vláken

Vytvořte **centrum vláken** pomocí následujícího příkazu CLI. Zadejte název pro vaše centrum vláken.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Vytvořit autorizační pravidlo centra vláken

Vytvořte [autorizační pravidlo](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) s oprávněními Odeslat a přijmout. Zadejte název pravidla.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Vytvořit koncový bod centra vláken

Vytvořte [koncový bod](concepts-route-events.md#create-an-endpoint) digitálních vláken Azure, který propojuje centrum událostí s instancí digitálních vláken Azure. Zadejte název koncového bodu centra vláken.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Vytvořit trasu události centra s dvojitými událostmi

Instance digitálních vláken Azure mohou generovat [události s dvojitou aktualizací](how-to-interpret-event-data.md) pokaždé, když se aktualizuje stav vlákna. V této části vytvoříte **trasu události** digitálních vláken Azure, která bude nasměrovat tyto události aktualizace na rozbočovač nevláken pro další zpracování.

Vytvořte [trasu](concepts-route-events.md#create-an-event-route) v rámci digitálních vláken Azure, která odešle události s dvojitou aktualizací do koncového bodu výše. Filtr v této trase umožní předat do koncového bodu pouze zprávy s dvojitou aktualizací. Zadejte název trasy události v centru s dvojitými událostmi.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Získání připojovacího řetězce centra vláken

Pomocí autorizačních pravidel, která jste vytvořili výše pro centrum vláken, Získejte [připojovací řetězec centra událostí](../event-hubs/event-hubs-get-connection-string.md).

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Poznamenejte si hodnotu **primaryConnectionString** z výsledku, abyste mohli nakonfigurovat nastavení aplikace pro centrum nevláken v tomto článku dále.

## <a name="create-time-series-hub"></a>Vytvořit centrum časových řad

Druhým centrem událostí, které vytvoříte v tomto článku, je **Centrum časových řad**. Toto je centrum událostí, které bude zasílat datový proud událostí digitálního vlákna Azure do Time Series Insights.
Pokud chcete nastavit Centrum časových řad, proveďte tyto kroky:

1. Vytvoření centra časových řad
2. Vytvoření autorizačního pravidla pro řízení oprávnění k centru
3. Získání připojovacího řetězce centra časových řad

Když později vytvoříte instanci Time Series Insights, připojíte toto centrum časových řad jako zdroj události pro instanci Time Series Insights.

Vytvořte **Centrum časových řad** pomocí následujícího příkazu. Zadejte název centra časových řad.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Vytvořit autorizační pravidlo centra časových řad

Vytvořte [autorizační pravidlo](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) s oprávněními Odeslat a přijmout. Zadejte název ověřovacího pravidla centra časových řad.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Získání připojovacího řetězce centra časových řad

Získání [připojovacího řetězce centra časových řad](../event-hubs/event-hubs-get-connection-string.md)pomocí autorizačních pravidel, která jste vytvořili výše pro Centrum časových řad:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Poznamenejte si hodnotu **primaryConnectionString** z výsledku a nakonfigurujte nastavení aplikace centra časových řad dále v tomto článku.

Také si poznamenejte následující hodnoty, které je později použijete k vytvoření instance Time Series Insights.
* Obor názvů centra událostí
* Název centra časových řad
* Ověřovací pravidlo centra časových řad

## <a name="create-a-function"></a>Vytvoření funkce

V této části vytvoříte funkci Azure, která převede události s dvojitou aktualizací z původního formuláře jako dokumenty opravy JSON na objekty JSON, které obsahují jenom aktualizované a přidané hodnoty z vašich vláken.

### <a name="step-1-create-function-app"></a>Krok 1: Vytvoření aplikace Function App

Nejprve v aplikaci Visual Studio vytvořte nový projekt aplikace Function App. Pokyny k tomu, jak to provést, naleznete v části [**Vytvoření aplikace Function App v sadě Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

### <a name="step-2-add-a-new-function"></a>Krok 2: Přidání nové funkce

Vytvořte novou funkci Azure s názvem *ProcessDTUpdatetoTSI. cs* a aktualizujte události telemetrie zařízení na Time Series Insights. Typ funkce bude **Trigger centra událostí**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Snímek obrazovky sady Visual Studio, ve kterém se vytvoří nová funkce Azure typu Trigger centra událostí":::

### <a name="step-3-fill-in-function-code"></a>Krok 3: vyplnění kódu funkce

Přidejte do projektu následující balíčky:
* [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Nahraďte kód v souboru *ProcessDTUpdatetoTSI. cs* následujícím kódem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Uložte kód funkce.

### <a name="step-4-publish-the-function-app-to-azure"></a>Krok 4: publikování aplikace Function App do Azure

Publikujte projekt pomocí funkce *ProcessDTUpdatetoTSI. cs* do aplikace Function App v Azure.

Pokyny k tomu, jak to provést, najdete v části [**publikování aplikace Function App do Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

Uložte název aplikace Function App a použijte ji později ke konfiguraci nastavení aplikace pro dvě centra událostí.

### <a name="step-5-security-access-for-the-function-app"></a>Krok 5: přístup k zabezpečení pro aplikaci Function App

Dále přiřaďte k této funkci **roli přístupu** a **nakonfigurujte nastavení aplikace** tak, aby měla přístup k instanci digitálních vláken Azure. Pokyny k tomu, jak to provést, najdete v části [**nastavení přístupu zabezpečení pro aplikaci Function App**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) tématu *Postupy: nastavení funkce pro zpracování dat* v článku.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Krok 6: Konfigurace nastavení aplikace pro dvě centra událostí

V dalším kroku přidáte proměnné prostředí v nastaveních aplikace Function App, které jim umožní přístup k rozbočovači s časem a časovou řadou.

Pomocí hodnoty **primaryConnectionString** hub hub, kterou jste předtím uložili, vytvořte nastavení aplikace ve vaší aplikaci Function App, které obsahuje připojovací řetězec rozbočovače s dvojitou dobou:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Pomocí hodnoty **primaryConnectionString** centra časových řad, kterou jste předtím uložili, vytvořte nastavení aplikace ve vaší aplikaci Function App, které obsahuje připojovací řetězec centra časových řad:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Vytvořit a připojit instanci Time Series Insights

V této části nastavíte instanci Time Series Insights pro příjem dat z vašeho centra časových řad. Další podrobnosti o tomto procesu najdete v tématu [*kurz: nastavení Azure Time Series Insights prostředí PAYG pro Gen2*](../time-series-insights/tutorial-set-up-environment.md). Pomocí následujících kroků vytvořte prostředí Time Series Insights.

1. V [Azure Portal](https://portal.azure.com)vyhledejte *Time Series Insights prostředí* a vyberte tlačítko **Přidat** . Chcete-li vytvořit prostředí časové řady, vyberte následující možnosti.

    * **Předplatné** – vyberte své předplatné.
        - **Skupina prostředků** – vyberte skupinu prostředků.
    * **Název prostředí** – zadejte název prostředí časových řad.
    * **Umístění** – vyberte umístění.
    * **Úroveň** – vyberte cenovou úroveň **Gen2 (L1)** .
    * **Název vlastnosti** – zadejte **$dtId** (Další informace o výběru hodnoty ID v tématu [*osvědčené postupy pro výběr ID časové řady*](../time-series-insights/how-to-select-tsid.md)).
    * **Název účtu úložiště** – zadejte název účtu úložiště.
    * **Povolit teplé úložiště** – nechte toto pole nastavené na *Ano*.

    Na této stránce můžete ponechat výchozí hodnoty pro další vlastnosti. Vyberte tlačítko **Další: zdroj události >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Snímek obrazovky Azure Portal, ve kterém se má vytvořit Time Series Insights prostředí V příslušných rozevíracích seznamech vyberte své předplatné, skupinu prostředků a umístění a zvolte název svého prostředí." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Snímek obrazovky Azure Portal, ve kterém se má vytvořit Time Series Insights prostředí Je vybraná cenová úroveň Gen2 (L1) a název vlastnosti časové řady $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Na kartě *zdroj události* vyberte následující pole:

   * **Vytvořit zdroj události?** -Vyberte *Ano*.
   * **Typ zdroje** – vyberte *centrum událostí*.
   * **Název** – zadejte název zdroje události.
   * **Předplatné** – vyberte předplatné Azure.
   * **Obor názvů centra událostí** – vyberte obor názvů, který jste vytvořili dříve v tomto článku.
   * **Název centra událostí** – vyberte název **centra časových řad** , který jste vytvořili dříve v tomto článku.
   * **Název zásad přístupu k centru událostí** – vyberte *pravidlo ověřování centra časových řad* , které jste vytvořili dříve v tomto článku.
   * **Skupina uživatelů centra událostí** – vyberte *Nový* a zadejte název skupiny uživatelů centra událostí. Pak vyberte *Přidat*.
   * **Název vlastnosti** – ponechte toto pole prázdné.
    
    Kliknutím na tlačítko **Revize + vytvořit** zkontrolujte všechny podrobnosti. Pak znovu vyberte tlačítko **Revize + vytvořit** a vytvořte prostředí Time Series.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Snímek obrazovky Azure Portal, ve kterém se má vytvořit Time Series Insights prostředí Vytváříte zdroj událostí s informacemi z centra událostí výše. Vytváříte také novou skupinu příjemců." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Posílání dat IoT do digitálních vláken Azure

Pokud chcete začít odesílat data do Time Series Insights, budete muset začít aktualizovat digitální vlastnosti v digitálních provlastnostech Azure s měnícími se hodnotami dat.

Pomocí následujícího příkazu rozhraní příkazového řádku aktualizujte vlastnost *teplota* v *thermostat67* s dvojitou podmínkou, kterou jste přidali do instance v části [požadavky](#prerequisites) .

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Tento příkaz opakujte alespoň čtyřikrát s různými hodnotami teploty** a vytvořte několik datových bodů, které je možné pozorovat později v prostředí Time Series Insights.

> [!TIP]
> Pokud byste chtěli Tento článek udělat s živými simulovanými daty namísto ruční aktualizace hodnot digitálního vlákna, nejdřív se ujistěte, že jste dokončili TIP z oddílu [*požadavky*](#prerequisites) , abyste nastavili funkci Azure, která aktualizuje vlákna z simulovaného zařízení.
Potom můžete spustit zařízení nyní, abyste mohli začít odesílat Simulovaná data a aktualizovat digitální vlákna prostřednictvím tohoto toku dat.

## <a name="visualize-your-data-in-time-series-insights"></a>Vizualizace dat v Time Series Insights

Nyní by data měla být předávána do instance Time Series Insights, která je připravena k analýze. Pomocí následujících kroků můžete prozkoumat data přicházející v.

1. V [Azure Portal](https://portal.azure.com)vyhledejte název prostředí časových řad, který jste vytvořili dříve. V možnostech nabídky na levé straně vyberte *Přehled* a zobrazí se *Adresa URL Time Series Insights Exploreru*. Vyberte adresu URL pro zobrazení změn teploty, které se projeví v prostředí Time Series Insights.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Snímek obrazovky Azure Portal pro výběr adresy URL Time Series Insights Exploreru na kartě Přehled vašeho prostředí Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. V Průzkumníkovi se zobrazí vlákna v instanci digitálního vlákna Azure zobrazené vlevo. Vyberte *thermostat67* vlákna, zvolte *teplotu* vlastností a stiskněte **Přidat**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Snímek obrazovky Průzkumníka Time Series Insights pro výběr thermostat67, vyberte teplotu vlastností a stiskněte tlačítko Přidat." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Nyní byste měli vidět počáteční čtení teploty z termostatu, jak je znázorněno níže. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Snímek obrazovky s Průzkumníkem TSI pro zobrazení počátečních dat o teplotě Jedná se o řádek náhodných hodnot mezi 68 a 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Pokud povolíte, aby simulace běžela mnohem déle, vizualizace bude vypadat přibližně takto:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Snímek obrazovky s Průzkumníkem TSI, kde jsou data o teplotě každého vlákna grafu ve třech paralelních řádcích různých barev." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Další kroky

Digitální vlákna jsou ve výchozím nastavení ukládána jako Plochá hierarchie v Time Series Insights, ale lze je rozšířit pomocí informací o modelu a víceúrovňové hierarchie pro organizaci. Další informace o tomto procesu najdete v článku: 

* [*Kurz: definování a použití modelu*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Můžete napsat vlastní logiku, která automaticky poskytne tyto informace pomocí modelu a dat grafu, která jsou již uložena v rámci digitálních vláken Azure. Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících odkazech:

* [*Postupy: Správa digitálního vlákna*](./how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](./how-to-query-graph.md)