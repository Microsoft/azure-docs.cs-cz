---
title: Začínáme se sadou SDK pro řeč zařízení
description: Požadavky a pokyny, jak začít se sadou SDK pro řeč zařízení.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283132"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Začínáme se sadou SDK pro řeč zařízení

Tento článek popisuje, jak nakonfigurovat váš vývojový počítač a vaše řeči zařízení development kit pro vývoj zařízení s podporou řeči pomocí sady SDK zařízení řeči. Bude potom sestavíte a nasadíte ukázkovou aplikaci na zařízení. 

Zdrojový kód ukázkové aplikace je součástí sady SDK zařízení pro zpracování řeči a je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Před zahájením vývoj pomocí sady SDK zařízení řeči, shromážděte informace a softwaru, které budete potřebovat.

* Získat development kit [z Roobo](http://ddk.roobo.com/). Sady jsou k dispozici s konfiguracemi pro pole Lineární nebo cyklický mikrofon; Vyberte tu správnou pro vaše potřeby.

    |Konfigurace Development kit|Umístění mluvčího|
    |-----------------------------|------------|
    |Cyklické|Libovolném směru ze zařízení|
    |Lineární|U zařízení|

* Získat nejnovější verzi sady SDK zařízení řeči, včetně Androidu ukázkovou aplikaci, ze sady SDK zařízení řeči [web pro stažení](https://shares.datatransfer.microsoft.com/). Extrahujte soubor ZIP do místní složky (například `C:\SDSDK`).

* Nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](http://vysor.io/download/) ve vašem počítači.

* Získat Speech service [klíč předplatného](get-started.md). Můžete získat 30denní bezplatné zkušební verze nebo získání klíče z řídicího panelu Azure.

* Pokud chcete použít rozpoznání záměru řeči service, přihlášení k odběru [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) a [získat klíč předplatného](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Vám může [vytvoření jednoduchého modelu LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) nebo použijte ukázku, LUIS model, `LUIS-example.json`, která je dostupná z sadou SDK pro řeč zařízení [web pro stažení](https://shares.datatransfer.microsoft.com/). Nahrání souboru JSON modelu [LUIS portál](https://www.luis.ai/home) kliknutím **Import novou aplikaci** a výběrem souboru JSON.

## <a name="set-up-the-development-kit"></a>Nastavení sady development kit

1. Zařaďte adaptér power development kit. Indikátor zelené power by bylo možné v horním panelu.

1. Připojení k počítači pomocí USB kabelu minimální development kit.

    ![připojení dev kit](media/speech-devices-sdk/qsg-1.jpg)

1. Vývojová sada orientovat odpovídajícím způsobem.

    |Konfigurace Development kit|Orientace|
    |-----------------------------|------------|
    |Cyklické|Sloupku s mikrofonů, kterým čelí horní mez|
    |Lineární|Na své straně s mikrofonů směrem k vám (viz dole)|

    ![orientace lineární dev kit](media/speech-devices-sdk/qsg-2.jpg)

1. Nainstalujte certifikáty a soubor tabulky probuzení word (klíčové slovo) a nastavit oprávnění zvukové zařízení. Zadejte následující příkazy v příkazovém okně.

    > [!NOTE]
    > Tyto příkazy používají Android Debug Bridge, `adb.exe`, který je součástí instalace sady Android Studio. Tento nástroj najdete v `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Tento adresář může přidat do cesty k němu pohodlnější, který má být vyvolán `adb`. V opačném případě musíte zadat úplnou cestu k instalaci sady `adb.exe` v každý příkaz, který vyvolá `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Ztlumení mikrofonu a reproduktorů počítače. Díky tomu lze zajistit pracujete s mikrofonů development kit a nebudou neúmyslně aktivovat zařízení se zvukem z počítače.
    
1.  Spusťte Vysor ve vašem počítači.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Vaše zařízení by měly být uvedeny v části "Zvolte zařízení". Klikněte na tlačítko **zobrazení** vedle sebe tlačítko. 
 
1.  Připojení k bezdrátové síti kliknutím **nastavení**, pak **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Spuštění ukázkové aplikace

Ke spuštění testů Roobo a ověření nastavení development kit, sestavit a nainstalovat ukázkovou aplikaci.

1.  Spusťte Android Studio.

1.  Vyberte a otevřete existující projekt Android Studio.

    ![Android studio – otevřít existující projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Přejděte do `C:\SDSDK\Android-Sample-Release\example`, pak klikněte na tlačítko **OK** Chcete-li spustit příklad projektu.
 
1.  Přidáte klíč předplatného řeči ke zdrojovému kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace. 

    Klíče a ukládá informace o aplikaci následující řádky ve zdrojovém souboru `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Výchozí probuzení word (klíčové slovo) je "Počítač".  Pokud chcete, může použijte jeden z nich k dispozici funkce slova, "Počítač" a "Assistant". Soubory prostředků pro těchto alternativních slov najdete v sadou SDK pro řeč zařízení ve složce "– klíčové slovo". Například `C:\SDSDK\Android-Sample-Release\keyword\Computer` obsahuje soubory používané pro "Počítač".

    Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    K instalaci požadovaného probuzení word:
 
    * Vytvoření `keyword` složky ve složce \data\ na zařízení spuštěním následujících příkazů v příkazovém okně.

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
    
    * Odkazovat na tyto soubory v ukázkové aplikaci. Vyhledejte následující řádky v `MainActivity.java`. Ujistěte se, že zadané klíčové slovo je použijete jeden a že cesta odkazuje na `kws.table` soubor, který jste odeslali do zařízení.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Ve svém vlastním kódu, můžete použít `kws.table` soubor pro vytvoření instance modelu – klíčové slovo a rozpoznávání spuštění následujícím způsobem.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Aktualizujte následující řádky obsahující nastavení geometrie pole mikrofonu.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Proměnná|Význam|Dostupné hodnoty|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfigurace fyzického mic|`Circular6+1` pro cyklické dev kit|
    ||| `Linear4` Lineární dev Kit|
    |`SelectedGeometry`|Konfigurace softwaru povinná kontrola úrovně důvěryhodnosti|`Circular6+1` Cyklické dev kit pomocí všech mikrofonu|
    |||`Circular3+1` Cyklické dev kit pomocí čtyř mikrofonu|
    |||`Linear4` Lineární dev kit pomocí všech mikrofonu|
    |||`Linear2` Lineární dev kit pomocí dvou mikrofonu|


1.  Sestavte aplikaci výběrem **spuštění "aplikace"** z nabídky spustit. Zobrazí se dialogové okno Vyberte cíl nasazení. Vyberte zařízení a klikněte na tlačítko **OK** k nasazení aplikace do zařízení.

    ![Vyberte cíl nasazení](media/speech-devices-sdk/qsg-7.png)
 
1.  Ukázková aplikace sadou SDK pro řeč zařízení spustí možnosti je vidět tady.

    ![ukázkové aplikace zařízení řeči](media/speech-devices-sdk/qsg-8.png)

1. Pohrajte si s ní!

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí selhání certifikátu, při použití služby řeči, ujistěte se, že zařízení má správné datum a čas.

Vývoj podrobnosti naleznete v tématu společnosti Roobo [Příručka pro vývojáře](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo poskytuje nástroj, který zachycuje všechny zvuk na flash paměti, což může pomoci při řešení potíží s zvuku. Verze nástroje se poskytuje pro každou konfiguraci development kit. Zvolte si zařízení na [lokality Roobo](http://ddk.roobo.com/), klikněte **ROOBO nástroje** odkaz v dolní části stránky.
