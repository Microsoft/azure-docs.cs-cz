---
title: Zpracování velkých zpráv v Azure Logic Apps | Microsoft Docs
description: Zjistěte, jak zpracovávat velké zpráva velikosti s rozdělování v aplikacích logiky
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: SyntaxC4
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 4/27/2018
ms.author: shhurst; LADocs
ms.openlocfilehash: 421a207456908fa3b10582c2287b1b2467ff74b1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="handle-large-messages-with-chunking-in-logic-apps"></a>Zpracování velkých zprávy s rozdělování v Logic Apps

Při zpracování zpráv, omezuje Logic Apps obsah zprávy maximální velikost.
Toto omezení pomáhá snížit režii vytvořené ukládání a zpracování velkých zpráv.
Zpracování zpráv větší než tento limit, může aplikace logiky *bloku* velké zprávu do menších zpráv. Tímto způsobem můžete stále přenos velkých souborů pomocí aplikace logiky pro určité podmínky.
Při komunikaci s jinými službami prostřednictvím konektory nebo HTTP, Logic Apps může využívat velké zprávy, ale *pouze* v bloků.
To znamená, že konektory musí také podporovat rozdělování nebo základní výměny zpráv protokolu HTTP mezi Logic Apps a tyto služby musíte použít rozdělování.

Tento článek ukazuje, jak můžete nastavit rozdělování podpora pro zpracování zprávy, které překračují limit.

## <a name="what-makes-messages-large"></a>Díky zprávy "velké"?

