---
title: Azure Event Hubs pro Kafka ekosystémů | Microsoft Docs
description: Přehled a úvod do Kafka povolené Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Služba Event Hubs pro Kafka ekosystémů

Event Hubs pro Kafka ekosystémů poskytuje Kafka koncový bod, který lze použít ve stávající Kafka aplikace založené na jako alternativu ke spuštění vlastního Kafka clusteru. Služba Event Hubs pro Kafka ekosystém podporuje [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) a novější verze klienta a spolupracuje s vaší stávající aplikace Kafka, včetně MirrorMaker. Změňte připojovací řetězec a spustit vysílání datových proudů z vaší aplikace, které používají protokol Kafka do centra událostí.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Co poskytuje služby Event Hubs pro Kafka ekosystémů?

Event Hubs pro ekosystém Kafka poskytuje head protokolu nad Azure Event Hubs, který je binární kompatibilní s Kafka verze 1.0 a novějším pro čtení i zápis do Kafka témata. Koncepčně Kafka a Event Hubs jsou téměř stejné: jsou obě oddílů protokoly vytvořen pro datový proud. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Koncepční mapování Kafka a centra událostí

| Koncept Kafka | Koncept centra událostí|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Event Hubs |
| Oddíl | Oddíl|
| Skupina uživatelů | Skupina uživatelů |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Hlavní rozdíly mezi Kafka a Event Hubs

Při [Apache Kafka](https://kafka.apache.org/) je software, který můžete spustit bez ohledu na zvolíte, Event Hubs je Cloudová služba, podobně jako úložiště objektů Blob Azure. Neexistují žádné servery nebo sítě ke správě a žádné zprostředkovatelé konfigurace. Vytvoření oboru názvů, který je plně kvalifikovaný název domény, ve kterém za provozu v tématech, a pak vytvořte Event Hubs nebo témata v daném oboru názvů. Další informace o službě Event Hubs a obory názvů najdete v tématu [co je Služba Event Hubs](event-hubs-what-is-event-hubs.md). Jako cloudová služba Event Hubs využívá jediné stabilní virtuální IP adresy jako koncový bod, takže klienti nemusí vědět o zprostředkovatelé nebo počítače v rámci clusteru. 

Měřítka ve službě Event Hubs je řízena pomocí kolik jednotek propustnosti jste si koupili, s jednotlivých jednotek propustnosti jste opravňující 1 MB za sekundu nebo 1000 událostí za sekundu příchozí. Ve výchozím nastavení, Event Hubs škálování jednotky propustnosti při dosažení limitu s [zvýšilo automaticky](event-hubs-auto-inflate.md) funkce; tato funkce také funguje službou Event Hubs pro Kafka ekosystémů. 

### <a name="security-and-authentication"></a>Zabezpečení a ověřování

Azure Event Hubs vyžaduje protokol SSL nebo TLS pro veškerou komunikaci a používá podpisy sdíleného přístupu (SAS) pro ověřování. Tento požadavek platí i pro koncový bod Kafka v rámci služby Event Hubs. K zajištění kompatibility se Kafka Event Hubs využívá SASL prostý pro ověřování a SASL protokolu SSL pro zabezpečení přenosu. Další informace o zabezpečení ve službě Event Hubs naleznete v tématu [Event Hubs ověřování a zabezpečení](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce služby Event Hubs dostupné pro Kafka

Centra událostí pro ekosystém Kafka umožňuje zapisovat s jeden protokol a číst s jinou, tak, aby vaše aktuální producenti Kafka můžete pokračovat v publikování prostřednictvím Kafka, a můžete přidat čtečky službou Event Hubs, jako je Azure Stream Analytics nebo Azure Functions. Kromě toho Služba Event Hubs funkce, jako [zaznamenat](event-hubs-capture-overview.md) a [zotavení po havárii geograficky](event-hubs-geo-dr.md) Event Hubs pro Kafka ekosystémů pracovat také.

## <a name="features-that-are-not-supported-in-the-preview"></a>Funkce, které nejsou podporovány ve verzi preview

Pro verzi public preview služby Event Hubs pro integraci ekosystémů Kafka nejsou podporovány následující funkce Kafka:

*   Producent Idempotent
*   Transakce
*   Komprese
*   Udržení na základě velikosti
*   Komprimace protokolu
*   Přidání oddílů do existující téma
*   Podpora protokolu HTTP Kafka rozhraní API
*   Kafka připojení
*   Kafka datové proudy

## <a name="next-steps"></a>Další postup

Tento článek poskytuje úvod do služby Event Hubs Kafka ekosystémů. Další informace najdete v následujících tématech:

* [Postup vytvoření Kafka povoleno Event Hubs](event-hubs-create-kafka-enabled.md)
* [Datový proud do centra událostí z aplikace Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

 
 

