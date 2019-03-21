---
title: Exportovat data do služby Azure Event Hubs a Azure Service Bus | Dokumentace Microsoftu
description: Jak exportovat data z Azure IoT Central aplikace do Azure Event Hubs a Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 700e8e9fe0dac182d71df8ca66800fa03cf25a2e
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295789"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportujte data v Azure IoT Central

*Toto téma se vztahuje na správce.*

Tento článek popisuje způsob použití souvislá datová funkce exportu v Azure IoT Central exportování dat na své vlastní **Azure Event Hubs**, a **Azure Service Bus** instancí. Můžete to taky **měření**, **zařízení**, a **šablon** do vlastní cíle pro horké cesty přehledů a analýz. Jedná se o aktivaci vlastního pravidla ve službě Azure Stream Analytics, aktivaci vlastních pracovních postupů v Azure Logic Apps nebo transformace dat a předají se jí prostřednictvím Azure Functions. 

> [!Note]
> Znovu až zapnete nepřetržitý export dat, zobrazí pouze data dále v tomto okamžiku. V současné době nelze načíst data po dobu, kdy byla nepřetržitý export dat vypnout. Pokud chcete zachovat dalších historických dat, zapněte nepřetržitý export dat již v rané fázi.


## <a name="prerequisites"></a>Požadavky

- Musíte být správcem ve vaší aplikaci IoT Central

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující sběrnice událostí služby/Hubs exportovat do aplikace, postupujte podle těchto kroků:

## <a name="create-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

