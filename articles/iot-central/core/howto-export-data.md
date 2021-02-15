---
title: Export dat z Azure IoT Central | Microsoft Docs
description: Jak používat novou export dat k exportu dat IoT do Azure a vlastních cílů cloudu.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: d31673b8d789cff5de3ddce63b67a98854b7aabc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515899"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Export dat IoT do cloudových cílů pomocí exportu dat

> [!Note]
> Tento článek popisuje funkce exportu dat v IoT Central. Informace o funkcích exportu starších dat najdete v tématu [Export dat IoT do cloudových cílů pomocí exportu dat (starší verze)](./howto-export-data-legacy.md).

Tento článek popisuje, jak používat novou funkci exportu dat v Azure IoT Central. Pomocí této funkce můžete průběžně exportovat filtrovaná a obohacená data IoT z vaší aplikace IoT Central. Export dat nabízí změny téměř v reálném čase do dalších částí vašeho cloudového řešení pro rychlé poznatky, analýzy a úložiště.

Můžete například:

- Průběžně exportujte data telemetrie a změny vlastností ve formátu JSON téměř v reálném čase.
- Filtrování datových proudů pro export dat, která odpovídají vlastním podmínkám.
- Obohacení datových proudů o vlastní hodnoty a hodnoty vlastností ze zařízení.
- Odešlete data do umístění, jako jsou například Azure Event Hubs, Azure Service Bus, Azure Blob Storage a koncové body Webhooku.

> [!Tip]
> Když zapnete export dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte nejdříve export dat.

## <a name="prerequisites"></a>Požadavky

Chcete-li používat funkce exportu dat, je nutné mít [aplikaci V3](howto-get-app-info.md)a musíte mít oprávnění k [exportu dat](howto-manage-users-roles.md) .

Pokud máte aplikaci v2, přečtěte si téma [migrace aplikace IoT Central v2 na V3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací exportu dat musí existovat váš cíl exportu. V současné době jsou k dispozici následující typy cílů:

- Azure Event Hubs
- Fronta služby Azure Service Bus
- Téma služby Azure Service Bus
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Vytvoření cíle Event Hubs

Pokud nemáte existující Event Hubs obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](../../event-hubs/event-hubs-create.md).

1. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

1. Vygenerujte klíč, který se použije při nastavení exportu dat v IoT Central:

    - Vyberte instanci centra událostí, kterou jste vytvořili.
    - Vyberte **nastavení > zásady sdíleného přístupu**.
    - Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** .
    - Zkopírujte buď primární nebo sekundární připojovací řetězec. Tento připojovací řetězec můžete použít k nastavení nového cíle v IoT Central.
    - Alternativně můžete vygenerovat připojovací řetězec pro celý Event Hubs obor názvů:
        1. V Azure Portal přejít na obor názvů Event Hubs.
        2. V části **Nastavení** vyberte **zásady sdíleného přístupu** .
        3. Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** .
        4. Zkopírování primárního nebo sekundárního připojovacího řetězce
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Vytvoření Service Bus fronty nebo cíle tématu

Pokud nemáte existující Service Bus obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentaci Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Chcete-li vytvořit frontu nebo téma pro export do, přejít na obor názvů Service Bus a vybrat **+ fronta** nebo **+ téma**.

1. Vygenerujte klíč, který se použije při nastavení exportu dat v IoT Central:

    - Vyberte frontu nebo téma, které jste vytvořili.
    - Vyberte **nastavení/zásady sdíleného přístupu**.
    - Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** .
    - Zkopírujte buď primární nebo sekundární připojovací řetězec. Tento připojovací řetězec můžete použít k nastavení nového cíle v IoT Central.
    - Alternativně můžete vygenerovat připojovací řetězec pro celý Service Bus obor názvů:
        1. V Azure Portal přejít na obor názvů Service Bus.
        2. V části **Nastavení** vyberte **zásady sdíleného přístupu** .
        3. Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** .
        4. Zkopírování primárního nebo sekundárního připojovacího řetězce

### <a name="create-an-azure-blob-storage-destination"></a>Vytvoření cíle Azure Blob Storage

Pokud nemáte existující účet úložiště Azure pro export do, postupujte takto:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů úložiště Azure Blob](../../storage/blobs/storage-quickstart-blobs-portal.md) nebo [Azure Data Lake Storagech účtů úložiště v2](../../storage/common/storage-account-create.md). Export dat může zapisovat jenom data do účtů úložiště, které podporují objekty blob bloku. Následující seznam uvádí známé kompatibilní typy účtů úložiště:

    |Úroveň výkonu|Typ účtu|
    |-|-|
    |Standard|Pro obecné účely v2|
    |Standard|Pro obecné účely v1|
    |Standard|Blob Storage|
    |Premium|Úložiště objektů blob bloku|

