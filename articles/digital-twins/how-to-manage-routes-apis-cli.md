---
title: Správa koncových bodů a tras (rozhraní API a rozhraní příkazového řádku)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit a spravovat koncové body a trasy událostí pro data digitálních vláken Azure.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 97fad1b984ad34722a952a31d8245eb68417a2ab
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779966"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Správa koncových bodů a tras v Azure Digital revláken (rozhraní API a CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

V digitálních prostředcích Azure můžete směrovat [oznámení událostí](how-to-interpret-event-data.md) na služby pro příjem dat nebo připojené výpočetní prostředky. K tomu je potřeba nejprve nastavit **koncové body**, které mohou události přijímat. Pak můžete vytvořit  [**trasy událostí**](concepts-route-events.md) , které určují, které události generované pomocí digitálních vláken Azure jsou doručeny do těch koncových bodů.

Tento článek vás provede procesem vytváření koncových bodů a tras pomocí [rozhraní REST API](/rest/api/azure-digitaltwins/), [sady .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)a rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

Alternativně můžete také spravovat koncové body a trasy pomocí [Azure Portal](https://portal.azure.com). Verzi tohoto článku, která místo toho používá portál, najdete v tématu [*How to: Manage Endpoints and Routes (portál)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Předpoklady

- Budete potřebovat **účet Azure** (můžete [si ho nastavit zdarma).](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- V předplatném Azure budete potřebovat **instanci digitálního vlákna Azure** . Pokud instanci již nemáte, můžete ji vytvořit pomocí kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-cli.md). Použijte následující hodnoty z instalačního programu užitečné pro pozdější použití v tomto článku:
    - Název instance
    - Skupina prostředků

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Vytvoření koncového bodu pro digitální vlákna Azure

Toto jsou podporované typy koncových bodů, které můžete vytvořit pro instanci:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Další informace o různých typech koncových bodů najdete v tématu [*Výběr mezi službou zasílání zpráv Azure*](../event-grid/compare-messaging-services.md).

V této části se dozvíte, jak tyto koncové body vytvořit pomocí rozhraní příkazového řádku Azure CLI. Můžete také spravovat koncové body pomocí [rozhraní API roviny ovládacího prvku DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Vytvoření koncového bodu

Jakmile vytvoříte prostředky koncového bodu, můžete je použít pro koncový bod digitálních vláken Azure. Následující příklady ukazují, jak vytvořit koncové body pomocí příkazu [AZ DT Endpoint Create](/cli/azure/ext/azure-iot/dt/endpoint/create) pro rozhraní příkazového [řádku Azure Digital zdvojené](how-to-use-cli.md). Zástupné symboly v příkazech nahraďte podrobnostmi vašich vlastních prostředků.

Vytvoření koncového bodu Event Grid:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Vytvoření koncového bodu Event Hubs (ověřování pomocí klíče):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Vytvoření koncového bodu tématu Service Bus (ověřování pomocí klíče):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Po úspěšném spuštění těchto příkazů budou k dispozici jako koncový bod v rámci digitálních vláken Azure pod názvem, který jste zadali s argumentem, a Service Bus téma Event Grid, Event hub nebo. `--endpoint-name` Tento název obvykle použijete jako cíl **trasy události**, kterou vytvoříte [později v tomto článku](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Vytvoření koncového bodu s ověřováním na základě identity

Můžete také vytvořit koncový bod, který má ověřování na základě identity, a použít koncový bod se [spravovanou identitou](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Tato možnost je k dispozici pouze pro centra událostí a koncové body typu Service Bus (není podporováno pro Event Grid).

Příkaz CLI pro vytvoření tohoto typu koncového bodu je uvedený níže. K připojení zástupných symbolů v příkazu budete potřebovat následující hodnoty:
* ID prostředku Azure instance digitálního vlákna Azure
* název koncového bodu
* Typ koncového bodu
* obor názvů prostředku koncového bodu
* název centra událostí nebo Service Bus tématu
* umístění instance digitálního vlákna Azure

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Vytvoření koncového bodu s nedoručenými písmeny

Když koncový bod nemůže doručovat událost v určitém časovém období nebo po pokusu o doručení události v určitém počtu opakování, může odeslat nedoručenou událost do účtu úložiště. Tento proces se označuje jako **nedoručené**.

Koncové body s povoleným nedoručenými zprávami je možné nastavit pomocí rozhraní příkazového [řádku](how-to-use-cli.md) Azure Digital vláknas CLI nebo [řídicí roviny](how-to-use-apis-sdks.md#overview-control-plane-apis).

Další informace o nedoručených písmenech najdete v tématu [*Koncepty: směrování událostí*](concepts-route-events.md#dead-letter-events). Pokyny, jak nastavit koncový bod pomocí nedoručených zpráv, můžete pokračovat ve zbytku této části.

#### <a name="set-up-storage-resources"></a>Nastavení prostředků úložiště

Před nastavením umístění nedoručených zpráv musíte mít v účtu Azure nastavený [účet úložiště](../storage/common/storage-account-create.md?tabs=azure-portal) s [kontejnerem](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) . 

Identifikátor URI pro tento kontejner zadáte při pozdějším vytvoření koncového bodu. Umístění nedoručených zpráv se poskytne koncovému bodu jako identifikátor URI kontejneru s [tokenem SAS](../storage/common/storage-sas-overview.md). Tento token potřebuje `write` oprávnění pro cílový kontejner v rámci účtu úložiště. Plně vytvořený **identifikátor URI SAS pro nedoručené písmeno** bude ve formátu: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

Postupujte podle následujících kroků a nastavte tyto prostředky úložiště ve vašem účtu Azure, abyste se připravili na nastavení připojení koncového bodu v další části.

1. Postupujte podle kroků v části [*Vytvoření účtu úložiště*](../storage/common/storage-account-create.md?tabs=azure-portal) pro vytvoření **účtu úložiště** v předplatném Azure. Poznamenejte si název účtu úložiště, abyste ho mohli později použít.
2. Postupujte podle kroků v části [*vytvoření kontejneru*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) a vytvořte **kontejner** v rámci nového účtu úložiště. Poznamenejte si název kontejneru, abyste ho mohli později použít.
3. Dále vytvořte **token SAS** pro účet úložiště, který může koncový bod použít pro přístup k němu. Začněte tím, že přejdete na svůj účet úložiště v [Azure Portal](https://ms.portal.azure.com/#home) (můžete ho najít podle názvu pomocí panelu hledání na portálu).
4. Na stránce účet úložiště v levém navigačním panelu vyberte odkaz _sdílený přístupový podpis_ a začněte s nastavením tokenu SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Stránka účtu úložiště v Azure Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Na *stránce sdílený přístupový podpis* v části *povolené služby* a *Povolené typy prostředků* vyberte libovolné nastavení, které chcete. Bude nutné vybrat alespoň jedno pole v každé kategorii. V části *povolená oprávnění* zvolte **zápis** (Pokud chcete, můžete také vybrat další oprávnění).
1. Nastavte libovolné hodnoty, které chcete pro zbývající nastavení.
1. Až budete hotovi, vyberte tlačítko _Generovat SAS a připojovací řetězec_ pro vygenerování tokenu SAS. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Stránka účet úložiště v Azure Portal zobrazující všechna nastavení výběru pro vygenerování tokenu SAS a zvýraznění tlačítka generovat SAS a připojovací řetězec" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Tím se vygeneruje několik hodnot SAS a připojovacích řetězců dole na stejné stránce pod vybraným nastavením. Posuňte se dolů a zobrazte hodnoty a pomocí ikony *Kopírovat do schránky* Zkopírujte hodnotu **tokenu SAS** . Uložte ho pro pozdější použití.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Kopírovat token SAS pro použití v tajných klíčích nedoručených zpráv" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Vytvoření koncového bodu s nedoručenými písmeny

Pokud chcete vytvořit koncový bod, který má povolené nedoručené zprávy, přidejte následující parametr nedoručených zpráv do příkazu [AZ DT Endpoint Create](/cli/azure/ext/azure-iot/dt/endpoint/create) pro rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

Hodnota parametru je **identifikátor URI SAS nedoručených zpráv** , který se skládá z názvu účtu úložiště, názvu kontejneru a tokenu SAS, který jste shromáždili v [předchozí části](#set-up-storage-resources). Tento parametr vytvoří koncový bod s ověřováním pomocí klíče.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Tento parametr přidejte na konec příkazů pro vytváření koncových bodů v části [*Vytvoření koncového bodu*](#create-the-endpoint) dříve a vytvořte koncový bod požadovaného typu, který má povoleno nedoručené e-maily.

Alternativně můžete vytvořit koncové body s nedoručenými písmeny pomocí [rozhraní API plochy pro řízení digitálních vláken Azure](how-to-use-apis-sdks.md#overview-control-plane-apis) místo rozhraní příkazového řádku. Provedete to tak, že si prohlédněte [dokumentaci k DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) , kde najdete informace o tom, jak strukturovat požadavek a přidat parametry nedoručených zpráv.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Vytvoření koncového bodu s nedoručenými zprávami s ověřováním na základě identity

Můžete také vytvořit koncový bod nedoručených zpráv, který má ověřování na základě identity, pro použití koncového bodu se [spravovanou identitou](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Tato možnost je k dispozici pouze pro centra událostí a koncové body typu Service Bus (není podporováno pro Event Grid).

Pokud chcete vytvořit tento typ koncového bodu, použijte stejný příkaz CLI ze starší verze a [vytvořte koncový bod s ověřováním založeným na identitě](#create-an-endpoint-with-identity-based-authentication)s polem extra v datové části JSON pro `deadLetterUri` .

Tady jsou hodnoty, které budete muset připojit k zástupným symbolům v příkazu:
* ID prostředku Azure instance digitálního vlákna Azure
* název koncového bodu
* Typ koncového bodu
* obor názvů prostředku koncového bodu
* název centra událostí nebo Service Bus tématu
* Podrobnosti o **identifikátoru URI SAS nedoručených písmen** : název účtu úložiště, název kontejneru
* umístění instance digitálního vlákna Azure

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Schéma úložiště zpráv

Po nastavení koncového bodu s nedoručenými zprávami budou v účtu úložiště uloženy nedoručené zprávy v následujícím formátu:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

Nedoručené zprávy budou odpovídat schématu původní události, která byla určena k doručení do původního koncového bodu.

Tady je příklad zprávy nedoručených zpráv pro [dvojitou dobu vytvoření oznámení](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Vytvoření trasy události

**Předpoklad**: před přechodem na vytvoření trasy je nutné vytvořit koncové body, jak je popsáno výše v tomto článku. Po dokončení nastavení koncových bodů můžete pokračovat v vytváření směrování událostí.

> [!NOTE]
> Pokud jste své koncové body v poslední době nasadili, ověřte, že se jejich nasazení dokončilo, **než** se pokusíte použít pro novou trasu události. Pokud se nasazení trasy nepovede, protože koncové body nejsou připravené, počkejte pár minut a zkuste to znovu.
>
> Pokud tento tok spouštíte, můžete si ho vymezit tak, že sestavíte za 2-3 minut čekání na dokončení nasazování služby koncového bodu a teprve potom přejdete na nastavení směrování.

Aby bylo možné ve skutečnosti odesílat data z digitálních vláken Azure do koncového bodu, bude nutné definovat **trasu události**. Trasy událostí se používají pro připojení toku událostí, v celém systému a služeb pro příjem dat.

Definice trasy může obsahovat tyto prvky:
* Název trasy, kterou chcete použít
* Název koncového bodu, který chcete použít
* Filtr definující události posílané do koncového bodu 

Pokud název trasy neexistuje, žádné zprávy se nesměrují mimo digitální vlákna Azure. Pokud je název trasy a filtr je `true` , všechny zprávy jsou směrovány do koncového bodu. Pokud je k dispozici název trasy a je přidán jiný filtr, budou zprávy na základě filtru filtrovány.

Jedna trasa by měla umožňovat výběr více oznámení a typů událostí. 

Trasy událostí je možné vytvořit pomocí [rozhraní API roviny dat služby Azure Digital **EventRoutess**](/rest/api/digital-twins/dataplane/eventroutes) a [příkazu **AZ DT Route** CLI](/cli/azure/ext/azure-iot/dt/route). Zbytek této části vás provede procesem vytváření.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Vytváření tras pomocí rozhraní API a sady C# SDK

Jedním ze způsobů, jak definovat trasy událostí, je s [rozhraními API roviny dat](how-to-use-apis-sdks.md#overview-data-plane-apis). Ukázky v této části používají [sadu .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client).

`CreateOrReplaceEventRouteAsync` je volání sady SDK, které se používá k přidání trasy události. Tady je příklad jeho použití:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

#### <a name="event-route-sample-sdk-code"></a>Ukázkový kód sady SDK pro směrování událostí

Následující ukázková metoda ukazuje, jak vytvořit, vypsat a odstranit trasu události pomocí sady C# SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Vytváření tras pomocí rozhraní příkazového řádku

Trasy je taky možné spravovat pomocí příkazů [AZ DT Route](/cli/azure/ext/azure-iot/dt/route) pro rozhraní příkazového řádku Azure Digital revlákens. 

Další informace o použití rozhraní příkazového řádku a příkazů, které jsou k dispozici, najdete v tématu [*Postup: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md).

## <a name="filter-events"></a>Události filtru

Bez filtrování se koncovým bodům dostanou nejrůznější události z digitálních vláken Azure:
* Telemetrie aktivovaná pomocí [digitálních vláken](concepts-twins-graph.md) pomocí rozhraní API služby Azure Digital Service
* Dvojitá oznámení o změně vlastností, která se aktivují při změnách vlastností u všech vláken v instanci digitálních vláken Azure
* Události životního cyklu aktivované při vytváření nebo odstraňování vazeb

Odesílaným událostem můžete omezit přidáváním **filtru** pro koncový bod do trasy události.

>[!NOTE]
> Filtry rozlišují **Velká** a malá písmena a musí se shodovat s případem datové části (což nemusí nutně odpovídat modelu Case).

Chcete-li přidat filtr, můžete použít požadavek PUT na *protokol https://{The-Azure-Digital-reprops-název_hostitele}/eventRoutes/{Event-Route-Name}? API-Version = 2020-10-31* s následujícím textem:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Tady jsou podporované filtry tras. Pomocí podrobností ve sloupci *schéma textu filtru* nahraďte `<filter-text>` zástupný text v těle žádosti výše.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o různých typech zpráv o událostech, které můžete získat:
* [*Postupy: interpretace dat událostí*](how-to-interpret-event-data.md)
