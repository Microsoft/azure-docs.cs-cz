---
title: Obslužné rutiny a cíle událostí – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Obslužné rutiny událostí a cíle v mřížce událostí na okraji
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849744"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Obslužné rutiny událostí a cíle v mřížce událostí na okraji

Obslužná rutina události je místo, kde událost pro další akci nebo pro zpracování události. S modulem Event Grid on Edge může být obslužná rutina události na stejném hraničním zařízení, jiném zařízení nebo v cloudu. Můžete použít libovolný WebHook ke zpracování událostí nebo odeslat události do jedné z nativních obslužných rutin, jako je Azure Event Grid.

Tento článek obsahuje informace o tom, jak nakonfigurovat každý.

## <a name="webhook"></a>Webový hák

Chcete-li publikovat do koncového `endpointType` bodu `WebHook` WebHook, nastavte na a zadejte:

* endpointUrl: Adresa URL koncového bodu WebHook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Pokud chcete publikovat do cloudového koncového `endpointType` `eventGrid` bodu Azure Event Grid, nastavte to to a zadejte:

* endpointUrl: Adresa URL tématu mřížky událostí v cloudu
* sasKey: Klíč SAS tématu mřížky událostí
* topicName: Název pro razítko všech odchozích událostí do mřížky událostí. Název tématu je užitečný při odesílání do tématu domény mřížky událostí.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>Centrum IoT Edge

Chcete-li publikovat v modulu `endpointType` `edgeHub` Edge Hub, nastavte na a zadejte:

* outputName: Výstup, na kterém modul Event Grid bude směrovat události, které odpovídají tomuto předplatnému edgeHub. Například události, které odpovídají níže odběr bude zapsána do /messages/modules/eventgridmodule/outputs/sampleSub4.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Chcete-li publikovat v centru `endpointType` `eventHub` událostí, nastavte to to a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní centrum událostí, na které cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Připojovací řetězec specifický pro entitu můžete vygenerovat tak, že přejdete do konkrétního centra událostí, do kterého chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový řetězec connecection specifické pro entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Fronty služby Service Bus

Chcete-li publikovat do fronty `endpointType` služby Service Bus, nastavte to to `serviceBusQueue` a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní frontu služby Service Bus, na kterou cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní frontu služby Service Bus, do které chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete řetězec útěchy specifický pro novou entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Témata služby Service Bus

Chcete-li publikovat v tématu `endpointType` `serviceBusTopic` sběrnice, nastavte to to a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní téma sběrnice, na které cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní téma služby Service Bus, které chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový řetězec connecection specifické pro entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Fronty služby Storage

Chcete-li publikovat do fronty `endpointType` `storageQueue` úložiště, nastavte to to a zadejte:

* název fronty úložiště, do které publikujete.
* ConnectionString: Připojovací řetězec pro účet úložiště, ve které se nachází fronta úložiště.

    >[!NOTE]
    > Unline centra událostí, fronty služby Service Bus a témata sběrnice, připojovací řetězec používaný pro fronty úložiště není specifický pro entitu. Místo toho musí, ale připojovací řetězec pro účet úložiště.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```