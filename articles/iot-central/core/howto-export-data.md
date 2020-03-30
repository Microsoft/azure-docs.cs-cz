---
title: Export dat Azure IoT Central | Dokumenty společnosti Microsoft
description: Jak exportovat data z aplikace Azure IoT Central do Azure Event Hubs, Azure Service Bus a Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157904"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Export dat IoT do cílů v Azure

*Toto téma platí pro správce.*

Tento článek popisuje, jak používat funkci průběžného exportu dat v Azure IoT Central k exportu dat do **Azure Event Hubs**, **Azure Service Bus**nebo Azure **Blob storage** instance. Data se exportují ve formátu JSON a mohou obsahovat telemetrii, informace o zařízení a informace o šablonách zařízení. Exportovaná data použijte pro:

- Vřelé poznatky a analýzy. Tato možnost zahrnuje aktivaci vlastních pravidel ve službě Azure Stream Analytics, aktivaci vlastních pracovních postupů v Aplikacích Azure Logic Apps nebo jejich předávání prostřednictvím funkcí Azure, které mají být transformovány.
- Analýzy studené cesty, jako jsou trénovací modely v Azure Machine Learning nebo dlouhodobá analýza trendů v Microsoft Power BI.

> [!Note]
> Když zapnete nepřetržitý export dat, získáte pouze data od tohoto okamžiku. V současné době nelze data načíst po dobu, kdy byl nepřetržitý export dat vypnutý. Chcete-li zachovat více historických dat, zapněte nepřetržitý export dat včas.

## <a name="prerequisites"></a>Požadavky

Musíte být správcem aplikace IoT Central nebo mít oprávnění k exportu dat.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací průběžného exportu dat musí existovat cíl exportu.

### <a name="create-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

Pokud nemáte existující obor názvů Event Hubs, do kterých byste mohli exportovat, postupujte takto:

1. Vytvořte [nový obor názvů Event Hubs na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentech Centra událostí Azure](../../event-hubs/event-hubs-create.md).

2. Zvolte předplatné. Data můžete exportovat do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Vytvořte centrum událostí v oboru názvů Event Hubs. Přejděte do oboru názvů a v horní části vyberte **+ Centrum událostí** a vytvořte instanci centra událostí.

### <a name="create-service-bus-namespace"></a>Vytvořit obor názvů služby Service Bus

Pokud nemáte existující obor názvů služby Service Bus, do kterých byste mohli exportovat, postupujte takto:

