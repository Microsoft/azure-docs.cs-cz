---
title: Java rychlý úvodní kurz pro Azure kognitivní služby Microsoft překladač řeči rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API pro rozpoznávání řeči překladač Microsoft v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 447164bd8d786fbfc46dff878b77f11a286bcfb8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342637"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-java"></a>Rychlý start pro Microsoft překladač řeči rozhraní API s Javou 
<a name="HOLTop"></a>

Tento článek ukazuje, jak používat rozhraní API pro rozpoznávání řeči překladač Microsoft překládat slova používaný v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [JDK 7 nebo 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) pro zkompilování a spuštění tohoto kódu. Pokud máte Oblíbené, ale bude stačit textového editoru můžete používat Java IDE.

Budete potřebovat [javax.websocket-api-1.1.jar](http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/) a [tyrus samostatné klienta 1.9.jar](http://repo1.maven.org/maven2/org/glassfish/tyrus/bundles/tyrus-standalone-client/1.9/) soubory.

Budete potřebovat soubor ve formátu WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, které zkompilujete z následující kód. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16kHz mono formátu. Můžete získat takové .wav soubor z [překladač Text řeči API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API služby Microsoft překladač řeči**. Budete potřebovat předplatné klíč z vaší [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Převede řeči

Následující kód překládá řeči z jednoho jazyka do druhého.

1. Vytvoření nového projektu Java v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Spusťte program.

Config.Java:

```java
import java.util.*;

import javax.websocket.ClientEndpointConfig;

public class Config extends ClientEndpointConfig.Configurator {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String key = "ENTER KEY HERE";

    @Override
    public void beforeRequest (Map<String,List<String>> headers) {
        headers.put("Ocp-Apim-Subscription-Key", Arrays.asList (key));
    }
}
```

Client.Java:

```java
import java.io.*;
import java.net.*;
import java.util.*;

import javax.websocket.ClientEndpoint;
import javax.websocket.CloseReason;
import javax.websocket.ContainerProvider;
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.WebSocketContainer;

/* Useful reference links:
    https://docs.oracle.com/javaee/7/api/javax/websocket/Session.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/RemoteEndpoint.Basic.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
    http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html
*/

@ClientEndpoint(configurator = Config.class)
public class Client {
    static String host = "wss://dev.microsofttranslator.com";
    static String path = "/speech/translate";
    static String params = "?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa";
    static String uri = host + path + params;

    static String input_path = "speak.wav";
    static String output_path = "speak2.wav";

    Session session = null;

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        System.out.println ("Connected.");
        SendAudio ();
    }

    @OnMessage
    public void onTextMessage(String message, Session session){
        System.out.println ("Text message received.");
        System.out.println (message);
    }

/*
Use the following signature to receive the message in parts:
    public void onBinaryMessage(byte[] buffer, boolean last, Session session)
Use the following signature to receive the entire message:
    public void onBinaryMessage(byte[] buffer, Session session)
See:
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
*/
    @OnMessage
    public void onBinaryMessage(byte[] buffer, Session session) {
        try {
            System.out.println ("Binary message received.");
            FileOutputStream stream = new FileOutputStream(output_path);
            stream.write(buffer);
            stream.close();
            System.out.println ("Message contents written to file.");
            Close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    @OnError
    public void onError(Session session, Throwable e) {
        System.out.println ("Error message received: " + e.getMessage ());
    }

    @OnClose
    public void myOnClose (CloseReason reason) {
        System.out.println ("Close message received: " + reason.getReasonPhrase());
    }

    public void SendAudio() {
        try {
            File file = new File(input_path);
            FileInputStream stream = new FileInputStream (file);
            byte buffer[] = new byte[(int)file.length()];
            stream.read(buffer);
            stream.close();
            sendMessage (buffer);
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void sendMessage(byte[] message) {
        try {
            System.out.println ("Sending audio.");
            OutputStream stream = this.session.getBasicRemote().getSendStream();
            stream.write (message);
/* Make sure the audio file is followed by silence.
 * This lets the service know that the audio input is finished. */
            System.out.println ("Sending silence.");
            byte silence[] = new byte[3200000];
            stream.write (silence);
            stream.flush();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Connect () throws Exception {
        try {
            System.out.println("Connecting.");
            WebSocketContainer container = ContainerProvider.getWebSocketContainer();
/* Some code samples show container.connectToServer as returning a Session, but this seems to be false. */
            container.connectToServer(this, new URI(uri));
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Close () throws Exception {
        try {
            System.out.println("Closing connection.");
            this.session.close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }
}
```

Speak.Java:

```java
/*
Download javax.websocket-api-1.1.jar from:
    http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/
Download tyrus-standalone-client-1.9.jar from:
    http://repo1.maven.org/maven2/org/glassfish/tyrus/bundles/tyrus-standalone-client/1.9/

Compile and run with:
    javac Config.java -cp .;javax.websocket-api-1.1.jar
    javac Client.java -cp .;javax.websocket-api-1.1.jar;tyrus-standalone-client-1.9.jar
    javac Speak.java
    java -cp .;javax.websocket-api-1.1.jar;tyrus-standalone-client-1.9.jar Speak
*/

public class Speak {
    public static void main(String[] args) {
        try {
            Client client = new Client ();
            client.Connect ();
            // Wait for the reply.
            System.out.println ("Press any key to exit the application at any time.");
            System.in.read();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Převede řeči odpovědi**

Úspěšné výsledkem je vytvoření souboru s názvem "speak2.wav". Tento soubor obsahuje překlad slova používaný v "speak.wav".

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladač řeči kurzu](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled řeči překladač](../overview.md)
[referenční dokumentace rozhraní API](http://docs.microsofttranslator.com/speech-translate.html)
