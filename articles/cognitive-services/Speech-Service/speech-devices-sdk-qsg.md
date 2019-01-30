---
title: Začínáme s zařízení sadou SDK pro řeč – hlasové služby
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny, jak začít se sadou SDK pro řeč zařízení.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 85e250f3dee3dbfc9e09524d5f3977a143ee8bac
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213255"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Začínáme se sadou SDK pro řeč zařízení

Tento článek popisuje, jak konfigurovat vaše vývojové počítače a řeči zařízení development kit pro vývoj pomocí sady SDK zařízení řeči podporou hlasových zařízení. Potom sestavíte a nasadíte ukázkovou aplikaci na zařízení.

Zdrojový kód ukázkové aplikace je součástí sady SDK zařízení pro rozpoznávání řeči. Je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

Než začnete s vývojem pomocí sady SDK zařízení řeči, shromážděte informace a softwaru, které budete potřebovat:

* Získání [sada z ROOBO](http://ddk.roobo.com/). Sady jsou k dispozici s konfiguracemi pro pole Lineární nebo cyklický mikrofon. Zvolte správnou konfiguraci pro vaše potřeby.

    |Konfigurace Development kit|Umístění mluvčího|
    |-----------------------------|------------|
    |Cyklické|Libovolném směru ze zařízení|
    |Lineární|U zařízení|

* Získat nejnovější verzi sady SDK zařízení řeči, která obsahuje aplikaci ukázka pro Android z [serveru pro stahování sadou SDK pro řeč zařízení](https://shares.datatransfer.microsoft.com/). Extrahujte soubor ZIP do místní složky, jako je C:\SDSDK.

* Nainstalujte [Android Studio](https://developer.android.com/studio/) a [Vysor](http://vysor.io/download/) ve vašem počítači.

* Získání [klíč předplatného služba Speech](get-started.md). Můžete získat 30denní bezplatnou zkušební verzi nebo získat klíč z řídicího panelu Azure.

* Pokud chcete použít rozpoznání záměru řeči service, přihlášení k odběru [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) a [získat klíč předplatného](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Je možné [vytvoření jednoduchého modelu LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) nebo použijte ukázku, model LUIS, LUIS example.json. Ukázka je k dispozici z modelu LUIS [serveru pro stahování sadou SDK pro řeč zařízení](https://shares.datatransfer.microsoft.com/). K nahrání souboru JSON modelu [LUIS portál](https://www.luis.ai/home)vyberte **Import novou aplikaci**a potom vyberte soubor JSON.

## <a name="set-up-the-development-kit"></a>Nastavení sady development kit

1. Vývojová sada připojit k počítači nebo napájení adaptér pomocí kabelu USB mini. Při připojení sadě zelené power indikátor aktivuje v horním panelu.

1. Vývojová sada připojení k počítači s použitím druhý mini kabelu USB.

    ![připojení dev kit](media/speech-devices-sdk/qsg-1.png)

1. Orientovat vývojové sady pro buď zacyklená nebo lineární konfigurace.

    |Konfigurace Development kit|Orientace|
    |-----------------------------|------------|
    |Cyklické|Sloupku s mikrofonů, kterým čelí horní mez|
    |Lineární|Na své straně s mikrofonů směrem k vám (viz následující obrázek)|

    ![orientace lineární dev kit](media/speech-devices-sdk/qsg-2.png)

1. Nainstalujte certifikáty a soubor tabulky probuzení word (klíčové slovo) a nastavit oprávnění zvukové zařízení. V okně příkazového řádku zadejte následující příkazy:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Tyto příkazy používají Android Debug Bridge, `adb.exe`, který je součástí instalace sady Android Studio. Tento nástroj se nachází v C:\Users\[uživatelské jméno] \AppData\Local\Android\Sdk\platform nástroje. Tento adresář můžete přidat do cesty k němu pohodlnější, který má být vyvolán `adb`. V opačném případě musíte zadat úplnou cestu k instalaci adb.exe v každý příkaz, který vyvolá `adb`.

    > [!TIP]
    > Ztlumte mikrofonu a reproduktorů Ujistěte se, že pracujete s mikrofonů development kit v počítači. Díky tomu nebude aktivujete omylem zařízení se zvukem z počítače.

1.  Vysor spusťte na svém počítači.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Vaše zařízení by měl být uvedený **zvolte zařízení**. Vyberte **zobrazení** tlačítko vedle zařízení.

1.  Připojení k bezdrátové síti tak, že vyberete ikonu složky a pak vyberte **nastavení** > **WLAN**.

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
    >
    > Pokud se chcete připojit mluvčího dev Kit, můžete ho připojíte k zvukového výstupu. Měli byste zvolit kvalitní, 3.5 mm mluvčího.
    >
    > ![Vysor zvuku](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Spuštění ukázkové aplikace

Ke spuštění testů ROOBO a ověření nastavení development kit, sestavit a nainstalovat ukázkovou aplikaci:

1.  Spusťte Android Studio.

1.  Vyberte **Open an existing Android Studio project** (Otevřít existující projekt Android Studia).

    ![Android Studio – otevřít existující projekt](media/speech-devices-sdk/qsg-5.png)

1.  Přejdete na C:\SDSDK\Android-Sample-Release\example. Vyberte **OK** Chcete-li spustit příklad projektu.

1.  Přidáte klíč předplatného řeči ke zdrojovému kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace.

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

    Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    Instalaci aplikace Microsoft word probuzení, který chcete použít:

    * Vytvořte složku – klíčové slovo spuštěním následujících příkazů v okně příkazového řádku ve složce data na zařízení:

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Zkopírujte soubory kws.table, kws_k.fst a words_kw.txt do složky \data\keyword zařízení. V okně příkazového řádku spusťte následující příkazy. Pokud jste vytvořili [vlastní probuzení slovo](speech-devices-sdk-create-kws.md), kws.table soubor generovaný z webu je ve stejném adresáři jako soubory kws.table kws_k.fst a words_kw.txt. Pro vlastní probuzení word, použijte `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` příkazu push souboru kws.table dev Kit:

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * Odkazovat na tyto soubory v ukázkové aplikaci. Vyhledejte následující řádky v MainActivity.java. Ujistěte se, že zadané klíčové slovo je ten, že používáte a že cesta odkazuje `kws.table` soubor, který jste odeslali do zařízení.

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Ve svém vlastním kódu můžete použít soubor kws.table pro vytvoření instance modelu – klíčové slovo a rozpoznávání spuštění:
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Aktualizujte následující řádky, které obsahují nastavení geometrie mikrofonu pole:

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    Následující tabulka popisuje dostupné hodnoty:

    |Proměnná|Význam|Dostupné hodnoty|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfigurace fyzického mic|Pro cyklické dev kit: `Circular6+1` |
    |||Lineární dev Kit: `Linear4`|
    |`SelectedGeometry`|Konfigurace softwaru povinná kontrola úrovně důvěryhodnosti|Pro cyklické dev kit, která používá všechny mikrofonů: `Circular6+1`|
    |||Pro cyklické dev kit, která používá čtyři mikrofonů: `Circular3+1`|
    |||Lineární dev Kit, která používá všechny mikrofonů: `Linear4`|
    |||Lineární dev Kit, která používá dva mikrofonů: `Linear2`|


1.  Jak vytvořit aplikaci, na **spustit** nabídce vyberte možnost **spuštění "aplikace"**. **Vyberte cíl nasazení** zobrazí se dialogové okno.

1. Vyberte zařízení a pak vyberte **OK** k nasazení aplikace do zařízení.

    ![Dialogové okno Vyberte cíl nasazení](media/speech-devices-sdk/qsg-7.png)

1.  Ukázková aplikace sadou SDK pro řeč zařízení spustí a zobrazí následující možnosti:

    ![Ukázková aplikace příklad sadou SDK pro řeč zařízení a možnosti](media/speech-devices-sdk/qsg-8.png)

1. Experiment!

## <a name="troubleshooting"></a>Řešení potíží

### <a name="certificate-failures"></a>Selhání certifikátu

Pokud se zobrazí selhání certifikátu, když používáte službu rozpoznávání řeči, ujistěte se, že vaše zařízení má správné datum a čas:

1. Přejděte na **nastavení**. V části **systému**vyberte **datum a čas**.

    ![V části Nastavení vyberte datum a čas](media/speech-devices-sdk/qsg-12.png)

1. Zachovat **automatické datum a čas** zaškrtnutou možnost. V části **vyberte časové pásmo**, vyberte aktuální časové pásmo.

    ![Vyberte datum a časové pásmo možnosti](media/speech-devices-sdk/qsg-13.png)

    Když se zobrazí, že dev kit čas odpovídá času ve vašem počítači, dev kit je připojený k Internetu.

    Další informace o vývoj, naleznete v tématu [Příručka pro vývojáře v ROOBO](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Zvuk

ROOBO poskytuje nástroj, který zachycuje všechny zvuk na flash paměti. Občas může pomoci při odstraňování problémů zvuku. Verze nástroje se poskytuje pro každou konfiguraci development kit. Na [ROOBO lokality](http://ddk.roobo.com/), vyberte zařízení a pak vyberte **ROOBO nástroje** odkaz v dolní části stránky.
