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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81272589"
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
> Pro parametr **jazyka** přiřaďte `eng` nebo nechejte prázdný, aby se zobrazila odpověď s podporou počítače **classification** (funkce Preview). **Tato funkce podporuje jenom angličtinu**.
>
> Pro detekci **podmínek vulgárních** výrazů použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo ponechte prázdné.

V možnosti **Automatické**hodnoty, **PII**a **klasifikovat (Preview)** vyberte **true (pravda**). Pole **ListId** ponechte prázdné.

  ![Parametry dotazu na konzolu text-obrazovka](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ obsahu

Jako **typ obsahu**vyberte typ obsahu, který chcete na obrazovce. V tomto příkladu použijte výchozí typ **textu/prostý** obsah. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

### <a name="sample-text-to-scan"></a>Ukázkový text, který se má zkontrolovat

Do pole text **žádosti** zadejte nějaký text. Následující příklad ukazuje úmyslné překlep v textu.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analyzovat odpověď

Následující odpověď ukazuje různé přehledy z rozhraní API. Obsahuje potenciální vulgární výrazy, osobní údaje, klasifikaci (Preview) a automaticky opravenou verzi.

> [!NOTE]
> Funkce klasifikace s asistencí počítače je ve verzi Preview a podporuje jenom angličtinu.

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

Podrobné vysvětlení všech sekcí v odpovědi JSON najdete v koncepční příručce pro [moderování textu](text-moderation-api.md) .

## <a name="next-steps"></a>Další kroky

Použijte REST API ve vašem kódu nebo postupujte podle pokynů pro [rychlý Start .NET SDK](dotnet-sdk-quickstart.md) pro integraci s vaší aplikací.
