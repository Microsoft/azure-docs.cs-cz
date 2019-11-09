---
title: Exportujte data služby Azure IoT Central | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Event Hubs, Azure Service Bus a Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 35415fd1a552328faa8d1ad5812f44d8f4b6d5e4
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894861"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Export dat Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central exportovat data do instancí **azure Event Hubs**, **Azure Service Bus**nebo **Azure Blob Storage** . Data se exportují ve formátu JSON a můžou zahrnovat informace o telemetrie, zařízeních a šablonách zařízení. Použít exportovaná data pro:

- Přehledy a analýzy teplé cesty. Tato možnost zahrnuje aktivaci vlastních pravidel v Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo jejich předání prostřednictvím Azure Functions pro transformaci.
- Analýzy studených cest, jako jsou například školicí modely v Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Když zapnete export průběžných dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.

## <a name="prerequisites"></a>Požadavky

Musíte být správcem aplikace IoT Central.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Před konfigurací průběžného exportu dat musí existovat váš cíl exportu.

### <a name="create-event-hubs-namespace"></a>Vytvořit obor názvů Event Hubs

Pokud nemáte existující Event Hubs obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Vyberte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako aplikace IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

### <a name="create-service-bus-namespace"></a>Vytvořit obor názvů Service Bus

Pokud nemáte existující Service Bus obor názvů pro export do, postupujte podle těchto kroků:

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Další informace najdete v [dokumentaci Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Vyberte předplatné. Můžete exportovat data do jiných předplatných, která nejsou ve stejném předplatném jako aplikace IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Pokud chcete vytvořit frontu nebo téma, do kterého chcete exportovat, klikněte na obor názvů Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části.

Pokud zvolíte Service Bus jako cíl exportu, nesmí fronty a témata obsahovat relace nebo je povoleno zjišťování duplicitních dat. Pokud je některá z těchto možností povolená, některé zprávy ve frontě nebo tématu nepřijde.

### <a name="create-storage-account"></a>Vytvoření účtu úložiště

Pokud nemáte existující účet Azure Storage pro export do, postupujte podle těchto kroků:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) nebo [účtů úložiště Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Pokud se rozhodnete exportovat data do účtu úložiště Azure Data Lake Storage v2, musíte jako **druh účtu**vybrat **BlobStorage** .
    - Data můžete exportovat do účtů úložiště v předplatných, která jsou odlišná než ta pro vaši průběžné platby IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

2. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service**vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.

## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte cíl exportovat data do, postupujte podle těchto kroků a nastavte průběžný export dat.

1. Přihlaste se k aplikaci IoT Central.

2. V levém podokně vyberte **exportovat data**.

    > [!Note]
    > Pokud nevidíte exportovat data v levém podokně, nejste správcem vaší aplikace. Pokud chcete nastavit export dat, obraťte se na správce.

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte jednu z **Event Hubs Azure**, **Azure Service Bus**nebo **Azure Blob Storage** . Maximální počet exportů na aplikaci je 5.

    ![Vytvořit nový export průběžných dat](media/howto-export-data/export-new2.png)

4. V rozevíracím seznamu vyberte **obor názvů Event Hubs**, **Service Bus obor**názvů, **obor názvů účtu úložiště**nebo **Zadejte připojovací řetězec**.

    - V rámci stejného předplatného jako aplikace pro IoT Central se zobrazí jenom účty úložiště, Event Hubs obory názvů a Service Bus obory názvů. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.
    - Pro sedm dní zkušebních aplikací je jediným způsobem, jak nakonfigurovat průběžný export dat, prostřednictvím připojovacího řetězce. Sedm dní zkušebních aplikací nemá přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí](media/howto-export-data/export-eh.png)

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

8. Pokud chcete zapnout funkci průběžného exportu dat, ujistěte se, že je **zapnutý přepínač pro** **Export dat** . Vyberte **Uložit**.

9. Po několika minutách se vaše data zobrazí ve zvoleném cíli.

## <a name="export-contents-and-format"></a>Exportovat obsah a formát

Exportovaná data telemetrie obsahují celou zprávu, kterou zařízení odesílá do IoT Central, nejen samotné hodnoty telemetrie. Data exportovaných zařízení obsahují změny vlastností a metadat všech zařízení a exportované šablony zařízení obsahují změny všech šablon zařízení.

Pro Event Hubs a Service Bus se data exportují prakticky v reálném čase. Data jsou umístěna ve vlastnosti text a jsou ve formátu JSON (příklady najdete níže).

V případě Blob Storage se data exportují jednou za minutu a každý soubor obsahující dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve formátu JSON ve třech složkách. Výchozí cesty v účtu úložiště jsou:

- Telemetrie: _{Container}/{App-ID}/Telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Zařízení: _{Container}/{App-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Šablony zařízení: _{Container}/{App-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Exportované soubory můžete procházet v Azure Portal tak, že přejdete do souboru a kliknete na kartu **Upravit objekt BLOB** .


## <a name="telemetry"></a>Telemetrická data

V případě Event Hubs a Service Bus je nová zpráva exportována rychle po IoT Central přijetí zprávy ze zařízení a Každá exportovaná zpráva obsahuje úplnou zprávu, kterou zařízení odeslalo ve formátu JSON.

U Blob Storage se zprávy účtují a exportují jednou za minutu. Exportované soubory používají stejný formát jako soubory zpráv exportované [IoT Hub směrováním zpráv](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) do úložiště objektů BLOB. 

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

Každá zpráva nebo záznam ve snímku představuje jednu nebo více změn zařízení a jeho vlastností od poslední exportované zprávy. To zahrnuje:

- `@id` zařízení v IoT Central
- `name` zařízení
- `deviceId` ze [služby Device Provisioning Service](../core/howto-connect-nodejs.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
- Informace o šabloně zařízení
- Hodnoty vlastností

Šablona zařízení, do které patří každé zařízení, je reprezentovaná `instanceOf`. Chcete-li získat název a další informace o šabloně zařízení, nezapomeňte také exportovat data šablony zařízení.

Odstraněná zařízení se neexportují. V současné době nejsou v exportovaných zprávách žádné indikátory pro Odstraněná zařízení.

V případě Event Hubs a Service Bus se zprávy obsahující data zařízení odesílají do centra událostí Service Bus nebo do fronty nebo tématu v reálném čase téměř v reálném čase, jak se zobrazuje v IoT Central. 

Pro Blob Storage nový snímek obsahující všechny změny od posledního napsaného typu se jednou za minutu vyexportuje.

Toto je ukázková zpráva týkající se zařízení a vlastností dat v centru událostí nebo Service Bus frontě nebo tématu:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
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
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Toto je ukázkový snímek obsahující zařízení a vlastnosti dat v Blob Storage. Exportované soubory obsahují jeden řádek na záznam.

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

## <a name="device-templates"></a>Šablony zařízení

Každá zpráva nebo záznam snímku představuje jednu nebo více změn šablony zařízení od poslední exportované zprávy. Mezi informace odesílané v každé zprávě nebo záznamu patří:

- `@id` šablony zařízení, která odpovídá `instanceOf` datového proudu zařízení výše
- `name` šablony zařízení
- `version` šablony zařízení
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
> [Jak aktivovat Azure Functions](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
