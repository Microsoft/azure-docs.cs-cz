---
title: 'Rychlý start: Vlastní hlasové první virtuálních asistentů (Preview), Java (Windows, Linux) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu dozvíte, jak používat v konzolové aplikace Java Cognitive Services řeči Software Development Kit (SDK). Dozvíte se, jak připojit klientské aplikace do dříve vytvořeného bot Bot Framework nakonfigurován na používání s přímým přístupem řeči řádek kanál povolení virtuálních asistentů hlasové první zkušenosti.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027016"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Rychlý start: Vytvoření první hlasové virtuálních asistentů se sadou SDK pro řeč, jazyk Java

V tomto článku vytvoříte konzolovou aplikaci Java pomocí [sadou SDK pro řeč Cognitive Services](speech-sdk.md). Aplikace se připojí k dříve vytvořené bot nakonfigurován na používání s přímým přístupem řeči řádek kanál, pošlete žádost o hlasové vrátit aktivitu hlasové odezvy (je-li konfigurováno). Aplikace je sestavená pomocí balíčku Maven řeči sady SDK a Java IDE Eclipse na Windows, Ubuntu Linux nebo macOS. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: Windows (64-bit), 16.04/18.04 Ubuntu Linux (64 bitů) a macOS 10.13 nebo novější
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Předem nakonfigurované bot vytvořené pomocí rozhraní Bot Framework verze 4.2 nebo novější. Robot by bylo potřeba přihlásit se k nové "Přímé řádku Speech" kanál pro příjem hlasového vstupu odběru.

    > [!NOTE]
    > Ve verzi preview, kanál s přímým přístupem řeči řádek aktuálně podporuje pouze **westus2** oblasti.

    > [!NOTE]
    > 30denní zkušební verze pro standardní cenová úroveň je popsáno v [hlasové služby si můžete vyzkoušet zdarma](get-started.md) omezen na **westus** (ne **westus2**) a není proto kompatibilní s přímým Řádek řeči. Úrovně Free a standard **westus2** předplatná jsou kompatibilní.

Pokud používáte Ubuntu 16.04/18.04, ujistěte se, že jsou tyto závislosti nainstalovány před zahájením Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Pokud používáte Windows (64-bit), ujistěte se, nainstalujete Microsoft Visual C++ Redistributable pro vaši platformu:
* [Stáhnout Microsoft Visual C++ Redistributable pro Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Volitelné: Rychlý začátek

V tomto rychlém startu popíše krok za krokem, jak vytvořit jednoduchý klientské aplikace pro připojení k rozpoznávání řeči robota. Pokud chcete rovnou, dokončena, připraveno kompilace zdrojového kódu v tomto rychlém startu je k dispozici v [ukázky řeči SDK](https://aka.ms/csspeech/samples) pod `quickstart` složky.

## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Kromě toho chce protokolování povolit, aktualizujte **pom.xml** souboru zahrnují následující závislost.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Pokud chcete do projektu Javy přidat novou prázdnou třídu, vyberte **File** (Soubor)  >  **New** (Nový)  >  **Class** (Třída).

1. V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otevřete nově vytvořený **hlavní** třídy a nahraďte obsah `Main.java` souboru počáteční následujícím kódem.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. V **hlavní** metodu, budete nejprve konfigurovat vaše `BotConnectorConfig` a použijte ji k vytvoření `SpeechBotConnector` instance. To se připojí ke kanálu Direct line řeči pro interakci s svého robota. `AudioConfig` Instance se také používá jako zdroj pro zvukového vstupu. V tomto příkladu se používá výchozí mikrofon s `AudioConfig.fromDefaultMicrophoneInput()`.

    * Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného, které můžete získat z [tady](get-started.md).
    * Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) spojených s vaším předplatným.
    * Nahraďte řetězec `YourChannelSecret` s váš tajný klíč řádku s přímým přístupem řeči kanálu.

    > [!NOTE]
    > Ve verzi preview, kanál s přímým přístupem řeči řádek aktuálně podporuje pouze **westus2** oblasti.

    > [!NOTE]
    > 30denní zkušební verze pro standardní cenová úroveň je popsáno v [hlasové služby si můžete vyzkoušet zdarma](get-started.md) omezen na **westus** (ne **westus2**) a není proto kompatibilní s přímým Řádek řeči. Úrovně Free a standard **westus2** předplatná jsou kompatibilní.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` spoléhá na několik událostí ke komunikaci se svou činnost robota, výsledky rozpoznávání řeči a další informace. Dále přidejte tyto naslouchacích procesů událostí.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Připojení `SpeechBotConnector` k přímé řeči řádku vyvoláním `connectAsync()` metoda. K otestování vašeho robota, můžete vyvolat `listenOnceAsync` metodu pro odeslání zvukového vstupu z mikrofonu. Kromě toho můžete použít také `sendActivityAsync` metodu pro odeslání vlastní aktivitu jako serializovaný řetězec. Tyto vlastní aktivity může poskytnout dodatečná data, svého robota použijete v konverzaci.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Uložit změny do `Main` souboru.

1. Pro podporu odpovědi přehrávání, přidejte další třídy, která bude obsahovat pomocné metody pro podporu zvuk. Povolit zvuk, přidejte další novou prázdnou třídu do projektu jazyka Java: vyberte **souboru** > **nový** > **třídy**.

1. V **nová třída Java** okno, zadejte **speechsdk.quickstart** do **balíčku** pole, a **AudioPlayer** do **Název** pole.

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otevřete nově vytvořený **AudioPlayer** třídy a nahraďte kód níže uvedené.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Uložit změny do `AudioPlayer` souboru.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).
Konzola zobrazí zpráva "Dejme tomu, že něco" v tomto okamžiku, mohou mluvit anglickou fráze nebo vedle věty, pochopili svého robota. Řeč budou odeslány službě svého robota prostřednictvím kanálu s přímým přístupem řeči řádku, kde bude rozpoznán, zpracovává vašeho robota a odpověď se vrátí jako aktivita. Pokud váš robot vrátí řeči jako odpověď, bude možné přehrát zvuk zpět pomocí `AudioPlayer` třídy.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči z zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Rychlé zprovoznění: Překlad řeči, jazyka Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
