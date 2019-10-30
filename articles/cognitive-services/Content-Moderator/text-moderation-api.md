---
title: Moderování textu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Používejte moderování textu pro možné nežádoucí text, osobní údaje a vlastní seznamy podmínek.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 1d147dcee31fa4b84b28ab62e8cd41aebc134a9b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043944"
---
# <a name="learn-text-moderation-concepts"></a>Základní informace o principech moderování textu

Používejte funkce moderování textu s asistencí Content Moderator a funkce pro [kontrolu lidského](Review-Tool-User-Guide/human-in-the-loop.md) textu pro střední obsah.

Obsah můžete buď blokovat, schvalovat nebo kontrolovat na základě vašich zásad a prahových hodnot. Využijte ji k rozšíření lidského moderování prostředí, kde partneři, zaměstnanci a spotřebitelé generují textový obsah. Patří mezi ně chatovací místnosti, diskuzní vývěsky, chatovacích robotů o, katalogy elektronického obchodování a dokumenty. 

Odpověď služby zahrnuje následující informace:

- Vulgární výrazy: shoda založená na termínech s integrovaným seznamem vulgárních výrazů v různých jazycích
- Klasifikace: klasifikace s podporou počítačů na tři kategorie
- Osobní údaje
- Automaticky opravený text
- Původní text
- Jazyk

## <a name="profanity"></a>Vulgárních výrazů

Pokud rozhraní API zjistí jakékoli vulgární výrazy v některém z [podporovaných jazyků](Text-Moderation-API-Languages.md), jsou tyto výrazy součástí odpovědi. Odpověď také obsahuje jejich umístění (`Index`) v původním textu. `ListId` v následujícím ukázkovém formátu JSON odkazuje na podmínky nalezené v [seznamech vlastních podmínek](try-terms-list-api.md) , pokud jsou k dispozici.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pro parametr **jazyka** přiřaďte `eng` nebo nechte prázdné, aby se zobrazila **odpověď s** podporou počítače (funkce Preview). **Tato funkce podporuje jenom angličtinu**.
>
> Pro detekci **podmínek vulgárních** výrazů použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo ponechte prázdné.

## <a name="classification"></a>Classification

**Funkce klasifikace textu** s asistencí Content moderator podporuje **pouze angličtinu**a pomáhá detekovat potenciálně nežádoucí obsah. Obsah označený příznakem může být v závislosti na kontextu posuzován jako nevhodný. Dává pravděpodobnost každé kategorie a může doporučit kontrolu lidského. Tato funkce používá trained model k identifikaci možného urážlivých, derogačních nebo diskriminačních jazyků. To zahrnuje slangem, zkrácená slova, urážlivá a záměrně nesprávně napsaná slova ke kontrole. 

Následující extrakce v extrakci JSON ukazuje příklad výstupu:

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

- `Category1` odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považovaný za zřejmý nebo dospělý.
- `Category2` odkazuje na potenciální přítomnost jazyka, který se může v určitých situacích považovat za pohlavní sugestivní nebo vyspělý.
- `Category3` odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za urážlivý.
- `Score` je mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model, který předpokládá, že se kategorie může použít. Tato funkce spoléhá na statistickou model namísto ručně kódovaných výsledků. Doporučujeme, abyste při testování pomocí vlastního obsahu určili, jak jednotlivé kategorie odpovídají vašim požadavkům.
- v závislosti na prahových hodnotách interního skóre je `ReviewRecommended` buď true, nebo false. Zákazníci by měli posoudit, jestli tuto hodnotu použít, nebo se rozhodnout o vlastních prahech na základě zásad obsahu.

## <a name="personal-data"></a>Osobní údaje

Funkce osobních údajů detekuje potenciální přítomnost těchto informací:

- E-mailová adresa
- Poštovní adresa USA
- IP adresa
- Telefonní číslo v USA
- Telefonní číslo v ČR
- Rodné číslo (sociální zabezpečení)

Následující příklad ukazuje ukázkovou odpověď:

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
      "Text":"6657789887",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1 Microsoft Way, Redmond, WA 98052",
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

## <a name="auto-correction"></a>Automatické opravy

Předpokládejme, že je vstupní text (lzay a F0X je úmyslné):

    The qu!ck brown f0x jumps over the lzay dog.

Pokud si vyžádáte automatické opravy, odpověď obsahuje opravenou verzi textu:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Vytváření a Správa vlastních seznamů podmínek

I když výchozí globální seznam pojmů funguje skvěle pro většinu případů, možná se budete chtít setkat s podmínkami, které jsou specifické pro vaše obchodní potřeby. Můžete například chtít vyfiltrovat všechny názvy značek konkurenčních uživatelů na základě příspěvků.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Následující příklad ukazuje ID odpovídajícího seznamu:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator poskytuje [rozhraní API pro seznam termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) s operacemi pro správu vlastních seznamů termínů. Začněte s [termínem obsahuje konzolu rozhraní API](try-terms-list-api.md) a použijte ukázky kódu REST API. Podívejte se také na [pojem seznam rychlých startů .NET](term-lists-quickstart-dotnet.md) , pokud jste obeznámeni C#se sadou Visual Studio a.

## <a name="next-steps"></a>Další kroky

Otestujte [konzolu rozhraní API pro moderování textu](try-text-api.md) a použijte ukázky kódu REST API. Pokud jste obeznámeni se sadou Visual Studio a C#, podívejte se také na oddíl moderování textu sady [.NET SDK pro rychlý Start](dotnet-sdk-quickstart.md) .
