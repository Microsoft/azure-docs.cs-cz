---
title: Vytvoření centra událostí s povoleným Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek popisuje postup vytvoření Apache Kafka s povoleným oborem názvů Azure Event Hubs pomocí Azure Portal.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555810"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Vytvoření centra událostí s povoleným Apache Kafkam

Azure Event Hubs je platforma jako služba (PaaS) pro streamování velkých objemů dat, která ingestuje miliony událostí za sekundu a poskytuje nízkou latenci a vysokou propustnost pro analýzy a vizualizace v reálném čase.

Azure Event Hubs poskytuje koncový bod Kafka. Tento koncový bod umožňuje vašemu oboru názvů Event Hubs nativně rozumět protokolům a rozhraním API [Apache Kafka](https://kafka.apache.org/intro) zpráv. Díky této funkci můžete komunikovat s centry událostí stejně, jako byste používali témata Kafka, aniž byste museli měnit klienty protokolu nebo spouštět vlastní clustery. Event Hubs podporuje [Apache Kafka verze 1,0](https://kafka.apache.org/10/documentation.html) a novější.

Tento článek popisuje, jak vytvořit obor názvů Event Hubs a získat připojovací řetězec potřebný pro připojení aplikací Kafka k centrům událostí s povoleným Kafka.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs s podporou Kafka

1. Přihlaste se k [Azure Portal][Azure portal]a v levém horním rohu obrazovky klikněte na **vytvořit prostředek** .

2. Vyhledejte službu Event Hubs a vyberte zde uvedené možnosti:
    
    ![Vyhledání služby Event Hubs na portálu](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Zadejte jedinečný název a povolte Kafka pro obor názvů. Klikněte na **Vytvořit**.
    
    ![Vytvoření oboru názvů](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Po vytvoření oboru názvů na kartě **Nastavení** klikněte na **Zásady sdíleného přístupu** a získejte připojovací řetězec.

    ![Kliknutí na Zásady sdíleného přístupu](./media/event-hubs-create/create-event-hub7.png)

5. Můžete zvolit výchozí zásadu **RootManageSharedAccessKey** nebo přidat novou. Klikněte na název zásady a zkopírujte připojovací řetězec. 
    
    ![Výběr zásady](./media/event-hubs-create/create-event-hub8.png)
 
6. Přidejte tento připojovací řetězec do konfigurace vaší aplikace Kafka.

Teď můžete ze svých aplikací používajících protokol Kafka streamovat události do služby Event Hubs.

## <a name="next-steps"></a>Další kroky

Další informace o Event Hubs najdete na těchto odkazech:

* [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