Zprávy jsou "velké" založené na službě zpracování těchto zpráv.
Limit velikosti přesně na velké zprávy se liší mezi konektory a aplikace logiky.
Konektory a aplikace logiky nemůže využívat přímo velké zprávy, které musí být blokové. Maximální velikost zprávy Logic Apps, naleznete v části [Logic Apps omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).
Limit velikosti zprávy pro každý konektor, najdete v článku [konektoru na konkrétní technické podrobnosti](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Zpracování pro Logic Apps bloku zpráv

Služba Logic Apps nelze použít přímo výstupy z bloku zprávy, které překračují maximální velikost zprávy Logic Apps.
Akce, které podporují rozdělování přístup k obsahu zprávy v těchto výstupů.
Ano, akci, která zpracovává velké zprávy musí *buď*:

* Nativně podporují rozdělování když tato akce patří do konektoru.
* Máte rozdělování podpora povolená v konfiguraci modulu runtime této akce.

Chyba za běhu, jinak hodnota získáte při pokusu o přístup k obsahu velké výstupu.
Chcete-li povolit rozdělování, přečtěte si téma [nastavit rozdělování podporu](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Zpracování pro konektory bloku zpráv

Služby, které komunikují s Logic Apps může mít vlastní zprávu omezení velikosti.
Tato omezení jsou často menší než limit Logic Apps. Například za předpokladu, že konektor podporuje rozdělování, konektor zvážit zprávu 30 MB jako velký, ale nikoli Logic Apps.
Abyste dosáhli souladu s limitem tento konektor, Logic Apps rozdělí jakékoli zprávy větší než 30 MB na menší skupiny.

Pro konektory, které podporují rozdělování základního protokolu bloku dat je neviditelná pro koncové uživatele.
Ale ne všechny konektory díky podpoře rozdělování, tyto konektory generují chyby za běhu v případě, že příchozí zprávy překročit omezení velikosti konektory.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Nastavit rozdělování přes protokol HTTP

V obecné scénáře HTTP můžete rozdělit velké stahování obsahu a odešle prostřednictvím protokolu HTTP, tak, aby svou aplikaci logiky a koncového bodu můžou vyměňovat zprávy velké. Však musí bloku dat zpráv ve způsobu, jakým očekává Logic Apps.

Pokud je koncový bod má povoleno rozdělování pro stahování nebo nahrávání, akce HTTP v aplikaci logiky automaticky bloku dat velké zprávy. Jinak musíte vytvořit rozdělování podpory v koncovém bodě. Pokud nemáte vlastníte nebo ovládáte koncového bodu nebo konektoru, nemusí mít možnost nastavit rozdělování.

Navíc pokud akce HTTP již neumožňuje rozdělování, musíte taky nastavit až rozdělování v akce `runTimeConfiguration` vlastnost.
Tato vlastnost uvnitř akce, můžete nastavit, buď přímo v editoru kódu zobrazení, jak je popsáno dále, nebo v Návrháři logiku aplikace podle postupu popsaného tady:

1. V pravém horním rohu akce HTTP, klikněte pravým tlačítkem na tlačítko se třemi tečkami (**...** ) a zvolte **nastavení**.

2. V části **přenosu obsahu**, nastavte **Povolit rozdělování** k **na**.

3. Chcete-li pokračovat rozdělování pro stahování nebo nahrávání, pokračujte v následujících částech.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Stažení obsahu v bloky dat

Mnoho koncových bodů automaticky odesílat zprávy velké v bloky dat při stažení prostřednictvím požadavku HTTP GET.
Pokud chcete bloku zpráv přes protokol HTTP z koncového bodu, koncový bod musí podporovat částečné požadavků obsahu, nebo *blokové stahování*. Pokud svou aplikaci logiky odešle požadavek HTTP GET na koncový bod pro stahování obsahu a koncový bod odpoví "206" stavový kód, odpověď obsahuje bloku obsah.
Služba Logic Apps nemůže řídit, jestli koncový bod podporuje částečné požadavky.
Ale pokud svou aplikaci logiky získá odpověď nejprve "206", aplikace logiky automaticky odesílá více žádostí stáhnout veškerý obsah.

Pokud chcete zkontrolovat, jestli koncový bod může podporovat Částečný obsah, poslat žádost HEAD. Tento požadavek vám pomůže určit, zda obsahuje odpovědi `Accept-Ranges` záhlaví.
Tímto způsobem, pokud koncový bod podporuje stahování bloku ale neodešle bloku obsah, můžete *navrhnout* tuto možnost nastavíte `Range` záhlaví ve vaší žádosti HTTP GET.

Podrobný proces, který používá Logic Apps pro stahování obsahu bloku z koncového bodu do aplikace logiky popisují tyto kroky:

1. Aplikace logiky odešle požadavek HTTP GET na koncový bod.

   Hlavička žádosti může volitelně obsahovat `Range` pole, která popisuje rozsah bajtů pro vyžadování obsahu bloků.

2. Koncový bod odpoví "206" stavový kód a textu zprávy HTTP.

    Podrobnosti o obsahu této bloku se zobrazí v odpovědi na `Content-Range` záhlaví, včetně informací, která pomáhá Logic Apps určit před rozdělování počáteční a koncová pro bloku dat a celková velikost celého obsahu.

3. Aplikace logiky automaticky odesílá následné požadavky HTTP GET.

    Aplikace logiky odešle následné požadavky GET před načtením celého obsahu.

Tato akce definice příkladu požadavek GET protokolu HTTP, která nastavuje `Range` záhlaví, *návrhy* , koncový bod odpoví blokové obsahu:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Požadavek GET nastaví hlavičku "Rozsah" na "bajtů = 0-1023", což je rozsah bajtů. Pokud koncový bod podporuje požadavky pro částečný obsah, koncový bod odpoví obsahu bloků dat z požadovaný rozsah.
Podle toho, koncový bod, přesném formátu, který pro pole hlavičky "Rozsah" se může lišit.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Nahrát obsah v bloky dat

Nahrát bloku obsah z akce HTTP, musíte akci povolena podpora bloku dat pomocí akce `runtimeConfiguration` vlastnost.
Toto nastavení umožňuje akci spuštění bloku dat protokolu.
Aplikace logiky můžete pak odešle zprávu počáteční Metoda POST nebo PUT do cílového koncového bodu.
Po koncový bod odpoví velikost navrhované bloku, následuje svou aplikaci logiky pomocí zasílání požadavků HTTP PATCH, které obsahují bloky obsahu.

Podrobný proces, který používá Logic Apps pro nahrávání bloku obsahu z aplikace logiky na koncový bod popisují tyto kroky:

1. Aplikace logiky odesílá počáteční požadavek HTTP POST nebo PUT s tělo zprávy prázdný. Hlavička požadavku obsahuje tyto informace o obsahu, který chce, aby se aplikace logiky v bloky dat odeslat:

   | Pole hlavičky požadavku Logic Apps | Hodnota | Typ | Popis |
   |---------------------------------|-------|------|-------------|
   | **x-ms přenos mode** | blokové | Řetězec | Určuje, zda je obsah nahrát bloky dat |
   | **x-ms-content-length** | <*Délka obsahu*> | Integer | Celý obsah velikost v bajtech před rozdělování |
   ||||

2. Koncový bod odpoví "200" úspěch stavový kód a tyto volitelné informace:

   | Pole hlavičky odpovědi koncového bodu | Typ | Požaduje se | Popis |
   |--------------------------------|------|----------|-------------|
   | **x-ms bloku velikost** | Integer | Ne | Velikost bloku navržené v bajtech |
   | **Umístění** | Řetězec | Ne | Umístění URL kam má posílat zprávy HTTP PATCH |
   ||||

3. Aplikace logiky vytváří a odesílá následné zprávy HTTP PATCH – každou s tyto informace:

   * Na základě obsahu bloku **x-ms bloku velikost** nebo některé interně počítané velikost až všechny obsahu součtem **x-ms-content-length** postupně nahraje

   * Tyto hlavičky podrobnosti o obsahu bloku v každé zprávě oprava zasláno:

     | Pole hlavičky požadavku Logic Apps | Hodnota | Typ | Popis |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*rozsah*> | Řetězec | Rozsah bajtů u obsahu bloku, včetně počáteční hodnotu, koncová hodnota a celková velikost obsahu, například: "bajtů = 0-1023/10100" |
     | **Content-Type** | <*Typ obsahu*> | Řetězec | Typ bloku obsahu |
     | **Content-Length** | <*Délka obsahu*> | Řetězec | Délka velikost v bajtech aktuální bloku |
     |||||

4. Koncový bod po každé žádosti o opravu potvrdí přijetí pro každého bloku pomocí reagovat s kódem stavu "200".

Tato akce definice zobrazuje požadavek HTTP POST pro nahrávání bloku obsahu pro koncový bod.
V rámci akce `runTimeConfiguration` vlastnost, `contentTransfer` sady vlastností `transferMode` k `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```