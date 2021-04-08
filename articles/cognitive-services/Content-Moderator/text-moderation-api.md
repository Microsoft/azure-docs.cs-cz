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
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912901"
---
# <a name="learn-text-moderation-concepts"></a>Základní informace o principech moderování textu

Pomocí modelů pro moderování textu Content Moderator můžete analyzovat textový obsah.

V závislosti na vašich zásadách a prahech můžete obsah zablokovat, schvalovat nebo kontrolovat (podívejte se na [recenze, pracovní postupy a úlohy,](./review-api.md) kde se dozvíte, jak nastavit lidské recenze). Pomocí modelů moderování textu můžete rozšířit moderování lidských prostředí, kde partneři, zaměstnanci a spotřebitelé vygenerují textový obsah. Patří sem chatovací místnosti, diskuzní vývěsky, chatovací roboti, katalogy elektronického obchodování a dokumenty.

Odpověď služby zahrnuje následující informace:

- Vulgární výrazy: shoda založená na termínech s integrovaným seznamem vulgárních výrazů v různých jazycích
- Klasifikace: klasifikace s podporou počítačů na tři kategorie
- Osobní údaje
- Automaticky opravený text
- Původní text
- Jazyk

## <a name="profanity"></a>Vulgární výrazy

Pokud rozhraní API zjistí jakékoli vulgární výrazy v některém z [podporovaných jazyků](./language-support.md), jsou tyto výrazy součástí odpovědi. Odpověď také obsahuje jejich umístění ( `Index` ) v původním textu. `ListId`V následujícím ukázkovém formátu JSON odkazuje na podmínky nalezené v [seznamech vlastních termínů](try-terms-list-api.md) , pokud jsou k dispozici.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> Pro parametr **jazyka** přiřaďte `eng` nebo nechejte prázdný, aby se zobrazila odpověď s podporou počítače  (funkce Preview). **Tato funkce podporuje jenom angličtinu**.
>
> Pro detekci **podmínek vulgárních** výrazů použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo ponechte prázdné.

## <a name="classification"></a>Klasifikace

**Funkce klasifikace textu** s asistencí Content moderator podporuje **pouze angličtinu** a pomáhá detekovat potenciálně nežádoucí obsah. Obsah označený příznakem může být v závislosti na kontextu posuzován jako nevhodný. Dává pravděpodobnost každé kategorie a může doporučit kontrolu lidského. Tato funkce používá trained model k identifikaci možného urážlivých, derogačních nebo diskriminačních jazyků. To zahrnuje slangem, zkrácená slova, urážlivá a záměrně nesprávně napsaná slova ke kontrole. 

Následující extrakce v extrakci JSON ukazuje příklad výstupu:

```json
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
```

### <a name="explanation"></a>Vysvětlení

- `Category1` odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za zřejmý nebo dospělý.
- `Category2` odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považovaný za zřejmý sugestivní nebo vyspělý.
- `Category3` odkazuje na potenciální přítomnost jazyka, který může být v určitých situacích považován za urážlivý.
- `Score` je mezi 0 a 1. Čím vyšší je skóre, tím vyšší je model, který předpokládá, že se kategorie může použít. Tato funkce spoléhá na statistickou model namísto ručně kódovaných výsledků. Doporučujeme, abyste při testování pomocí vlastního obsahu určili, jak jednotlivé kategorie odpovídají vašim požadavkům.
- `ReviewRecommended` je buď true, nebo false v závislosti na vnitřních prahech skóre. Zákazníci by měli posoudit, jestli tuto hodnotu použít, nebo se rozhodnout o vlastních prahech na základě zásad obsahu.

## <a name="personal-data"></a>Osobní údaje

Funkce osobních údajů detekuje potenciální přítomnost těchto informací:

- E-mailová adresa
- Poštovní adresa USA
- IP adresa
- Telefonní číslo v USA

Následující příklad ukazuje ukázkovou odpověď:

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

## <a name="auto-correction"></a>Automatické opravy

Předpokládejme, že je vstupní text (lzay a F0X je úmyslné):

> Qu! CK Brown F0X přeskočí přes lzay pes.

Pokud si vyžádáte automatické opravy, odpověď obsahuje opravenou verzi textu:

> Rychlý hnědý Fox přeskočí přes opožděný pes.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Vytváření a Správa vlastních seznamů podmínek

I když výchozí globální seznam pojmů funguje skvěle pro většinu případů, možná se budete chtít setkat s podmínkami, které jsou specifické pro vaše obchodní potřeby. Můžete například chtít vyfiltrovat všechny názvy značek konkurenčních uživatelů na základě příspěvků.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Následující příklad ukazuje ID odpovídajícího seznamu:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

Content Moderator poskytuje [rozhraní API pro seznam termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) s operacemi pro správu vlastních seznamů termínů. Začněte s [termínem obsahuje konzolu rozhraní API](try-terms-list-api.md) a použijte ukázky kódu REST API. Podívejte se také na [pojem seznam rychlých startů .NET](term-lists-quickstart-dotnet.md) , pokud jste obeznámeni se sadou Visual Studio a C#.

## <a name="next-steps"></a>Další kroky

Otestujte rozhraní API [konzolou rozhraní API pro moderování textu](try-text-api.md). Podívejte se také na [recenze, pracovní postupy a úlohy,](./review-api.md) kde se dozvíte, jak nastavit lidské recenze.