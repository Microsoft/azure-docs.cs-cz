---
title: Začínáme se sadou SDK řeči zařízení | Microsoft Docs
description: Požadavky a pokyny pro Začínáme se sadou SDK řeči zařízení.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 6cc5ff1c532d67c48beac1a2a10d034f5d9d7501
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343788"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Začínáme se sadou SDK řeči zařízení

Tento článek popisuje, jak nakonfigurovat váš vývojový počítač a vaše řeči zařízení development kit pro vývoj pomocí sady SDK zařízení řeči zařízení rozpoznávání řeči. Potom sestavení a nasazení ukázkové aplikace do zařízení. 

Zdrojový kód pro ukázkovou aplikaci, je součástí sady SDK zařízení řeči a je také [dostupná na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Před zahájením vývoj pomocí sady SDK zařízení rozpoznávání řeči, shromážděte informace a softwaru, které budete potřebovat.

* Získat development kit [z Roobo](http://ddk.roobo.com/). Sady jsou k dispozici s konfigurací pole Lineární nebo cyklické mikrofon; Vyberte tu správnou vašim potřebám.

    |Konfigurace Development kit|Mluvčího umístění|
    |-----------------------------|------------|
    |Cyklické|Libovolným směrem ze zařízení|
    |Lineární|Před zařízení|

* Získejte nejnovější verzi sady SDK zařízení rozpoznávání řeči, včetně Android ukázkové aplikace, ze sady SDK zařízení řeči [ke stažení](https://shares.datatransfer.microsoft.com/). Extrahujte soubor ZIP do místní složky (například `C:\SDSDK`).

* Nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](http://vysor.io/download/) ve vašem počítači.

* Získat služby řeči [klíč předplatného](get-started.md). Můžete získat bezplatnou 30denní zkušební verzi nebo získání klíče z řídicího panelu Azure.

* Pokud chcete použít záměrné rozpoznávání řeči služby, přihlášení k odběru [znalosti jazyka služby](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LEOŠ) a [získat klíč předplatného](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Mohou [vytvoření jednoduchého modelu LEOŠ](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) nebo použijte ukázku, LEOŠ modelu `LUIS-example.json`, k dispozici ze sady SDK zařízení řeči [ke stažení](https://shares.datatransfer.microsoft.com/). Nahrát soubor JSON váš model do [LEOŠ portál](https://www.lui.ai/applications) kliknutím **novou aplikaci Import** a výběr souboru JSON.

## <a name="set-up-the-development-kit"></a>Nastavit development kit

1. Připojte se v adaptéru power development kit. Na zelenou power ukazatel by měl light v horním panelu.

1. Připojte k počítači pomocí kabelu USB malé sadě pro vývoj.

    ![připojení kit vývojářů](media/speech-devices-sdk/qsg-1.jpg)

1. Vaše development kit orientaci správně.

    |Konfigurace Development kit|Orientace|
    |-----------------------------|------------|
    |Cyklické|Svisle s mikrofon čelí horní meze.|
    |Lineární|Na jeho straně s mikrofon směrem k vám (zobrazené dole)|

    ![orientace kit lineární vývojářů](media/speech-devices-sdk/qsg-2.jpg)

1. Nainstalujte certifikáty a soubor tabulky probuzení word (– klíčové slovo) a nastavte oprávnění zvukového zařízení. Zadejte následující příkazy v příkazovém okně.

    > [!NOTE]
    > Tyto příkazy používají most ladění Android, `adb.exe`, který je součástí instalace Android Studio. Tento nástroj naleznete v `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Tento adresář může přidat do své cestě aby pohodlnější k vyvolání `adb`. Jinak, musíte zadat úplnou cestu pro instalaci produktu `adb.exe` v každé příkazu, která volá `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Ztlumení mikrofon a mluvčího vašeho počítače. Tímto způsobem lze zajistit pracujete s mikrofon development kit a nebudou neúmyslně aktivuje zařízení s zvuk z počítače.
    
1.  Spusťte Vysor ve vašem počítači.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Zařízení by měl být uvedený v části "Vyberte zařízení". Klikněte **zobrazení** vedle sebe tlačítko. 
 
1.  Připojit k bezdrátové síti kliknutím **nastavení**, pak **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Spuštění ukázkové aplikace

Pokud chcete spustit testy Roobo a ověření vašeho development kit nastavení, sestavte a nainstalovat ukázkovou aplikaci.

1.  Spustíte Android Studio.

1.  Vyberte, chcete-li otevřít existující projekt Android Studio.

    ![Android studio – otevřít existující projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Přejděte do `C:\SDSDK\Android-Sample-Release\example`, pak klikněte na tlačítko **OK** otevřete příklad projektu.
 
1.  Přidejte svůj klíč předplatného řeči ke zdrojovému kódu. Pokud chcete vyzkoušet záměrné rozpoznávání, přidejte také vaše [znalosti jazyka služby](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace. 

    Klíče a ukládá informace o aplikaci následující řádky do zdrojového souboru `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Aplikace word probuzení výchozí (– klíčové slovo) je "Počítač".  Pokud chcete, můžete zkusit akci jednu z dalších poskytuje probuzení slova, "Počítač" a "Pomocníka." Soubory prostředků pro tyto alternativní slova naleznete v sadě SDK řeči zařízení ve složce "– klíčové slovo". Například `C:\SDSDK\Android-Sample-Release\keyword\Computer` obsahuje soubory používané pro "Počítač".

    Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    Instalace požadované probuzení word:
 
    * Vytvoření `keyword` složky ve složce \data\ na zařízení tak, že spustíte následující příkazy v příkazovém okně.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Zkopírujte soubory `kws.table`, `kws_g.fst`, `kws_k.fst`, a `words_kw.txt`) do složky \data\keyword\ zařízení. Spusťte následující příkazy v příkazovém okně o.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Odkazovat na tyto soubory v ukázkové aplikaci. Najít následující řádky do `MainActivity.java`. Ujistěte se, že zadané klíčové slovo je ten, který je používáte a že cesta odkazuje na `kws.table` soubor, který instaluje do zařízení.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > V kódu, můžete použít `kws.table` soubor k vytvoření instance modelu – klíčové slovo a spouštění rozpoznávání následujícím způsobem.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Aktualizujte následující řádky obsahující nastavení geometrie mikrofon pole.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Proměnná|Význam|Dostupné hodnoty|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfigurace fyzického povinná kontrola úrovně důvěryhodnosti|`Circular6+1` pro cyklické dev kit|
    ||| `Linear4` pro vývojáře lineární kit|
    |`SelectedGeometry`|Konfigurace softwaru povinná kontrola úrovně důvěryhodnosti|`Circular6+1` pro cyklické dev kit pomocí všechny MIC, algoritmus|
    |||`Circular3+1` pro cyklické dev kit pomocí čtyř MIC, algoritmus|
    |||`Linear4` pro použití všechny MIC, algoritmus kit lineární vývojářů|
    |||`Linear2` pro použití dvou MIC, algoritmus kit lineární vývojářů|


1.  Sestavení aplikace tak, že zvolíte **spuštění "aplikace"** z nabídky spustit. Otevře se dialogové okno Vybrat cíl nasazení. Vyberte zařízení a klikněte na **OK** k nasazení aplikace do zařízení.

    ![Vyberte cíl nasazení](media/speech-devices-sdk/qsg-7.png)
 
1.  Ukázková aplikace sady SDK zařízení řeči spustí tady zobrazené možnosti.

    ![Ukázková aplikace zařízení řeči](media/speech-devices-sdk/qsg-8.png)

1. Si ji vyzkoušeli!

## <a name="troubleshooting"></a>Řešení potíží

Pokud při používání služby řeči selhání certifikátu, ujistěte se, že zařízení má správné datum a čas.

Další informace vývoj najdete v tématu na Roobo [Průvodce vývojem](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo poskytuje nástroj, který zachycuje všechny zvuk do paměti, která může pomoci při řešení potíží s zvuk flash. Verze nástroje se poskytuje pro každé konfiguraci development kit. Vyberte si zařízení na [webu Roobo](http://ddk.roobo.com/), klikněte **ROOBO nástroje** odkaz ve spodní části stránky.
