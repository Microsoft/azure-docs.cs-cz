---
title: Mírný text pomocí rozhraní API pro moderování textu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderování textu testovacího disku pomocí rozhraní API pro moderování textu v online konzole.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 467bf7fe26df2f826d6d44d42a9e30b98795232f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043933"
---
# <a name="moderate-text-from-the-api-console"></a>Střední text z konzoly API

[Rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) v Azure Content moderator slouží ke kontrole vulgárních textů a jejich porovnání s vlastními a sdílenými seznamy.

## <a name="get-your-api-key"></a>Získání klíče rozhraní API

Než budete moct otestovat rozhraní API v online konzole, budete potřebovat svůj klíč předplatného. Najdete ho na kartě **Nastavení** v poli **OCP-APIM-Subscription-Key** . Další informace najdete v tématu [Přehled](overview.md).

## <a name="navigate-to-the-api-reference"></a>Přejít na reference k rozhraní API

Přejít na [odkaz rozhraní API pro moderování textu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 

  Otevře se stránka s **textovou obrazovkou** .

## <a name="open-the-api-console"></a>Otevřete konzolu rozhraní API.

V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 

  ![Výběr oblasti stránky text-obrazovka](images/test-drive-region.png)

  Otevře se konzola rozhraní API pro **textovou obrazovku** .

## <a name="select-the-inputs"></a>Vybrat vstupy

### <a name="parameters"></a>Parametry

Vyberte parametry dotazu, které chcete použít na textové obrazovce. V tomto příkladu použijte výchozí hodnotu pro **jazyk**. Můžete ho nechat prázdné, protože operace automaticky detekuje pravděpodobný jazyk v rámci provádění.

> [!NOTE]
> Pro parametr **jazyka** přiřaďte `eng` nebo nechte prázdné, aby se zobrazila **odpověď s** podporou počítače (funkce Preview). **Tato funkce podporuje jenom angličtinu**.
>
> Pro detekci **podmínek vulgárních** výrazů použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo ponechte prázdné.

V možnosti **Automatické**hodnoty, **PII**a **klasifikovat (Preview)** vyberte **true (pravda**). Pole **ListId** ponechte prázdné.

  ![Parametry dotazu na konzolu text-obrazovka](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu

Jako **typ obsahu**vyberte typ obsahu, který chcete na obrazovce. V tomto příkladu použijte výchozí typ **textu/prostý** obsah. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text, který se má zkontrolovat

Do pole text **žádosti** zadejte nějaký text. Následující příklad ukazuje úmyslné překlep v textu.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analyzovat odpověď

Následující odpověď ukazuje různé přehledy z rozhraní API. Obsahuje potenciální vulgární výrazy, osobní údaje, klasifikaci (Preview) a automaticky opravenou verzi.

> [!NOTE]
> Funkce klasifikace s asistencí počítače je ve verzi Preview a podporuje jenom angličtinu.

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

Podrobné vysvětlení všech sekcí v odpovědi JSON najdete v koncepční příručce pro [moderování textu](text-moderation-api.md) .

## <a name="next-steps"></a>Další kroky

Použijte REST API ve vašem kódu nebo postupujte podle pokynů pro [rychlý Start .NET SDK](dotnet-sdk-quickstart.md) pro integraci s vaší aplikací.
