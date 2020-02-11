---
title: 'Rychlý Start: Custom hlas Assistant, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se vytvářet aplikace hlasového asistenta v jazyce Java v Androidu pomocí sady Speech SDK
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: d4f84bad6139210dcff3d3f3726d8b7a17d92a5b
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119750"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý Start: vytvoření hlasového asistenta v jazyce Java v Androidu pomocí sady Speech SDK

K dispozici je také rychlý Start pro [Převod řeči na text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) a [Převod textu na řeč](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

V tomto článku vytvoříte hlasového asistenta s Java pro Android pomocí [sady Speech SDK](speech-sdk.md). Tato aplikace se připojí ke robotu, kterou už máte vytvořenou a nakonfigurovanou pomocí [kanálu Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Pak pošle hlasovou žádost na robota a nabídne aktivitu odezvy s povoleným hlasem.

Tato aplikace je sestavená pomocí balíčku sady Speech SDK Maven a Android Studio 3,3. Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, která používají 32bitové nebo 64bitové procesory ARM a kompatibilní procesory Intel x86 nebo x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma](get-started.md) nebo ho vytvořte na [Azure Portal](https://portal.azure.com).
- Dřív vytvořený robot nakonfigurovaný s [kanálem Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Android Studio](https://developer.android.com/studio/) v 3.3 nebo novějších

    > [!NOTE]
    > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

V této části vytvoříme pro aplikaci základní uživatelské rozhraní (UI). Pojďme začít otevřením hlavní aktivity: `activity_main.xml`. Základní šablona obsahuje záhlaví s názvem aplikace a `TextView` se zprávou "Hello World!".

Dále nahraďte obsah `activity_main.xml` následujícím kódem:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Tento kód XML definuje jednoduché uživatelské rozhraní pro interakci s robotem.

- Element `button` zahájí interakci a při kliknutí vyvolá metodu `onBotButtonClicked`.
- Element `recoText` zobrazí výsledky převodu řeči na text při komunikaci s robotem.
- Element `activityText` zobrazí datovou část JSON pro nejnovější aktivitu rozhraní robota z vaší robota.

Text a grafická reprezentace vašeho uživatelského rozhraní by teď měla vypadat takto:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete `MainActivity.java`a nahraďte obsah následujícím kódem:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Metoda `onCreate` obsahuje kód, který žádá o přístup přes mikrofon a Internet.

   * Metoda `onBotButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka aktivuje jednu interakci ("turn") s robotem.

   * Metoda `registerEventListeners` ukazuje události používané `DialogServiceConnector` a základní zpracování příchozích aktivit.

1. Ve stejném souboru nahraďte konfigurační řetězce odpovídajícími prostředky:

    * Nahraďte `YourChannelSecret` tajným klíčem pro robota s přímým řádkem.

    * Místo `YourSpeechSubscriptionKey` použijte váš klíč předplatného.

    * Nahraďte `YourServiceRegion` [oblastí](regions.md) přidruženou k vašemu předplatnému, protože funkce Direct line Speech v současné době podporuje jenom podmnožinu oblastí služby Speech Service. Další informace najdete v tématu [oblasti](regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options).

1. Stisknutím kláves Ctrl+F9 nebo volbou příkazu **Build (Sestavení)**  > **Make Project (Vytvořit projekt)** na řádku nabídek aplikaci sestavte.

1. Aplikaci spustíte stisknutím kláves Shift+F10 nebo volbou příkazu **Run (Spustit)**  >  **Run „aplikace“ (Spustit „aplikace“)** .

1. V okně s cílem nasazení, které se zobrazí, zvolte zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](media/sdk/qs-java-android-12-deploy.png)

Jakmile se aplikace a její činnost spustí, klikněte na tlačítko a začněte mluvit s robotem. Přepisu text se zobrazí při mluvení a poslední aktivita získaná od robota se zobrazí při přijetí. Pokud je robot nakonfigurovaný tak, aby poskytoval mluvené odezvy, bude se automaticky přehrávat převod řeči na text.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a nasazení základního chatbota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Viz také
- [O hlasových asistentcích](voice-assistants.md)
- [Získejte zdarma klíč předplatného služby Speech](get-started.md)
- [Vlastní klíčová slova](speech-devices-sdk-create-kws.md)
- [Připojení přímého Lineového rozpoznávání řeči k robotovi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)
