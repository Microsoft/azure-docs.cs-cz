---
title: 'Rychlý start: Rozpoznávání řeči, Java (Windows, Linux) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se naučíte vytvořit jednoduchou aplikaci Java, která zachytává a transcribes uživatele z mikrofonu vašeho počítače.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554104"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK pro Java

K dispozici jsou také rychlé starty pro [Převod řeči](quickstart-translate-speech-java-jre.md) a pro cloudového [prvního virtuálního pomocníka](quickstart-virtual-assistant-java-jre.md).

V případě potřeby vyberte jiný programovací jazyk nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci Javy pomocí [sady Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Aplikace se sestavuje pomocí balíčku Maven sady Speech SDK a Java IDE zatmění (v 4.8) na 64 Windows, 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) nebo v macOS 10,13 nebo novějším. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64-bit Windows, 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) nebo macOS 10,13 nebo novější
* [Java IDE zatmění](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

Pokud používáte systém Linux, ujistěte se, že jsou tyto závislosti nainstalovány před spuštěním služby zatmění.

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

Pokud používáte Windows (64-bit), ujistěte se, že máte nainstalovanou aplikaci C++ Microsoft Visual Redistributable pro vaši platformu.
* [Stažení sady Microsoft C++ Visual Redistributable pro visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

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

## <a name="next-steps"></a>Další postup

Další ukázky, například jak číst řeč ze zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Rychlé zprovoznění: Převod řeči, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
