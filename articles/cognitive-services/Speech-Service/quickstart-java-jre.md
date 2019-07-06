---
title: 'Rychlý start: Rozpoznávání řeči, jazyka Java (Windows, Linux) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, k vytvoření jednoduché aplikace v Javě, které zachytí a transcribes řeči uživatele z počítače mikrofonu.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: dce0a1b2adf20b2301402f37307e7ee1284c9aee
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605155"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro Javu

Rychlí průvodci jsou také k dispozici pro [řeči pro zpracování řeči překlad](quickstart-translate-speech-java-jre.md) a [virtuálních asistentů hlasové první](quickstart-virtual-assistant-java-jre.md).

V případě potřeby vyberte jiný programovací jazyk a/nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci Javy pomocí [sady Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Aplikace je sestavená pomocí balíčku Maven řeči sady SDK a Java IDE Eclipse (v4.8) na Windows 64-bit, 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), nebo v systému macOS 10.13 nebo novější. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64bitová verze Windows 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) a macOS 10.13 nebo novější
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

Pokud používáte systém Linux, ujistěte se, že jsou tyto závislosti nainstalovány před zahájením Eclipse.

* On Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Na Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Pokud používáte Windows (64-bit), ujistěte se, nainstalujete Microsoft Visual C++ Redistributable pro vaši platformu.
* [Stáhnout Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

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

Další ukázky, jako je čtení řeči z zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Rychlé zprovoznění: Překlad řeči, jazyka Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
