---
title: Azure obsahu moderátora - bitové kopie přerušování | Microsoft Docs
description: Pomocí bitové kopie přerušování střední nevhodných obrázků
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: c7cbc343c6e9113642d0ac79f4a4d60a404e8171
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343596"
---
# <a name="image-moderation"></a>Moderování obrázků

Použít přerušování moderátora obsah s asistencí počítače bitové kopie a [lidské Zkontrolujte nástroj](Review-Tool-User-Guide/human-in-the-loop.md) na střední bitové kopie na obsah pro dospělé a zájem. Kontrola bitové kopie pro textový obsah a rozbalte tento text a zjišťovat řezy. Můžete bitové kopie u vlastních seznamů, které odpovídají a provádět další akce.

## <a name="evaluating-for-adult-and-racy-content"></a>Vyhodnocení na obsah pro dospělé a zájem

**Evaluate** operace vrátí spolehlivosti skóre mezi 0 a 1. Také vrátí logický datový rovna hodnotě true nebo false. Tyto hodnoty předvídání, zda bitová kopie obsahuje potenciální obsah pro dospělé nebo zájem. Při volání rozhraní API pomocí bitové kopie (soubor nebo adresa URL), vrácený odpověď obsahuje následující informace:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]

> - `isImageAdultClassified` představuje potenciální přítomnost bitové kopie, které lze považovat za zřejmý explicitní nebo pro dospělé v určitých situacích.
> - `isImageRacyClassified` představuje potenciální přítomnost bitové kopie, které lze považovat za zřejmý sugestivní nebo vyspělá v určitých situacích.
> - Skóre jsou mezi 0 a 1. Čím více bodů, tím vyšší modelu je predikci, můžou být příslušné kategorii. Tato verze preview spoléhá na statistické model, místo ručně programové výstupy. Doporučujeme, abyste testování s vlastním obsahem určit, jakým způsobem bude každou kategorii zarovnán vašim požadavkům.
> - Logické hodnoty jsou true nebo false, v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, jestli se má použít tuto hodnotu nebo rozhodněte o vlastní prahové hodnoty na základě jejich obsahu zásad.
>

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Zjišťování textu s optické rozpoznávání znaků (rozpoznávání znaků)

**Optické rozpoznávání znaků (rozpoznávání znaků)** operace předpovídá přítomnost textového obsahu v obrázku a extrahuje pro text přerušování, mezi nějaká jiná použití. Zadaný jazyk. Pokud nezadáte žádný jazyk, detekce výchozí angličtině.

Odpověď obsahuje následující informace:
- Původní text.
- Elementy zjištěné text jejich spolehlivosti skóre.

Příklad extrakce:

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


## <a name="detecting-faces"></a>Zjišťování řezy

Zjišťování řezy pomáhá rozpoznat identifikovatelné osobní údaje (PII), jako jsou tyto řezy v bitové kopie. Zjistit potenciální řezy a počet možných tyto řezy v každé bitové kopie.

Odpověď obsahuje tyto informace:

- Počet řezy
- Seznam umístění řezy zjistil

Příklad extrakce:


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

V mnoha online komunit po uživatelé odesílat obrázky nebo jiný typ obsahu, urážlivé položek může získat sdílet vícekrát po následující počet dnů, týdnů a měsíců. Nákladů na opakovaného vyhledávání a filtrování stejnou bitovou kopii nebo byť jen mírně upravené verze bitové kopie z více míst, může být nákladné a k chybám.

Místo moderování vícekrát stejnou bitovou kopii, přidat urážlivé bitové kopie do vlastní seznamu blokovaných obsahu. Tímto způsobem systému obsahu přerušování porovná příchozí bitové kopie u vlastních seznamů a zastaví další zpracování.

> [!NOTE]
> Maximální limit je **5 obrázku seznamy** s každou seznamu **není delší než 10 000 image**.
>

Poskytuje úplný obsahu moderátora [rozhraní API správy seznamu Image](try-image-list-api.md) s operacemi sady pro správu seznam vlastních bitových kopií. Začínat [obrázku uvádí rozhraní API konzoly](try-image-list-api.md) a používat ukázky kódu rozhraní REST API. Také si přečtěte [rychlé spuštění bitové kopie seznamu .NET](image-lists-quickstart-dotnet.md) Pokud jste obeznámeni s Visual Studio a C#.

## <a name="matching-against-your-custom-lists"></a>Porovnání vlastních seznamů

Operace porovnání umožňuje přibližné shody příchozí bitové kopie pro některé z vlastních seznamů, vytvořit a spravovat pomocí operace výpisu.

Pokud je nalezena shoda, vrátí operaci identifikátor a značky přerušování odpovídající bitové kopie. Odpověď obsahuje tyto informace:

- Shoda skóre (mezi 0 a 1)
- Odpovídající bitové kopie
- Obrázek značky (přiřazen během předchozí přerušování)
- Popisky bitové kopie

Příklad extrakce:

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

## <a name="human-review-tool"></a>Nástroj pro recenze prováděné lidmi

Další nuanced případech použít moderátora obsah [Zkontrolujte nástroj](Review-Tool-User-Guide/human-in-the-loop.md) a její rozhraní API prezentovat přerušování výsledky a obsah v revize pro vaše lidského moderátorů. Jejich zkontrolujte značky počítač přiřazen a potvrďte jejich konečné rozhodnutí.

![Zkontrolujte bitové kopie pro lidské moderátorů](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Další postup

Vyzkoušejte [Image přerušování API konzoly](try-image-api.md) a používat ukázky kódu rozhraní REST API. Také si přečtěte [Image přerušování .NET rychlý Start](image-moderation-quickstart-dotnet.md) Pokud jste obeznámeni s Visual Studio a C#.
