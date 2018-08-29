---
title: Odesílání událostí do služby Azure Event Hubs pomocí Javy | Dokumentace Microsoftu
description: Začínáme s odesíláním do služby Event Hubs pomocí Javy
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/27/2018
ms.author: shvija
ms.openlocfilehash: f67982eda60a8fdfdf0d50785827c513275fd202
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124751"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Odesílání událostí do služby Azure Event Hubs pomocí Javy

Event Hubs je vysoce škálovatelná služba systému, která dokáže ingestovat miliony událostí za sekundu a umožňuje aplikaci zpracovávat a analyzovat velké objemy dat vytvářené vašimi připojenými zařízeními a aplikacemi. Jakmile jsou shromážděna do centra událostí, dají se transformovat a ukládat data pomocí libovolného poskytovatele analýz v reálném čase nebo úložného clusteru.

Další informace najdete v tématu [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz ukazuje, jak odesílat události do centra událostí pomocí konzolové aplikace v Javě. Příjem událostí pomocí knihovny Java Event Processor Host, najdete v článku [v tomto článku](event-hubs-java-get-started-receive-eph.md), nebo klikněte na příslušný přijímající jazyk v obsahu vlevo.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, budete potřebovat následující:

* Vývojové prostředí Java. Tento kurz používá [Eclipse](https://www.eclipse.org/).
* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet][].

Kód v tomto kurzu vychází z [SimpleSend Githubu ukázky](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), které můžete zkontrolovat zobrazíte kompletní funkční aplikaci.

## <a name="send-events-to-event-hubs"></a>Odesílání událostí do služby Event Hubs

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Můžete odkazovat na tuto knihovnu pomocí následující deklarace závislostí uvnitř souboru projektu Maven. Aktuální verze je verze 1.0.2:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Pro různé typy prostředí sestavení, můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pro jednoduché události vydavatele, import *com.microsoft.azure.eventhubs* balíček pro klientské třídy služby Event Hubs a *com.microsoft.azure.servicebus* jako balíček pro nástroj třídy běžné výjimky, které jsou sdíleny s klienta zasílání zpráv Azure Service Bus. 

### <a name="declare-the-send-class"></a>Deklarace třídy odeslat

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Název třídy `SimpleSend`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Vytvoření připojovacího řetězce

ConnectionStringBuilder třídu použijte k sestavení kompletních hodnotu připojovacího řetězce k předání do instance služby Event Hubs klienta. Nahraďte zástupné symboly hodnotami, které jste získali při vytváření obor názvů a Centrum událostí:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Odesílání událostí

Vytvořte jednotný událost pomocí transformace řetězce do jeho kódování bajtů kódování UTF-8. Potom vytvořte nové instance služby Event Hubs klienta z připojovacího řetězce a odeslat zprávu:   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

### <a name="how-messages-are-routed-to-eventhub-partitions"></a>Směrování zpráv do oddílů centra událostí

Předtím, než zprávy jsou načítána pro spotřebitele, mají být publikována do oddílů nejprve podle vydavatele. Po publikování zprávy do centra událostí synchronně pomocí metody sendSync() com.microsoft.azure.eventhubs.EventHubClient objektu, může být zprávu odeslat do konkrétního oddílu ani distribuován do všech dostupných oddílech způsobem kruhové dotazování v závislosti na tom, jestli je nebo není zadána klíč oddílu.

Pokud je zadán řetězec představující klíč oddílu, klíč se k určení oddíl, který se k odeslání události do hashovat.

Pokud není nastaven klíč oddílu, pak zprávy budou kruhové robined na všechny dostupné oddíly.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Příjem událostí pomocí třídy EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

