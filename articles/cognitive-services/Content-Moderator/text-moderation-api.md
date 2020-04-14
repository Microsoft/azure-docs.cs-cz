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
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274006"
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
- Americké telefonní číslo

Následující příklad ukazuje vzorovou odpověď:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
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