1. Vytvoření [nový obor názvů služby Event Hubs na portálu Azure portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace v [dokumentace služby Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.
3. Vytvoření centra událostí ve vašem oboru názvů služby Event Hubs. Přejděte do svého oboru názvů a vyberte **+ Centrum událostí** v horní části stránky pro vytvoření instance centra událostí.

## <a name="create-service-bus-namespace"></a>Vytvoření oboru názvů služby Service Bus

1. Vytvoření [nový obor názvů služby Service Bus na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Další informace v [dokumentace služby Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.

3. Přejděte do svého oboru názvů služby Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části stránky k vytvoření fronty nebo tématu exportovat do.


## <a name="set-up-continuous-data-export"></a>Nastavit nepřetržitý export dat

Teď, když máte cíl exportovat data do Event Hubs a služby Service Bus, nastavit nepřetržitý export dat pomocí těchto kroků. 

1. Přihlaste se do vaší aplikace IoT Central.

2. V nabídce vlevo vyberte **průběžný Export dat**.

    > [!Note]
    > Pokud nevidíte průběžný Export dat v nabídce vlevo, nejste správcem ve vaší aplikaci. Obraťte se na správce nastavit export dat.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export_menu.PNG)

3. Vyberte **+ nová** tlačítko v pravém horním rohu. Vyberte jednu z **Azure Event Hubs** nebo **Azure Service Bus** jako cíl pro export. 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový nepřetržitý export dat](media/howto-export-data/export_new.PNG)

4. V rozevíracím seznamu vyberte vaše **oboru názvů Event Hubs oboru názvů/služby Service Bus**. V seznamu, který je můžete také vybrat jako poslední možnost **zadejte připojovací řetězec**. 

    > [!NOTE] 
    > Zobrazí se pouze obory názvů úložiště účtů nebo Event Hubs obory názvů nebo služby Service Bus v **stejném předplatném jako aplikace IoT Central**. Pokud chcete exportovat do umístění mimo toto předplatné, zvolte **zadejte připojovací řetězec** a přejděte ke kroku 5.

    > [!NOTE] 
    > 7 dnů, zkušební verze aplikace, jediný způsob, jak nakonfigurovat průběžné data exportovat je do připojovacího řetězce. Je to proto 7denní zkušební verze aplikace nemusí k přidruženému předplatnému Azure.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export_create.PNG)

5. (Volitelné) Pokud jste zvolili **zadejte připojovací řetězec**, můžete vložit připojovací řetězec se zobrazí nové pole. Chcete-li získat připojovací řetězec pro váš:
    - Event Hubs nebo Azure Service Bus, přejděte do oboru názvů na webu Azure Portal.
        - V části **nastavení**vyberte **sdílené zásady přístupu**
        - Zvolte výchozí **RootManageSharedAccessKey** nebo vytvořte novou
        - Zkopírujte primární nebo sekundární připojovací řetězec
 
6. Z rozevíracího seznamu zvolte Event hub či fronty nebo tématu.

7. V části **Data pro export**, určete každý typ hledaných dat exportovat na základě nastavení typu na **na**.

6. Nepřetržitý export dat zapnout, ujistěte se, že **export dat** je **na**. Vyberte **Uložit**.

  ![Nepřetržitý export dat konfigurace](media/howto-export-data/export_list.PNG)

7. Po několika minutách by se vaše data zobrazí v zvolený cíl.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Export do služby Azure Event Hubs a Azure Service Bus

Měření, zařízení a zařízení šablony data se exportují do centra událostí nebo fronty Service Bus nebo téma v téměř reálném čase. Exportované měření dat obsahuje celé zprávy zařízení odesílat IoT Central, ne jenom hodnoty měření sami. Exportovaná zařízení data obsahují změny vlastnosti a nastavení všech zařízení a zařízení exportované šablony obsahuje změny pro všechny šablony zařízení. Exportovaná data se ve vlastnosti "text" a je ve formátu JSON.

> [!NOTE]
> Když vyberete jako cíl pro export, fronty a témata služby Service Bus **nesmí mít relace nebo vyhledávání duplicit povolené**. Pokud některý z těchto možností jsou povolené, nebudou přicházet některé zprávy do fronty nebo tématu.

### <a name="measurements"></a>Měření

Nová zpráva se exportují rychle po IoT Central přijímá zprávy ze zařízení. Každá zpráva exportované do služby Event Hubs a služby Service Bus obsahuje celé zprávy zařízení odesílají v vlastnost "text" ve formátu JSON. 

> [!NOTE]
> Zařízení, které odesílají měření jsou reprezentovány v ID zařízení (viz následující části). Chcete-li získat názvy zařízení, exportovat data zařízení a korelovat každý býváte pomocí **connectionDeviceId** , který odpovídá **deviceId** zprávy typu zařízení.

Následující příklad ukazuje přijata zpráva o měření dat v Centru událostí nebo fronty Service Bus nebo téma.

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

Zprávy obsahující data zařízení odesílají do centra událostí nebo fronty Service Bus nebo téma každých několik minut. To znamená, že každých několik minut, dávku zpráv, které budou doručeny s daty o
- Nová zařízení, které byly přidány
- Zařízení s změněných vlastností a nastavení hodnot

Každá zpráva představuje jeden nebo více změn do zařízení od poslední exportované zprávy. Obsahuje informace, které se odesílají v každé zprávě:
- `id` zařízení v IoT Central
- `name` zařízení
- `deviceId` z [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností
- Nastavení hodnot

> [!NOTE]
> Zařízení odstranit, protože poslední dávky neexportují. V exportovaném zprávy pro odstraněné zařízení v současné době nejsou žádné ukazatele.
>
> Šablonu zařízení, každé zařízení patří, je reprezentována ID zařízení šablony. Pokud chcete získat název šablony zařízení, je nutné exportovat data šablony zařízení moc.

Následující příklad ukazuje zpráva týkající se dat zařízení v Centru událostí nebo fronty Service Bus nebo téma:


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

Zprávy obsahující data šablony zařízení odesílají do centra událostí nebo fronty Service Bus nebo téma každých několik minut. To znamená, že každých několik minut, dávku zpráv, které budou doručeny s daty o
- Nové šablony zařízení, které byly přidány
- Šablony pro zařízení s změněné měření, vlastnosti a nastavení definice

Každá zpráva představuje jeden nebo více změn do šablony zařízení od poslední exportované zprávy. Obsahuje informace, které se odesílají v každé zprávě:
- `id` šablony zařízení
- `name` šablony zařízení
- `version` šablony zařízení
- Měření datových typů a minimální/maximální hodnoty
- Vlastnost datové typy a výchozí hodnoty
- Nastavení datových typů a výchozí hodnoty

> [!NOTE]
> Šablony zařízení odstranit, protože poslední dávky neexportují. V exportované zprávy pro odstraněné zařízení šablony v současné době nejsou žádné ukazatele.

Následující příklad ukazuje zpráva týkající se data šablony zařízení v Centru událostí nebo fronty Service Bus nebo téma:

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

## <a name="next-steps"></a>Další postup

Teď, když víte, jak exportovat data do služby Azure Event Hubs a Azure Service Bus, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak aktivovat Azure Functions](howto-trigger-azure-functions.md)
