---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: 38e6bae69710dc9e1dbc8789ee9ccb636193c7f7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80671752"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](~/articles/cognitive-services/speech-service/get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Vytvoření robota připojeného k [kanálu pro přímý vstup řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

  > [!NOTE]
  > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Pokud chcete povolit protokolování, aktualizujte soubor _pom.xml_ tak, aby zahrnoval následující závislost:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Chcete-li do projektu Java přidat novou prázdnou třídu, vyberte možnost **soubor**  >  **Nová**  >  **Třída**.

1. V okně **nové třídy Java** zadejte _speechsdk. Start_ do pole **balíček** a do pole **název** zadejte _Main_ .

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](~/articles/cognitive-services/speech-service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Otevřete nově vytvořenou `Main` třídu a nahraďte obsah `Main.java` souboru následujícím počátečním kódem:

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

1. V `main` metodě nejprve nakonfigurujte `DialogServiceConfig` a použijte ji k vytvoření `DialogServiceConnector` instance. Tato instance se připojuje k přímému line kanálu pro práci s robotem. `AudioConfig`Instance se také používá k určení zdroje zvukového vstupu. V tomto příkladu se používá výchozí mikrofon s nástrojem `AudioConfig.fromDefaultMicrophoneInput()` .

   - Nahraďte řetězec `YourSubscriptionKey` vaším klíčem předplatného, který můžete získat z [tohoto webu](~/articles/cognitive-services/speech-service/get-started.md).
   - Nahraďte řetězec `YourServiceRegion` [oblastí](~/articles/cognitive-services/speech-service/regions.md) , která je přidružená k vašemu předplatnému.

   > [!NOTE]
   > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

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

1. Připojení `DialogServiceConnector` k přímému line Speech vyvoláním `connectAsync()` metody. K otestování robota můžete vyvolat `listenOnceAsync` metodu pro odesílání zvukového vstupu z mikrofonu. Kromě toho můžete také použít `sendActivityAsync` metodu k odeslání vlastní aktivity jako serializovaného řetězce. Tyto vlastní aktivity můžou poskytovat další data, která robot v konverzaci používá.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Uložte změny do `Main` souboru.

1. Pro podporu přehrávání odpovědí přidejte další třídu, která transformuje objekt PullAudioOutputStream vrácený z rozhraní getaudio () API na InputStream Java pro usnadnění manipulace. Toto `ActivityAudioStream` je specializovaná třída, která zpracovává zvukovou odpověď z přímého kanálu s rozpoznáváním řádků. Poskytuje přistupující objekty pro načtení informací o formátu zvuku, které jsou požadovány pro zpracování přehrávání. V takovém případě vyberte možnost **soubor**  >  **Nová**  >  **Třída**.

1. V okně **nové třídy Java** zadejte _speechsdk. Start_ do pole **Package** a do pole **název** _ActivityAudioStream_ .

1. Otevřete nově vytvořenou `ActivityAudioStream` třídu a nahraďte ji následujícím kódem:

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

1. Uložte změny do `ActivityAudioStream` souboru.

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

Vyberte F11 nebo vyberte **Spustit**  >  **ladění**.
Konzola zobrazí zprávu "říká něco".
V tomto okamžiku si přečtete anglickou frázi nebo větu, kterou vaše robot může pochopit. Váš řeč se přenáší na robota prostřednictvím kanálu přímého Lineového rozpoznávání, kde ho rozpozná a zpracuje robot. Odpověď se vrátí jako aktivita. Pokud robot vrátí hlas jako odpověď, zvuk se přehraje pomocí `AudioPlayer` třídy.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
