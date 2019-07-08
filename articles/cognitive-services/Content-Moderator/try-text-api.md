---
title: Moderování textu s použitím API pro moderování textu – Content Moderator
titlesuffix: Azure Cognitive Services
description: Vyzkoušejte si moderování textu s použitím rozhraní API pro moderování textu v konzole online.
services: cognitive-services
author: sanjeev3
ms.author: sajagtap
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: a3eb134d655f2a25acb45e0d249aa421667d1520
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621389"
---
# <a name="moderate-text-from-the-api-console"></a>Moderování textu z konzoly pro rozhraní API

Použití [rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) v Azure Content Moderator kontrolovat obsah textu pro vulgárních výrazů a porovnání vlastní a sdílené seznamy.

## <a name="get-your-api-key"></a>Získejte klíč k rozhraní API

Předtím, než můžete vyzkoušet rozhraní API v konzole online, budete potřebovat klíč předplatného. To je umístěn na **nastavení** kartě **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [Přehled](overview.md).

## <a name="navigate-to-the-api-reference"></a>Přejděte na reference k rozhraní API

Přejděte [reference k rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  **Text – obrazovky** otevře se stránka.

## <a name="open-the-api-console"></a>Otevřete konzolu pro rozhraní API

Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Text – výběr oblasti obrazovky stránky](images/test-drive-region.png)

  **Text – obrazovky** otevře se konzola rozhraní API.

## <a name="select-the-inputs"></a>Vyberte vstupy

### <a name="parameters"></a>Parametry

Vyberte parametry dotazu, které chcete použít na obrazovce text. V tomto příkladu použijte výchozí hodnotu pro **jazyka**. Můžete také necháte prázdné vzhledem k tomu, že operace bude automaticky rozpoznávat pravděpodobně jazyka jako součást jeho spuštění.

> [!NOTE]
> Pro **jazyk** parametr, přiřaďte `eng` nebo toto podokno nechat prázdné najdete v článku s počítače **klasifikace** odpovědí (funkce preview). **Tato funkce podporuje pouze angličtinu**.
>
> Pro **vulgárních výrazů podmínek** detekce, použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo nechte prázdné.

Pro **automatických oprav**, **identifikovatelné osobní údaje**, a **klasifikaci (preview)** vyberte **true**. Nechte **ListId** prázdné pole.

  ![Text – parametry dotazu obrazovky konzoly](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu

Pro **Content-Type**, vyberte typ obsahu, které chcete na obrazovku. V tomto příkladu použijte výchozí **text/plain** typ obsahu. V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text vyhledávání

V **text žádosti** zadejte nějaký text. Následující příklad ukazuje úmyslné překlep v textu.

> [!NOTE]
> Neplatné číslo sociálního pojištění v následující ukázkový text je úmyslné. Slouží k předání ukázkové vstupní a výstupní formát.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analyzovat odpověď

Následující odpověď obsahuje různé přehledy z rozhraní API. Obsahuje vulgární, osobní údaje, klasifikace (preview) a verze automaticky opravit.

> [!NOTE]
> "Klasifikaci" funkcí s asistencí počítače je ve verzi preview a podporuje pouze angličtinu.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
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
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
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

Podrobné vysvětlení všech oddílů v odpovědi JSON, najdete [moderování textu](text-moderation-api.md) koncepční průvodce.

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [rychlý úvod k .NET pro moderování textu](text-moderation-quickstart-dotnet.md) integrovat s vaší aplikací.
