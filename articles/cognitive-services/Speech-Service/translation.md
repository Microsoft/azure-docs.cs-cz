---
title: Ukázka pro překlad | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Zde je ukázka pro rozpoznávání řeči překlad.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: a02a89de1d05ec5388a92f14a0fd206e0ee966a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343777"
---
# <a name="sample-for-translation"></a>Ukázka pro překlad

> [!NOTE]
> Pokyny ke stažení této ukázce a ostatní uživatele najdete v tématu [ukázky pro rozpoznávání řeči SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Pro všechny ukázky níže následující nejvyšší úrovně deklarace musí být k dispozici:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Překlad pomocí mikrofon

Následující fragment kódu ukazuje, jak přeložte řeči vstup z angličtina, němčina a také získat výstup hlasové přeložený text. Používá mikrofonu.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Překlad pomocí vstupní soubor

Následující fragment kódu ukazuje, jak přeložit řeči vstup z angličtina němčině a francouzštinu.
Soubor používá jako vstup.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Ukázka zdrojového kódu

Nejnovější verze ukázek a i pokročilejší ukázky jsou vyhrazená [úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Další postup

- [Rozpoznávání řeči](./speech-to-text-sample.md)

- [Záměrné rozpoznávání](./intent.md)
