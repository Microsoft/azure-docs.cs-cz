---
title: Vytvoření povolené platformy Apache Kafka, Azure Event Hubs | Dokumentace Microsoftu
description: Vytvoření oboru názvů Azure Event Hubs pomocí webu Azure portal povolené Kafka
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: b72f6b190aad436398629c67f271020ffd0a8da9
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822071"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Vytvoření služby event hubs pro Apache Kafka povoleno

Azure Event Hubs je velké objemy dat, streamování platforma jako služba (PaaS), která ingestuje miliony událostí za sekundu a poskytuje nízkou latenci a vysokou propustností pro analýzu v reálném čase a vizualizace.

Azure Event Hubs poskytuje koncový bod Kafka. Umožňuje tento koncový bod vašeho oboru názvů služby Event Hubs nativně pochopit [Apache Kafka](https://kafka.apache.org/intro) zprávy protokolu a rozhraní API. Díky této funkci může komunikovat s event hubs jako při použití témat Kafka bez změny vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Služba Event Hubs [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html) a novější.

Tento článek popisuje, jak vytvořit obor názvů služby Event Hubs a získání připojovacího řetězce požadované pro připojení aplikace Kafka a podporou Kafka event hubs.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka

1. Přihlaste se k [webu Azure portal][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.

2. Vyhledejte službu Event Hubs a vyberte zde uvedené možnosti:
    
    ![Vyhledání služby Event Hubs na portálu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Zadejte jedinečný název a povolte Kafka pro obor názvů. Klikněte na možnost **Vytvořit**.
    
    ![Vytvoření oboru názvů](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po vytvoření oboru názvů na kartě **Nastavení** klikněte na **Zásady sdíleného přístupu** a získejte připojovací řetězec.

    ![Kliknutí na Zásady sdíleného přístupu](./media/event-hubs-create/create-event-hub7.png)

5. Můžete zvolit výchozí zásadu **RootManageSharedAccessKey** nebo přidat novou. Klikněte na název zásady a zkopírujte připojovací řetězec. 
    
    ![Výběr zásady](./media/event-hubs-create/create-event-hub8.png)
 
6. Přidejte tento připojovací řetězec do konfigurace vaší aplikace Kafka.

Teď můžete ze svých aplikací používajících protokol Kafka streamovat události do služby Event Hubs.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
