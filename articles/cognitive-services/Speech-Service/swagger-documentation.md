---
title: Dokumentace k Swagger – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Dokumentaci Swagger lze použít k automatickému generování sad SDK pro řadu programovacích jazyků. Swagger podporuje všechny operace v naší službě.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636264"
---
# <a name="swagger-documentation"></a>Dokumentace ke Swaggeru

Služba rozpoznávání řeči nabízí specifikaci Swagger pro interakci s několik rozhraní REST API, která se používají k importu dat, vytváření modelů, přesnosti testovacích modelů, vytváření vlastních koncových bodů, zařazování přepisů dávek a správě předplatných. Většina operací, které jsou dostupné prostřednictvím [Custom Speech oblasti sady Speech Studio,](https://aka.ms/customspeech) se dá pomocí těchto rozhraní API dokončit programově.

> [!NOTE]
> Služba Speech má několik rozhraní REST API pro [Převod řeči na text](rest-speech-to-text.md) a [Převod textu na řeč](rest-text-to-speech.md).  
>
> Ve specifikaci Swagger se ale zdokumentují jenom [Převod řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) a v 2.0. Informace o všech ostatních rozhraních REST API služby Speech Services najdete v dokumentech, na které se odkazuje v předchozím odstavci.

## <a name="generating-code-from-the-swagger-specification"></a>Generování kódu ze specifikace Swagger

[Specifikace Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) obsahuje možnosti, které umožňují rychlé testování různých cest. Někdy je však žádoucí vygenerovat kód pro všechny cesty a vytvořit jedinou knihovnu volání, na kterých můžete založit budoucí řešení. Pojďme se podívat na proces generování knihovny Pythonu.

Musíte nastavit Swagger na oblast svého prostředku řeči. Oblast můžete v části **Přehled** nastavení prostředků rozpoznávání řeči v Azure Portal potvrdit. Úplný seznam podporovaných oblastí je k dispozici [zde](regions.md#speech-to-text).

1. V prohlížeči přejdete na specifikaci Swagger pro vaši [oblast](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Na této stránce klikněte na možnost **definice rozhraní API** a klikněte na **Swagger**. Zkopírujte adresu URL stránky, která se zobrazí.
1. V novém prohlížeči, přejít na [https://editor.swagger.io](https://editor.swagger.io)
1. Klikněte na možnost **soubor**, klikněte na položku **importovat adresu URL**, vložte adresu URL a klikněte na tlačítko **OK**.
1. Klikněte na **vygenerovat klienta** a vyberte **Python**. Klientská knihovna se stáhne do vašeho počítače v `.zip` souboru.
1. Extrahujte vše ze staženého souboru. Můžete použít `tar -xf` k extrakci všeho.
1. Do prostředí Pythonu nainstalujte extrahovaný modul:  
      `pip install path/to/package/python-client`
1. Instalovaný balíček je pojmenován `swagger_client` . Ověřte, že instalace fungovala:  
       `python -c "import swagger_client"`

Můžete použít knihovnu Python, kterou jste vygenerovali pomocí [ukázek služby pro rozpoznávání řeči na GitHubu](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Referenční dokumenty

* [Swagger: Převod řeči na text REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Rozhraní REST API pro převod řeči na text](rest-speech-to-text.md)
* [Rozhraní REST API pro převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

* [Ukázky služby Speech na GitHubu](https://aka.ms/csspeech/samples).
* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
