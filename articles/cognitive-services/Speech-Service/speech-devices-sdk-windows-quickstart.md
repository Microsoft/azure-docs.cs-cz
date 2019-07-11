---
title: 'Rychlý start: Spuštění zařízení řeči SDK ve Windows – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny, jak začít na sadu SDK Windows řeči zařízení.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723381"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Rychlý start: Spuštění ukázkové aplikace sadou SDK pro řeč zařízení na Windows

V tomto rychlém startu se dozvíte postupy řeč zařízení sady SDK pro Windows můžete vytvářet produkt podporou řeči nebo jej použít jako [konverzace určené k transkripci](conversation-transcription-service.md) zařízení. Momentálně se podporuje jenom [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) je podporována.

Aplikace je sestavená s balíček sadou SDK pro řeč a Java IDE Eclipse (v4) na Windows 64-bit. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Tato příručka vyžaduje [Azure Cognitive Services](get-started.md) účet s prostředkem hlasové služby. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód [ukázkovou aplikaci](https://aka.ms/sdsdk-download-JRE) je součástí sady SDK zařízení pro rozpoznávání řeči. Je také [k dispozici na Githubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64bitová verze Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) pouze.
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Stáhněte si nejnovější verzi [sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-download-JRE) pro Javu a extrahování ZIP do pracovního adresáře.
   > [!NOTE]
   > Soubor prostředí JRE. ukázka Release.zip obsahuje ukázkovou aplikaci v prostředí JRE a v tomto rychlém startu se předpokládá, že aplikace je extrahován do C:\SDSDK\JRE-Sample-Release

Přepis konverzace je momentálně dostupný jenom pro "en US" a "zh-CN", v oblasti "centralus" a "východní Asie". V těchto oblastech použít určené k transkripci konverzace, musí mít klíč řeči.

Pokud plánujete používat příkazů bude nutné [služby Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) předplatného. Další informace o LUIS a rozpoznání záměru, naleznete v tématu [rozpoznávání řeči záměry služba luis C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). A [ukázkový LUIS model](https://aka.ms/sdsdk-luis) je k dispozici pro tuto aplikaci.

## <a name="create-and-configure-the-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. V **Spouštěč integrovaného vývojového prostředí Eclipse**v **pracovní prostor** pole, zadejte název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](media/speech-devices-sdk/eclipse-launcher.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. Na panelu nabídek Eclipse vytvořte nový projekt výběrem **souboru** > **nový** > **projektu v Javě**. Pokud není k dispozici tlačítko **projektu** a potom **projektu v Javě**.

1. **Nového projektu v Javě** spustí se průvodce. **Procházet** pro umístění ukázkového projektu. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](media/speech-devices-sdk/eclipse-new-java-project.png)

1. V **Průzkumníku balíčků**, klikněte pravým tlačítkem na projekt. V místní nabídce zvolte **Configure** (Konfigurovat)  >  **Convert to Maven Project** (Převést na projekt Maven). Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průzkumníkem balíčků](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Kopírování `kws.table`, `participants.properties` a `Microsoft.CognitiveServices.Speech.extension.pma.dll` do složky projektu **target\classes**

## <a name="configure-the-sample-application"></a>Nakonfigurovat ukázkovou aplikaci

1. Přidáte klíč předplatného řeči ke zdrojovému kódu. Pokud chcete vyzkoušet rozpoznání záměru, přidejte také vaše [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klíč předplatného a aplikace.

   Pro zpracování řeči a LUIS, vaše údaje začnou `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Používáte konverzace určené k transkripci řeči klíč a oblasti informace jsou také potřebná v `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Výchozí probuzení word (klíčové slovo) je "Počítač". Můžete také zkusit jednu z nich k dispozici funkce slova, třeba "Počítač" nebo "Assistant". Soubory prostředků pro tato slova alternativní probuzení se v zařízení sadou SDK pro řeč, ve složce – klíčové slovo. Například `C:\SDSDK\JRE-Sample-Release\keyword\Computer` obsahuje soubory používané pro probuzení slovo "Počítač".

   > [!TIP]
   > Můžete také [vytvořit vlastní probuzení slovo](speech-devices-sdk-create-kws.md).

    Pokud chcete používat nové probuzení slovo, aktualizujte následující dva řádky v `FunctionsList.java`a zkopírujte balíček aplikace word probuzení do vaší aplikace. Například použijte probuzení slovo 'Počítače' z balíčku aplikace word probuzení `kws-machine.zip`:

   * Zkopírujte balíček aplikace word probuzení do složky projektu **cílové/třídy**.

   * Aktualizace `FunctionsList.java` pomocí klíčového slova a název balíčku:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Spuštění ukázkové aplikace z nástroje Eclipse

1. Na panelu nabídek Eclipse **spustit** > **spustit jako** > **aplikace v Javě**. Potom vyberte **FunctionsList** a **OK**.

   ![Snímek obrazovky aplikace vyberte v Javě](media/speech-devices-sdk/eclipse-run-sample.png)

1. Ukázková aplikace sadou SDK pro řeč zařízení spustí a zobrazí následující možnosti:

   ![Ukázková aplikace příklad sadou SDK pro řeč zařízení a možnosti](media/speech-devices-sdk/java-sample-app-windows.png)

1. Vyzkoušejte novou **konverzace určené k transkripci** ukázku. Spustit zdlouhavého přepisování pořídili s **relace** > **Start**. Ve výchozím nastavení všichni uživatelé jsou hosta. Nicméně, pokud máte podpisů hlasové účastníka, můžou být přepnuté do souboru `participants.properties` ve složce projektu **cílové/třídy**. Na vygenerovat signaturu hlasové, podívejte se na [přepisy konverzace (SDK)](how-to-use-conversation-transcription-service.md).

   ![Ukázkové aplikace určené k transkripci konverzace](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Vytvoření a spuštění samostatné aplikace

1. V **Průzkumníku balíčků**, klikněte pravým tlačítkem na projekt. Zvolte **exportovat**. 

1. **Exportovat** zobrazí se okno. Rozbalte **Java** a vyberte **JAR spustitelný soubor** a pak vyberte **Další**.

   ![Snímek obrazovky okna exportu](media/speech-devices-sdk/eclipse-export-windows.png) 

1. **Spustitelných JAR soubor exportu** zobrazí se okno. Zvolte **cíl exportu** pro aplikaci a pak vyberte **Dokončit**.
 
   ![Snímek obrazovky s pro spustitelný soubor JAR souboru exportu](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Prosím vložte `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` a `Microsoft.CognitiveServices.Speech.extension.pma.dll` v cílové složce zvoleno výše, protože tyto soubory jsou potřebné pro aplikaci.

1. Ke spuštění samostatné aplikace

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
