---
title: 'Rychlý start: Spusťte sadu Speech Devices SDK pro Linux-Speech Service'
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení práce se sadou SDK pro zařízení se systémem Linux Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: 3ee39f4cf0c78850b0128fc5e497f2b2aee57bbc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559077"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Rychlý start: Spuštění ukázkové aplikace sady Speech Apps SDK v systému Linux

V tomto rychlém startu se dozvíte, jak pomocí sady Speech Devices SDK pro Linux vytvořit produkt s podporou řeči nebo ho použít jako zařízení [přepisující konverzaci](conversation-transcription-service.md) . V současné době je podporována pouze služba [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Aplikace je sestavena pomocí balíčku sady Speech SDK a Java IDE v 64 (v4) na-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Tato příručka vyžaduje účet [Azure Cognitive Services](get-started.md) s prostředkem služeb Speech Services. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód [ukázkové aplikace](https://aka.ms/sdsdk-download-JRE) je součástí sady Speech Devices SDK. Je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE zatmění](https://www.eclipse.org/downloads/)
* Jenom [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).
* Stáhněte si nejnovější verzi [sady Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) pro jazyk Java a extrahujte soubor. zip do pracovního adresáře.
   > [!NOTE]
   > Soubor JRE-Sample-Release. zip obsahuje ukázkovou aplikaci JRE a v tomto rychlém startu se předpokládá, že se aplikace extrahuje do/home/wcaltest/JRE-Sample-Release

Před spuštěním zatmění se ujistěte, že jsou tyto závislosti nainstalované.

* V Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* V Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Přepis konverzace je v současné době dostupný pouze pro "en-US" a "zh-CN" v oblastech "centralus" a "eastasia". Pokud chcete použít přepis konverzace, musíte mít v jedné z těchto oblastí klíč řeči.

Pokud plánujete použít záměry, budete potřebovat předplatné [služby Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Další informace o LUIS a rozpoznávání záměrů najdete v tématu [rozpoznávání hlasových záměrů pomocí C#Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Pro tuto aplikaci je k dispozici [Vzorový model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. Ve **Spouštěči s IDE pro zatmění**zadejte do pole **pracovní prostor** název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. V panelu nabídek zatmění vytvořte nový projekt kliknutím na **soubor** > **Nový** > **projekt Java**. Pokud není k dispozici, zvolte **projekt** a pak **projekt Java**.

1. Spustí se Průvodce vytvořením **nového projektu Java** . **Vyhledejte** umístění ukázkového projektu. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. V místní nabídce zvolte **Configure** (Konfigurovat)  >  **Convert to Maven Project** (Převést na projekt Maven). Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průzkumníkem balíčků](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **vlastnosti**a pak **Spustit/ladit nastavení** > **nové...** > **Aplikace Java**. 

1. Zobrazí se okno **Upravit konfiguraci** . Do pole **název** zadejte **Main (hlavní**) a pomocí **hledání** **hlavní třídy** Najděte a vyberte **com. Microsoft. cognitiveservices Account. Speech. Samples. FunctionsList**.

   ![Snímek obrazovky s konfigurací spuštění úprav](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Také z okna **Upravit konfiguraci** vyberte stránku **prostředí** a **Nový**. Zobrazí se okno **Nová proměnná prostředí** . Do pole **název** zadejte **LD_LIBRARY_PATH** a do pole **hodnota** zadejte složku, která obsahuje soubory *. so, například **/Home/wcaltest/JRE-Sample-Release** .

1. Kopírovat `kws.table` a`participants.properties` do složky **cíle/třídy** projektu


## <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace.

   V případě řeči a LUIS se vaše informace `FunctionsList.java`nacházejí v těchto případech:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Pokud používáte přepis konverzace, informace o vašich klíčích a oblastech řeči jsou také potřeba v `Cts.java`nástroji:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Výchozí probuzení word (klíčové slovo) je "Počítač". Můžete také zkusit jednu z nich k dispozici funkce slova, třeba "Počítač" nebo "Assistant". Soubory prostředků pro tato slova alternativní probuzení se v zařízení sadou SDK pro řeč, ve složce – klíčové slovo. `/home/wcaltest/JRE-Sample-Release/keyword/Computer` Obsahuje například soubory používané pro slovo "počítač" Wake.

   > [!TIP]
   > Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    Chcete-li použít nové slovo Wake, aktualizujte následující dva řádky `FunctionsList.java`v a zkopírujte balíček aplikace Word pro probuzení do aplikace. Chcete-li například použít slovo "počítač" z balíčku `kws-machine.zip`funkce Wake Word:

   * Zkopírujte balíček aplikace Word pro probuzení do složky **cíl/třídy**projektu.

   * Aktualizujte klíčové slovo pomocí klíčového slova a názvu balíčku: `FunctionsList.java`

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Spuštění ukázkové aplikace z zatmění

1. V řádku nabídek zatmění **Spusťte** > příkaz**Run** . 

1. Ukázková aplikace sadou SDK pro řeč zařízení spustí a zobrazí následující možnosti:

   ![Ukázková aplikace příklad sadou SDK pro řeč zařízení a možnosti](media/speech-devices-sdk/java-sample-app-linux.png)

1. Vyzkoušejte si novou ukázku **přepisu konverzace** . Spusťte zdlouhavého přepisování s zahájením **relace** > . Ve výchozím nastavení je každý host. Nicméně pokud máte signatury hlasu účastníka, mohou být umístěny do `participants.properties` složky **cíl/třídy**projektu. Pokud chcete vygenerovat hlasový podpis, podívejte se na [konverzace přepisovat (SDK)](how-to-use-conversation-transcription-service.md).

   ![Ukázková aplikace přepisu konverzace](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Vytvoření a spuštění samostatné aplikace

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **exportovat**. 
1. Zobrazí se okno **exportovat** . Rozbalte **Java** a vyberte **soubor JAR spustitelný** a pak vyberte **Další**.

   ![Snímek obrazovky okna exportu](media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zobrazí se okno pro **Export souboru jar spustitelný** . Zvolte **cíl exportu** pro aplikaci a pak vyberte **Dokončit**.
 
   ![Snímek obrazovky spustitelný pro export souborů JAR](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Vložte `kws.table` a`participants.properties` do cílové složky zvolené výše jako soubory, které aplikace potřebuje.

1. Nastavte LD_LIBRARY_LIB na složku obsahující soubory *. so.

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Spuštění samostatné aplikace

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
