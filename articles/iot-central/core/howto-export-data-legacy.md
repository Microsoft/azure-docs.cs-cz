---
title: Exportovat data z Azure IoT Central (starší verze) | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Event Hubs, Azure Service Bus a Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065435"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Export dat IoT do cílů cloudu pomocí exportu dat (starší verze)

> [!Note]
> Tento článek popisuje funkce exportu starších dat v IoT Central.
>
> - Informace o nejnovějších funkcích exportu dat najdete v tématu [Export dat IoT do cloudových cílů pomocí exportu dat](./howto-export-data.md).
> - Další informace o rozdílech mezi funkcemi exportu dat ve verzi Preview a starších verzí exportu dat najdete v [srovnávací tabulce](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

Tento článek popisuje, jak používat funkci exportu dat v Azure IoT Central. Tato funkce umožňuje nepřetržitě exportovat data do **azure Event Hubs**, **Azure Service Bus** nebo instancí služby **Azure Blob Storage** . Export dat používá formát JSON a může zahrnovat telemetrii, informace o zařízení a informace o šabloně zařízení. Použít exportovaná data pro:

- Přehledy a analýzy teplé cesty. Tato možnost zahrnuje aktivaci vlastních pravidel v Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo jejich předání prostřednictvím Azure Functions pro transformaci.
- Analýzy studených cest, jako jsou například školicí modely v Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Když zapnete export dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte nejdříve export dat.

## <a name="prerequisites"></a>Požadavky

Musíte být správce aplikace IoT Central, nebo mít oprávnění k exportu dat.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací exportu dat musí existovat váš cíl exportu.

### <a name="create-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

Pokud nemáte existující Event Hubs obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Zvolte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

### <a name="create-service-bus-namespace"></a>Vytvořit obor názvů Service Bus

Pokud nemáte existující Service Bus obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentaci Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Zvolte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Chcete-li vytvořit frontu nebo téma pro export do, přejít na obor názvů Service Bus a vybrat **+ fronta** nebo **+ téma**.

Pokud zvolíte Service Bus jako cíl exportu, nesmí fronty a témata obsahovat relace nebo je povoleno zjišťování duplicitních dat. Pokud je některá z těchto možností povolená, některé zprávy ve frontě nebo tématu nepřijde.

### <a name="create-storage-account"></a>Vytvoření účtu úložiště

Pokud nemáte existující účet úložiště Azure pro export do, postupujte takto:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů úložiště Azure Blob](../../storage/blobs/storage-quickstart-blobs-portal.md) nebo [Azure Data Lake Storagech účtů úložiště v2](../../storage/common/storage-account-create.md). Export dat může zapisovat jenom data do účtů úložiště, které podporují objekty blob bloku. Následující seznam uvádí známé kompatibilní typy účtů úložiště:

    |Úroveň výkonu|Typ účtu|
    |-|-|
    |Standardní|Pro obecné účely v2|
    |Standardní|Pro obecné účely v1|
    |Standardní|Blob Storage|
    |Premium|Úložiště objektů blob bloku|

2. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service** vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.

## <a name="set-up-data-export"></a>Nastavení exportu dat

Teď, když máte cíl pro export dat, postupujte podle těchto kroků a nastavte export dat.

1. Přihlaste se k aplikaci IoT Central.

2. V levém podokně vyberte **exportovat data**.

    > [!Tip]
    > Pokud nevidíte **exportovat data** v levém podokně, nemáte oprávnění ke konfiguraci exportu dat ve vaší aplikaci. Pokud chcete nastavit export dat, obraťte se na správce.

3. Vyberte tlačítko **+ Nový** . Vyberte jednu z **BLOB Storage Azure**, **Azure Event Hubs**, **frontu Azure Service Bus** nebo **Azure Service Bus téma** jako cíl exportu. Maximální počet exportů na aplikaci je 5.

4. Zadejte název exportu. V rozevíracím seznamu vyberte svůj **obor názvů** nebo **Zadejte připojovací řetězec**.

    - V rámci stejného předplatného jako aplikace pro IoT Central se zobrazí jenom účty úložiště, Event Hubs obory názvů a Service Bus obory názvů. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a podívejte se na krok 6.
    - U aplikací vytvořených pomocí bezplatného cenového plánu je jediným způsobem, jak nakonfigurovat export dat, prostřednictvím připojovacího řetězce. Pro aplikace v cenovém plánu zdarma nemáte přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí](media/howto-export-data-legacy/export-event-hub.png)

5. V rozevíracím seznamu vyberte centrum událostí, frontu, téma nebo kontejner.

6. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Získání připojovacího řetězce pro:

    - Event Hubs nebo Service Bus v Azure Portal přejít na obor názvů:
        - Použití připojovacího řetězce pro celý obor názvů:
            1. V části **Nastavení** vyberte **zásady sdíleného přístupu** .
            2. Vytvořte nový klíč nebo vyberte existující klíč, který má oprávnění **Odeslat** .
            3. Zkopírování primárního nebo sekundárního připojovacího řetězce
        - Pokud chcete použít připojovací řetězec pro konkrétní instanci centra událostí nebo Service Bus frontu nebo téma, přejít na **entity > Event Hubs** nebo **entity > fronty** nebo **entity > témata**. Vyberte konkrétní instanci a použijte stejný postup k získání připojovacího řetězce.
    - Účet úložiště, v Azure Portal přejít na účet úložiště:
        - Podporují se jenom připojovací řetězce pro celý účet úložiště. Připojovací řetězce s oborem pro jeden kontejner se nepodporují.
          1. V části **Nastavení** vyberte **přístupové klíče** .
          2. Zkopírujte buď připojovací řetězec klíč1, nebo připojovací řetězec key2.

    Vložte do připojovacího řetězce. Zadejte **název kontejneru** s rozlišováním instance nebo velikosti písmen.

7. V části **data, která chcete exportovat**, vyberte typy dat k exportu nastavením typ na **zapnuto**.

8. Pokud chcete zapnout export dat, ujistěte se, že **je zapnutý** přepínač **zapnuto** . Vyberte **Uložit**.

9. Po několika minutách se vaše data zobrazí ve zvoleném cíli.

## <a name="export-contents-and-format"></a>Exportovat obsah a formát

Exportovaná data telemetrie obsahují celou zprávu, kterou zařízení odesílá do IoT Central, nejen samotné hodnoty telemetrie. Data exportovaných zařízení obsahují změny vlastností a metadat všech zařízení a exportované šablony zařízení obsahují změny všech šablon zařízení.

Pro Event Hubs a Service Bus se data exportují prakticky v reálném čase. Data jsou ve `body` vlastnosti a jsou ve formátu JSON. Příklady najdete níže.

Pro úložiště objektů BLOB se data exportují jednou za minutu a každý soubor, který obsahuje dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve formátu JSON ve třech složkách. Výchozí cesty v účtu úložiště jsou:

