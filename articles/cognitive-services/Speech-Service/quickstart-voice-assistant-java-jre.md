---
title: 'Rychlý Start: Custom hlas Assistant pro Java (Windows, Linux) – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se naučíte používat sadu Cognitive Services Speech SDK v konzolové aplikaci Java. Dozvíte se, jak můžete připojit klientskou aplikaci k dřív vytvořenému robotu rozhraní robot Framework, který je nakonfigurovaný tak, aby používal kanál Direct line Speech a umožnil prostředí hlasového asistenta.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: bidishac
ms.openlocfilehash: 29f6596408a825a571682d14aceca01fa8108515
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815859"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Rychlý Start: vytvoření hlasového asistenta pomocí sady Speech SDK, Java (Preview)

K dispozici jsou také rychlé starty pro převod [řeči na text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), převod [textu na řeč](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)a [Převod řeči](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

V tomto článku vytvoříte konzolovou aplikaci Java pomocí [sady Azure Cognitive Services Speech SDK](speech-sdk.md). Aplikace se připojí k dřív vytvořenému robotovi, který je nakonfigurovaný tak, aby používal kanál Direct line Speech, pošle hlasovou žádost a vrátí aktivitu odezvy na hlas (Pokud je nakonfigurovaná). Aplikace je sestavená pomocí balíčku Maven sady Speech SDK a Java IDE zatmění ve Windows, Ubuntu Linux nebo na macOS. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

- Operační systém: Windows (64-bit), Ubuntu Linux 16.04/18.04 (64-bit) nebo macOS 10,13 nebo novější.
- [Java IDE zatmění](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma](get-started.md) nebo ho vytvořte v [Azure Portal](https://portal.azure.com).
- Předem konfigurovaný robot vytvořený pomocí rozhraní bot Framework verze 4,2 nebo vyšší. Robot se musí přihlásit k odběru nového kanálu přímého řádku řeči, aby přijímal vstupy hlasu.

  > [!NOTE]
  > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

Pokud používáte Ubuntu 16.04/18.04, ujistěte se, že jsou tyto závislosti nainstalované před začátkem zatmění:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Pokud používáte systém Windows (64 bitů), ujistěte se, že jste nainstalovali Microsoft Visual C++ Redistributable pro vaši platformu:

- [Stažení sady Microsoft C++ Visual Redistributable pro visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Volitelné: rychlý začátek

Tento rychlý Start popisuje, krok za krokem, jak vytvořit jednoduchou klientskou aplikaci pro připojení ke robotu s povoleným rozpoznáváním řeči. Pokud chcete podrobně přímo v, je kompletní zdrojový kód připravený k zkompilování použitý v tomto rychlém startu k dispozici v [ukázkách sady Speech SDK](https://aka.ms/csspeech/samples) ve složce `quickstart`.

## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Kromě toho, pokud chcete povolit protokolování, aktualizujte soubor _pom. XML_ tak, aby zahrnoval následující závislost:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Pokud chcete do projektu Javy přidat novou prázdnou třídu, vyberte **File** (Soubor)  >  **New** (Nový)  >  **Class** (Třída).

1. V okně **nové třídy Java** zadejte _speechsdk. Start_ do pole **balíček** a do pole **název** zadejte _Main_ .

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otevřete nově vytvořenou třídu `Main` a nahraďte obsah souboru `Main.java` následujícím počátečním kódem:

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

1. V metodě `main` nejprve nakonfigurujte `DialogServiceConfig` a použijte ho k vytvoření instance `DialogServiceConnector`. Tato instance se připojuje k přímému line kanálu pro práci s robotem. Instance `AudioConfig` se také používá k určení zdroje zvukového vstupu. V tomto příkladu se používá výchozí mikrofon s `AudioConfig.fromDefaultMicrophoneInput()`.

   - Nahraďte řetězec `YourSubscriptionKey` klíčem předplatného, který můžete získat z [tohoto webu](get-started.md).
   - Nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) přidruženým k vašemu předplatnému.

   > [!NOTE]
   > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Konektor `DialogServiceConnector` spoléhá na několik událostí, aby komunikoval své aktivity robota, výsledky rozpoznávání řeči a další informace. Přidejte následující naslouchací procesy událostí.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Připojte `DialogServiceConnector` k přímému použití hlasového rozpoznávání, a to vyvoláním metody `connectAsync()`. K otestování robota můžete vyvolat metodu `listenOnceAsync` pro odeslání zvukového vstupu z mikrofonu. Kromě toho můžete také použít metodu `sendActivityAsync` k odeslání vlastní aktivity jako serializovaného řetězce. Tyto vlastní aktivity můžou poskytovat další data, která robot v konverzaci používá.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Uložte změny do souboru `Main`.

1. Pro podporu přehrávání odpovědí přidejte další třídu, která transformuje objekt PullAudioOutputStream vrácený z rozhraní getaudio () API na InputStream Java pro usnadnění manipulace. Toto `ActivityAudioStream` je specializovaná třída, která zpracovává zvukovou odpověď z přímého kanálu s rozpoznáváním řádků. Poskytuje přistupující objekty pro načtení informací o formátu zvuku, které jsou požadovány pro zpracování přehrávání. V takovém případě vyberte **soubor** > **Nový** > **třídy**.

1. V okně **nové třídy Java** zadejte _speechsdk. Start_ do pole **Package** a do pole **název** _ActivityAudioStream_ .

1. Otevřete nově vytvořenou třídu `ActivityAudioStream` a nahraďte ji následujícím kódem:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
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
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
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
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
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
         * As this implementation does not buffer, this will be defaulted to 0
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
         * Enum defining the types of audio encoding supported by this stream.
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

1. Uložte změny do souboru `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Vyberte F11 nebo vyberte **spustit** > **ladění**.
Konzola zobrazí zprávu "říká něco".
V tomto okamžiku si přečtete anglickou frázi nebo větu, kterou vaše robot může pochopit. Váš řeč se přenáší na robota prostřednictvím kanálu přímého Lineového rozpoznávání, kde ho rozpozná a zpracuje robot. Odpověď se vrátí jako aktivita. Pokud robot vrátí hlas jako odpověď, zvuk se přehraje pomocí `AudioPlayer` třídy.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Další kroky

Další ukázky, například jak číst řeč ze zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Vytvoření a nasazení základního chatbota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Další informace najdete v tématech

- [O hlasových asistentcích](voice-assistants.md)
- [Získejte zdarma klíč předplatného služby Speech](get-started.md)
- [Vlastní klíčová slova](speech-devices-sdk-create-kws.md)
- [Připojení přímého Lineového rozpoznávání řeči k robotovi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)
