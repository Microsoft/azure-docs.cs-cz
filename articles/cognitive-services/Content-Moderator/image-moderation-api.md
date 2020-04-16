---
title: Moderování obrázků – moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Pomocí nástroje Content Moderator pro moderování obrazu s pomocí stroje a nástroje kontrola člověka ve smyčce můžete moderovat obrázky pro obsah pro dospělé a pikantní.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404265"
---
# <a name="learn-image-moderation-concepts"></a>Naučte se koncepty moderování obrázků

Pomocí nástroje Content Moderator pro moderování obrazu s pomocí stroje a [nástroje kontrola člověka ve smyčce](Review-Tool-User-Guide/human-in-the-loop.md) můžete moderovat obrázky pro obsah pro dospělé a pikantní. Skenujte obrázky, kde najdete textový obsah, extrahuje tento text a detekuje tváře. Obrázky můžete porovnat s vlastními seznamy a provést další kroky.

## <a name="evaluating-for-adult-and-racy-content"></a>Hodnocení obsahu pro dospělé a pikantní

**Vyhodnocení** operace vrátí skóre spolehlivosti mezi 0 a 1. Vrátí také logická data rovná true nebo false. Tyto hodnoty předpovídají, zda obrázek obsahuje potenciální obsah pro dospělé nebo pikantní. Při volání rozhraní API s obrázkem (soubor nebo adresa URL) obsahuje vrácená odpověď následující informace:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně explicitní nebo pouze pro dospělé.
> - `isImageRacyClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně sugestivní nebo pouze pro dospělé.
> - Skóre je mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model předpovídá, že kategorie může být použitelná. Tento náhled závisí na statistickém modelu, nikoli na ručně kódovaných výsledcích. Doporučujeme testování s vlastním obsahem, abyste zjistili, jak jednotlivé kategorie odpovídají vašim požadavkům.
> - Logické hodnoty jsou true nebo false v závislosti na prahových hodnotách vnitřního skóre. Zákazníci by měli posoudit, zda mají tuto hodnotu použít, nebo se rozhodnout pro vlastní prahové hodnoty na základě svých zásad obsahu.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detekce textu pomocí optického rozpoznávání znaků (OCR)

Operace **Optické rozpoznávání znaků (OCR)** předpovídá přítomnost textového obsahu v obraze a extrahuje jej pro moderování textu, mimo jiné použití. Můžete zadat jazyk. Pokud nezadáte jazyk, výchozí nastavení detekce na angličtinu.

Odpověď obsahuje následující informace:
- Původní text.
- Zjištěné textové prvky s jejich skóre spolehlivosti.

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

Detekce tváří pomáhá detekovat osobní údaje, jako jsou tváře na obrázcích. Zjistíte potenciální plochy a počet potenciálních ploch v každém obrázku.

Odpověď obsahuje tyto informace:

- Počet ploch
- Seznam zjištěných míst ploch

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

## <a name="creating-and-managing-custom-lists"></a>Vytváření a správa vlastních seznamů

V mnoha online komunitách mohou být urážlivé položky v mnoha online komunitách sdíleny vícekrát v následujících dnech, týdnech a měsících. Náklady na opakované skenování a filtrování stejného obrázku nebo dokonce mírně upravených verzí obrazu z více míst mohou být drahé a náchylné k chybám.

Místo toho, abyste vícekrát moderovali stejný obrázek, přidáte urážlivé obrázky do vlastního seznamu blokovaného obsahu. Tímto způsobem systém moderování obsahu porovná příchozí obrázky s vlastními seznamy a zastaví jakékoli další zpracování.

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Content Moderator poskytuje kompletní [rozhraní API pro správu seznamu obrázků](try-image-list-api.md) s operacemi pro správu seznamů vlastních bitových kopií. Začněte konzolou [rozhraní API seznamů obrázků](try-image-list-api.md) a použijte ukázky kódu rozhraní REST API. Také se podívejte na [seznam obrázků .NET rychlý start,](image-lists-quickstart-dotnet.md) pokud jste obeznámeni s Visual Studio a C#.

## <a name="matching-against-your-custom-lists"></a>Párování s vlastními seznamy

Operace Shoda umožňuje přibližné párování příchozích obrázků s libovolnými vlastními seznamy, vytvořenými a spravovanými pomocí operací Seznamu.

Pokud je nalezena shoda, operace vrátí identifikátor a značky moderování odpovídající hoobrázku. Odpověď obsahuje tyto informace:

- Skóre zápasu (mezi 0 a 1)
- Odpovídající obrázek
- Značky obrázků (přiřazené během předchozího moderování)
- Popisky obrázků

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

## <a name="review-tool"></a>Nástroj pro revize

Chcete-li zobrazit podrobnější případy, použijte nástroj Content Moderator [Review](Review-Tool-User-Guide/human-in-the-loop.md) a jeho rozhraní API k zobrazení výsledků moderování a obsahu v recenzi pro vaše lidské moderátory. Zkontrolují strojově přiřazené značky a potvrdí svá konečná rozhodnutí.

![Kontrola obrázku lidskými moderátory](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Další kroky

Otestujte konzolu [rozhraní API pro moderování bitových kódu](try-image-api.md) a použijte ukázky kódu rozhraní REST API. Také se podívejte na část Moderování obrázků [na rychlém startu sady .NET SDK,](dotnet-sdk-quickstart.md) pokud jste obeznámeni s Visual Studio a C#.
