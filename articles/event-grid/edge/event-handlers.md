---
title: Obslužné rutiny událostí a cíle – Azure Event Grid IoT Edge | Microsoft Docs
description: Obslužné rutiny událostí a cíle v Event Grid na hraničních zařízeních
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171580"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Obslužné rutiny událostí a cíle v Event Grid na hraničních zařízeních

Obslužná rutina události je místo, kde událost pro další akci nebo zpracování události. V modulu Event Grid na Edge může být obslužná rutina události na stejném hraničním zařízení, v jiném zařízení nebo v cloudu. Můžete použít jakýkoliv Webhook pro zpracování událostí nebo odesílat události do jedné z nativních obslužných rutin, jako je Azure Event Grid.

Tento článek poskytuje informace o tom, jak je nakonfigurovat.

## <a name="webhook"></a>Webhooku

Pokud chcete publikovat do koncového bodu Webhooku, nastavte na `endpointType` `WebHook` a zadání:

* endpointUrl: adresa URL koncového bodu Webhooku

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

Pokud chcete publikovat do koncového bodu cloudu Azure Event Grid, nastavte na `endpointType` `eventGrid` a zadáním:

* endpointUrl: adresa URL tématu Event Grid v cloudu
* sasKey: klíč SAS Event Gridho tématu
* Název tématu: název pro označení všech odchozích událostí, které mají být Event Grid. Název tématu je užitečný při odeslání do tématu Event Grid domény.

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

Chcete-li publikovat do modulu Edge hub, nastavte `endpointType` na `edgeHub` a zadáním:

* outputName: výstup, na kterém bude modul Event Grid směrovat události, které odpovídají tomuto předplatnému edgeHub. Například události, které odpovídají níže uvedenému předplatnému, budou zapsány do/messages/modules/eventgridmodule/outputs/sampleSub4.

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

Pokud chcete publikovat do centra událostí, nastavte na `endpointType` `eventHub` a poskytnutí:

* connectionString: připojovací řetězec pro konkrétní centrum událostí, které cílíte vygenerovat prostřednictvím zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Připojovací řetězec specifický pro entitu můžete vygenerovat tak, že přejdete do konkrétního centra událostí, na které chcete publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

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

Chcete-li publikovat do fronty Service Bus, nastavte `endpointType` na `serviceBusQueue` a zadejte:

* connectionString: připojovací řetězec pro konkrétní frontu Service Bus, na kterou cílíte, vygenerovali pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní Service Bus frontu, na kterou chcete publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

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

Chcete-li publikovat do tématu Service Bus, nastavte `endpointType` na `serviceBusTopic` a poskytnutí:

* connectionString: připojovací řetězec pro konkrétní Service Bus téma, které cílíte vygenerované pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní Service Bus téma, na které byste chtěli publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

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

K publikování do fronty úložiště nastavte  `endpointType` `storageQueue` a zadejte:

* Queue: název fronty úložiště, do které se chystáte publikovat.
* connectionString: připojovací řetězec pro účet úložiště, ve kterém je fronta úložiště.

    >[!NOTE]
    > Odřádkování Event Hubs, front Service Bus a Service Busch témat, připojovací řetězec používaný pro fronty úložiště není specifický pro entitu. Místo toho je třeba použít připojovací řetězec pro účet úložiště.

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