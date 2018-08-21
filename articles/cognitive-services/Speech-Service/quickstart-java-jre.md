---
title: 'Rychlý start: Rozpoznávat řeč v jazyce Java (Windows nebo Linux)'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce Java (Windows nebo Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234319"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Rychlý start: Rozpoznávat řeč v jazyce Java (Windows nebo Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Tento dokument popisuje, jak vytvořit aplikace založené na jazyce Java konzoly pro Java běhové prostředí (prostředí JRE), který využívá sadou SDK pro řeč.
Aplikace je založena na Microsoft Cognitive Services SDK Maven balíček.
Používáme jako integrované vývojové prostředí (IDE) Eclipse.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Počítač (x64 Windows, Ubuntu 16.04 x64) umožňující spusťte Eclipse, s mikrofonem pracovní.
* 64bitové prostředí JRE/JDK pro Javu 8.
* Verze 4.8 [Eclipse](https://www.eclipse.org), 64bitovou verzi.
* Ubuntu 16.04 spusťte následující příkazy pro instalaci požadované balíčky:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Eclipse.

1. Ve Spouštěči Eclipse, zadejte název nového adresáře do **pracovní prostor** pole.
   Pak klikněte na tlačítko **spuštění**.

   ![Vytvoření pracovního prostoru Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Po chvíli zobrazí hlavní okno integrovaného vývojového prostředí Eclipse se.
   Pokud je v něm úvodní obrazovce, zavřete ho.

1. Vyberte **souboru** \> **nové** \> **projektu**.

1. V **nový projekt** průvodce, který se zobrazí, vyberte **projektu v Javě**a klikněte na tlačítko **Další**.

   ![Vyberte průvodce](media/sdk/qs-java-jre-02-select-wizard.png)

1. V dalším okně zadejte **rychlý Start** jako projekt pojmenujte a zvolte **JavaSE 1.8** (nebo novější) jako spouštěcí prostředí.
   Klikněte na **Dokončit**.

   ![Vyberte průvodce](media/sdk/qs-java-jre-03-create-java-project.png)

1. Pokud se okno s názvem **otevřít související perspektivy?** pop up, vyberte **otevřít perspektivy**.

1. V **Průzkumníku balíčků**, klikněte pravým tlačítkem myši **rychlý Start** projektu a vyberte **konfigurovat** \> **převést na projekt Maven s**.

   ![Převést na projekt v Mavenu](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. V okně, která se otevře, zadejte **com.microsoft.cognitiveservices.speech.samples** jako **Id skupiny** a **rychlý Start** jako **Id artefaktu**. Vyberte **Finish** (Dokončit).

   ![Nakonfigurovat Maven POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Upravit **pom.xml** souboru.

  * Na konci souboru, před uzavírací značku `</project>`, vytvořte oddíl úložiště s odkazem na úložiště Maven pro sadou SDK pro řeč:

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Přidejte také později oddílu závislosti se sadou SDK pro řeč verzi 0.6.0 jako závislost:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Uložte změny.

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Vyberte **souboru** \> **nový** \> **třídy** pro přidání nové prázdné třídy do projektu jazyka Java.

1. V okně **nová třída Java** zadejte **speechsdk.quickstart** do **balíčku** pole, a **hlavní** do **název**  pole.

   ![Vytvoření hlavní třídy](media/sdk/qs-java-jre-06-create-main-java.png)

1. Nahraďte veškerý kód v `Main.java` následujícím fragmentem kódu:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Stisknutím klávesy F11 nebo vyberte **spustit** \> **ladění**.
Další 15 sekund hlasový vstup z mikrofonu se bude používat a protokolovány v okně konzoly.

![Výstup na konzole po úspěšné rozpoznávání](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/java-jre` složky.

## <a name="next-steps"></a>Další postup

* [Získat naše ukázky](speech-sdk.md#get-the-samples)
