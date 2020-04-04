---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 1269ff3e21a794fddc2e0f0dc5d62816fa98215b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658707"
---
V tomto rychlém startu se dozvíte, jak pomocí sady SDK pro zařízení pro rozpoznávání řeči pro Systém Windows vytvořit produkt s podporou řeči nebo jej použít jako zařízení [pro přepis konverzace.](../conversation-transcription-service.md) Pro přepis konverzace je podporována pouze [Azure Kinect DK.](https://azure.microsoft.com/services/kinect-dk/) Pro ostatní řeči použití lineární mic pole, které poskytují geometrii pole mikrofonu jsou podporovány.

Aplikace je vytvořena s balíčkem Speech SDK a Eclipse Java IDE (v4) na 64bitovém systému Windows. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Tato příručka vyžaduje účet [Azure Cognitive Services](../get-started.md) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód [ukázkové aplikace](https://aka.ms/sdsdk-download-JRE) je součástí sady SDK pro řečová zařízení. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64bitový systém Windows
* Pole mikrofonu, například [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Pouze Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Klíč předplatného Azure pro službu Řeč. [Získejte jeden zdarma](../get-started.md).
* Stáhněte si nejnovější verzi sady [SDK pro zařízení pro rozpoznávání řeči](https://aka.ms/sdsdk-download-JRE) pro jazyk Java a extrahujte soubor ZIP do pracovního adresáře.
   > [!NOTE]
   > Tento rychlý start předpokládá, že aplikace je extrahována do C:\SDSDK\JRE-Sample-Release

Přepis konverzace je v současné době k dispozici pouze pro "en US" a "zh-CN", v oblastech "centralus" a "eastasia". Chcete-li používat přepis konverzace, musíte mít v jedné z těchto oblastí klíč pro řeč.

Pokud máte v plánu použít záměry, budete potřebovat předplatné [služby language understanding service (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Další informace o službě LUIS a rozpoznávání záměru najdete v [tématu Rozpoznávání záměrů řeči pomocí služby LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). [Ukázkový model LUIS](https://aka.ms/sdsdk-luis) je k dispozici pro tuto aplikaci.

## <a name="create-and-configure-the-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. Do **spouštěče Eclipse IDE**zadejte do pole **Pracovní prostor** název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](../media/speech-devices-sdk/eclipse-launcher.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. Z řádku nabídek Eclipse vytvořte nový projekt výběrem **možnosti Soubor** > **nového** > **projektu Java**. Pokud není k **dispozici,** zvolte Project a potom **Java Project**.

1. Spustí se Průvodce **novým projektem Java.** **Vyhledejte** umístění ukázkového projektu. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. V **průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Z kontextové nabídky zvolte **Konfigurovat** > **převést na projekt Maven.** Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průzkumníkem balíčků](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Otevřete soubor pom.xml a upravte ho.

    Na konci souboru, před uzavírací `</project>`značkou , vytvořte `repositories` a `dependencies` elementy, jak je znázorněno zde, a ujistěte se, že `version` odpovídá aktuální verzi:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Zkopírujte obsah **systému Windows x64** do umístění projektu Java, například **C:\SDSDK\JRE-Sample-Release**

1. Kopírovat `kws.table` `participants.properties` a `Microsoft.CognitiveServices.Speech.extension.pma.dll` do složky projektu **cíl\třídy**

## <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete zkusit záměr uznání, přidejte také klíč předplatného [služby Language Understanding a](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID aplikace.

   Pro řeč a LUIS, `FunctionsList.java`vaše informace přejde do :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Pokud používáte přepis konverzace, je klíč řeči a informace `Cts.java`o oblasti potřebné také v :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Výchozí klíčové slovo (klíčové slovo) je "Počítač". Můžete také vyzkoušet jedno z dalších zadaných klíčových slov, například "Stroj" nebo "Asistent". Soubory prostředků pro tato alternativní klíčová slova jsou v sadě SDK řeči zařízení, ve složce klíčových slov. Obsahuje například `C:\SDSDK\JRE-Sample-Release\keyword\Computer` soubory použité pro klíčové slovo "Počítač".

    > [!TIP]
    > Můžete také [vytvořit vlastní klíčové slovo](../speech-devices-sdk-create-kws.md).

    Chcete-li použít nové klíčové slovo, aktualizujte následující řádek v `FunctionsList.java`aplikaci a zkopírujte klíčové slovo do aplikace. Chcete-li například použít klíčové slovo "Stroj" z balíčku `machine.zip`klíčových slov :

   * Zkopírujte `kws.table` soubor z balíčku zip do **cílové složky projektu nebo tříd .**
   * Aktualizujte `FunctionsList.java` název klíčového slova:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Spuštění ukázkové aplikace z Eclipse

1. Z řádku nabídek Eclipse **spustit** > **jako** > **java aplikaci**. Pak vyberte **FunctionsList** a **OK**.

   ![Snímek obrazovky s vybranou aplikací Java](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Spustí se ukázková aplikace sady Speech Devices SDK a zobrazí následující možnosti:

   ![Ukázka aplikace a možností sady SDK pro řečová zařízení](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Vyzkoušejte novou ukázku **přepisu konverzace.** Začněte přepisovat**pomocí spuštění** **relace** > . Ve výchozím nastavení je hostem každý. Pokud však máte hlasové podpisy účastníka, mohou `participants.properties` být vloženy do souboru v **cílovém nebo třídách**složky projektu . Chcete-li generovat hlasový podpis, podívejte se na [přepis konverzace (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikace pro přepis demo konverzace](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Vytvoření a spuštění samostatné aplikace

1. V **průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **Exportovat**.

1. Zobrazí se okno **Exportovat.** Rozbalte **Jazyk Java** a vyberte **Runnable JAR soubor** a pak vyberte **Další**.

   ![Snímek obrazovky s oknem Export](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Zobrazí se okno **Runnable JAR File Export** . Zvolte **cíl exportu** pro aplikaci a pak vyberte **Dokončit**.

   ![Snímek obrazovky s exportem souboru Runnable JAR](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Vložte `kws.table` `participants.properties`, `unimic_runtime.dll` `pma.dll` , `Microsoft.CognitiveServices.Speech.extension.pma.dll` a do výše vybrané cílové složky, protože tyto soubory jsou aplikací potřebné.

1. Spuštění samostatné aplikace

   ```powershell
   java -jar SpeechDemo.jar
   ```
