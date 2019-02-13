---
title: 'Rychlý start: Překlad řeči, jazyka Java (Windows, Linux) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci Java zaznamenat uživatelské řeči, přeloží ji do jiného jazyka a panelem text do příkazového řádku. Tato příručka je určená pro uživatele Windows a Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 23582e25a7695f4573863b77b83d73408e77ac4e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105633"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Rychlý start: Překlad řeči se sadou SDK pro řeč pro Javu

V tomto rychlém startu vytvoříte jednoduchou aplikaci Java, která zachycuje uživatele řeči z vašeho počítače mikrofonu, přeloží řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržen pro spouštění na Windows 64-bit nebo 16.04/18.04 64-bit Ubuntu Linux a vytvořených pomocí balíčku Maven řeči sady SDK a Eclipse Java IDE.

Úplný seznam jazyků, které jsou k dispozici pro překlad řeči, naleznete v tématu [jazykovou podporu](language-support.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Operační systém: 64bitová verze Windows nebo Ubuntu Linux 64-bit 16.04/18.04
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

Pokud používáte Ubuntu 16.04/18.04, ujistěte se, že jsou tyto závislosti nainstalovány před zahájením Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
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

Řeči vstup z mikrofon se převede do němčiny a protokolovány v okně konzoly. Stisknutím klávesy "Enter" zastavit zachytávání řeči.

![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči ze zvukových souboru a výstup přeložený text jako řečového, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Rychlé zprovoznění: Rozpoznávání řeči, jazyka Java (Windows, Linux)](quickstart-java-jre.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
