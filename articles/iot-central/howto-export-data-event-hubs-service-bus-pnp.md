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
ms.openlocfilehash: b5a78901a8fc3f4c370216c0cc378f57630d345e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879757"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Export dat ve službě Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central exportovat data do vlastních **Event Hubs Azure**a instancí služby **Azure Service Bus** . **Měření**, **zařízení**a **šablony zařízení** můžete exportovat do svého vlastního cíle a získat tak přehled o cestách a analýzách. To zahrnuje aktivaci vlastních pravidel v Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo transformaci dat a jejich předávání prostřednictvím Azure Functions. 

> [!Note]
> Po opětovném zapnutí průběžného exportu dat získáte od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.


## <a name="prerequisites"></a>Požadavky

- Musíte být správcem aplikace IoT Central.

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující Event Hubs/Service Bus k exportu do, postupujte podle těchto kroků:

## <a name="create-event-hubs-namespace"></a>Vytvořit obor názvů Event Hubs

1. Vytvořte [Nový obor názvů Event Hubs v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace najdete v [dokumentaci k Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Vyberte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, která se neshodují s touto aplikací pro IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.
3. Vytvořte centrum událostí v oboru názvů Event Hubs. Vytvořte instanci centra událostí tak, že přejdete do svého oboru názvů a vyberete **+ centrum událostí** v horní části.

## <a name="create-service-bus-namespace"></a>Vytvořit obor názvů Service Bus

1. Vytvořte [Nový obor názvů Service Bus v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Další informace najdete v [dokumentaci Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Vyberte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, která se neshodují s touto aplikací pro IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.

3. Pokud chcete vytvořit frontu nebo téma, do kterého chcete exportovat, klikněte na obor názvů Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části.


## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte Event Hubs/Service Bus cíl pro export dat, postupujte podle těchto kroků a nastavte průběžný export dat. 

1. Přihlaste se k aplikaci IoT Central.

2. V nabídce vlevo vyberte **exportovat data**.

    > [!Note]
    > Pokud v levé nabídce nevidíte průběžný export dat, nejste správcem vaší aplikace. Pokud chcete nastavit export dat, obraťte se na správce.

    ![Vytvořit nové centrum událostí CDE](media/howto-export-data-pnp/export-menu1.png)

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte jednu z **Event Hubs Azure** nebo **Azure Service Bus** . 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový export průběžných dat](media/howto-export-data-pnp/export-new1.png)

4. V rozevíracím seznamu vyberte **obor názvů Event Hubs nebo obor názvů Service Bus**. Můžete také vybrat poslední možnost v seznamu a **zadat připojovací řetězec**. 

    > [!NOTE] 
    > V rámci **stejného předplatného jako aplikace pro IoT Central**se zobrazí jenom účty úložiště/obory názvů Event Hubs obory názvů nebo Service Bus. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.

    > [!NOTE] 
    > U 7 dní zkušebních aplikací je jediným způsobem konfigurace průběžného exportu dat prostřednictvím připojovacího řetězce. Důvodem je to, že 7 dní zkušebních aplikací nemá přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí CDE](media/howto-export-data-pnp/export-create1.png)

5. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Získání připojovacího řetězce pro:
    - Event Hubs nebo Service Bus, v Azure Portal přejít na obor názvů.
        - V části **Nastavení**vyberte **zásady sdíleného přístupu** .
        - Vyberte výchozí **RootManageSharedAccessKey** nebo vytvořte nový.
        - Zkopírování primárního nebo sekundárního připojovacího řetězce
 
6. V rozevíracím seznamu vyberte centrum událostí/frontu nebo téma.

7. V části **data, která chcete exportovat**, určete každý typ dat k exportu nastavením typ na **zapnuto**.

6. Pokud chcete zapnout funkci průběžného exportu dat, ujistěte se,že je **Export dat** zapnutý. Vyberte **Uložit**.

    ![Konfigurace průběžného exportu dat](media/howto-export-data-pnp/export-list1.png)

7. Po několika minutách se vaše data zobrazí ve zvoleném cíli.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Export do Azure Event Hubs a Azure Service Bus

Data o měřeních, zařízeních a šablonách zařízení se exportují do centra událostí nebo Service Bus fronty nebo tématu v reálném čase. Data exportovaných měření obsahují celou zprávu, kterou zařízení odesílá do IoT Central, nikoli jenom hodnoty samotných měření. Data exportovaných zařízení obsahují změny vlastností a nastavení všech zařízení a exportované šablony zařízení obsahují změny všech šablon zařízení. Exportovaná data jsou v rámci vlastnosti "tělo" a jsou ve formátu JSON.

> [!NOTE]
> Při volbě Service Bus jako cíle exportu nesmí fronty a témata **obsahovat relace nebo povolenou detekci**duplicit. Pokud je některá z těchto možností povolená, některé zprávy ve frontě nebo tématu nepřijde.

### <a name="measurements"></a>Měření

Po IoT Central obdrží zprávu ze zařízení, bude nová zpráva exportována rychle. Každá exportovaná zpráva v Event Hubs a Service Bus obsahuje úplnou zprávu, kterou zařízení ve formátu JSON poslalo ve vlastnosti "tělo". 

> [!NOTE]
> Zařízení, která odesílají měření, jsou představována ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportovat data ze zařízení a korelovat jednotlivé Messsage pomocí ConnectionDeviceIdu, která odpovídá ID **deviceId** zprávy zařízení.

Následující příklad ukazuje zprávu o měření dat přijatých v centru událostí nebo v Service Bus fronty nebo tématu.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Zařízení

Zprávy obsahující data zařízení se odesílají do centra událostí nebo do fronty nebo tématu Service Bus v každé pár minutách. To znamená, že každých pár minut přijde dávka zpráv s údaji o
- Nově přidaná zařízení
- Zařízení se změněnou vlastností a hodnotami nastavení

Každá zpráva představuje jednu nebo více změn zařízení od poslední exportované zprávy. Mezi informace, které se odešlou v každé zprávě, patří:
- `id`zařízení v IoT Central
- `name`zařízení
- `deviceId`ze [služby Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností
- Nastavení hodnot

> [!NOTE]
> Zařízení Odstraněná od poslední dávky nejsou exportována. V současné době nejsou v exportovaných zprávách žádné indikátory pro Odstraněná zařízení.
>
> Šablona zařízení, do které patří každé zařízení, je reprezentovaná ID šablony zařízení. Chcete-li získat název šablony zařízení, nezapomeňte také exportovat data šablony zařízení.

Následující příklad ukazuje zprávu o datech zařízení v centru událostí nebo Service Bus frontě nebo tématu:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Šablony zařízení

Zprávy obsahující šablony zařízení se odesílají do centra událostí nebo Service Bus fronty nebo tématu, a to každých několik minut. To znamená, že každých pár minut přijde dávka zpráv s údaji o
- Nové šablony zařízení, které byly přidány
- Šablony zařízení se změněnými měřeními, vlastnostmi a definicemi nastavení

Každá zpráva představuje jednu nebo více změn šablony zařízení od poslední exportované zprávy. Mezi informace, které se odešlou v každé zprávě, patří:
- `id`šablony zařízení
- `name`šablony zařízení
- `version`šablony zařízení
- Měření datových typů a minimální/maximální hodnoty
- Datové typy a výchozí hodnoty vlastností
- Nastavení datových typů a výchozích hodnot

> [!NOTE]
> Šablony zařízení odstraněné od poslední dávky nejsou exportovány. V současné době nejsou v exportovaných zprávách pro odstraněné šablony zařízení žádné indikátory.

Následující příklad ukazuje zprávu o datech šablon zařízení v centru událostí nebo Service Bus frontě nebo tématu:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Další kroky

Teď, když víte, jak exportovat data do Azure Event Hubs a Azure Service Bus, přejděte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak aktivovat Azure Functions](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
