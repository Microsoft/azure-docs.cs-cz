---
title: Moderování obrázku – Content Moderator
titleSuffix: Azure Cognitive Services
description: Využijte Nástroj pro kontrolu obrazu s asistencí Content Moderator a nástroj pro recenze na základě lidského provozu pro použití pro dospělé a pikantní obsah.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85800991"
---
# <a name="learn-image-moderation-concepts"></a>Základní informace o principech moderování obrázků

Pro dospělé a pikantní obsah Content Moderator použijte nástroj pro moderování imagí a [Nástroj pro kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) obrazu s asistencí pro počítače. Naskenujte obrázky pro textový obsah a extrahuje text a detekuje obličeje. Obrázky můžete porovnávat s vlastními seznamy a provádět další akce.

## <a name="evaluating-for-adult-and-racy-content"></a>Hodnocení obsahu pro dospělé a pikantní

Operace **vyhodnocení** vrátí skóre spolehlivosti mezi 0 a 1. Vrátí také logická data rovnající se hodnotě true nebo false. Tyto hodnoty předpovídá, jestli image obsahuje potenciálně dospělý nebo pikantní obsah. Při volání rozhraní API s obrázkem (soubor nebo adresa URL) vrácená odpověď obsahuje následující informace:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně explicitní nebo pouze pro dospělé.
> - `isImageRacyClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně sugestivní nebo pouze pro dospělé.
> - Skóre jsou mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model, který předpokládá, že se kategorie může použít. Tato verze Preview spoléhá na místo ručně kódovaných výsledků z statistického modelu. Doporučujeme, abyste při testování pomocí vlastního obsahu určili, jak jednotlivé kategorie odpovídají vašim požadavkům.
> - Hodnoty Boolean jsou buď true, nebo false v závislosti na vnitřních prahech skóre. Zákazníci by měli posoudit, jestli tuto hodnotu použít, nebo se rozhodnout o vlastních prahech na základě zásad obsahu.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detekce textu pomocí optického rozpoznávání znaků (OCR)

Operace **optického rozpoznávání znaků (OCR)** předpovídá přítomnost textového obsahu v obrázku a extrahuje jej pro moderování textu, a to mimo jiné použití. Můžete zadat jazyk. Pokud nezadáte jazyk, je výchozí hodnota detekce angličtina.

Odpověď obsahuje následující informace:
- Původní text.
- Zjištěné textové prvky s výsledky jejich spolehlivosti.

Příklad extrakce:

```json
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
```

## <a name="detecting-faces"></a>Rozpoznávání tváří

Detekce plošek pomáhá detekovat osobní údaje, jako jsou obličeje v obrázcích. Detekujete potenciální plošky a počet potenciálních plošek v jednotlivých obrazech.

Odpověď obsahuje tyto informace:

- Počet plošek
- Seznam umístění zjištěných plošek

Příklad extrakce:

```json
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
```

## <a name="creating-and-managing-custom-lists"></a>Vytváření a Správa vlastních seznamů

V mnoha online komunitách se po odeslání obrázků nebo jiného typu obsahu můžou v průběhu následujících dnů, týdnů a měsíců sdílet urážlivé položky několikrát. Náklady na opakované prohledávání a filtrování ve stejné imagi nebo dokonce mírně upravené verze image z více míst můžou být nákladné a náchylné k chybám.

Místo toho, aby se stejná image několikrát vymezily, přidáte do svého vlastního seznamu blokovaného obsahu tyto urážlivé image. Tímto způsobem systém Moderování obsahu porovnává příchozí image s vlastními seznamy a zastaví jakékoli další zpracování.

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

Content Moderator poskytuje kompletní [rozhraní API pro správu seznamu obrázků](try-image-list-api.md) s operacemi pro správu seznamů vlastních imagí. Začněte s [imagí](try-image-list-api.md) a použijte REST API ukázky kódu. Pokud jste obeznámeni se sadou Visual Studio a jazykem C#, podívejte se také na [rychlý Start v seznamu obrázků .NET](image-lists-quickstart-dotnet.md) .

## <a name="matching-against-your-custom-lists"></a>Porovnání s vlastními seznamy

Operace porovnávání umožňuje přibližnou shodu příchozích imagí s libovolnými vlastními seznamy vytvořenými a spravované pomocí operací se seznamem.

Pokud je nalezena shoda, operace vrátí identifikátor a značky moderování odpovídajícího obrázku. Odpověď obsahuje tyto informace:

- Skóre shody (mezi 0 a 1)
- Odpovídající obrázek
- Značky obrázku (přiřazené během předchozího moderování)
- Popisky obrázků

Příklad extrakce:

```json
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
```

## <a name="review-tool"></a>Nástroj pro revize

Pro více případů odlišit použijte [Nástroj pro kontrolu](Review-Tool-User-Guide/human-in-the-loop.md) Content moderator a jeho rozhraní API k obstudování výsledků moderování a obsahu v recenzi pro své lidské moderátoři. Posuzuje značky přiřazené počítači a potvrdí jejich konečné rozhodnutí.

![Kontrola obrázku lidskými moderátory](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Další kroky

Otestujte [konzolu rozhraní API pro moderování imagí](try-image-api.md) a použijte ukázky kódu REST API. Podívejte se také na [recenze, pracovní postupy a úlohy,](./review-api.md) kde se dozvíte, jak nastavit lidské recenze.
