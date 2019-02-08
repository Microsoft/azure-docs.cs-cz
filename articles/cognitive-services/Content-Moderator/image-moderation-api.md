---
title: Obrázek moderování – Content Moderator
titlesuffix: Azure Cognitive Services
description: Pomocí Content Moderator moderování obrázků s podporou počítače a nástroj pro recenze lidských v the smyčky pro moderování obrázků pro dospělé nebo pikantního obsahu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1dba848be0c24ef26dcefd23d4fc3f7201420449
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873508"
---
# <a name="learn-image-moderation-concepts"></a>Další koncepty moderování obrázků

Použít moderování obrázků s podporou počítače Content Moderator a [nástroj pro recenze lidských v the smyčky](Review-Tool-User-Guide/human-in-the-loop.md) pro moderování obrázků pro dospělé nebo pikantního obsahu. Kontroly imagí pro textový obsah a extrahovat text a rozpoznávání tváří. Můžete odpovídat bitové kopie na základě vlastních seznamů a provádět další akci.

## <a name="evaluating-for-adult-and-racy-content"></a>Vyhodnocení pro obsahu pro dospělé nebo pikantního obsahu

**Vyhodnotit** operace vrátí skóre spolehlivosti mezi 0 a 1. Také vrátí boolean – datový rovna true nebo false. Tyto hodnoty předpovědět, zda bitová kopie obsahuje potenciální obsah pro dospělé nebo pikantního. Při volání rozhraní API pomocí bitové kopie (soubor nebo adresa URL) vrácená odpověď obsahuje následující informace:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]

> - `isImageAdultClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně explicitní nebo pouze pro dospělé.
> - `isImageRacyClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně sugestivní nebo pouze pro dospělé.
> - Skóre jsou v rozmezí od 0 do 1. Čím více bodů vyšší model je predikce kategorii lze použít. V této verzi preview spoléhá na statistických modelů a nikoli ručně kódovaný výsledků. Doporučujeme ve Visual Basicu s vlastní obsah k určení, jak každou kategorii odpovídá vašim požadavkům.
> - Logické hodnoty jsou true nebo false, v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, zda chcete použít tuto hodnotu nebo při rozhodování o vlastní prahové hodnoty na základě jejich obsahu zásad.
>

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Rozpoznání textu s optického rozpoznávání znaků (OCR)

**Optického rozpoznávání znaků (OCR)** operace předpovídá přítomnost textový obsah v obrázku a extrahuje pro moderování textu, mimo jiné účely. Můžete určit jazyk. Pokud jazyk není zadán, výchozí hodnota je detekce na angličtinu.

Odpověď obsahuje následující informace:
- Původní text.
- Zjištěné textových prvků s jejich skóre spolehlivosti.

Příklad výpisu:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Rozpoznávání tváří

Rozpoznání tváře pomáhá rozpoznat identifikovatelné osobní údaje (PII) například tváří na obrázcích. Zjistit potenciální tváří a počet potenciálních tváří v každé image.

Odpověď obsahuje tyto informace:

- Počet tváří
- Seznam umístění zjistil tváří

Příklad výpisu:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Vytváření a správě vlastních seznamů

V mnoha online komunit po uživatelé odesílat obrázky nebo jiný typ obsahu, urážlivé položek může získat sdílené více než jednou průběhu následujících dnů, týdnů a měsíců. Ceny za opakovaného vyhledávání a filtrování byť jen mírně upravenou verzí bitové kopie z více míst pro stejnou bitovou kopii nebo může být náročné a náchylné k chybě.

Místo moderování více než jednou stejnou bitovou kopii, přidat urážlivé bitové kopie na vlastní seznam blokovaných obsah. Tímto způsobem, moderování obsahu systému porovná příchozí bitové kopie na základě vlastních seznamů a zastaví další zpracování.

> [!NOTE]
> Limit je maximálně **5 seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Content Moderator poskytuje kompletní [rozhraní API pro správu seznamu obrázků](try-image-list-api.md) s operacemi správy seznam vlastních imagí. Začněte [Image obsahuje rozhraní API konzoly](try-image-list-api.md) a používat rozhraní REST API ukázky kódu. Podívejte se také [rychlý úvod k .NET seznam obrázků](image-lists-quickstart-dotnet.md) Pokud jste se seznámili s Visual Studio a C#.

## <a name="matching-against-your-custom-lists"></a>Přiřazování na základě vlastních seznamů

Operace shody umožňuje přibližné shody příchozí bitové kopie pro některý z vlastních seznamů, vytváří a spravují pomocí operace výpisu.

Pokud se najde shoda, vrátí operaci značky moderování odpovídající Image a identifikátor. Odpověď obsahuje tyto informace:

- Skóre shodu (mezi 0 a 1)
- Odpovídající image
- Obrázek značky (přiřazené během předchozí moderování)
- Obrázek popisky

Příklad výpisu:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Nástroj pro kontrolu prováděnou lidmi

Další odlišování případů použití Content Moderator [nástroji pro kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) a jeho rozhraní API k poskytování výsledky při moderování a obsah revize pro lidské moderátory. Zkontrolovat počítač přiřazený značky a potvrdit jejich konečné rozhodnutí.

![Kontrola obrázku lidskými moderátory](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Další postup

Testovací verze [konzole rozhraní API pro moderování obrázků](try-image-api.md) a používat rozhraní REST API ukázky kódu. Podívejte se také [rychlý úvod k .NET pro moderování obrázků](image-moderation-quickstart-dotnet.md) Pokud jste se seznámili s Visual Studio a C#.
