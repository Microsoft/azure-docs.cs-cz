---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 2ee6b12923bfd0e06343e8f185226cb72280d806
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383998"
---
V tomto rychlém startu se dozvíte, jak pomocí sady SDK pro zařízení pro rozpoznávání řeči pro Android vytvořit produkt s podporou řeči nebo jej použít jako zařízení [pro přepis konverzace.](../conversation-transcription-service.md)

Tato příručka vyžaduje účet [Azure Cognitive Services](../get-started.md) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód ukázkové aplikace je součástí sady SDK pro řečová zařízení. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete používat sadu SDK pro řečová zařízení, budete muset:

- Chcete-li přístroj zapnout, postupujte podle pokynů dodaných s [vývojovou sadou.](../get-speech-devices-sdk.md)

- Stáhněte si nejnovější verzi sady [SDK pro řečová zařízení](https://aka.ms/sdsdk-download)a extrahujte soubor ZIP do pracovního adresáře.

  > [!NOTE]
  > Tento rychlý start předpokládá, že aplikace je extrahována do C:\SDSDK\Android-Sample-Release

- Získání [klíče předplatného Azure pro službu Speech](../get-started.md)

- Pokud máte v plánu použít přepis konverzace, musíte použít [kruhové mikrofonní zařízení](../get-speech-devices-sdk.md) a tato funkce je v současné době k dispozici pouze pro "en US" a "zh-CN" v oblastech, "centralus" a "eastasia". Chcete-li používat přepis konverzace, musíte mít v jedné z těchto oblastí klíč pro řeč.

- Pokud máte v plánu použít službu Řeči k identifikaci záměrů (nebo akcí) z projevy uživatelů, budete potřebovat předplatné [služby language understanding service (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Další informace o službě LUIS a rozpoznávání záměru najdete v [tématu Rozpoznávání záměrů řeči pomocí služby LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Můžete [vytvořit jednoduchý model LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) nebo použít ukázkový model LUIS, LUIS-example.json. Ukázkový model LUIS je k dispozici na [webu pro stahování sady Speech Devices SDK](https://aka.ms/sdsdk-luis). Chcete-li nahrát soubor JSON modelu na [portál LUIS](https://www.luis.ai/home), vyberte **importovat novou aplikaci**a pak vyberte soubor JSON.

- Nainstalujte do počítače [Android Studio](https://developer.android.com/studio/) a [Vysor.](https://vysor.io/download/)

## <a name="set-up-the-device"></a>Nastavení zařízení

1. Spusťte v počítači aplikaci Vysor.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Vaše zařízení by mělo být uvedeno v části **Volba zařízení**. Vyberte tlačítko **Zobrazit** vedle zařízení.

1. Připojte se k bezdrátové síti výběrem ikony složky a potom vyberte **nastavení** > **sítě WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Pokud má vaše společnost zásady týkající se připojení zařízení k wi-fi systému, musíte získat adresu MAC a kontaktovat it oddělení, jak ji připojit k Wi-Fi vaší společnosti.
   >
   > Chcete-li najít mac adresu sady dev kit, vyberte ikonu složky souborů na ploše sady pro řešení.
   >
   > ![Složka souborů Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Vyberte **Nastavení**. Vyhledejte "mac adresu" a pak vyberte **mac adresu** > **Advanced WLAN**. Poznamenejte si adresu MAC, která se zobrazí v dolní části dialogového okna.
   >
   > ![Vysor MAC adresa](../media/speech-devices-sdk/qsg-11.png)
   >
   > Některé společnosti mohou mít časový limit, jak dlouho může být zařízení připojeno k jejich wi-fi systému. Možná budete muset rozšířit registraci sady pro devit se svým Wi-Fi systémem po určitém počtu dní.

## <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Chcete-li ověřit nastavení vývojové sady, vytvořte a nainstalujte ukázkovou aplikaci:

1. Spusťte Android Studio.

1. Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

   ![Android Studio - Otevření existujícího projektu](../media/speech-devices-sdk/qsg-5.png)

1. Přejděte na příklad c:\SDSDK\Android-Sample-Release\example. Výběrem **možnosti OK** otevřete ukázkový projekt.

1. Nakonfigurujte gradle tak, aby odkazoval na sadu Speech SDK. Následující soubory lze nalézt v části **Gradle Skripty** v Android Studio.

    Aktualizujte **build.gradle(Project:example)**, blok všech projektů by se měl shodovat níže, přidáním maven řádků.

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

    Aktualizujte **build.gradle(Module:app)** přidáním tohoto řádku do oddílu závislostí. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.10.0'
    ```
    
1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete zkusit záměr uznání, přidejte také klíč předplatného [služby Language Understanding a](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID aplikace.

   Pro řeč a LUIS, vaše informace přejde do MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Pokud používáte přepis konverzace, je v konverzačním souboru potřeba také klíč řeči a informace o oblasti:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Výchozí klíčové slovo je "Počítač". Můžete také vyzkoušet jedno z dalších zadaných klíčových slov, například "Stroj" nebo "Asistent". Soubory prostředků pro tato alternativní klíčová slova jsou v sadě SDK řeči zařízení, ve složce klíčových slov. Například C:\SDSDK\Android-Sample-Release\keyword\Computer obsahuje soubory použité pro klíčové slovo "Počítač".

   > [!TIP]
   > Můžete také [vytvořit vlastní klíčové slovo](../speech-devices-sdk-create-kws.md).

   Chcete-li použít nové klíčové slovo, `MainActivity.java`aktualizujte následující dva řádky v aplikaci a zkopírujte balíček klíčových slov do aplikace. Chcete-li například použít klíčové slovo "Stroj" z klíčového slova balíček kws-machine.zip:

   - Zkopírujte balíček klíčových slov do složky "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Aktualizujte `MainActivity.java` pomocí klíčového slova a názvu balíčku:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aktualizujte následující řádky, které obsahují nastavení geometrie pole mikrofonu:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   V této tabulce jsou uvedeny podporované hodnoty:

   | Proměnná | Význam | Dostupné hodnoty |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfigurace fyzického mikrofonu | Pro kruhový dev kit:`Circular6+1` |
   |          |         | Pro lineární dev kit:`Linear4` |
   | `SelectedGeometry` | Konfigurace mikrofonu softwaru | Pro kruhovou dev kit, který používá všechny mikrofony:`Circular6+1` |
   |          |         | Pro kruhovou dev kit, který používá čtyři mikrofony:`Circular3+1` |
   |          |         | Pro lineární dev kit, který používá všechny mikrofony:`Linear4` |
   |          |         | Pro lineární dev kit, který používá dva mikrofony:`Linear2` |

1. Chcete-li vytvořit aplikaci, vyberte v nabídce **Spustit** **možnost Spustit aplikaci**. Zobrazí se dialogové okno **Vybrat cíl nasazení.**

1. Vyberte zařízení a pak vyberte **OK,** chcete-li aplikaci nasadit do zařízení.

   ![Dialogové okno Vybrat cíl nasazení](../media/speech-devices-sdk/qsg-7.png)

1. Spustí se ukázková aplikace sady Speech Devices SDK a zobrazí následující možnosti:

   ![Ukázka aplikace a možností sady SDK pro řečová zařízení](../media/speech-devices-sdk/qsg-8.png)

1. Vyzkoušejte novou ukázku přepisu konverzace. Začněte přepisovat pomocí úvodní relace. Ve výchozím nastavení je hostem každý. Pokud však máte hlasové podpisy účastníka, mohou `/video/participants.properties` být vloženy do souboru v zařízení. Chcete-li generovat hlasový podpis, podívejte se na [přepis konverzace (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikace pro přepis demo konverzace](../media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Řešení potíží

Pokud se nemůžete připojit k řečovému zařízení. Do okna příkazového řádku zadejte následující příkaz. Vrátí seznam zařízení:

```powershell
 adb devices
```

> [!NOTE]
> Tento příkaz používá most ladění `adb.exe`androida , který je součástí instalace sady Android Studio. Tento nástroj je umístěn v\[c:\Users uživatelské jméno]\AppData\Local\Android\Sdk\platform-tools. Tento adresář můžete přidat do cesty, aby `adb`bylo vhodnější vyvolat . V opačném případě je nutné zadat úplnou cestu k instalaci programu `adb`adb.exe v každém příkazu, který vyvolá .
>
> Pokud se zobrazí `no devices/emulators found` chyba, zkontrolujte, zda je připojen kabel USB a ujistěte se, že je použit vysoce kvalitní kabel.
