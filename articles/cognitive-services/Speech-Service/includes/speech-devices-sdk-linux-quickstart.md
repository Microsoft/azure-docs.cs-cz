---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 81c77b2f6ae0c4f8497716c168a937657ceb57dd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78384054"
---
V tomto rychlém startu se dozvíte, jak pomocí sady SDK pro zařízení pro rozpoznávání řeči pro Linux vytvořit produkt s podporou řeči nebo jej použít jako zařízení [pro přepis konverzace.](../conversation-transcription-service.md) V současné době je podporována jenom [Azure Kinect DK.](https://azure.microsoft.com/services/kinect-dk/)

Aplikace je postavena s balíčkem Speech SDK a Eclipse Java IDE (v4) na 64bitovém Linuxu (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Tato příručka vyžaduje účet [Azure Cognitive Services](../get-started.md) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

Zdrojový kód [ukázkové aplikace](https://aka.ms/sdsdk-download-JRE) je součástí sady SDK pro řečová zařízení. Je také [k dispozici na GitHubu](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64bitový Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Pouze Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného Azure pro službu Řeč. [Získejte jeden zdarma](../get-started.md).
* Stáhněte si nejnovější verzi sady [SDK pro zařízení pro rozpoznávání řeči](https://aka.ms/sdsdk-download-JRE) pro jazyk Java a extrahujte soubor ZIP do pracovního adresáře.
   > [!NOTE]
   > Tento rychlý start předpokládá, že aplikace je extrahována do /home/wcaltest/JRE-Sample-Release

Ujistěte se, že tyto závislosti jsou nainstalovány před spuštěním Eclipse.

* Na Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Na Debianu 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Na rhel/centos 8:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> Na RHEL/CentOS 8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Přepis konverzace je v současné době k dispozici pouze pro "en US" a "zh-CN", v oblastech "centralus" a "eastasia". Chcete-li používat přepis konverzace, musíte mít v jedné z těchto oblastí klíč pro řeč.

Pokud máte v plánu použít záměry, budete potřebovat předplatné [služby language understanding service (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Další informace o službě LUIS a rozpoznávání záměru najdete v [tématu Rozpoznávání záměrů řeči pomocí služby LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). [Ukázkový model LUIS](https://aka.ms/sdsdk-luis) je k dispozici pro tuto aplikaci.

## <a name="create-and-configure-the-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. Do **spouštěče Eclipse IDE**zadejte do pole **Pracovní prostor** název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. Z řádku nabídek Eclipse vytvořte nový projekt výběrem **možnosti Soubor** > **nového** > **projektu Java**. Pokud není k **dispozici,** zvolte Project a potom **Java Project**.

1. Spustí se Průvodce **novým projektem Java.** **Vyhledejte** umístění ukázkového projektu. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

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
             <version>1.10.0</version>
        </dependency>
    </dependencies>
   ```

1. V **průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **Vlastnosti**, potom **spustit/ladit nastavení** > **nové...** > **Java aplikace**. 

1. Zobrazí se okno **Upravit konfiguraci.** Do pole **Název** zadejte **hlavní**položku a pomocí funkce **Hledat** **hlavní třídu** vyhledejte a vyberte **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Snímek obrazovky s úpravou konfigurace spuštění](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Zkopírujte zvukové binární soubory pro cílovou architekturu, z **linuxové nebo** **linuxové x64**, do umístění projektu Java, **např.**

1. Také z okna **Upravit konfiguraci** vyberte stránku **Prostředí** a **Nový**. Zobrazí se okno **Nová proměnná prostředí.** Do pole **Název** zadejte **LD_LIBRARY_PATH** a do pole **hodnoty** zadejte složku obsahující soubory *.so, například **/home/wcaltest/JRE-Sample-Release**

1. Kopírování `kws.table` `participants.properties` a do **cíle/tříd** složky projektu


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

1. Výchozí klíčové slovo (klíčové slovo) je "Počítač". Můžete také vyzkoušet jedno z dalších zadaných klíčových slov, například "Stroj" nebo "Asistent". Soubory prostředků pro tato alternativní klíčová slova jsou v sadě SDK řeči zařízení, ve složce klíčových slov. Obsahuje například `/home/wcaltest/JRE-Sample-Release/keyword/Computer` soubory použité pro klíčové slovo "Počítač".

   > [!TIP]
   > Můžete také [vytvořit vlastní klíčové slovo](../speech-devices-sdk-create-kws.md).

    Chcete-li použít nové klíčové slovo, aktualizujte následující řádek v `FunctionsList.java`aplikaci a zkopírujte klíčové slovo do aplikace. Chcete-li například použít klíčové slovo "Stroj" z balíčku `machine.zip`klíčových slov :

   * Zkopírujte `kws.table` soubor z balíčku zip do **cílové složky projektu nebo tříd .**

   * Aktualizujte `FunctionsList.java` název klíčového slova:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Spuštění ukázkové aplikace z Eclipse

1. Z řádku nabídek Eclipse **spustit** > **spustit** 

1. Spustí se ukázková aplikace sady Speech Devices SDK a zobrazí následující možnosti:

   ![Ukázka aplikace a možností sady SDK pro řečová zařízení](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Vyzkoušejte novou ukázku **přepisu konverzace.** Začněte přepisovat**pomocí spuštění** **relace** > . Ve výchozím nastavení je hostem každý. Pokud však máte hlasové podpisy účastníka, `participants.properties` mohou být vloženy do **cílové nebo třídy**složky projektu . Chcete-li generovat hlasový podpis, podívejte se na [přepis konverzace (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplikace pro přepis demo konverzace](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Vytvoření a samostatné spuštění aplikace

1. V **průzkumníku balíčků**klikněte pravým tlačítkem myši na projekt. Zvolte **Exportovat**. 
1. Zobrazí se okno **Exportovat.** Rozbalte **Jazyk Java** a vyberte **Runnable JAR soubor** a pak vyberte **Další**.

   ![Snímek obrazovky s oknem Export](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Zobrazí se okno **Runnable JAR File Export** . Zvolte **cíl exportu** pro aplikaci a pak vyberte **Dokončit**.
 
   ![Snímek obrazovky s exportem souboru Runnable JAR](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Prosím, `kws.table` `participants.properties` vložte a v cílové složce zvolené výše, protože tyto soubory jsou potřebné pro aplikaci.

1. Nastavení LD_LIBRARY_LIB do složky obsahující soubory *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Spuštění samostatné aplikace

     ```bash
     java -jar SpeechDemo.jar
     ```
