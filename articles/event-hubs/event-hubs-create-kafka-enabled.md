---
title: Vytvoření Kafka povoleno Azure Event Hubs | Microsoft Docs
description: Vytvoření Kafka povoleno oboru názvů služby Azure Event Hubs pomocí portálu Azure
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942547"
---
# <a name="create-kafka-enabled-event-hubs"></a>Vytvoření Kafka povoleno Event Hubs

Azure Event Hubs je velkých objemů dat streamování platforma jako služba (PaaS), který ingestuje miliony událostí za sekundu a zajišťuje nízkou latencí a vysokou propustnost pro analýzu v reálném čase a vizualizace.

Azure Event Hubs pro Kafka ekosystémů vám poskytne koncový bod. Tento koncový bod umožňuje vašeho oboru názvů služby Event Hubs nativně pochopit [Apache Kafka](https://kafka.apache.org/intro) zprávy protokolu a rozhraní API. Díky této funkci může komunikovat s Event Hubs stejně jako s Kafka témata bez změna vaši klienti protokolu nebo vlastní clustery spuštěná. Služba Event Hubs pro Kafka ekosystémů podporuje [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html) a novější.

Tento článek popisuje postup vytvoření oboru názvů Event Hubs a získat připojení řetězec požadované pro připojení aplikací Kafka Kafka povoleno Event Hubs.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před zahájením.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření Kafka povoleno oboru názvů služby Event Hubs

1. Přihlaste se k [portál Azure][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.

2. Vyhledejte Event Hubs a vyberte požadované možnosti znázorněno zde:
    
    ![Vyhledávání pro službu Event Hubs na portálu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Vytvoření oboru názvů**, tím poskytuje jedinečný název a povolte Kafka v oboru názvů. Klikněte na možnost **Vytvořit**.
    
    ![Vytvoření oboru názvů](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Po vytvoření oboru názvů, na **nastavení** , klikněte na **zásady sdíleného přístupu** získat připojovací řetězec.

    ![Klikněte na zásady sdíleného přístupu](./media/event-hubs-create/create-event-hub7.png)

5. Můžete zvolit výchozí **RootManageSharedAccessKey**, nebo přidejte nové zásady. Klikněte na název zásady a zkopírujte připojovací řetězec. 
    
    ![Vyberte zásadu](./media/event-hubs-create/create-event-hub8.png)
 
6. Konfigurace aplikace Kafka přidejte tento připojovací řetězec.

Můžete teď datového proudu událostí z vaší aplikace, které používají protokol Kafka do centra událostí.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* [Datový proud do centra událostí z aplikace Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Přečtěte si informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Další informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
