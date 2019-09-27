---
title: 'Rychlý start: Převod řeči, C# (.NET Framework Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte .NET Frameworkovou aplikaci pro zaznamenání uživatelského rozpoznávání řeči, překladu na jiný jazyk a výstup textu do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327349"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Převod řeči pomocí sady Speech SDK pro .NET Framework (Windows)

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-dotnet-windows.md) a [funkce pro syntézu řeči](quickstart-text-to-speech-dotnet-windows.md).

V tomto rychlém startu vytvoříte aplikaci .NET Framework, která zachycuje řeč uživatele z mikrofonu vašeho počítače, převede řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace může běžet v 32 nebo 64 bitových oknech a je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019.

Úplný seznam jazyků dostupných pro překlad řeči najdete v tématu [Podpora jazyků](language-support.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs**a v něm nahraďte veškerý kód následujícím kódem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, oblast je `westus`.

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavení** **řešení**  >  pro sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte možnost **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte aplikaci **HelloWorld** .

1. Přemluvte si anglickou frázi nebo větu do mikrofonu vašeho zařízení. Aplikace přenáší váš hlas do služby pro rozpoznávání řeči, která překládá řeč na text v jiném jazyce (v tomto případě je to němčina). Služba Speech odesílá přeložený text zpátky do aplikace, která zobrazuje překlad v okně.

   ![Uživatelské rozhraní překladu řeči](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Další kroky

Na GitHubu jsou k dispozici další ukázky, jako je například čtení řeči ze zvukového souboru a výstup přeloženého textu jako syntetizované řeči.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)
