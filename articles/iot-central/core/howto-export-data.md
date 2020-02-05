---
title: Exportujte data služby Azure IoT Central | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Event Hubs, Azure Service Bus a Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 058fe9aea87879fe85dcbc6dcb864fd841fcb049
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026793"
---
# <a name="export-your-azure-iot-central-data"></a>Export dat IoT Central Azure



*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central exportovat data do instancí **azure Event Hubs**, **Azure Service Bus**nebo **Azure Blob Storage** . Data se exportují ve formátu JSON a můžou zahrnovat informace o telemetrie, zařízeních a šablonách zařízení. Použít exportovaná data pro:

- Přehledy a analýzy teplé cesty. Tato možnost zahrnuje aktivaci vlastních pravidel v Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo jejich předání prostřednictvím Azure Functions pro transformaci.
- Analýzy studených cest, jako jsou například školicí modely v Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Když zapnete export průběžných dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.

## <a name="prerequisites"></a>Požadavky

Musíte být správce aplikace IoT Central, nebo mít oprávnění k exportu dat.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací průběžného exportu dat musí existovat váš cíl exportu.

### <a name="create-event-hubs-namespace"></a>Vytvořit obor názvů Event Hubs

Pokud nemáte existující Event Hubs obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Vyberte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

### <a name="create-service-bus-namespace"></a>Vytvořit obor názvů Service Bus

Pokud nemáte existující Service Bus obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentaci Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Vyberte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Pokud chcete vytvořit frontu nebo téma, do kterého chcete exportovat, klikněte na obor názvů Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části.

Pokud zvolíte Service Bus jako cíl exportu, nesmí fronty a témata obsahovat relace nebo je povoleno zjišťování duplicitních dat. Pokud je některá z těchto možností povolená, některé zprávy ve frontě nebo tématu nepřijde.

### <a name="create-storage-account"></a>Vytvoření účtu úložiště

Pokud nemáte existující účet Azure Storage pro export do, postupujte podle těchto kroků:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) nebo [účtů úložiště Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Pokud se rozhodnete exportovat data do účtu úložiště Azure Data Lake Storage v2, musíte jako **druh účtu**vybrat **BlobStorage** .
    - Data můžete exportovat do účtů úložiště v předplatných, která jsou odlišná než ta pro vaši aplikaci IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

2. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service**vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.

## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte cíl exportovat data do, postupujte podle těchto kroků a nastavte průběžný export dat.

1. Přihlaste se k aplikaci IoT Central.

2. V levém podokně vyberte **exportovat data**.

    > [!Note]
    > Pokud nevidíte exportovat data v levém podokně, nemáte oprávnění ke konfiguraci exportu dat ve vaší aplikaci. Pokud chcete nastavit export dat, obraťte se na správce.

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte jednu z **Event Hubs Azure**, **Azure Service Bus**nebo **Azure Blob Storage** . Maximální počet exportů na aplikaci je 5.

    ![Vytvořit nový export průběžných dat](media/howto-export-data/new-export-definition.png)

4. V rozevíracím seznamu vyberte **obor názvů Event Hubs**, **Service Bus obor**názvů, **obor názvů účtu úložiště**nebo **Zadejte připojovací řetězec**.

    - V rámci stejného předplatného jako aplikace pro IoT Central se zobrazí jenom účty úložiště, Event Hubs obory názvů a Service Bus obory názvů. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.
    - U aplikací vytvořených pomocí bezplatného cenového plánu je jediným způsobem, jak nakonfigurovat průběžný export dat, prostřednictvím připojovacího řetězce. Pro aplikace v cenovém plánu zdarma nemáte přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí](media/howto-export-data/export-event-hub.png)

5. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Získání připojovacího řetězce pro:
    - Event Hubs nebo Service Bus, v Azure Portal přejít na obor názvů.
        - V části **Nastavení**vyberte **zásady sdíleného přístupu** .
        - Vyberte výchozí **RootManageSharedAccessKey** nebo vytvořte nový.
        - Zkopírování primárního nebo sekundárního připojovacího řetězce
    - Účet úložiště, v Azure Portal přejít na účet úložiště:
        - V části **Nastavení**vyberte **přístupové klíče** .
        - Zkopírujte buď připojovací řetězec klíč1, nebo připojovací řetězec key2.

6. V rozevíracím seznamu vyberte centrum událostí, frontu, téma nebo kontejner.

7. V části **data, která chcete exportovat**, vyberte typy dat k exportu nastavením typ na **zapnuto**.

8. Pokud chcete zapnout funkci průběžného exportu dat, **Ujistěte se,** že je zapnutý přepínač **zapnuto** . Vyberte **Uložit**.

9. Po několika minutách se vaše data zobrazí ve zvoleném cíli.

## <a name="export-contents-and-format"></a>Exportovat obsah a formát

Exportovaná data telemetrie obsahují celou zprávu, kterou zařízení odesílá do IoT Central, nejen samotné hodnoty telemetrie. Data exportovaných zařízení obsahují změny vlastností a metadat všech zařízení a exportované šablony zařízení obsahují změny všech šablon zařízení.

Pro Event Hubs a Service Bus se data exportují prakticky v reálném čase. Data jsou umístěna ve vlastnosti text a jsou ve formátu JSON (příklady najdete níže).

V případě Blob Storage se data exportují jednou za minutu a každý soubor obsahující dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve formátu JSON ve třech složkách. Výchozí cesty v účtu úložiště jsou:

