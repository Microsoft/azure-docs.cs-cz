---
title: 'Rychlý Start: spuštění sady Speech Devices SDK v Androidu'
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
ms.openlocfilehash: c3eb6ec28879a7c53feb270e33857cd67dc06b0b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111685"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Rychlý Start: spuštění ukázkové aplikace sady Speech Devices SDK v Androidu

V tomto rychlém startu se dozvíte, jak pomocí sady Speech Devices SDK pro Android vytvořit produkt s podporou řeči nebo ho použít jako zařízení [přepisující konverzaci](conversation-transcription-service.md) .

Tato příručka vyžaduje účet [Azure Cognitive Services](get-started.md) s prostředkem služeb Speech Services. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód ukázkové aplikace je součástí sady SDK zařízení pro rozpoznávání řeči. Je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete používat sadu Speech SDK pro zařízení, budete muset:

- Pokud chcete zařízení zapnout, postupujte podle pokynů v sadě [Development Kit](get-speech-devices-sdk.md) .

- Stáhněte si nejnovější verzi [sady Speech Devices SDK](https://aka.ms/sdsdk-download)a extrahujte soubor. zip do pracovního adresáře.

  > [!NOTE]
  > Soubor Android-Sample-Release. zip obsahuje ukázkovou aplikaci pro Android a v tomto rychlém startu se předpokládá, že se aplikace extrahuje do C:\SDSDK\Android-Sample-Release.

- Získání [klíče předplatného Azure pro služby řeči](get-started.md)

- Pokud plánujete použití přepisu konverzace, musíte použít [kruhový mikrofon](get-speech-devices-sdk.md) a tato funkce je v tuto chvíli dostupná jenom pro "en-US" a "zh-CN" v oblastech, "centralus" a "eastasia". Pokud chcete použít přepis konverzace, musíte mít v jedné z těchto oblastí klíč řeči.

- Pokud máte v úmyslu identifikovat záměry (nebo akce) od uživatele projevy pomocí služby Speech, budete potřebovat předplatné [služby Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Další informace o LUIS a rozpoznávání záměrů najdete v tématu [rozpoznávání hlasových záměrů pomocí C#Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Je možné [vytvoření jednoduchého modelu LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) nebo použijte ukázku, model LUIS, LUIS example.json. Ukázka je k dispozici z modelu LUIS [serveru pro stahování sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-luis). K nahrání souboru JSON modelu [LUIS portál](https://www.luis.ai/home)vyberte **Import novou aplikaci**a potom vyberte soubor JSON.

- Nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](https://vysor.io/download/) ve vašem počítači.

## <a name="set-up-the-device"></a>Nastavení zařízení

1. Vysor spusťte na svém počítači.

   ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Vaše zařízení by měl být uvedený **zvolte zařízení**. Vyberte **zobrazení** tlačítko vedle zařízení.

1. Připojení k bezdrátové síti tak, že vyberete ikonu složky a pak vyberte **nastavení** > **WLAN**.

   ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Pokud má vaše společnost zásady o připojení zařízení k jeho Wi-Fi systému, musíte získat adresu MAC a kontaktovat oddělení IT o tom, jak ji připojit k firemní Wi-Fi.
   >
   > Najít adresu MAC dev kit, vyberte ikonu složky souboru na ploše dev kit.
   >
   > ![Složka souboru Vysor](media/speech-devices-sdk/qsg-10.png)
   >
   > Vyberte **nastavení**. Vyhledejte "adresa mac" a pak vyberte **Adresa Mac** > **rozšířeného sítě WLAN**. Poznamenejte si adresu MAC, který se zobrazí v dolní části dialogu.
   >
   > ![Adresa Vysor MAC](media/speech-devices-sdk/qsg-11.png)
   >
   > Některé společnosti můžou mít časový limit na jak dlouho smí být zařízení připojená k jejich Wi-Fi systému. Můžete potřebovat k rozšíření dev kit registrace k Wi-Fi systému po určitém počtu dní.

## <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Chcete-li ověřit nastavení vývojové sady, sestavte a nainstalujte ukázkovou aplikaci:

1. Spusťte Android Studio.

1. Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

   ![Android Studio – otevřít existující projekt](media/speech-devices-sdk/qsg-5.png)

1. Přejdete na C:\SDSDK\Android-Sample-Release\example. Vyberte **OK** Chcete-li spustit příklad projektu.

1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace.

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

   - Zkopírujte balíček s klíčovým slovem do složky "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Aktualizujte `MainActivity.java` klíčovým slovem a názvem balíčku:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aktualizujte následující řádky, které obsahují nastavení geometrie mikrofonu pole:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   V této tabulce jsou uvedené podporované hodnoty:

   | Proměnná | Význam | Dostupné hodnoty |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfigurace fyzického mic | Pro cyklické dev kit: `Circular6+1` |
   |          |         | Lineární dev Kit: `Linear4` |
   | `SelectedGeometry` | Konfigurace softwaru povinná kontrola úrovně důvěryhodnosti | Pro cyklické dev kit, která používá všechny mikrofonů: `Circular6+1` |
   |          |         | Pro cyklické dev kit, která používá čtyři mikrofonů: `Circular3+1` |
   |          |         | Lineární dev Kit, která používá všechny mikrofonů: `Linear4` |
   |          |         | Lineární dev Kit, která používá dva mikrofonů: `Linear2` |

1. Jak vytvořit aplikaci, na **spustit** nabídce vyberte možnost **spuštění "aplikace"** . **Vyberte cíl nasazení** zobrazí se dialogové okno.

1. Vyberte zařízení a pak vyberte **OK** k nasazení aplikace do zařízení.

   ![Dialogové okno Vyberte cíl nasazení](media/speech-devices-sdk/qsg-7.png)

1. Ukázková aplikace sadou SDK pro řeč zařízení spustí a zobrazí následující možnosti:

   ![Ukázková aplikace příklad sadou SDK pro řeč zařízení a možnosti](media/speech-devices-sdk/qsg-8.png)

1. Vyzkoušejte si novou ukázku přepisu konverzace. Spusťte zdlouhavého přepisování pomocí příkazu ' spustit relaci '. Ve výchozím nastavení je každý host. Pokud ale máte signatury hlasu účastníka, můžou se do souboru `/video/participants.properties` na zařízení umístit. Pokud chcete vygenerovat hlasový podpis, podívejte se na [konverzace přepisovat (SDK)](how-to-use-conversation-transcription-service.md).

   ![Ukázková aplikace přepisu konverzace](media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Řešení potíží

Pokud se nemůžete připojit k zařízení pro rozpoznávání řeči. V okně příkazového řádku zadejte následující příkaz. Vrátí se seznam zařízení:

```powershell
 adb devices
```

> [!NOTE]
> Tento příkaz používá Android Debug Bridge, `adb.exe`, která je součástí instalace Android Studio. Tento nástroj se nachází v C:\Users\[uživatelské jméno] \AppData\Local\Android\Sdk\platform nástroje. Tento adresář můžete přidat do cesty k němu pohodlnější, který má být vyvolán `adb`. V opačném případě musíte zadat úplnou cestu k instalaci adb.exe v každý příkaz, který vyvolá `adb`.
>
> Pokud se zobrazí chyba `no devices/emulators found` pak zkontrolujte, zda je kabel USB připojen a zda je použit kabel s vysokou kvalitou.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