- Telemetrie: _{Container}/{App-ID}/Telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Zařízení: _{Container}/{App-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Šablony zařízení: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Chcete-li procházet exportované soubory v Azure Portal, přejděte do souboru a vyberte kartu **Upravit objekt BLOB** .

## <a name="telemetry"></a>Telemetrie

Pro Event Hubs a Service Bus IoT Central exportuje novou zprávu rychle po přijetí zprávy ze zařízení. Každá exportovaná zpráva obsahuje úplnou zprávu, kterou zařízení odeslalo ve formátu JSON.

Pro úložiště objektů BLOB se zprávy účtují a exportují jednou za minutu. Exportované soubory používají stejný formát jako soubory zpráv exportované [IoT Hub směrováním zpráv](../../iot-hub/tutorial-routing.md) do úložiště objektů BLOB.

> [!NOTE]
> V případě úložiště objektů BLOB zajistěte, aby vaše zařízení odesílala zprávy, které mají `contentType: application/JSON` a `contentEncoding:utf-8` (nebo `utf-16` `utf-32` ). Příklad najdete v [dokumentaci k IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Zařízení, které poslalo telemetrii, je reprezentované ID zařízení (viz následující oddíly). Pokud chcete získat názvy zařízení, exportovat data zařízení a sladit každou zprávu pomocí **connectionDeviceId** , který odpovídá ID zařízení zprávy zařízení. 

Následující příklad ukazuje zprávu přijatou z centra událostí nebo Service Bus fronty nebo tématu:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Tato zpráva neobsahuje ID zařízení odesílajícího zařízení.

K načtení ID zařízení z dat zprávy v Azure Stream Analytics dotazu použijte funkci [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Příklad najdete v dotazech v tématu věnovaném [rozšiřování Azure IoT Central vlastními pravidly pomocí Stream Analytics, Azure functions a SendGrid](./howto-create-custom-rules.md).

Pokud chcete načíst ID zařízení v pracovním prostoru Azure Databricks nebo Apache Spark, použijte [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Příklad najdete v pracovním prostoru datacihly v tématu [Rozšířené Azure IoT Central s využitím vlastních analýz pomocí Azure Databricks](./howto-create-custom-analytics.md).

Následující příklad ukazuje záznam exportovaný do úložiště objektů BLOB:

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

Každá zpráva nebo záznam ve snímku představuje jednu nebo více změn zařízení a vlastností zařízení a cloudu od poslední exportované zprávy. Zpráva obsahuje:

- `id` zařízení v IoT Central
- `displayName` zařízení
- ID šablony zařízení v `instanceOf`
- `simulated` příznak, true, pokud je zařízení simulované zařízení
- `provisioned` příznak, true, pokud bylo zařízení zřízené
- `approved` příznak, true, pokud bylo zařízení schváleno k odesílání dat
- Hodnoty vlastností
- `properties` Zahrnutí hodnot vlastností zařízení a cloudu

Odstraněná zařízení se neexportují. V současné době nejsou v exportovaných zprávách žádné indikátory pro Odstraněná zařízení.

V případě Event Hubs a Service Bus odesílá IoT Central zprávy obsahující data zařízení do centra událostí nebo Service Bus fronty nebo tématu téměř v reálném čase.

V případě služby Blob Storage je nový snímek obsahující všechny změny od posledního napsaného typu exportován jednou za minutu.

Následující příklad zprávy zobrazuje informace o zařízeních a vlastnostech v centru událostí nebo v Service Bus fronty nebo tématu:

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

Tento snímek obsahuje ukázkovou zprávu, která zobrazuje zařízení a vlastnosti dat v úložišti objektů BLOB. Exportované soubory obsahují jeden řádek na záznam.

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

- `id` šablony zařízení, která odpovídá `instanceOf` datovému proudu zařízení výše
- `displayName` šablony zařízení
- Zařízení `capabilityModel` `interfaces` , včetně definicí a telemetrie, vlastností a příkazů
- `cloudProperties` definici
- Přepisuje a počáteční hodnoty, které jsou vloženy do `capabilityModel`

Odstraněné šablony zařízení se neexportují. V současné době nejsou v exportovaných zprávách pro odstraněné šablony zařízení žádné indikátory.

U Event Hubs a Service Bus odesílá IoT Central zprávy obsahující data šablony zařízení do centra událostí nebo do fronty Service Bus nebo v téměř reálném čase.

V případě služby Blob Storage je nový snímek obsahující všechny změny od posledního napsaného typu exportován jednou za minutu.

V tomto příkladu se zobrazuje zpráva o datech šablon zařízení v centru událostí nebo Service Bus frontě nebo tématu:

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

Tento ukázkový snímek zobrazuje zprávu, která obsahuje data zařízení a vlastností v úložišti objektů BLOB. Exportované soubory obsahují jeden řádek na záznam.

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

Pokud máte v aplikaci ve verzi Preview existující export dat se zapnutými datovými proudy *šablon* *zařízení* a zařízení, aktualizujte svůj export o **30. června 2020**. Tento požadavek platí pro exporty do Azure Blob Storage, Azure Event Hubs a Azure Service Bus.

Od 3. února 2020 budou všechny nové exporty v aplikacích se zapnutými šablonami zařízení a zařízení mít formát dat popsaný výše. Všechny exporty vytvořené před tímto datem zůstanou ve starém formátu dat až do 30. června 2020, kdy se tyto exporty automaticky migrují do nového formátu dat. Nový formát dat odpovídá vlastnostem [zařízení](/rest/api/iotcentral/devices/get), [vlastnosti zařízení](/rest/api/iotcentral/devices/getproperties), [cloudové vlastnosti zařízení](/rest/api/iotcentral/devices/getcloudproperties)a objekty [šablon zařízení](/rest/api/iotcentral/devicetemplates/get) ve IoT Central veřejném rozhraní API.

U **zařízení** se jedná o významné rozdíly mezi starým a novým datovým formátem:
- `@id` v případě odebrání zařízení se `deviceId` přejmenuje na `id` 
- `provisioned` Přidání příznaku pro popis stavu zřizování zařízení
- `approved` Přidání příznaku k popisu stavu schválení zařízení
- `properties` zahrnutí vlastností zařízení a cloudu, které odpovídají entitám ve veřejném rozhraní API

V případě **šablon zařízení** jsou významné rozdíly mezi starým a novým datovým formátem:

- `@id` pro šablonu zařízení je přejmenovaná na `id`
- `@type` pro šablonu zařízení se přejmenuje na `types` a je teď polem.

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
> [Jak spouštět vlastní analýzy pomocí datacihlů](./howto-create-custom-analytics.md)