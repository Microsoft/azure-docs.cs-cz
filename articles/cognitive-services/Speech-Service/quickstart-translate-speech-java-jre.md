---
title: 'Rychlý start: Převod řeči, Java (Windows, Linux) – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci v jazyce Java, která bude zachytávání uživatelského rozpoznávání řeči, jeho překladu do jiného jazyka a výstup textu do příkazového řádku. Tato příručka je určená pro uživatele systému Windows a Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cbb86dd4b24cb325b8ea6708ebc2ffc89a697757
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553392"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Rychlý start: Překlad řeči se sadou SDK pro řeč pro Javu

K dispozici jsou také rychlé starty pro [virtuální asistenty](quickstart-virtual-assistant-java-jre.md) [pro převod řeči na text](quickstart-java-jre.md) a hlas.

V tomto rychlém startu vytvoříte jednoduchou aplikaci v jazyce Java, která zachycuje řeč uživatele z mikrofonu vašeho počítače, převede řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržená tak, aby běžela na 64 Windows nebo 64 systému Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9), nebo v macOS 10,13 nebo novějším. Je sestavená pomocí balíčku Maven sady Speech SDK a Java IDE zatmění.

Úplný seznam jazyků dostupných pro překlad řeči najdete v tématu [Podpora jazyků](language-support.md).

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

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Pokud chcete do projektu Javy přidat novou prázdnou třídu, vyberte **File** (Soubor)  >  **New** (Nový)  >  **Class** (Třída).

1. V okně **New Java Class** (Nová třída Javy) zadejte do pole **Package** (Balíček) **speechsdk.quickstart** a do pole **Name** (Název) zadejte **Main** (Hlavní).

   ![Snímek obrazovky s oknem New Java Class (Nová třída Javy)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Veškerý kód v `Main.java` nahraďte následujícím fragmentem kódu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).

Vstup z mluveného slova z vašeho mikrofonu se přepisu do němčiny a přihlásí v okně konzoly. Zachytávání řeči zastavíte stisknutím klávesy ENTER.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Další postup

Na GitHubu jsou k dispozici další ukázky, jako je například čtení řeči ze zvukového souboru a výstup přeloženého textu jako syntetizované řeči.

> [!div class="nextstepaction"]
> [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Rychlé zprovoznění: Rozpoznávání řeči, Java (Windows, Linux)](quickstart-java-jre.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
