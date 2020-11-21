---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: c341ba20ece26e15255faf086e5bd2904fbaa797
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098077"
---
V tomto rychlém startu se dozvíte, jak pomocí sady Speech Devices SDK pro Android vytvořit produkt s podporou řeči nebo ho použít jako zařízení [přepisující konverzaci](../conversation-transcription.md) .

Tato příručka vyžaduje účet [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) s prostředkem služby Speech.

Zdrojový kód ukázkové aplikace je součástí sady Speech Devices SDK. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete používat sadu Speech SDK pro zařízení, budete muset:

- Pokud chcete zařízení zapnout, postupujte podle pokynů v sadě [Development Kit](../get-speech-devices-sdk.md) .

- Stáhněte si nejnovější verzi [sady Speech Devices SDK](../speech-devices-sdk.md)a extrahujte soubor. zip do pracovního adresáře.

  > [!NOTE]
  > V tomto rychlém startu se předpokládá, že se aplikace extrahuje do C:\SDSDK\Android-Sample-Release.

- Získání [klíče předplatného Azure pro službu Speech](../overview.md#try-the-speech-service-for-free)

- Pokud plánujete použití přepisu konverzace, musíte použít [kruhový mikrofon](../get-speech-devices-sdk.md) a tato funkce je v tuto chvíli dostupná jenom pro "en-US" a "zh-CN" v oblastech, "centralus" a "eastasia". Pokud chcete použít přepis konverzace, musíte mít v jedné z těchto oblastí klíč řeči.

- Pokud plánujete použít službu Speech k identifikaci záměrů (nebo akcí) od uživatele projevy, budete potřebovat předplatné [služby Language Understanding (Luis)](../../luis/luis-how-to-azure-subscription.md) . Další informace o LUIS a rozpoznávání záměrů najdete v tématu [rozpoznávání hlasových záměrů pomocí Luis, C#](../how-to-recognize-intents-from-speech-csharp.md).

  Můžete [vytvořit jednoduchý model Luis](../../luis/index.yml) nebo použít Vzorový model Luis LUIS-example.jsv. Vzorový model LUIS je k dispozici na [webu pro stažení sady Speech Devices SDK](https://aka.ms/sdsdk-luis). Pokud chcete nahrát soubor JSON modelu na [portál Luis](https://www.luis.ai/home), vyberte **importovat novou aplikaci** a pak vyberte soubor JSON.

- Na svůj počítač nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](https://vysor.io/download/) .

## <a name="set-up-the-device"></a>Nastavení zařízení

1. V počítači spusťte Vysor.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Vaše zařízení by mělo být uvedené v části **zvolit zařízení**. Vyberte tlačítko **Zobrazit** vedle zařízení.

1. Připojte se k bezdrátové síti tak, že vyberete ikonu složky a pak vyberete **Nastavení**  >  **síť WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Pokud má vaše společnost zásady týkající se připojení zařízení ke svému Wi-Fi systému, je potřeba získat adresu MAC a kontaktovat oddělení IT, jak ho připojit k Wi-Fi vaší společnosti.
   >
   > Pokud chcete najít adresu MAC pro vývojovou sadu, vyberte ikonu složky souborů na ploše sady dev Kit.
   >
   > ![Složka souborů Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Vyberte **Nastavení**. Vyhledejte adresu MAC a pak vyberte **MAC adresa**  >  **Upřesnit síť WLAN**. Zapište adresu MAC, která se zobrazí v dolní části dialogového okna.
   >
   > ![Adresa MAC Vysor](../media/speech-devices-sdk/qsg-11.png)
   >
   > Některé společnosti můžou mít časový limit, jak dlouho může být zařízení připojené k Wi-Fimu systému. Po určitém počtu dnů možná budete muset v Wi-Fi systému zvětšit registraci sady dev Kit.

## <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Chcete-li ověřit nastavení vývojové sady, sestavte a nainstalujte ukázkovou aplikaci:

1. Spusťte Android Studio.

1. Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

   ![Android Studio – otevření existujícího projektu](../media/speech-devices-sdk/qsg-5.png)

1. Přejít na C:\SDSDK\Android-Sample-Release\example. Výběrem **OK** otevřete vzorový projekt.

1. Nakonfigurujte Gradle tak, aby odkazoval na sadu Speech SDK. Následující soubory najdete v části **Gradle skripty** v Android Studio.

    Aktualizujte **Build. Gradle (projekt: example)**, blok allprojects by se měl shodovat s tím, že přidá řádky Maven.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Aktualizujte **Build. Gradle (Module: App)** tak, že přidáte tento řádek do oddílu závislosti. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
    ```
    
1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete vyzkoušet rozpoznávání záměru, přidejte také klíč předplatného [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) a ID aplikace.

   V případě řeči a LUIS se vaše informace nacházejí v MainActivity. Java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Pokud používáte přepis konverzace, v konverzaci. Java jsou taky potřebné informace o vašem klíči a oblastech řeči:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Klíčové slovo Default je "Computer". Můžete také vyzkoušet jedno z dalších poskytnutých klíčových slov, například "počítač" nebo "asistent". Soubory prostředků pro tato alternativní klíčová slova jsou v sadě Speech Devices SDK ve složce klíčová slova. Například C:\SDSDK\Android-Sample-Release\keyword\Computer obsahuje soubory používané pro klíčové slovo "Computer".

   > [!TIP]
   > Můžete také [vytvořit vlastní klíčové slovo](../custom-keyword-basics.md).

   Chcete-li použít nové klíčové slovo, aktualizujte následující dva řádky v `MainActivity.java` a zkopírujte balíček klíčových slov do aplikace. Například pro použití klíčového slova ' Machine ' z kws-machine.zip balíčku klíčového slova:

   - Zkopírujte balíček s klíčovým slovem do složky "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets \" .
   - Aktualizujte `MainActivity.java` klíčové slovo pomocí klíčového slova a názvu balíčku:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aktualizujte následující řádky, které obsahují nastavení geometrie pole mikrofonu:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   V této tabulce jsou uvedené podporované hodnoty:

   | Proměnná | Význam | Dostupné hodnoty |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfigurace fyzického mikrofonu | Pro kruhovou sadu dev Kit: `Circular6+1` |
   |          |         | Pro lineární sadu dev Kit: `Linear4` |
   | `SelectedGeometry` | Konfigurace softwaru MIC | Pro kruhovou sadu pro vývoj, která používá všechny mics: `Circular6+1` |
   |          |         | Pro kruhovou sadu pro vývoj, která používá čtyři mics: `Circular3+1` |
   |          |         | Pro lineární vývojovou sadu, která používá všechny mics: `Linear4` |
   |          |         | Pro lineární vývojovou sadu, která používá dvě mics: `Linear2` |

1. Aplikaci vytvoříte tak, že v nabídce **Spustit** vyberete **Spustit aplikaci**. Zobrazí se dialogové okno **vybrat cíl nasazení** .

1. Vyberte své zařízení a pak kliknutím na **OK** Nasaďte aplikaci do zařízení.

   ![Dialogové okno vybrat cíl nasazení](../media/speech-devices-sdk/qsg-7.png)

1. Spustí se ukázka aplikace Speech Devices SDK a zobrazí následující možnosti:

   ![Ukázková sada Speech Devices SDK – ukázková aplikace a možnosti](../media/speech-devices-sdk/qsg-8.png)

1. Vyzkoušejte si novou ukázku přepisu konverzace. Spusťte zdlouhavého přepisování pomocí příkazu ' spustit relaci '. Ve výchozím nastavení je každý host. Pokud ale máte signatury hlasu účastníka, můžou se do zařízení umístit do souboru `/video/participants.properties` . Pokud chcete vygenerovat hlasový podpis, podívejte se na [konverzace přepisovat (SDK)](../how-to-use-conversation-transcription.md).

   ![Ukázková aplikace přepisu konverzace](../media/speech-devices-sdk/qsg-15.png)

1. Začátku!

## <a name="troubleshooting"></a>Řešení potíží

Pokud se nemůžete připojit k zařízení pro rozpoznávání řeči. V okně příkazového řádku zadejte následující příkaz. Vrátí se seznam zařízení:

```powershell
 adb devices
```

> [!NOTE]
> Tento příkaz používá Android Debug Bridge, `adb.exe` , který je součástí instalace Android Studio. Tento nástroj je umístěný v umístění C:\Users \[ User name] \AppData\Local\Android\Sdk\platform-Tools. Tento adresář můžete přidat do své cesty, abyste ho mohli lépe vyvolávat `adb` . V opačném případě je nutné zadat úplnou cestu k instalaci adb.exe v každém příkazu, který vyvolá `adb` .
>
> Pokud se zobrazí chyba, `no devices/emulators found` Zkontrolujte, zda je kabel USB připojen a zda je použit kabel s vysokou kvalitou.