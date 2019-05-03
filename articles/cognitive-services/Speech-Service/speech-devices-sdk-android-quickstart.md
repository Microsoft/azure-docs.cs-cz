---
title: 'Rychlý start: Spuštění zařízení řeči sady SDK v systému Android – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny, jak začít s Androidem sadou SDK pro řeč zařízení.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: d5af2bb61eeb986f02a31d45ff9236ecc0c8427e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026192"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Rychlý start: Spuštění ukázkové aplikace sadou SDK pro řeč zařízení v Androidu

V tomto rychlém startu budete zjistěte, jak můžete vytvářet produkt podporou hlasových sadou SDK pro řeč zařízení pro Android.

Tato příručka vyžaduje [Azure Cognitive Services](get-started.md) účet s prostředkem hlasové služby. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód ukázkové aplikace je součástí sady SDK zařízení pro rozpoznávání řeči. Je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete používat sadou SDK pro řeč zařízení, bude nutné:

* Postupujte podle pokynů dodaných s vaší [sada](get-speech-devices-sdk.md) k zapnutí zařízení.

* Stáhněte si nejnovější verzi [sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-download)a extrahujte .zip do pracovního adresáře.
   > [!NOTE]
   > Soubor ZIP obsahuje Android ukázkovou aplikaci.

* Chcete-li získat [klíč předplatného pro hlasové služby](get-started.md)

* Pokud máte v plánu můžete identifikovat záměry (nebo akce) z uživatele projevy hlasové služby, bude nutné [služby Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) předplatného. Další informace o LUIS a rozpoznání záměru, naleznete v tématu [rozpoznávání řeči záměry služba luis C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Je možné [vytvoření jednoduchého modelu LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) nebo použijte ukázku, model LUIS, LUIS example.json. Ukázka je k dispozici z modelu LUIS [serveru pro stahování sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-luis). K nahrání souboru JSON modelu [LUIS portál](https://www.luis.ai/home)vyberte **Import novou aplikaci**a potom vyberte soubor JSON.

* Nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](https://vysor.io/download/) ve vašem počítači.

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
    >  ![Složka souboru Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Vyberte **nastavení**. Vyhledejte "adresa mac" a pak vyberte **Adresa Mac** > **rozšířeného sítě WLAN**. Poznamenejte si adresu MAC, který se zobrazí v dolní části dialogu.
    >
    > ![Adresa Vysor MAC](media/speech-devices-sdk/qsg-11.png)
    >
    > Některé společnosti můžou mít časový limit na jak dlouho smí být zařízení připojená k jejich Wi-Fi systému. Můžete potřebovat k rozšíření dev kit registrace k Wi-Fi systému po určitém počtu dní.

## <a name="run-the-sample-application"></a>Spuštění ukázkové aplikace

Pokud chcete ověřit nastavení development kit, sestavení a nainstalovat ukázkovou aplikaci:

1. Spusťte Android Studio.

1. Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

   ![Android Studio – otevřít existující projekt](media/speech-devices-sdk/qsg-5.png)

1. Přejdete na C:\SDSDK\Android-Sample-Release\example. Vyberte **OK** Chcete-li spustit příklad projektu.

1. Přidáte klíč předplatného řeči ke zdrojovému kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace.

   Klíče a informace o aplikaci najdete v následujících řádcích v zdrojový soubor MainActivity.java:

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. Výchozí probuzení word (klíčové slovo) je "Počítač". Můžete také zkusit jednu z nich k dispozici funkce slova, třeba "Počítač" nebo "Assistant". Soubory prostředků pro tato slova alternativní probuzení se v zařízení sadou SDK pro řeč, ve složce – klíčové slovo. Například C:\SDSDK\Android-Sample-Release\keyword\Computer obsahuje soubory používané pro probuzení slovo "Počítač".

   > [!TIP]
   > Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    Pokud chcete používat nové probuzení slovo, aktualizujte následující dva řádky v `MainActivity.java`a zkopírujte balíček aplikace word probuzení do vaší aplikace. Například použijte probuzení slovo 'Počítače' z kws probuzení slova balíčku-machine.zip:

   * Zkopírujte balíček aplikace word probuzení do složky "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Aktualizace `MainActivity.java` pomocí klíčového slova a název balíčku:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aktualizujte následující řádky, které obsahují nastavení geometrie mikrofonu pole:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Tato tabulka uvádí podporované hodnoty:

   |Proměnná|Význam|Dostupné hodnoty|
   |--------|-------|----------------|
   |`DeviceGeometry`|Konfigurace fyzického mic|Pro cyklické dev kit: `Circular6+1` |
   |||Lineární dev Kit: `Linear4`|
   |`SelectedGeometry`|Konfigurace softwaru povinná kontrola úrovně důvěryhodnosti|Pro cyklické dev kit, která používá všechny mikrofonů: `Circular6+1`|
   |||Pro cyklické dev kit, která používá čtyři mikrofonů: `Circular3+1`|
   |||Lineární dev Kit, která používá všechny mikrofonů: `Linear4`|
   |||Lineární dev Kit, která používá dva mikrofonů: `Linear2`|

1. Jak vytvořit aplikaci, na **spustit** nabídce vyberte možnost **spuštění "aplikace"**. **Vyberte cíl nasazení** zobrazí se dialogové okno.

1. Vyberte zařízení a pak vyberte **OK** k nasazení aplikace do zařízení.

    ![Dialogové okno Vyberte cíl nasazení](media/speech-devices-sdk/qsg-7.png)

1. Ukázková aplikace sadou SDK pro řeč zařízení spustí a zobrazí následující možnosti:

   ![Ukázková aplikace příklad sadou SDK pro řeč zařízení a možnosti](media/speech-devices-sdk/qsg-8.png)

1. Experiment!

## <a name="troubleshooting"></a>Řešení potíží

   Pokud se nemůžete připojit k zařízení řeči. V okně příkazového řádku zadejte následující příkaz. Vrátí seznam zařízení:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Tento příkaz používá Android Debug Bridge, `adb.exe`, který je součástí instalace sady Android Studio. Tento nástroj se nachází v C:\Users\[uživatelské jméno] \AppData\Local\Android\Sdk\platform nástroje. Tento adresář můžete přidat do cesty k němu pohodlnější, který má být vyvolán `adb`. V opačném případě musíte zadat úplnou cestu k instalaci adb.exe v každý příkaz, který vyvolá `adb`.
   >
   > Pokud se zobrazí chyba `no devices/emulators found` pak zkontrolujte připojení USB kabelu a zajistit vysoce kvalitní kabel používal.
   >

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
