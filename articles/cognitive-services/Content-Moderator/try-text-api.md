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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272589"
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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analyzovat odpověď

Následující odpověď ukazuje různé přehledy z rozhraní API. Obsahuje potenciální vulgární výrazy, osobní údaje, klasifikaci (náhled) a automaticky opravenou verzi.

> [!NOTE]
> Funkce "Klasifikace" s pomocí stroje je ve verzi preview a podporuje pouze angličtinu.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Podrobné vysvětlení všech oddílů v odpovědi JSON naleznete v koncepční příručce [Moderování textu.](text-moderation-api.md)

## <a name="next-steps"></a>Další kroky

Použijte rozhraní REST API ve vašem kódu nebo postupujte podle [rychlého startu sady .NET SDK](dotnet-sdk-quickstart.md) pro integraci s vaší aplikací.
