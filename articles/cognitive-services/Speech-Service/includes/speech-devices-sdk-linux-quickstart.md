---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 8c0aef1817581e2b09297d8807676cf0049e1d3e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92169064"
---
V tomto rychlém startu se dozvíte, jak pomocí sady Speech Devices SDK pro Linux vytvořit produkt s podporou řeči nebo ho použít jako zařízení [přepisující konverzaci](../conversation-transcription-service.md) . V současné době je podporována pouze služba [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE na 64 (v4) na. Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Tato příručka vyžaduje účet [Azure Cognitive Services](../get-started.md) s prostředkem služby Speech. 

Zdrojový kód [ukázkové aplikace](https://aka.ms/sdsdk-download-JRE) je součástí sady Speech Devices SDK. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* Operační systém: 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE zatmění](https://www.eclipse.org/downloads/)
* Jenom [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](../get-started.md).
* Stáhněte si nejnovější verzi [sady Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) pro jazyk Java a extrahujte soubor. zip do pracovního adresáře.
   > [!NOTE]
   > V tomto rychlém startu se předpokládá, že se aplikace extrahuje do/home/wcaltest/JRE-Sample-Release.

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

* V RHEL/CentOS:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Přepis konverzace je v současné době dostupný pouze pro "en-US" a "zh-CN" v oblastech "centralus" a "eastasia". Pokud chcete použít přepis konverzace, musíte mít v jedné z těchto oblastí klíč řeči.

Pokud plánujete použít záměry, budete potřebovat předplatné [služby Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Další informace o LUIS a rozpoznávání záměrů najdete v tématu [rozpoznávání hlasových záměrů pomocí Luis, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Pro tuto aplikaci je k dispozici [Vzorový model Luis](https://aka.ms/sdsdk-luis) .

## <a name="create-and-configure-the-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. Ve **Spouštěči s IDE pro zatmění**zadejte do pole **pracovní prostor** název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky zobrazující spouštěč zatmění](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. V panelu nabídek zatmění vytvořte nový projekt kliknutím na **soubor**  >  **Nový**  >  **projekt Java**. Pokud není k dispozici, zvolte **projekt** a pak **projekt Java**.

1. Spustí se Průvodce vytvořením **nového projektu Java** . **Vyhledejte** umístění ukázkového projektu. Vyberte **Dokončit**.

   ![Snímek obrazovky s průvodcem novým projektem Javy](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. V místní nabídce vyberte **Konfigurace**  >  **převést na projekt Maven** . Vyberte **Dokončit**.

   ![Snímek obrazovky s průzkumníkem balíčků](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Otevřete soubor pom.xml a upravte ho.

    Na konci souboru před uzavírací značkou `</project>` vytvořte `repositories` a `dependencies` prvky, jak je znázorněno zde, a ujistěte se, že `version` odpovídá aktuální verzi:
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
             <version>1.13.0</version>
        </dependency>
    </dependencies>
   ```

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **vlastnosti**a pak **Spustit/ladit nastavení**  >  **nové...** > **Aplikace Java**. 

1. Zobrazí se okno **Upravit konfiguraci** . Do pole **název** zadejte **Main (hlavní**) a pomocí **hledání** **hlavní třídy** Najděte a vyberte **com. Microsoft. cognitiveservices Account. Speech. Samples. FunctionsList**.

   ![Snímek obrazovky s konfigurací spuštění úprav](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Zkopírujte zvukové binární soubory pro cílovou architekturu, od **Linux – ARM** nebo **Linux-x64**, do umístění projektu Java, např. **/Home/wcaltest/JRE-Sample-Release**

1. Také z okna **Upravit konfiguraci** vyberte stránku **prostředí** a **Nový**. Zobrazí se okno **Nová proměnná prostředí** . Do pole **název** zadejte **LD_LIBRARY_PATH** a do pole **hodnota** zadejte složku obsahující soubory *. so, například **/Home/wcaltest/JRE-Sample-Release**

1. Kopírovat `kws.table` a `participants.properties` do složky **cíle/třídy** projektu


## <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

1. Přidejte klíč předplatného řeči do zdrojového kódu. Pokud chcete vyzkoušet rozpoznávání záměru, přidejte také klíč předplatného [služby Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) a ID aplikace.

   V případě řeči a LUIS se vaše informace nacházejí v těchto případech `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Pokud používáte přepis konverzace, informace o vašich klíčích a oblastech řeči jsou také potřeba v nástroji `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Výchozí klíčové slovo (klíčové slovo) je "Computer". Můžete také vyzkoušet jedno z dalších poskytnutých klíčových slov, například "počítač" nebo "asistent". Soubory prostředků pro tato alternativní klíčová slova jsou v sadě Speech Devices SDK ve složce klíčová slova. `/home/wcaltest/JRE-Sample-Release/keyword/Computer`Obsahuje například soubory používané pro klíčové slovo "Computer".

   > [!TIP]
   > Můžete také [vytvořit vlastní klíčové slovo](../speech-devices-sdk-create-kws.md).

    Chcete-li použít nové klíčové slovo, aktualizujte následující řádek v `FunctionsList.java` a zkopírujte klíčové slovo do aplikace. Například pro použití klíčového slova ' Machine ' z balíčku klíčového slova `machine.zip` :

   * Zkopírujte `kws.table` soubor z balíčku zip do složky **cíl/třídy**projektu.

   * Aktualizujte `FunctionsList.java` název klíčového slova:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Spuštění ukázkové aplikace z zatmění

1. V řádku nabídek zatmění spusťte příkaz **Run**  >  **Run** . 

1. Spustí se ukázka aplikace Speech Devices SDK a zobrazí následující možnosti:

   ![Snímek obrazovky, který zobrazuje sadu Speech pro zařízení s ukázkami, aplikace a možnosti](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Vyzkoušejte si novou ukázku **přepisu konverzace** . Spusťte zdlouhavého přepisování s **Session**  >  **zahájením**relace. Ve výchozím nastavení je každý host. Nicméně pokud máte signatury hlasu účastníka, mohou být umístěny do `participants.properties` složky **cíl/třídy**projektu. Pokud chcete vygenerovat hlasový podpis, podívejte se na [konverzace přepisovat (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Snímek obrazovky, který zobrazuje ukázkovou aplikaci přepisující konverzaci](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Vytvoření a spuštění samostatné aplikace

1. V **Průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Vyberte **exportovat**. 
1. Zobrazí se okno **exportovat** . Rozbalte **Java** a vyberte **soubor JAR spustitelný** a pak vyberte **Další**.

   ![Snímek obrazovky zobrazující okno exportu](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zobrazí se okno pro **Export souboru jar spustitelný** . Zvolte **cíl exportu** pro aplikaci a pak vyberte **Dokončit**.
 
   ![Snímek obrazovky zobrazující okno pro export souboru JAR spustitelný](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Vložte `kws.table` a `participants.properties` do cílové složky zvolené výše jako soubory, které aplikace potřebuje.

1. Nastavte LD_LIBRARY_LIB do složky obsahující soubory *. so.

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Spuštění samostatné aplikace

     ```bash
     java -jar SpeechDemo.jar
     ```
