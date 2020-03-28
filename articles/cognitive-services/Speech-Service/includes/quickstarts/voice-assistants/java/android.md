---
title: 'Úvodní příručka: Vytvoření vlastního hlasového asistenta, Java (Android) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit vlastního hlasového asistenta v Jazyce Java v Systému Android pomocí sady Speech SDK.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: 24030a568dd4df7f3e0888e2bda8f1335e52ba38
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241168"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](~/articles/cognitive-services/speech-service/get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android)
> * Vytvoření robota připojeného ke [kanálu řeči přímé čáry](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

  > [!NOTE]
  > Informace o tom, že hlasové asistenty nasadíte v jedné z těchto oblastí, naleznete v [seznamu podporovaných oblastí.](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

V této části vytvoříme základní uživatelské rozhraní (UI) pro aplikaci. Začněme otevřením hlavní činnosti: `activity_main.xml`. Základní šablona obsahuje záhlaví s názvem aplikace a `TextView` se zprávou "Hello world!".

Dále nahraďte obsah `activity_main.xml` následujícího kódu:

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

Tento kód XML definuje jednoduché rozhraní pro interakci s robotem.

- Prvek `button` iniciuje interakci `onBotButtonClicked` a vyvolá metodu po klepnutí.
- Prvek `recoText` zobrazí výsledky řeči na text, když mluvíte s botem.
- Prvek `activityText` zobrazí datovou část JSON pro nejnovější aktivitu Bot Framework od vašeho robota.

Text a grafické znázornění uživatelského rozhraní by teď měly vypadat takto:

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

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

   * Metoda `onCreate` obsahuje kód, který požaduje oprávnění mikrofonu a internetu.

   * Metoda `onBotButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka spustí jednu interakci ("turn") s vaším robotem.

   * Metoda `registerEventListeners` demonstruje události používané `DialogServiceConnector` a základní zpracování příchozích aktivit.

1. Ve stejném souboru nahraďte konfigurační řetězce tak, aby odpovídaly vašim prostředkům:

    * Místo `YourSpeechSubscriptionKey` použijte váš klíč předplatného.

    * Nahradit `YourServiceRegion` [oblastí](~/articles/cognitive-services/speech-service/regions.md) přidruženou k vašemu předplatnému Je aktuálně podporována pouze podmnožina oblastí služby Rozpoznávání řeči s přímým přímým převodem řeči. Další informace naleznete v tématu [Oblasti](~/articles/cognitive-services/speech-service/regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options).

1. Chcete-li vytvořit aplikaci, stiskněte kombinaci kláves Ctrl+F9 nebo zvolte **Vytvořit** > **projekt** z panelu nabídek.

1. Chcete-li aplikaci spustit, stiskněte Shift+F10 nebo zvolte **Spustit** > **spustit aplikaci**.

1. V okně s cílem nasazení, které se zobrazí, zvolte zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Jakmile aplikace a její činnost byly zahájeny, klikněte na tlačítko začít mluvit s bot. Přepsaný text se zobrazí, jak mluvíte a nejnovější aktivity, které jste obdrželi od svého robota se objeví, když je přijat. Pokud je váš robot nakonfigurován tak, aby poskytoval mluvené odpovědi, automaticky se přehraje převod řeči na text.

![Snímek obrazovky aplikace pro Android](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