- Telemetrie: _{Container}/{App-ID}/Telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Zařízení: _{Container}/{App-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Šablony zařízení: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Exportované soubory můžete procházet v Azure Portal tak, že přejdete do souboru a kliknete na kartu **Upravit objekt BLOB** .


## <a name="telemetry"></a>Telemetrie

V případě Event Hubs a Service Bus je nová zpráva exportována rychle po IoT Central přijetí zprávy ze zařízení a Každá exportovaná zpráva obsahuje úplnou zprávu, kterou zařízení odeslalo ve formátu JSON.

U Blob Storage se zprávy účtují a exportují jednou za minutu. Exportované soubory používají stejný formát jako soubory zpráv exportované [IoT Hub směrováním zpráv](../../iot-hub/tutorial-routing.md) do úložiště objektů BLOB. 

> [!NOTE]
> V případě Blob Storage zajistěte, aby vaše zařízení odesílala zprávy, které mají `contentType: application/JSON` a `contentEncoding:utf-8` (nebo `utf-16``utf-32`). Příklad najdete v [dokumentaci k IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Zařízení, které poslalo telemetrii, je reprezentované ID zařízení (viz následující oddíly). Pokud chcete získat názvy zařízení, exportovat data zařízení a sladit každou zprávu pomocí **connectionDeviceId** , který odpovídá ID zařízení zprávy zařízení.

Toto je ukázková zpráva přijatá v centru událostí nebo v Service Bus frontě nebo tématu.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Toto je ukázkový záznam exportovaný do úložiště objektů BLOB:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Zařízení

Každá zpráva nebo záznam ve snímku představuje jednu nebo více změn zařízení a vlastností zařízení a cloudu od poslední exportované zprávy. To zahrnuje:

- `id` zařízení v IoT Central
- `displayName` zařízení
- ID šablony zařízení v `instanceOf`
- příznak `simulated`, true, pokud je zařízení simulované zařízení
- příznak `provisioned`, true, pokud bylo zařízení zřízené
- příznak `approved`, true, pokud bylo zařízení schválené k odesílání dat
- Hodnoty vlastností
- `properties` včetně hodnot vlastností zařízení a cloudu

Odstraněná zařízení se neexportují. V současné době nejsou v exportovaných zprávách žádné indikátory pro Odstraněná zařízení.

V případě Event Hubs a Service Bus se zprávy obsahující data zařízení odesílají do centra událostí Service Bus nebo do fronty nebo tématu v reálném čase téměř v reálném čase, jak se zobrazuje v IoT Central. 

Pro Blob Storage nový snímek obsahující všechny změny od posledního napsaného typu se jednou za minutu vyexportuje.

Toto je ukázková zpráva týkající se zařízení a vlastností dat v centru událostí nebo Service Bus frontě nebo tématu:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Toto je ukázkový snímek obsahující zařízení a vlastnosti dat v Blob Storage. Exportované soubory obsahují jeden řádek na záznam.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Šablony zařízení

Každý záznam zprávy nebo snímku představuje jednu nebo více změn v publikované šabloně zařízení od poslední exportované zprávy. Mezi informace odesílané v každé zprávě nebo záznamu patří:

- `id` šablony zařízení, která odpovídá `instanceOf` datového proudu zařízení výše
- `displayName` šablony zařízení
- Zařízení `capabilityModel` včetně `interfaces`a definic telemetrie, vlastností a příkazů.
- definice `cloudProperties`
- Přepisuje a počáteční hodnoty, vloženo s `capabilityModel`

Odstraněné šablony zařízení se neexportují. V současné době nejsou v exportovaných zprávách pro odstraněné šablony zařízení žádné indikátory.

V případě Event Hubs a Service Bus se zprávy obsahující data šablony zařízení odesílají do centra událostí Service Bus nebo do fronty nebo tématu v reálném čase téměř v reálném čase, jak se zobrazuje v IoT Central. 

Pro Blob Storage nový snímek obsahující všechny změny od posledního napsaného typu se jednou za minutu vyexportuje.

Toto je ukázková zpráva o datech šablon zařízení v centru událostí nebo Service Bus frontě nebo tématu:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Toto je ukázkový snímek obsahující zařízení a vlastnosti dat v Blob Storage. Exportované soubory obsahují jeden řádek na záznam.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Upozornění na změnu formátu dat

> [!Note]
> Tato změna nemá vliv na formát dat datového proudu telemetrie. Jsou ovlivněna pouze data datových proudů zařízení a zařízení.

Pokud máte v aplikaci ve verzi Preview existující export dat se zapnutými datovými proudy *šablon* *zařízení* a zařízení, budete muset svůj export aktualizovat o **30. června 2020**. To platí pro exporty do Azure Blob Storage, Azure Event Hubs a Azure Service Bus.

Od 3. února 2020 budou všechny nové exporty v aplikacích se zapnutými šablonami zařízení a zařízení mít formát dat popsaný výše. Všechny exporty vytvořené před tímto způsobem zůstanou ve starém formátu dat až do 30. června 2020, potom se tyto exporty automaticky migrují do nového formátu dat. Nový formát dat odpovídá objektům [zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [vlastnosti zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [vlastnosti cloudu zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) a [šablonám zařízení](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) ve veřejném rozhraní API IoT Central. 
 
U **zařízení**se jedná o významné rozdíly mezi starým a novým datovým formátem:
- `@id` pro zařízení se odeberou, `deviceId` se přejmenuje na `id` 
- Přidání příznaku `provisioned` k popisu stavu zřizování zařízení
- Přidání příznaku `approved` k popisu stavu schválení zařízení
- `properties`, včetně vlastností zařízení a cloudu, odpovídá entitám ve veřejném rozhraní API.

V případě **šablon zařízení**jsou významné rozdíly mezi starým a novým datovým formátem:

- `@id` pro šablonu zařízení je přejmenována na `id`
- `@type` pro šablonu zařízení se přejmenují na `types`a teď je pole.

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Zařízení (formát se už nepoužívá od 3. února 2020)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Šablony zařízení (formát se už nepoužívá od 3. února 2020)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>Další kroky

Teď, když víte, jak exportovat data do Azure Event Hubs, Azure Service Bus a Azure Blob Storage, přejděte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Postup vytvoření webhooků](./howto-create-webhooks.md)
