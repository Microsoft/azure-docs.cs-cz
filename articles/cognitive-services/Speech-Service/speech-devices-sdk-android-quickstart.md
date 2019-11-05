---
title: 'Rychlý Start: spuštění sady Speech Devices SDK v Androidu – Speech Service'
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení práce se sadou Speech pro zařízení s Androidem
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acb041ce29d0340686a09764158063ad8d000c7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491311"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Rychlý Start: spuštění ukázkové aplikace sady Speech Devices SDK v Androidu

V tomto rychlém startu se dozvíte, jak pomocí sady Speech Devices SDK pro Android vytvořit produkt s podporou řeči nebo ho použít jako zařízení [přepisující konverzaci](conversation-transcription-service.md) .

Tato příručka vyžaduje účet [Azure Cognitive Services](get-started.md) s prostředkem služeb Speech Services. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód ukázkové aplikace je součástí sady Speech Devices SDK. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete používat sadu Speech SDK pro zařízení, budete muset:

* Pokud chcete zařízení zapnout, postupujte podle pokynů v sadě [Development Kit](get-speech-devices-sdk.md) .

* Stáhněte si nejnovější verzi [sady Speech Devices SDK](https://aka.ms/sdsdk-download)a extrahujte soubor. zip do pracovního adresáře.
   > [!NOTE]
   > Soubor Android-Sample-Release. zip obsahuje ukázkovou aplikaci pro Android a v tomto rychlém startu se předpokládá, že se aplikace extrahuje do C:\SDSDK\Android-Sample-Release.

* Získání [klíče předplatného Azure pro služby řeči](get-started.md)

* Pokud plánujete použití přepisu konverzace, musíte použít [kruhový mikrofon](get-speech-devices-sdk.md) a tato funkce je v tuto chvíli dostupná jenom pro "en-US" a "zh-CN" v oblastech, "centralus" a "eastasia". Pokud chcete použít přepis konverzace, musíte mít v jedné z těchto oblastí klíč řeči.

* Pokud máte v úmyslu identifikovat záměry (nebo akce) od uživatele projevy pomocí služby Speech, budete potřebovat předplatné [služby Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Další informace o LUIS a rozpoznávání záměrů najdete v tématu [rozpoznávání hlasových záměrů pomocí C#Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Můžete [vytvořit jednoduchý model Luis](https://docs.microsoft.com/azure/cognitive-services/luis/) nebo použít ukázkový model Luis Luis-example. JSON. Vzorový model LUIS je k dispozici na [webu pro stažení sady Speech Devices SDK](https://aka.ms/sdsdk-luis). Pokud chcete nahrát soubor JSON modelu na [portál Luis](https://www.luis.ai/home), vyberte **importovat novou aplikaci**a pak vyberte soubor JSON.

* Na svůj počítač nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](https://vysor.io/download/) .

## <a name="set-up-the-device"></a>Nastavení zařízení

1. V počítači spusťte Vysor.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Vaše zařízení by mělo být uvedené v části **zvolit zařízení**. Vyberte tlačítko **Zobrazit** vedle zařízení.

1. Připojte se k bezdrátové síti tak, že vyberete ikonu složky a pak vyberete **nastavení** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Pokud má vaše společnost zásady týkající se připojení zařízení k systému Wi-Fi, musíte získat adresu MAC a kontaktovat oddělení IT, jak ho připojit k Wi-Fi vaší společnosti.
    >
    > Pokud chcete najít adresu MAC pro vývojovou sadu, vyberte ikonu složky souborů na ploše sady dev Kit.
    >
    >  ![Složka souborů Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Vyberte **Nastavení**. Vyhledejte adresu MAC a pak vyberte **MAC adresa** > **Rozšířená síť WLAN**. Zapište adresu MAC, která se zobrazí v dolní části dialogového okna.
    >
    > ![Adresa MAC Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Některé společnosti můžou mít časový limit, jak dlouho může být zařízení připojené k systému Wi-Fi. Po určitém počtu dnů možná budete muset v systému Wi-Fi zvětšit registraci sady dev Kit.

## <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Chcete-li ověřit nastavení vývojové sady, sestavte a nainstalujte ukázkovou aplikaci:

1. Spusťte Android Studio.

1. Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

   ![Android Studio – otevření existujícího projektu](media/speech-devices-sdk/qsg-5.png)

1. Přejít na C:\SDSDK\Android-Sample-Release\example. Výběrem **OK** otevřete vzorový projekt.

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
   > Můžete také [vytvořit vlastní klíčové slovo](speech-devices-sdk-create-kws.md).

    Chcete-li použít nové klíčové slovo, aktualizujte následující dva řádky v `MainActivity.java`a zkopírujte balíček klíčových slov do aplikace. Například pro použití klíčového slova ' Machine ' z klíčového slova Package KWS-Machine. zip:

   * Zkopírujte balíček s klíčovým slovem do složky "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Aktualizujte `MainActivity.java` klíčovým slovem a názvem balíčku:

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

   |Proměnná|Význam|Dostupné hodnoty|
   |--------|-------|----------------|
   |`DeviceGeometry`|Konfigurace fyzického mikrofonu|Pro kruhovou sadu dev Kit: `Circular6+1` |
   |||Pro lineární vývojovou sadu: `Linear4`|
   |`SelectedGeometry`|Konfigurace softwaru MIC|Pro kruhovou sadu pro vývoj, která používá všechny mics: `Circular6+1`|
   |||Pro kruhovou sadu pro vývoj, která používá čtyři mics: `Circular3+1`|
   |||Pro lineární vývojovou sadu, která používá všechny mics: `Linear4`|
   |||Pro lineární vývojovou sadu, která používá dvě mics: `Linear2`|

1. Aplikaci vytvoříte tak, že v nabídce **Spustit** vyberete **Spustit aplikaci**. Zobrazí se dialogové okno **vybrat cíl nasazení** .

1. Vyberte své zařízení a pak kliknutím na **OK** Nasaďte aplikaci do zařízení.

    ![Dialogové okno vybrat cíl nasazení](media/speech-devices-sdk/qsg-7.png)

1. Spustí se ukázka aplikace Speech Devices SDK a zobrazí následující možnosti:

   ![Ukázková sada Speech Devices SDK – ukázková aplikace a možnosti](media/speech-devices-sdk/qsg-8.png)

1. Vyzkoušejte si novou ukázku přepisu konverzace. Spusťte zdlouhavého přepisování pomocí příkazu ' spustit relaci '. Ve výchozím nastavení je každý host. Pokud ale máte signatury hlasu účastníka, můžou se do souboru `/video/participants.properties` na zařízení umístit. Pokud chcete vygenerovat hlasový podpis, podívejte se na [konverzace přepisovat (SDK)](how-to-use-conversation-transcription-service.md).

   ![Ukázková aplikace přepisu konverzace](media/speech-devices-sdk/qsg-15.png)

1. Začátku!

## <a name="troubleshooting"></a>Řešení potíží

   Pokud se nemůžete připojit k zařízení pro rozpoznávání řeči. V okně příkazového řádku zadejte následující příkaz. Vrátí se seznam zařízení:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Tento příkaz používá Android Debug Bridge, `adb.exe`, která je součástí instalace Android Studio. Tento nástroj se nachází v C:\Users\[jméno uživatele] \AppData\Local\Android\Sdk\platform-tools. Tento adresář můžete přidat do své cesty, aby bylo lépe praktické vyvolat `adb`. V opačném případě je nutné zadat úplnou cestu k instalaci souboru ADB. exe do každého příkazu, který vyvolá `adb`.
   >
   > Pokud se zobrazí chyba `no devices/emulators found` pak zkontrolujte, zda je kabel USB připojen a zda je použit kabel s vysokou kvalitou.
   >

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si poznámky k verzi.](devices-sdk-release-notes.md)
