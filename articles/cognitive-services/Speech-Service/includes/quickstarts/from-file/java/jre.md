---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 06d8037011fbd837dc6169480877518c825de897
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275024"
---
## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Chcete-li do projektu Java přidat novou prázdnou**třídu,** vyberte **možnost Soubor** > **nové** > třídy .

1. V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Veškerý kód v `Main.java` nahraďte následujícím fragmentem kódu:

   ```java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";

               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               String serviceRegion = "YourServiceRegion";

               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               switch (result.getReason()) {
                   case ResultReason.RecognizedSpeech: {
                           System.out.println("We recognized: " + result.getText());
                           exitCode = 0;
                       }
                       break;
                   case ResultReason.NoMatch:
                       System.out.println("NOMATCH: Speech could not be recognized.");
                       break;
                   case ResultReason.Canceled: {
                           CancellationDetails cancellation = CancellationDetails.fromResult(result);
                           System.out.println("CANCELED: Reason=" + cancellation.getReason());
        
                           if (cancellation.getReason() == CancellationReason.Error) {
                               System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                               System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                               System.out.println("CANCELED: Did you update the subscription info?");
                           }
                       }
                       break;
               }

               reco.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Nahraďte `whatstheweatherlike.wav` řetězec vlastním názvem souboru.

1. Uložte změny do projektu.

> [!NOTE]
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte klávesu F11 nebo vyberte **spustit** > **ladění**.
Prvních 15 sekund hlasového vstupu ze zvukového souboru bude rozpoznáno a zaznamenáno v okně konzoly.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]