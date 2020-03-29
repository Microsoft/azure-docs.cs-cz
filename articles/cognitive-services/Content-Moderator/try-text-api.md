---
title: Moderování textu pomocí rozhraní API pro moderování textu – moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Moderování textu testovací jednotky pomocí rozhraní API pro moderování textu v online konzole.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538830"
---
# <a name="moderate-text-from-the-api-console"></a>Moderování textu z konzoly rozhraní API

Pomocí [rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) v Moderátoru obsahu Azure můžete naskenovat textový obsah, aby neobsahuje vulgární výrazy, a porovnat ho s vlastními a sdílenými seznamy.

## <a name="get-your-api-key"></a>Získání klíče rozhraní API

Než budete moci testovací disk API v online konzole, budete potřebovat klíč předplatného. To se nachází na kartě **Nastavení,** v **poli Ocp-Apim-Subscription-Key.** Další informace najdete v tématu [Přehled](overview.md).

## <a name="navigate-to-the-api-reference"></a>Přechod na odkaz rozhraní API

Přejděte na [odkaz na rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Otevře se stránka **Text – obrazovka.**

## <a name="open-the-api-console"></a>Otevření konzoly rozhraní API

V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

  ![Text – výběr oblasti stránky obrazovky](images/test-drive-region.png)

  Otevře se konzola Rozhraní API **obrazovky.**

## <a name="select-the-inputs"></a>Vyberte vstupy

### <a name="parameters"></a>Parametry

Vyberte parametry dotazu, které chcete použít na textové obrazovce. V tomto příkladu použijte výchozí hodnotu pro **jazyk**. Můžete jej také ponechat prázdné, protože operace automaticky rozpozná pravděpodobný jazyk jako součást jeho spuštění.

> [!NOTE]
> Pro parametr **jazyka** `eng` přiřaďte nebo ponechte prázdný, abyste viděli odpověď **klasifikace** s podporou počítače (funkce náhledu). **Tato funkce podporuje pouze angličtinu**.
>
> Pro **detekci vulgárních výrazů** použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku nebo jej ponechte prázdný.

**Chcete-li automatické opravy**, **PII**a **klasifikovat (náhled),** vyberte **true**. Ponechte pole **ListId** prázdné.

  ![Text – parametry dotazu konzoly obrazovky](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu

V **části Typ obsahu**vyberte typ obsahu, který chcete promítat. V tomto příkladu použijte výchozí **text nebo prostý** typ obsahu. Do pole **Ocp-Apim-Subscription-Key** zadejte klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text ke skenování

Do pole **Vyžádat text** zadejte nějaký text. Následující příklad ukazuje záměrné překlep v textu.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analyzovat odpověď

Následující odpověď ukazuje různé přehledy z rozhraní API. Obsahuje potenciální vulgární výrazy, osobní údaje, klasifikaci (náhled) a automaticky opravenou verzi.

> [!NOTE]
> Funkce "Klasifikace" s pomocí stroje je ve verzi preview a podporuje pouze angličtinu.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
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
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
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
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Podrobné vysvětlení všech oddílů v odpovědi JSON naleznete v koncepční příručce [Moderování textu.](text-moderation-api.md)

## <a name="next-steps"></a>Další kroky

Použijte rozhraní REST API ve vašem kódu nebo postupujte podle [rychlého startu sady .NET SDK](dotnet-sdk-quickstart.md) pro integraci s vaší aplikací.
