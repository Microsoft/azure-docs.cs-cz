---
title: Dokumentace ke službě – hlasové služby swagger
titleSuffix: Azure Cognitive Services
description: Dokumentace ke službě Swagger lze automaticky generovat sady SDK pro řadu programovacích jazyků. Podporovány jsou všechny operace v naší službě ve Swaggeru
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743209"
---
# <a name="swagger-documentation"></a>Dokumentace ke Swaggeru

Hlasové služby nabízejí specifikace Swagger pro interakci s několika rozhraní REST API používá k umožňuje importovat data, vytvářet modely, testování přesnosti modelu, vytvořit vlastní koncové body, fronty batch přepisů a spravovat předplatná. Většinu operací, které jsou k dispozici prostřednictvím portálu pro Custom Speech je možné provést prostřednictvím kódu programu pomocí těchto rozhraní API. 

> [!NOTE]
> Převod řeči na Text a převod textu na řeč operace jsou podporovány dostupná jako rozhraní REST API, která zase popsané ve specifikaci Swaggeru.

## <a name="generating-code-from-the-swagger-specification"></a>Generování kódu ze specifikace Swagger

[Vytvořené specifikace Swaggeru](https://cris.ai/swagger/ui/index) obsahuje možnosti, které umožňují rychle otestovat u různých cest. Někdy je však vhodné pro generování kódu pro všechny cesty, vytváření knihovny jednoho volání, které můžete založit na budoucí řešení. Pojďme se podívat na proces generovat knihovnu Pythonu.

Budete muset nastavit Swagger do stejné oblasti jako vaše předplatné Speech Service. V rámci vašeho prostředku hlasové služby můžete ověřit oblast ve na webu Azure portal. Úplný seznam podporovaných oblastí najdete v tématu [oblastech](regions.md).

1. Přejděte na https://editor.swagger.io.
2. Klikněte na tlačítko **souboru**, pak klikněte na tlačítko **importu**
3. Zadejte adresu URL swaggeru, včetně oblastí pro vaše předplatné hlasové služby `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Klikněte na tlačítko **generovat klientské** a vyberte Pythonu
5. Uložit klientské knihovny

Můžete použít knihovnu Pythonu, který jste vygenerovali pomocí [hlasové služby ukázky na Githubu](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referenční dokumenty

* [REST (Swagger): Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)

## <a name="next-steps"></a>Další postup

* [Ukázky služby řeči na Githubu](https://aka.ms/csspeech/samples).
* [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
