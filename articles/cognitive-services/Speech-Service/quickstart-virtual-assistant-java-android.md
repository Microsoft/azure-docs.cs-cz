---
title: 'Rychlý start: Vlastní hlasové první virtuálních asistentů (Preview), Java (Android) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit virtuálních asistentů hlasové první aplikaci v jazyce Java v Androidu pomocí sadou SDK pro řeč
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 5/24/2019
ms.author: travisw
ms.openlocfilehash: 5991388e47981c83eec24b0d8f955f7c292180da
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081530"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý start: Vytvoření první hlasové virtuálních asistentů v jazyce Java v Androidu pomocí sadou SDK pro řeč

Rychlý start je také k dispozici pro [speech to text](quickstart-java-android.md).

V tomto článku vytvoříte virtuálních asistentů hlasové první s Javou pro Android pomocí [sadou SDK pro řeč](speech-sdk.md). Tato aplikace se připojí k robotovi, který jste již vytvořené a nakonfigurovanou [kanál s přímým přístupem řádku řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Potom pošle žádost o hlasové robotu a prezentovat aktivitu hlasové služby odpovědi.

Tato aplikace sestavena pomocí balíčku Maven řeči sady SDK a Android Studio 3.3. Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, která používají 32bitové nebo 64bitové procesory ARM a kompatibilní procesory Intel x86 nebo x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

* Klíče pro hlasové služby v rozhraní předplatného Azure **westus2** oblasti. Vytvoření odběru na [webu Azure portal](https://portal.azure.com).
* Dříve vytvořeného bot nakonfigurovanou [kanál s přímým přístupem řádku řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v3.3 nebo novější
 
    > [!NOTE]
    > Řeči řádku s přímým přístupem (Preview) je momentálně dostupná jenom **westus2** oblasti.

    > [!NOTE]
    > 30denní zkušební verze pro standardní cenová úroveň je popsáno v [hlasové služby si můžete vyzkoušet zdarma](get-started.md) omezen na **westus** (ne **westus2**) a není proto kompatibilní s přímým Řádek řeči. Úrovně Free a standard **westus2** předplatná jsou kompatibilní.

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

V této části vytvoříme základní uživatelské rozhraní (UI) pro aplikaci. Začněme tím, že otevřete v hlavní aktivitě: `activity_main.xml`. Základní šablona obsahuje záhlaví s názvem vaší aplikace a `TextView` se zprávou "Hello world!".

V dalším kroku nahraďte obsah `activity_main.xml` následujícím kódem:

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

Tohoto XML kódu definuje jednoduché uživatelské rozhraní pro interakci s vaším robotem.

* `button` Element inicializuje interakce a vyvolá `onBotButtonClicked` metoda po kliknutí.
* `recoText` Element zobrazí výsledky převodu řeči na text, jak komunikovat s svého robota.
* `activityText` Element zobrazí datovou část JSON pro poslední aktivita Bot Framework ze svého robota.

Textové a grafické vyjádření uživatelském rozhraní by teď měl vypadat takto:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřít `MainActivity.java`a nahraďte jeho obsah následujícím kódem:

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
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only 'westus2' is currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private SpeechBotConnector botConnector;

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
            // Recreate the SpeechBotConnector on each button press, ensuring that the existing one is closed
            if (botConnector != null) {
                botConnector.close();
                botConnector = null;
            }

            // Create the SpeechBotConnector from the channel and speech subscription information
            BotConnectorConfig config = BotConnectorConfig.fromSecretKey(channelSecret, speechSubscriptionKey, serviceRegion);
            botConnector = new SpeechBotConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            botConnector.connectAsync();

            // Register the SpeechBotConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            botConnector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            botConnector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            botConnector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            botConnector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            botConnector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            botConnector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                botConnector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            botConnector.activityReceived.addEventListener((o, activityArgs) -> {
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

   * `onCreate` Zahrnuje kód, který požaduje oprávnění mikrofon a Internetu.

   * Metoda `onBotButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stiskněte tlačítko aktivuje jeden interakce ("zapnout") pomocí svého robota.

   * `registerEventListeners` Metoda ukazuje události používané SpeechBotConnector a základní zpracování příchozí aktivit.

1. Ve stejném souboru nahraďte konfiguračních řetězců tak, aby odpovídaly prostředky:

    * Nahraďte `YourChannelSecret` s tajným klíčem s přímým přístupem řeči řádek kanál pro svého robota.

    * Místo `YourSpeechSubscriptionKey` použijte váš klíč předplatného.

    * Nahraďte `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (Poznámka: momentálně se podporuje jenom westus2).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options).

1. Stisknutím kláves Ctrl+F9 nebo volbou příkazu **Build (Sestavení)**  > **Make Project (Vytvořit projekt)** na řádku nabídek aplikaci sestavte.

1. Aplikaci spustíte stisknutím kláves Shift+F10 nebo volbou příkazu **Run (Spustit)**  >  **Run „aplikace“ (Spustit „aplikace“)** .

1. V okně s cílem nasazení, které se zobrazí, zvolte zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](media/sdk/qs-java-android-12-deploy.png)

Po spuštění aplikace a jeho aktivity, klikněte na tlačítko Zahájit konverzaci pro svého robota. Přepisována text se zobrazí jako vy, mluví a posledních aktivit jste obdrželi od váš robot se zobrazí, když je obdržena. Pokud váš robot je nakonfigurován pro poskytnutí mluvené slovo odpovědi, bude přehrávat automaticky speech to text.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)
> [připojení s přímým přístupem řádku řeči pro svého robota](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="see-also"></a>Další informace najdete v tématech
- [O virtuálních asistentů hlasové první](voice-first-virtual-assistants.md)
- [Vlastní probuzení slova](speech-devices-sdk-create-kws.md)
