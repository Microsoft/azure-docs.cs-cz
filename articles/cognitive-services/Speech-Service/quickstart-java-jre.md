---
title: 'Rychlý start: Rozpoznávání řeči, jazyka Java – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Javě (Windows nebo Linux).
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 9b8327a529baf230bc64dd9abcc808924b31293b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603431"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce Java na Windows nebo Linux s použitím sady SDK služby řeči

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci Javy pomocí [sady Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Aplikace je sestavená pomocí balíčku Maven řeči sady SDK a Java IDE Eclipse (v4.8) na Ubuntu Linux 16.04 nebo Windows 64-bit / 18.04. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).


## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

Pokud používáte Ubuntu 16.04 nebo 18.04, před zahájením Eclipse, spusťte následující příkazy, abyste měli jistotu, že jsou nainstalované požadované balíčky.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Spusťte Eclipse.

1. Ve spouštěcím programu Eclipse zadejte do pole **Workspace** (Pracovní prostor) název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. Vytvořte nový projekt tak, že na řádku nabídek Eclipse vyberete **File** (Soubor)  >  **New** (Nový)  >  **Project** (Projekt).

1. Zobrazí se dialogové okno **Nový projekt**. Vyberte **Java Project** (Projekt Javy) a vyberte **Next** (Další).

   ![Snímek obrazovky dialogového okna New Project (Nový projekt) se zvýrazněnou možností Java Project (Projekt Javy)](media/sdk/qs-java-jre-02-select-wizard.png)

1. Spustí se průvodce novým projektem Javy. Do pole **Project name** (Název projektu) zadejte **quickstart** a jako spouštěcí prostředí zvolte **JavaSE-1.8**. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](media/sdk/qs-java-jre-03-create-java-project.png)

1. Pokud se zobrazí okno **Open Associated Perspective?** (Otevřít související perspektivu?), vyberte **Open Perspective** (Otevřít perspektivu).

1. V **průzkumníku balíčků** klikněte pravým tlačítkem myši na projekt **quickstart**. V místní nabídce zvolte **Configure** (Konfigurovat)  >  **Convert to Maven Project** (Převést na projekt Maven).

   ![Snímek obrazovky s průzkumníkem balíčků](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zobrazí se okno **Create new POM** (Vytvořit nový POM). Do pole **Group Id** (ID skupiny) zadejte **com.microsoft.cognitiveservices.speech.samples** a do pole **Artifact Id** (ID artefaktu) zadejte **quickstart**. Pak vyberte **Dokončit**.

   ![Snímek obrazovky s oknem Create new POM (Vytvořit nový POM)](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otevřete soubor **pom.xml** a upravte ho.

   * Na konci souboru před ukončovací značkou `</project>` vytvořte element `repositories` s odkazem na úložiště Maven pro sadu Speech SDK, jak můžete vidět tady:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Také přidat `dependencies` element se sadou SDK pro řeč verzi 1.2.0 jako závislost:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Uložte změny.

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Pokud chcete do projektu Javy přidat novou prázdnou třídu, vyberte **File** (Soubor)  >  **New** (Nový)  >  **Class** (Třída).

1. V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Veškerý kód v `Main.java` nahraďte následujícím fragmentem kódu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).
Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/java-jre`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro Javu](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
