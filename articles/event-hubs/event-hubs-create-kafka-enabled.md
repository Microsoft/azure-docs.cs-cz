---
title: Vytvoření povolené platformy Apache Kafka, Azure Event Hubs | Dokumentace Microsoftu
description: Vytvoření oboru názvů Azure Event Hubs pomocí webu Azure portal povolené Kafka
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 79b6b879bd2332c044ce871e2c9a938c6b9c900c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285073"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Vytvoření služby event hubs pro Apache Kafka povoleno

Azure Event Hubs je velké objemy dat, streamování platforma jako služba (PaaS), která ingestuje miliony událostí za sekundu a poskytuje nízkou latenci a vysokou propustností pro analýzu v reálném čase a vizualizace.

Azure Event Hubs poskytuje koncový bod Kafka. Umožňuje tento koncový bod vašeho oboru názvů služby Event Hubs nativně pochopit [Apache Kafka](https://kafka.apache.org/intro) zprávy protokolu a rozhraní API. Díky této funkci může komunikovat s event hubs jako při použití témat Kafka bez změny vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Služba Event Hubs [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html) a novější.

Tento článek popisuje, jak vytvořit obor názvů služby Event Hubs a získání připojovacího řetězce požadované pro připojení aplikace Kafka a podporou Kafka event hubs.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs povolené Kafka

1. Přihlaste se k [webu Azure portal][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.

2. Vyhledání služby Event Hubs a vyberte požadované možnosti je vidět tady:
    
    ![Vyhledávání pro službu Event Hubs na portálu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Zadejte jedinečný název a povolte Kafka v oboru názvů. Klikněte na možnost **Vytvořit**.
    
    ![Vytvoření oboru názvů](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Po vytvoření oboru názvů, dále **nastavení** klikněte na kartu **zásady sdíleného přístupu** k získání připojovacího řetězce.

    ![Klikněte na zásady sdíleného přístupu](./media/event-hubs-create/create-event-hub7.png)

5. Můžete použít výchozí **RootManageSharedAccessKey**, nebo přidat nové zásady. Klikněte na název zásady a zkopírujte připojovací řetězec. 
    
    ![Vyberte zásadu](./media/event-hubs-create/create-event-hub8.png)
 
6. Přidáte konfiguraci aplikace Kafka tento připojovací řetězec.

Nyní můžete Streamovat události z aplikací, které používají protokol Kafka do služby Event Hubs.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* [Stream do služby Event Hubs ze svých aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Další informace o službě Event Hubs pro systém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
