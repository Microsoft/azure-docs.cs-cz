---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, C++ (Windows) - služba rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznat řeč v jazyce C++ v systému Windows Desktop pomocí sady Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 416c2893ebf07cd638f3f1c06c709db2586245d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925927"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte `YourServiceRegion` řetězec **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené `westus` k vašemu předplatnému (například pro bezplatné zkušební předplatné).

1. Na řádku nabídek zvolte **Soubor** > **Uložit vše**.

> [!NOTE]
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci **Helloworld,** zvolte Ladění**spouštění ladění** (nebo stiskněte **klávesu F5).**

1. Vyslovte anglickou frázi nebo větu. Aplikace přenáší váš projev do služby Speech, která přepisuje na text a odešle jej zpět do aplikace pro zobrazení.

   ![Výstup konzoly po úspěšném rozpoznání](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]