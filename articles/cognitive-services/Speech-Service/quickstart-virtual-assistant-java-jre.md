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
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: 78e80b276a13ee6e27fdf0515f2901fdeaa20c5d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604913"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Rychlý start: Vytvoření první hlasové virtuálních asistentů se sadou SDK pro řeč, jazyk Java

Rychlí průvodci jsou také k dispozici pro [speech to text](quickstart-java-jre.md) a [překlad řeči](quickstart-translate-speech-java-jre.md).

V tomto článku vytvoříte konzolovou aplikaci Java pomocí [sadou SDK pro řeč Cognitive Services](speech-sdk.md). Aplikace se připojí k dříve vytvořené bot nakonfigurován na používání s přímým přístupem řeči řádek kanál, pošlete žádost o hlasové vrátit aktivitu hlasové odezvy (je-li konfigurováno). Aplikace je sestavená pomocí balíčku Maven řeči sady SDK a Java IDE Eclipse na Windows, Ubuntu Linux nebo macOS. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: Windows (64-bit), 16.04/18.04 Ubuntu Linux (64 bitů) a macOS 10.13 nebo novější
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného pro hlasové služby. [Získat zdarma](get-started.md) neexistuje, vytvořte ho na [webu Azure portal](https://portal.azure.com).
* Předem nakonfigurované bot vytvořené pomocí rozhraní Bot Framework verze 4.2 nebo novější. Robot by bylo potřeba přihlásit se k nové "Přímé řádku Speech" kanál pro příjem hlasového vstupu odběru.

    > [!NOTE]
    > Řeči řádku s přímým přístupem (Preview) je momentálně dostupný v podmnožině oblastí s hlasové služby. Najdete [seznam podporovaných oblastí pro virtuálních asistentů hlasové první](regions.md#Voice-first virtual assistants) a ujistěte se prostředky nasadí v těchto oblastech.

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

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. V **hlavní** metodu, budete nejprve konfigurovat vaše `DialogServiceConfig` a použijte ji k vytvoření `DialogServiceConnector` instance. To se připojí ke kanálu Direct line řeči pro interakci s svého robota. `AudioConfig` Instance se také používá jako zdroj pro zvukového vstupu. V tomto příkladu se používá výchozí mikrofon s `AudioConfig.fromDefaultMicrophoneInput()`.

    * Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného, které můžete získat z [tady](get-started.md).
    * Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) spojených s vaším předplatným.
    * Nahraďte řetězec `YourChannelSecret` s váš tajný klíč řádku s přímým přístupem řeči kanálu.

    > [!NOTE]
    > Řeči řádku s přímým přístupem (Preview) je momentálně dostupný v podmnožině oblastí s hlasové služby. Najdete [seznam podporovaných oblastí pro virtuálních asistentů hlasové první](regions.md#voice-first-virtual-assistants) a ujistěte se prostředky nasadí v těchto oblastech.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector` spoléhá na několik událostí ke komunikaci se svou činnost robota, výsledky rozpoznávání řeči a další informace. Dále přidejte tyto naslouchacích procesů událostí.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Připojení `DialogServiceConnector` k přímé řeči řádku vyvoláním `connectAsync()` metoda. K otestování vašeho robota, můžete vyvolat `listenOnceAsync` metodu pro odeslání zvukového vstupu z mikrofonu. Kromě toho můžete použít také `sendActivityAsync` metodu pro odeslání vlastní aktivitu jako serializovaný řetězec. Tyto vlastní aktivity může poskytnout dodatečná data, svého robota použijete v konverzaci.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Uložit změny do `Main` souboru.

1. Pro podporu odpovědi přehrávání, přidejte další třídy, která transformuje PullAudioOutputStream objekt vrácený z getAudio() rozhraní API k Javě InputStream pro snadnou manipulaci. Tato ActivityAudioStream je specializované třídy, který bude zpracovávat zvuku neodpověděla "přímé řeči řádek kanál". Poskytne přistupující objekty k načtení informací o zvukový formát, vyžaduje se pro zpracování přehrávání: K tomu vyberte **souboru** > **nový** > **třídy**.

1. V **nová třída Java** okno, zadejte **speechsdk.quickstart** do **balíčku** pole, a **ActivityAudioStream** do  **Název** pole.

1. Otevřete nově vytvořený **ActivityAudioStream** třídy a nahraďte kód níže uvedené.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Uložit změny do `ActivityAudioStream` souboru.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).
Konzola zobrazí zpráva "Dejme tomu, že něco" v tomto okamžiku, mohou mluvit anglickou fráze nebo vedle věty, pochopili svého robota. Řeč budou odeslány službě svého robota prostřednictvím kanálu s přímým přístupem řeči řádku, kde bude rozpoznán, zpracovává vašeho robota a odpověď se vrátí jako aktivita. Pokud váš robot vrátí řeči jako odpověď, bude možné přehrát zvuk zpět pomocí `AudioPlayer` třídy.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči z zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení základního robota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Další informace najdete v tématech

- [O virtuálních asistentů hlasové první](voice-first-virtual-assistants.md)
- [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
- [Vlastní probuzení slova](speech-devices-sdk-create-kws.md)
- [Připojení s přímým přístupem řádku řeči pro svého robota](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)
