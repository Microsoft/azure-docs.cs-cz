---
title: Odesílání událostí do centra událostí Azure pomocí Java | Microsoft Docs
description: Začínáme odesílá do centra událostí se používá Java
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.openlocfilehash: 6d3bf0b8ac5c5bdc7bf3deda21e800fe3cc6be2e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626407"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Odesílání událostí do centra událostí Azure používá Java

Event Hubs je vysoce škálovatelná služba, kterou lze přijímat miliony událostí za sekundu, povolení aplikaci zpracovávat a analyzovat masivní objemy dat vytvářených připojených zařízení a aplikací. Až se shromáždí do centra událostí, můžete transformovat a ukládat data pomocí úložného clusteru nebo všechny zprostředkovatele datové analýzy v reálném čase.

Další informace najdete v tématu [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz ukazuje, jak odesílat události do centra událostí pomocí konzolové aplikace v jazyce Java. Chcete-li přijímat události pomocí knihovny Java Event Processor Host, přečtěte si téma [v tomto článku](event-hubs-java-get-started-receive-eph.md), nebo klikněte na příslušný jazyk přijímající v levé tabulce obsahu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující požadavky:

* Vývojové prostředí Java. Tento kurz používá [Eclipse](https://www.eclipse.org/).
* Aktivní účet Azure. Pokud nemáte předplatné Azure, vytvořte [bezplatný účet][] před zahájením.

Kód v tomto kurzu vychází z [SimpleSend Githubu ukázka](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), který můžete zkontrolovat zobrazíte kompletní funkční aplikaci.

## <a name="send-events-to-event-hubs"></a>Odesílání událostí do centra událostí

Klientská knihovna Java pro službu Event Hubs je k dispozici pro použití v projektech Maven z [Maven centrálním úložišti](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Tato knihovna pomocí následující prohlášení závislostí v souboru projektu Maven, můžete odkazovat. Aktuální verze je 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Pro různé typy prostředí sestavení, můžete explicitně získat nejnovější vydaná JAR soubory z [Maven centrálním úložišti](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Jednoduchá událost vydavatel, importovat *com.microsoft.azure.eventhubs* balíček pro třídy klienta služby Event Hubs a *com.microsoft.azure.servicebus* balíček pro nástroj třídy, jako běžné výjimky, které jsou sdíleny s klientem zasílání zpráv Azure Service Bus. 

### <a name="declare-the-send-class"></a>Deklarování třídy pro odesílání

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Název třídy `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Vytvoření připojovacího řetězce

Třída ConnectionStringBuilder slouží k vytvoření připojení řetězcovou hodnotu předat instanci klienta služby Event Hubs. Zástupné názvy nahraďte hodnoty, které jste získali při vytváření centra obor názvů a událostí:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Odesílání událostí

Vytvořte singulární událostí transformace řetězec na jeho kódování bajtů ve formátu UTF-8. Pak vytvořte novou instanci služby Event Hubs klienta z připojovacího řetězce a odeslat zprávu:   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přijímat události pomocí knihovny EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

