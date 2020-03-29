---
title: Moderování textu – moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Moderování textu použijte pro možný nežádoucí text, osobní údaje a vlastní seznamy termínů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538848"
---
# <a name="learn-text-moderation-concepts"></a>Naučte se koncepty moderování textu

Moderování textu moderátora obsahu a funkce [lidské kontroly](Review-Tool-User-Guide/human-in-the-loop.md) slouží k moderování textového obsahu moderátorem.

Obsah můžete blokovat, schvalovat nebo kontrolovat na základě zásad a prahových hodnot. Použijte ji k rozšíření lidské umírněnosti prostředí, kde partneři, zaměstnanci a spotřebitelé vytvářejí textový obsah. Patří sem chatovací místnosti, diskuzní vývěsky, chatovací roboti, katalogy elektronického obchodování a dokumenty. 

Odpověď služby zahrnuje následující informace:

- Vulgární výrazy: termín-založené odpovídající s vestavěným-in seznam vulgárních pojmů v různých jazycích
- Klasifikace: klasifikace se strojovou podporou do tří kategorií
- Osobní údaje
- Automaticky opravený text
- Původní text
- Jazyk

## <a name="profanity"></a>Vulgární výrazy

Pokud rozhraní API zjistí vulgární výrazy v některém z [podporovaných jazyků](Text-Moderation-API-Languages.md), jsou tyto termíny zahrnuty v odpovědi. Odpověď také obsahuje jejich`Index`umístění ( ) v původním textu. V `ListId` následující ukázce JSON odkazuje na termíny nalezené ve [vlastní termín seznamy,](try-terms-list-api.md) pokud jsou k dispozici.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pro parametr **jazyka** `eng` přiřaďte nebo ponechte prázdný, abyste viděli odpověď **klasifikace** s podporou počítače (funkce náhledu). **Tato funkce podporuje pouze angličtinu**.
>
> Pro **detekci vulgárních výrazů** použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku nebo jej ponechte prázdný.

## <a name="classification"></a>Classification

**Funkce klasifikace textu** moderátora obsahu podporuje pouze **angličtinu**a pomáhá detekovat potenciálně nežádoucí obsah. Označený obsah může být v závislosti na kontextu posouzen jako nevhodný. Vyjadřuje pravděpodobnost každé kategorie a může doporučit lidské hodnocení. Funkce používá trénovaný model k identifikaci možného urážlivého, hanlivého nebo diskriminačního jazyka. To zahrnuje slang, zkrácená slova, urážlivé a záměrně chybně napsaná slova k recenzi. 

Následující výňatek v json extraktu ukazuje příklad výstupu:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Vysvětlení

- `Category1`odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za sexuálně explicitní nebo dospělý.
- `Category2`odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za sexuálně sugestivní nebo zralý.
- `Category3`odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za urážlivý.
- `Score`je mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model předpovídá, že kategorie může být použitelná. Tato funkce závisí na statistickém modelu, nikoli na ručně kódovaných výsledcích. Doporučujeme testování s vlastním obsahem, abyste zjistili, jak jednotlivé kategorie odpovídají vašim požadavkům.
- `ReviewRecommended`je pravda nebo nepravda v závislosti na prahových hodnotách vnitřního skóre. Zákazníci by měli posoudit, zda mají tuto hodnotu použít, nebo se rozhodnout pro vlastní prahové hodnoty na základě svých zásad obsahu.

## <a name="personal-data"></a>Osobní údaje

Funkce osobních údajů detekuje potenciální přítomnost těchto informací:

- E-mailová adresa
- Poštovní adresa v USA
- IP adresa
- Telefonní číslo v USA
- UK Telefonní číslo
- Číslo sociálního pojištění (SSN)

Následující příklad ukazuje vzorovou odpověď:

```json
"PII":{ 
  "Email":[ 
    { 
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[ 
    { 
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[ 
    { 
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[ 
    { 
      "Text":"999999999",
      "Index":56
    },
    { 
      "Text":"999-99-9999",
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatická oprava

Předpokládejme, že vstupní text je ('lzay' a 'f0x' jsou úmyslné):

    The qu!ck brown f0x jumps over the lzay dog.

Pokud požádáte o automatickou opravu, odpověď obsahuje opravenou verzi textu:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Vytváření a správa vlastních seznamů termínů

Zatímco výchozí globální seznam termínů funguje skvěle ve většině případů, můžete chtít zobrazit obrazovku proti termínům, které jsou specifické pro vaše obchodní potřeby. Můžete například odfiltrovat všechny konkurenční značky z příspěvků uživatelů.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Následující příklad ukazuje odpovídající ID seznamu:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator poskytuje [rozhraní API seznamu termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) s operacemi pro správu vlastních seznamů termínů. Začněte s [konzolou rozhraní API seznamy termínů](try-terms-list-api.md) a použijte ukázky kódu rozhraní REST API. Také se podívejte na [seznamy termínů .NET rychlý start,](term-lists-quickstart-dotnet.md) pokud jste obeznámeni s Visual Studio a C#.

## <a name="next-steps"></a>Další kroky

Test ovat konzolu [rozhraní API moderování textu](try-text-api.md) a použít ukázky kódu rozhraní REST API. Také se podívejte na část Moderování textu [na rychlém startu sady .NET SDK,](dotnet-sdk-quickstart.md) pokud jste obeznámeni s Visual Studio a C#.
