---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 0dbfab9b1886b39f166b94447fed8dc6707404bd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795983"
---
## <a name="prerequisites"></a>Předpoklady

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=jre)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Pokud chcete do projektu Javy přidat novou prázdnou třídu, vyberte **File** (Soubor)  >  **New** (Nový)  >  **Class** (Třída).

1. V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Veškerý kód v `Main.java` nahraďte následujícím fragmentem kódu:

   ```Java

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
               // Replace below with your own service region (e.g., "westus").
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

               if (result.getReason() == ResultReason.RecognizedSpeech) {
                   System.out.println("We recognized: " + result.getText());
                   exitCode = 0;
               }
               else if (result.getReason() == ResultReason.NoMatch) {
                   System.out.println("NOMATCH: Speech could not be recognized.");
               }
               else if (result.getReason() == ResultReason.Canceled) {
                   CancellationDetails cancellation = CancellationDetails.fromResult(result);
                   System.out.println("CANCELED: Reason=" + cancellation.getReason());

                   if (cancellation.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
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

1. Nahraďte řetězec `whatstheweatherlike.wav` vlastním názvem souboru.

1. Uložte změny do projektu.

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).
V okně konzoly bude rozpoznáno a zaznamenáno prvních 15 sekund vstupu řeči ze zvukového souboru.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]