1. Vytvořte [nový obor názvů Service Bus na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentech Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Zvolte předplatné. Data můžete exportovat do jiných předplatných, která nejsou ve stejném předplatném jako vaše aplikace IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Přejděte do oboru názvů Service Bus a v horní části vyberte **+ Fronta** nebo **+ Téma,** do které chcete vytvořit frontu nebo téma, do kterých chcete exportovat.

Pokud jako cíl exportu zvolíte service bus, nemusí být v frontách a tématech povoleny relace nebo vyhledávání duplicit. Pokud je některá z těchto možností povolena, některé zprávy nedorazí do fronty nebo tématu.

### <a name="create-storage-account"></a>Vytvoření účtu úložiště

Pokud nemáte existující účet Azure Storage, na který byste mohli exportovat, postupujte takto:

1. Vytvořte [nový účet úložiště na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Další informace o vytváření nových [účtů Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) nebo Azure Data Lake Storage [v2 storage accounts](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Export dat může zapisovat pouze data do účtů úložiště, které podporují objekty BLOB bloku. Následuje seznam známých kompatibilních typů účtů úložiště: 

    |Úroveň výkonu|Typ účtu|
    |-|-|
    |Standard|Obecný účel V2|
    |Standard|Obecný účel V1|
    |Standard|Blob Storage|
    |Premium|Úložiště objektů blob bloku|

2. Vytvořte si kontejner ve svém účtu úložiště. Přejděte na svůj účet úložiště. V části **Blob Service**vyberte **Procházet objekty BLOB**. Chcete-li vytvořit nový kontejner, vyberte nahoře + **Kontejner.**

## <a name="set-up-continuous-data-export"></a>Nastavení průběžného exportu dat

Nyní, když máte cíl pro export dat do, postupujte takto nastavit nepřetržitý export dat.

1. Přihlaste se k aplikaci IoT Central.

2. V levém podokně vyberte **Export dat**.

    > [!Note]
    > Pokud export dat v levém podokně nevidíte, nemáte oprávnění ke konfiguraci exportu dat v aplikaci. Chcete-li nastavit export dat, poraďte se se správcem.

3. Vpravém horním rohu vyberte tlačítko **+ Nový.** Jako cíl exportu si vyberte jeden z **Center událostí Azure**, Azure Service **Bus**nebo úložiště **objektů Blob Azure.** Maximální počet exportů na aplikaci je pět.

    ![Vytvoření nového průběžného exportu dat](media/howto-export-data/new-export-definition.png)

4. V rozevíracím seznamu vyberte **obor názvů Centra událostí**, Obor názvů Service **Bus**, Obor názvů **Účtu úložiště**nebo **Zadejte připojovací řetězec**.

    - Ve stejném předplatném jako aplikace IoT Central se zobrazují jenom účty úložiště, obory názvů event hubů a obory názvů Service Bus. Pokud chcete exportovat do cíle mimo toto předplatné, zvolte **Zadat připojovací řetězec** a podívejte se na krok 5.
    - U aplikací vytvořených pomocí bezplatného cenového plánu je jediným způsobem konfigurace průběžného exportu dat prostřednictvím připojovacího řetězce. Aplikace v bezplatném cenovém tarifu nemají přidružené předplatné Azure.

    ![Vytvořit nové Centrum událostí](media/howto-export-data/export-event-hub.png)

5. (Nepovinné) Pokud jste zvolili **Zadat připojovací řetězec**, zobrazí se nové pole pro vložení připojovacího řetězce. Chcete-li získat připojovací řetězec pro:
    - Centra událostí nebo Service Bus přejděte do oboru názvů na webu Azure Portal.
        - V části **Nastavení**vyberte **Zásady sdíleného přístupu.**
        - Zvolte výchozí **rootmanagesharedaccesskey** nebo vytvořte nový
        - Kopírování primárního nebo sekundárního připojovacího řetězce
    - Účet úložiště, přejděte na účet úložiště na webu Azure Portal:
        - V části **Nastavení**vyberte **Přístupové klávesy.**
        - Zkopírujte připojovací řetězec key1 nebo připojovací řetězec key2

6. Z rozevíracího seznamu vyberte centrum událostí, frontu, téma nebo kontejner.

7. V části **Data k exportu**zvolte typy dat, které chcete exportovat, nastavením typu **na Zapnuto**.

8. Chcete-li zapnout nepřetržitý export dat, zkontrolujte, zda je přepínač **Povoleno** **zapnuto**. Vyberte **Uložit**.

9. Po několika minutách se data zobrazí ve zvoleném cíli.

## <a name="export-contents-and-format"></a>Export obsahu a formátu

Exportovaná telemetrická data obsahují celou zprávu, kterou vaše zařízení odeslala do IoT Central, nejen samotné telemetrické hodnoty. Data exportovaných zařízení obsahují změny vlastností a metadat všech zařízení a exportované šablony zařízení obsahují změny ve všech šablonách zařízení.

Pro centra událostí a service bus data se exportují téměř v reálném čase. Data jsou ve vlastnosti těla a jsou ve formátu JSON (viz níže příklady).

V úložišti objektů blob se data exportují jednou za minutu, přičemž každý soubor obsahuje dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve třech složkách ve formátu JSON. Výchozí cesty v účtu úložiště jsou:

- Telemetrie: _{container}/{app-id}/telemetrie/{YYYY}/{MM}/{dd}/{hh}/{mm}/{název_souboru}_
- Zařízení: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{název_souboru}_
- Šablony zařízení: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{název_souboru}_

Exportované soubory na webu Azure Portal můžete procházet tak, že přejdete k souboru a zvolíte kartu **Upravit objekt blob.**


## <a name="telemetry"></a>Telemetrie

Pro centra událostí a service bus nová zpráva se exportuje rychle po IoT Central obdrží zprávu ze zařízení a každá exportovaná zpráva obsahuje úplnou zprávu zařízení odeslané ve vlastnosti body ve formátu JSON.

Pro úložiště objektů Blob jsou zprávy dávkové a exportované jednou za minutu. Exportované soubory používají stejný formát jako soubory zpráv exportované [směrováním zpráv služby IoT Hub](../../iot-hub/tutorial-routing.md) do úložiště objektů blob. 

> [!NOTE]
> V úložišti objektů Blob se ujistěte, `contentEncoding:utf-8` že `utf-16` `utf-32`vaše zařízení posílají zprávy, které mají `contentType: application/JSON` a (nebo , ). Podívejte se na příklad v dokumentaci k [centru IoT Hub.](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)

Zařízení, které poslalo telemetrii, je reprezentováno ID zařízení (viz následující části). Chcete-li získat názvy zařízení, exportujte data zařízení a korelujte každou zprávu pomocí **connectionDeviceId,** které odpovídá **deviceId** zprávy zařízení.

Toto je ukázková zpráva přijatá ve frontě nebo tématu centra událostí nebo služby Service Bus.

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

Toto je příklad záznamu exportovaného do úložiště objektů blob:

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

Každá zpráva nebo záznam ve snímku představuje jednu nebo více změn zařízení a jeho vlastností zařízení a cloudu od poslední exportované zprávy. To zahrnuje:

- `id`zařízení v IoT Central
- `displayName`zařízení
- Id šablony zařízení`instanceOf`
- `simulated`příznak, true, pokud je zařízení simulované zařízení
- `provisioned`příznak, true, pokud bylo zařízení zřízeno
- `approved`true, pokud bylo zařízení schváleno k odesílání dat
- Hodnoty vlastností
- `properties`včetně hodnot vlastností zařízení a cloudu

Odstraněná zařízení se neexportují. V současné době nejsou v exportovaných zprávách pro odstraněná zařízení žádné indikátory.

Pro centra událostí a service bus zprávy obsahující data zařízení jsou odesílány do centra událostí nebo fronty service bus nebo tématu téměř v reálném čase, jak se zobrazí v IoT Central. 

Pro úložiště objektů blob nový snímek obsahující všechny změny od posledního zapsaného se exportuje jednou za minutu.

Toto je ukázková zpráva o zařízeních a datech vlastností ve frontě nebo tématu služby Service Bus:

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

Toto je příklad snímku obsahujícího zařízení a data vlastností v úložišti objektů Blob. Exportované soubory obsahují jeden řádek na záznam.

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

Každá zpráva nebo snímek záznam představuje jednu nebo více změn v šabloně publikovaného zařízení od poslední exportované zprávy. Informace odeslané v každé zprávě nebo záznamu zahrnují:

- `id`šablony zařízení, která `instanceOf` odpovídá proudu zařízení nad
- `displayName`šablony zařízení
- Zařízení `capabilityModel` včetně `interfaces`jeho a telemetrie, vlastnosti a příkazy definice
- `cloudProperties`Definice
- Přepsání a počáteční hodnoty, v souladu s`capabilityModel`

Odstraněné šablony zařízení se neexportují. V současné době nejsou v exportovaných zprávách pro odstraněné šablony zařízení žádné indikátory.

V centru událostí a service bus jsou zprávy obsahující data šablony zařízení odesílány do vašeho centra událostí nebo fronty nebo tématu Service Bus téměř v reálném čase, jak se zobrazují v IoT Central. 

Pro úložiště objektů blob nový snímek obsahující všechny změny od posledního zapsaného se exportuje jednou za minutu.

Toto je ukázková zpráva o datech šablon zařízení v centru událostí nebo ve frontě nebo tématu service bus:

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

Toto je příklad snímku obsahujícího zařízení a data vlastností v úložišti objektů Blob. Exportované soubory obsahují jeden řádek na záznam.

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
## <a name="data-format-change-notice"></a>Oznámení o změně formátu dat

> [!Note]
> Tato změna neovlivní datový formát telemetrického datového proudu. Ovlivněny jsou pouze streamy dat zařízení a šablon zařízení.

Pokud máte existující export dat v aplikaci preview se *zapnutými streamy šablon* *Zařízení* a zařízení, budete muset export aktualizovat do **30.** To platí pro exporty do Azure Blob Storage, Azure Event Hubs a Azure Service Bus.

února 2020 budou mít všechny nové exporty v aplikacích s povolenými šablonami Zařízení a zařízení formát dat popsaný výše. Všechny exporty vytvořené před tímto zůstane na starý formát dat až do 30 Červen 2020, po které čas tyto exporty budou automaticky přeneseny do nového formátu dat. Nový formát dat odpovídá objektu [zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [vlastnosti zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [vlastnosti cloud zařízení](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) a [objekty šablony zařízení](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) ve veřejném rozhraní API IoT Central. 
 
U **zařízení**patří mezi významné rozdíly mezi starým datovým a novým formátem dat:
- `@id`pro zařízení je `deviceId` odstraněn, je přejmenován na`id` 
- `provisioned`je přidán příznak, který popisuje stav zřizování zařízení
- `approved`je přidán příznak, který popisuje stav schválení zařízení
- `properties`včetně vlastností zařízení a cloudu, odpovídá entitám ve veřejném rozhraní API

U **šablon zařízení**patří mezi významné rozdíly mezi starým datovým a novým formátem dat:

- `@id`pro šablonu zařízení je přejmenován na`id`
- `@type`pro šablonu zařízení je `types`přejmenován na , a je nyní pole

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Zařízení (formát se zastaralě o do 3. února 2020)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Šablony zařízení (formát se zastaralěo od 3. února 2020)
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

Teď, když víte, jak exportovat data do Azure Event Hubs, Azure Service Bus a Azure Blob Storage, pokračujte dalším krokem:

> [!div class="nextstepaction"]
> [Jak vytvořit webhooky](./howto-create-webhooks.md)