1. Pokud chcete vytvořit kontejner v účtu úložiště, klikněte na účet úložiště. V části **BLOB Service** vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.

1. Kliknutím na **nastavení > přístupové klíče** vygenerujte připojovací řetězec pro váš účet úložiště. Zkopírujte jeden ze dvou připojovacích řetězců.

### <a name="create-a-webhook-endpoint"></a>Vytvoření koncového bodu Webhooku

Data můžete exportovat do veřejně dostupného koncového bodu HTTP Webhooku. Pomocí [RequestBin](https://requestbin.net/)můžete vytvořit testovací koncový bod Webhooku. RequestBin omezuje požadavek na dosažení limitu požadavků:

1. Otevřete [RequestBin](https://requestbin.net/).
2. Vytvořte novou RequestBin a zkopírujte **adresu URL přihrádky**. Tuto adresu URL použijete při testování exportu dat.

## <a name="set-up-data-export"></a>Nastavení exportu dat

Teď, když máte cíl exportovat data do, nastavte export dat do aplikace IoT Central:

1. Přihlaste se k aplikaci IoT Central.

1. V levém podokně vyberte **exportovat data**.

    > [!Tip]
    > Pokud nevidíte **exportovat data** v levém podokně, nemáte oprávnění ke konfiguraci exportu dat ve vaší aplikaci. Pokud chcete nastavit export dat, obraťte se na správce.

1. Vyberte **+ Nový export**.

1. Zadejte zobrazovaný název pro nový export a ujistěte se, že je **povolen** export dat.

1. Vyberte typ dat, který chcete exportovat. Následující tabulka uvádí podporované typy exportu dat:

    | Datový typ | Popis | Formát dat |
    | :------------- | :---------- | :----------- |
    |  Telemetrie | Exportujte zprávy telemetrie ze zařízení téměř v reálném čase. Každá exportovaná zpráva obsahuje úplný obsah původní zprávy zařízení, normalizováno.   |  [Formát zprávy telemetrie](#telemetry-format)   |
    | Změny vlastností | Exportujte změny do vlastností zařízení a cloudu téměř v reálném čase. V případě vlastností zařízení jen pro čtení jsou exportovány změny hlášených hodnot. Pro vlastnosti pro čtení i zápis jsou vyexportovány obě hlášené i požadované hodnoty. | [Formát zprávy o změně vlastnosti](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Volitelně můžete přidat filtry pro snížení objemu exportovaných dat. Pro každý typ exportu dat jsou k dispozici různé typy filtrů:

    K filtrování telemetrie můžete:

    - **Vyfiltruje** exportovaný datový proud tak, aby obsahoval pouze telemetrii ze zařízení, která odpovídají názvu zařízení, ID zařízení a podmínky filtru šablony zařízení.
    - **Filtrovat** přes možnosti: Pokud zvolíte položku telemetrie v rozevíracím seznamu **název** , exportovaný datový proud obsahuje jenom telemetrii, která splňuje podmínku filtru. Pokud v rozevíracím seznamu **název** zvolíte položku zařízení nebo cloudová vlastnost, exportovaný datový proud obsahuje jenom telemetrii ze zařízení s vlastnostmi, které odpovídají podmínkám filtru.
    - **Filtr vlastností zpráv**: zařízení, která používají sady SDK pro zařízení, mohou odesílat *vlastnosti zprávy* nebo *Vlastnosti aplikace* v každé zprávě telemetrie. Vlastnosti jsou kontejner párů klíč-hodnota, které označí zprávu vlastními identifikátory. Chcete-li vytvořit filtr vlastností zprávy, zadejte klíč vlastnosti zprávy, který hledáte, a zadejte podmínku. Exportují se jenom zprávy telemetrie s vlastnostmi, které odpovídají zadané podmínce filtru. Jsou podporovány následující řetězcové operátory porovnání: Equals, není rovno, obsahuje, neobsahuje, existuje, neexistuje. [Přečtěte si další informace o vlastnostech aplikace z IoT Hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Chcete-li filtrovat změny vlastností, použijte **Filtr schopností**. V rozevíracím seznamu vyberte položku Vlastnosti. Exportovaný datový proud obsahuje pouze změny vybrané vlastnosti, která splňuje podmínku filtru.

<a name="DataExportEnrichmnents"></a>
1. Volitelně můžete rozšířit exportované zprávy s dalšími metadaty páru klíč-hodnota. K dispozici jsou následující obohacení pro telemetrie a vlastnost pro změny typů exportu dat:

    - **Vlastní řetězec**: přidá do každé zprávy vlastní statický řetězec. Zadejte libovolný klíč a zadejte libovolnou hodnotu řetězce.
    - **Vlastnost**: přidá do každé zprávy aktuální nahlášenou vlastnost nebo hodnotu vlastnosti cloudu. Zadejte libovolný klíč a vyberte vlastnost zařízení nebo cloudu. Pokud je vyexportovaná zpráva ze zařízení, které nemá zadanou vlastnost, vyexportovaná zpráva nezíská obohacení.

1. Přidejte nový cíl nebo přidejte cíl, který jste už vytvořili. Vyberte odkaz **vytvořit nové** a přidejte následující informace:

    - **Název cíle**: zobrazovaný název cíle v IoT Central.
    - **Cílový typ**: Vyberte typ cíle. Pokud jste ještě nevytvořili cíl, přečtěte si téma [Nastavení cíle exportu](#set-up-export-destination).
    - V případě služby Azure Event Hubs Azure Service Bus Queue nebo téma vložte připojovací řetězec pro váš prostředek a v případě potřeby zadejte název centra událostí s rozlišováním velkých a malých písmen, fronty nebo tématu.
    - Pro Azure Blob Storage vložte připojovací řetězec pro váš prostředek a v případě potřeby zadejte název kontejneru rozlišující velká a malá písmena.
    - Pro Webhook vložte adresu URL zpětného volání pro svůj koncový bod Webhooku. Volitelně můžete nakonfigurovat autorizaci Webhooku (OAuth 2,0 a autorizační token) a přidat vlastní hlavičky. 
        - U OAuth 2,0 se podporuje jenom tok přihlašovacích údajů klienta. Po uložení cíle IoT Central bude komunikovat se zprostředkovatelem OAuth, aby získal autorizační token. Tento token bude připojen k hlavičce "Authorization" pro každou zprávu odeslanou do tohoto cíle.
        - U autorizačního tokenu můžete zadat hodnotu tokenu, která bude přímo připojena k hlavičce "Authorization" pro každou zprávu odeslanou do tohoto cíle.
    - Vyberte **Vytvořit**.

1. Vyberte **+ cíl** a zvolte cíl z rozevíracího seznamu. Do jednoho exportu můžete přidat až pět cílů.

1. Po dokončení nastavení exportu vyberte **Uložit**. Po několika minutách se vaše data zobrazí ve vašich cílech.

## <a name="monitor-your-export"></a>Monitorování exportu

Kromě toho, že se ve službě IoT Central zobrazuje stav exportů, můžete sledovat, kolik dat provádí vaše exporty, a sledovat chyby při exportu v datové platformě Azure Monitor. K metrikám o vašich exportech a stavu zařízení můžete přistupovat v grafech v Azure Portal, REST API nebo dotazech v PowerShellu nebo v rozhraní příkazového řádku Azure. V současné době můžete monitorovat tyto metriky exportu dat v Azure Monitor:

1. Počet zpráv, které jsou příchozí pro export před použitím filtrů
2. Počet zpráv, které procházejí filtry
3. Počet zpráv úspěšně exportovaných do cílových umístění
4. Počet zjištěných chyb
 
[Přečtěte si další informace o tom, jak získat přístup k IoT Central metriky.](howto-monitor-application-health.md)

## <a name="destinations"></a>Cíle

### <a name="azure-blob-storage-destination"></a>Cíl Azure Blob Storage

Data se exportují jednou za minutu a každý soubor, který obsahuje dávku změn od předchozího exportu. Exportovaná data se ukládají ve formátu JSON. Výchozí cesty k exportovaným datům v účtu úložiště jsou:

- Telemetrie: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Změny vlastností: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Chcete-li procházet exportované soubory v Azure Portal, přejděte k souboru a vyberte **Upravit objekt BLOB**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Cíle Azure Event Hubs a Azure Service Bus

Data se exportují prakticky v reálném čase. Data jsou v těle zprávy a jsou ve formátu JSON kódovaném jako UTF-8.

Poznámky nebo kontejner systému vlastností zprávy obsahují `iotcentral-device-id` pole,, `iotcentral-application-id` `iotcentral-message-source` a `iotcentral-message-type` , která mají stejné hodnoty jako odpovídající pole v těle zprávy.

### <a name="webhook-destination"></a>Cíl Webhooku

V případě míst pro Webhooky se data exportují i téměř v reálném čase. Data v těle zprávy jsou ve stejném formátu jako pro Event Hubs a Service Bus.

## <a name="telemetry-format"></a>Formát telemetrie

Každá exportovaná zpráva obsahuje normalizovanou podobu celé zprávy odeslané zařízením v těle zprávy. Zpráva je ve formátu JSON a je kódovaná jako UTF-8. Mezi informace v každé zprávě patří:

- `applicationId`: ID aplikace IoT Central.
- `messageSource`: Zdroj zprávy – `telemetry` .
- `deviceId`: ID zařízení, které odeslalo zprávu telemetrie.
- `schema`: Název a verze schématu datové části.
- `templateId`: ID šablony zařízení přidružené k zařízení.
- `enrichments`: Jakékoli obohacení nastavené na export.
- `messageProperties`: Další vlastnosti, které zařízení poslalo se zprávou. Tyto vlastnosti jsou někdy označovány jako *Vlastnosti aplikace*. [Další informace najdete v dokumentaci IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Pro Event Hubs a Service Bus IoT Central exportuje novou zprávu rychle po přijetí zprávy ze zařízení. Ve vlastnostech uživatele (také označovaných jako vlastnosti aplikace) každé zprávy `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` jsou automaticky zahrnuty, a.

Pro úložiště objektů BLOB se zprávy účtují a exportují jednou za minutu.

Následující příklad ukazuje exportovanou zprávu telemetrie:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Vlastnosti zprávy

Zprávy telemetrie mají kromě datové části telemetrie také vlastnosti pro metadata. Předchozí fragment kódu ukazuje příklady systémových zpráv, například `deviceId` a `enqueuedTime` . Další informace o vlastnostech systémové zprávy najdete v tématu [systémové vlastnosti D2C zpráv o IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Pokud potřebujete do zpráv telemetrie přidat vlastní metadata, můžete do zpráv telemetrie přidat vlastnosti. Například je třeba přidat časové razítko, když zařízení vytvoří zprávu.

Následující fragment kódu ukazuje, jak přidat `iothub-creation-time-utc` vlastnost do zprávy při jejím vytváření na zařízení:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

Následující fragment kódu ukazuje tuto vlastnost ve zprávě exportované do úložiště objektů BLOB:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Formát změn vlastností

Každá zpráva nebo záznam představuje jednu změnu vlastnosti zařízení nebo cloudu. U vlastností zařízení se jako samostatná zpráva exportují jenom změny v hlášené hodnotě. Mezi informace v exportované zprávě patří:

- `applicationId`: ID aplikace IoT Central.
- `messageSource`: Zdroj zprávy – `properties` .
- `messageType`: Buď `cloudPropertyChange` , `devicePropertyDesiredChange` nebo `devicePropertyReportedChange` .
- `deviceId`: ID zařízení, které odeslalo zprávu telemetrie.
- `schema`: Název a verze schématu datové části.
- `templateId`: ID šablony zařízení přidružené k zařízení.
- `enrichments`: Jakékoli obohacení nastavené na export.

V případě Event Hubs a Service Bus IoT Central exportuje data nových zpráv do centra událostí nebo Service Bus fronty nebo tématu téměř v reálném čase. Ve vlastnostech uživatele (také označovaných jako vlastnosti aplikace) každé zprávy `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` jsou automaticky zahrnuty,,, a `iotcentral-message-type` .

Pro úložiště objektů BLOB se zprávy účtují a exportují jednou za minutu.

Následující příklad ukazuje zprávu o změně exportovaných vlastností přijatou v Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Porovnání exportu a exportu dat ze starších verzí

V následující tabulce jsou uvedeny rozdíly mezi [exportem starších dat](howto-export-data-legacy.md) a novými funkcemi exportu dat:

| Možnosti  | Export zastaralých dat | Nový export dat |
| :------------- | :---------- | :----------- |
| Dostupné datové typy | Telemetrie, zařízení a šablony zařízení | Telemetrie, změny vlastností |
| Filtrování | Žádné | Závisí na typu exportovaného dat. Pro telemetrii, filtrování podle telemetrie, vlastností zpráv a hodnot vlastností |
| Obohacení | Žádné | Obohacení vlastním řetězcem nebo hodnotou vlastnosti v zařízení |
| Cíle | Azure Event Hubs, Azure Service Bus fronty a témata, Azure Blob Storage | Stejné jako u starších verzí exportu dat a webhooků|
| Podporované verze aplikace | V2, V3 | Jenom V3 |
| Významné limity | 5 exportů na aplikaci, 1 cíl na export | 10 EXPORTS – cílová připojení na aplikaci |

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak používat nový export dat, je navržený další krok, kde se dozvíte, [Jak používat analýzy v IoT Central](./howto-create-analytics.md)
