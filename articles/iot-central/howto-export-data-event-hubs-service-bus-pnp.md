---
title: Exportujte data do Azure Event Hubs a Azure Service Bus | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Event Hubs a Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973221"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Export dat ve službě Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central exportovat data do vlastních **Event Hubs Azure**a instancí služby **Azure Service Bus** . Můžete exportovat **telemetrie**, **zařízení**a **šablony zařízení** do svého vlastního cíle pro rychlé poznatky a analýzy. To zahrnuje aktivaci vlastních pravidel v Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo jejich předání prostřednictvím Azure Functions pro transformaci.

> [!Note]
> Po opětovném zapnutí průběžného exportu dat získáte od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.

## <a name="prerequisites"></a>Předpoklady

- Musíte být správcem aplikace IoT Central.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující Event Hubs/Service Bus k exportu do, postupujte podle těchto kroků a vytvořte jednu z těchto akcí:

### <a name="create-event-hubs-namespace"></a>Vytvořit obor názvů Event Hubs

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Vyberte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, která se **neshodují** s touto aplikací pro IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.
3. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

### <a name="create-service-bus-namespace"></a>Vytvořit obor názvů Service Bus

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Další informace najdete v [dokumentaci Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Vyberte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, která se **neshodují** s touto aplikací pro IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Pokud chcete vytvořit frontu nebo téma, do kterého chcete exportovat, klikněte na obor názvů Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části.


## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte Event Hubs/Service Bus cíl pro export dat, postupujte podle těchto kroků a nastavte průběžný export dat. 

1. Přihlaste se k aplikaci IoT Central.

2. V nabídce vlevo vyberte **exportovat data**.

    > [!Note]
    > Pokud v nabídce vlevo nevidíte exportovat data, nejste správcem vaší aplikace. Pokud chcete nastavit export dat, obraťte se na správce.

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte jednu z **Event Hubs Azure** nebo **Azure Service Bus** . 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový export průběžných dat](media/howto-export-data-pnp/export-new2.png)

4. V rozevíracím seznamu vyberte **obor názvů Event Hubs nebo obor názvů Service Bus**. Můžete také vybrat poslední možnost v seznamu a **zadat připojovací řetězec**. 

    > [!NOTE] 
    > V rámci **stejného předplatného jako aplikace pro IoT Central**se zobrazí jenom účty úložiště/obory názvů Event Hubs obory názvů nebo Service Bus. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.

    > [!NOTE] 
    > U 7 dní zkušebních aplikací je jediným způsobem konfigurace průběžného exportu dat prostřednictvím připojovacího řetězce. Důvodem je to, že 7 dní zkušebních aplikací nemá přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí CDE](media/howto-export-data-pnp/export-eh.png)

5. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Získání připojovacího řetězce pro:
    - Event Hubs nebo Service Bus, v Azure Portal přejít na obor názvů.
        - V části **Nastavení**vyberte **zásady sdíleného přístupu** .
        - Vyberte výchozí **RootManageSharedAccessKey** nebo vytvořte nový.
        - Zkopírování primárního nebo sekundárního připojovacího řetězce

6. V rozevíracím seznamu vyberte centrum událostí/frontu nebo téma.

7. V části **data, která chcete exportovat**, určete každý typ dat k exportu nastavením typ na **zapnuto**.

8. Pokud chcete zapnout funkci průběžného exportu dat, ujistěte se, že je **zapnutý přepínač pro** **Export dat** . Vyberte **Save** (Uložit).

9. Po několika minutách se vaše data zobrazí ve zvoleném cíli.


## <a name="data-format"></a>Formát dat

Data ze šablon telemetrie, zařízení a zařízení se exportují do centra událostí nebo Service Bus fronty nebo tématu v reálném čase. Exportovaná data telemetrie obsahují celou zprávu, kterou zařízení odesílá do IoT Central, nejen samotné hodnoty telemetrie. Data exportovaných zařízení obsahují změny vlastností a metadat všech zařízení a exportované šablony zařízení obsahují změny všech šablon zařízení. Exportovaná data jsou v rámci vlastnosti "tělo" a jsou ve formátu JSON.

> [!NOTE]
> Při volbě Service Bus jako cíle exportu nesmí fronty a témata **obsahovat relace nebo povolenou detekci**duplicit. Pokud je některá z těchto možností povolená, některé zprávy ve frontě nebo tématu nepřijde.

### <a name="telemetry"></a>Telemetrie

Po IoT Central obdrží zprávu ze zařízení, bude nová zpráva exportována rychle. Každá exportovaná zpráva v Event Hubs a Service Bus obsahuje úplnou zprávu, kterou zařízení ve formátu JSON poslalo ve vlastnosti "tělo". 

> [!NOTE]
> Zařízení, která odesílají telemetrii, jsou představována ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportovat data ze zařízení a korelovat jednotlivé Messsage pomocí **connectionDeviceIdu** , která odpovídá ID **deviceId** zprávy zařízení.

Následující příklad ukazuje zprávu o datech telemetrie přijatých v centru událostí nebo v Service Bus fronty nebo tématu.

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

### <a name="devices"></a>Zařízení

Zprávy obsahující data zařízení se odesílají do centra událostí nebo do fronty nebo tématu Service Bus v každé pár minutách. To znamená, že každých pár minut přijde dávka zpráv s údaji o
- Nově přidaná zařízení
- Zařízení se změněnými hodnotami vlastností

Každá zpráva představuje jednu nebo více změn zařízení od poslední exportované zprávy. Mezi informace, které se odešlou v každé zprávě, patří:
- `@id` zařízení v IoT Central
- `name` zařízení
- @no__t – 0 ze [služby Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností

> [!NOTE]
> Zařízení Odstraněná od poslední dávky nejsou exportována. V současné době nejsou v exportovaných zprávách žádné indikátory pro Odstraněná zařízení.
>
> Šablona zařízení, do které patří každé zařízení, je reprezentovaná ID šablony zařízení. Chcete-li získat název šablony zařízení, nezapomeňte také exportovat data šablony zařízení.

Následující příklad ukazuje zprávu o datech zařízení v centru událostí nebo Service Bus frontě nebo tématu:


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

### <a name="device-templates"></a>Šablony zařízení

Zprávy obsahující šablony zařízení se odesílají do centra událostí nebo Service Bus fronty nebo tématu, a to každých několik minut. To znamená, že každých pár minut přijde dávka zpráv s údaji o
- Nové šablony zařízení, které byly přidány nebo ve verzi
- Šablony zařízení se změněnými capabilityModels, cloudProperties, přepsáními a počátečními hodnotami

Každá zpráva představuje jednu nebo více změn šablony zařízení od poslední exportované zprávy. Mezi informace, které se odešlou v každé zprávě, patří:
- `@id` šablony zařízení
- `name` šablony zařízení
- `version` šablony zařízení
- Zařízení `capabilityModel` včetně `interfaces`, a definice telemetrie, vlastností a příkazů.
- definice `cloudProperties`
- Přepisuje a počáteční hodnoty, které jsou vložené s `capabilityModel`.

> [!NOTE]
> Šablony zařízení odstraněné od poslední dávky nejsou exportovány. V současné době nejsou v exportovaných zprávách pro odstraněné šablony zařízení žádné indikátory.

Následující příklad ukazuje zprávu šablony zařízení v centru událostí nebo Service Bus fronty nebo tématu:

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

## <a name="next-steps"></a>Další kroky

Teď, když víte, jak exportovat data do Azure Event Hubs a Azure Service Bus, přejděte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak aktivovat Azure Functions](howto-trigger-azure-functions.md)
