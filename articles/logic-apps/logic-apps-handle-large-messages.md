---
title: Zpracování velkých zpráv – Azure Logic Apps | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat velikost velkých zpráv s dělením dat do bloků v Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: e6ac6a4aa46feb768df437ff9d5969b2b41092c3
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041641"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Zpracování velkých zpráv s dělením dat do bloků v Azure Logic Apps

Při zpracování zpráv, Logic Apps omezuje obsah zprávy do maximální velikosti. Toto omezení pomáhá snížit režii vytvořené ukládání a zpracování velkých zpráv. Zpracování zprávy větší než tento limit, Logic Apps můžete *bloků* objemné zprávy do menších zpráv. Tímto způsobem můžete stále přenos velkých souborů pomocí Logic Apps za určitých podmínek. Při komunikaci s dalšími službami prostřednictvím konektory nebo HTTP, Logic Apps může spotřebovat velké zprávy, ale *pouze* v blocích. Tento stav znamená, že konektory musí také podporovat bloků nebo základní výměny zpráv protokolu HTTP mezi Logic Apps a tyto služby musíte použít dělením dat do bloků.

Tento článek popisuje, jak můžete nastavit podporu pro zprávy, které jsou větší než omezení dělením dat do bloků.

## <a name="what-makes-messages-large"></a>Čím se zprávy "velké (large)?

Zprávy jsou "velký" podle služby, zpracování zprávy. Přesnou velikost limitu velké zprávy se liší v Logic Apps a konektorů. Logic Apps a konektorů nejde používat přímo velkých zpráv, které musí být rozdělený do bloků dat. Maximální velikost zprávy Logic Apps, najdete v části [Logic Apps omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).
Maximální velikost zprávy pro jednotlivé konektory, najdete v článku [konkrétní technické podrobnosti konektoru](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Zpracování pro Logic Apps bloku zpráv

Logic Apps nejde používat přímo vytvořené jako výstupy z bloku dat zpráv, které jsou větší než maximální velikost zprávy. Jenom akce, které podporují bloků přístup k obsahu zprávy v těchto výstupů. Ano, musí splňovat akci, která zpracovává velký zprávy *buď* tato kritéria:

* Nativně podporují bloků při této akci patří do konektoru. 
* Jste v konfiguraci modulu runtime tuto akci je povolená podpora bloků. 

Při pokusu o přístup k velké obsahu výstup, jinak, zobrazí chyba za běhu. Povolit bloků, naleznete v tématu [nastavení bloků podporu](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Zpracování pro konektory bloku zpráv

Služby, které komunikují s Logic Apps může mít vlastní omezení velikosti zpráv. Tato omezení jsou často menší než limit Logic Apps. Za předpokladu, že konektor podporuje bloků, konektor může Představte si třeba 30 MB zprávy jako velké, ale nikoli Logic Apps. Pro dosažení souladu s limitem tento konektor, Logic Apps rozdělí jakékoli zprávy větší než 30 MB do menších bloků.

Pro konektory, které podporují bloků je neviditelné koncovým uživatelům na základním bloku dat protokolu. Ale ne všechny konektory podporují bloků, tak tyto konektory generovat chyby za běhu, když příchozí zprávy překročí limity velikosti konektory.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Nastavení dat prostřednictvím protokolu HTTP

Ve scénářích obecný HTTP můžete rozdělit velké stahování obsahu a odešle přes protokol HTTP, tak, aby vaše aplikace logiky a koncový bod si mohou vyměňovat velkých zpráv. Musíte však bloku dat zpráv způsobem, který očekává, že Logic Apps. 

Povolí koncový bod pro stahování a nahrávání dat, akce HTTP v aplikaci logiky automaticky bloku dat velkých zpráv. V opačném případě musíte nastavit dat podpory v koncovém bodě. Pokud není vlastníte nebo ovládáte koncového bodu nebo konektoru, nemusí mít možnost nastavit dělením dat do bloků.

Navíc pokud akce HTTP již neumožňuje bloků, musíte také nastavit bloků v rámci akce `runTimeConfiguration` vlastnost. Tato vlastnost v akci, můžete nastavit, buď přímo v editoru kódu zobrazit, jak je popsáno dále, nebo v návrháři pro Logic Apps podle postupu popsaného tady:

1. V pravém horním rohu akce HTTP, zvolte tlačítko se třemi tečkami (**...** ) a klikněte na tlačítko **nastavení**.

   ![Pro akci otevřete nabídku nastavení](./media/logic-apps-handle-large-messages/http-settings.png)

2. V části **přenosu obsahu**, nastavte **povolit bloků** k **na**.

   ![Zapnout dělením dat do bloků](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Chcete-li pokračovat pro stahování a nahrávání dat, pokračujte v následujících částech.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Stáhnout obsah do bloků dat

Mnoho koncových bodů automaticky odesílání velkých zpráv v blocích po stažení prostřednictvím požadavku HTTP GET. Stáhnout bloku zprávy z koncového bodu prostřednictvím protokolu HTTP, koncový bod musí podporovat částečného obsahu žádosti, nebo *rozdělený do bloků dat soubory ke stažení*. Pokud vaše aplikace logiky odešle požadavek HTTP GET na koncový bod pro stahování obsahu a koncový bod odpovídá "206" stavovým kódem, odpověď obsahuje bloku obsahu. Logic Apps nejde určit, jestli koncový bod podporuje částečné požadavky. Ale když svou aplikaci logiky dostane odpověď nejprve "206", aplikace logiky automaticky odesílá více požadavků, stáhnout veškerý obsah.

Pokud chcete zkontrolovat, jestli koncový bod může podporovat Částečný obsah, odešlete požadavek HEAD. Tuto žádost vám pomůže určit, zda obsahuje odpovědi `Accept-Ranges` záhlaví. Tímto způsobem, pokud koncový bod podporuje stahování bloku ale neposílá bloku obsahu můžete *navrhnout* tuto možnost nastavíte `Range` záhlaví ve vaší žádosti HTTP GET. 

Tyto kroky popisují podrobný postup, který Logic Apps se používá pro stahování obsahu bloku dat z koncového bodu do aplikace logiky:

1. Aplikace logiky odešle požadavek HTTP GET na koncový bod.

   Hlavičky žádosti může volitelně zahrnovat `Range` pole, které popisuje rozsah bajtů pro vyžádání obsahu bloků.

2. Koncový bod odpoví "206" stavový kód a text zprávy HTTP.

    Podrobnosti o obsahu v tomto bloku se zobrazí v odpovědi na `Content-Range` záhlaví, včetně informací, které Logic Apps pomáhá určit začátek a konec bloku dat a celkovou velikost celého obsahu před dělením dat do bloků.

3. Aplikace logiky odešle automaticky zpracování požadavků HTTP GET.

    Aplikace logiky odešle zpracování požadavků GET, dokud se načítá celý obsah.

Tato akce definice příkladu požadavek HTTP GET, který nastaví `Range` záhlaví. Záhlaví *navrhuje* , že koncový bod by měl odpovědět rozdělený do bloků dat obsah:

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

Požadavek na získání nastaví záhlaví "Rozsah" na "bajtů = 0 1023", což je rozsah bajtů. Pokud koncový bod podporuje požadavky pro částečný obsah, koncový bod odpoví obsahu bloku dat z požadovaný rozsah. Založené na koncový bod, mohou lišit přesný formát pro pole hlavičky "Rozsah".

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Nahrát obsah do bloků dat

Nahrát obsah bloku dat z akce HTTP, musíte akci povolili vytváření bloků podporu prostřednictvím akce `runtimeConfiguration` vlastnost. Toto nastavení povolí akce ke spuštění bloku dat protokolu. Odešlete svou aplikaci logiky můžete počáteční zprávy POST a PUT pro cílový koncový bod. Poté, co koncový bod odpoví velikost navrhované deduplikačního bloku dat, aplikace logiky sleduje odesláním žádosti HTTP PATCH, které obsahují bloky obsahu.

Tyto kroky popisují podrobný postup, který Logic Apps se používá pro nahrávání bloku obsahu vaší aplikace logiky do koncového bodu:

1. Aplikace logiky odesílá počáteční požadavek HTTP POST a PUT s prázdným textem zprávy. Hlavičky žádosti obsahuje tyto informace o obsahu, který chce, aby se aplikace logiky k nahrání do bloků dat:

   | Pole hlavičky požadavku Logic Apps | Hodnota | Typ | Popis |
   |---------------------------------|-------|------|-------------|
   | **x-ms přenos režim** | rozdělený do bloků dat | Řetězec | Označuje, že obsah se nahraje do bloků dat |
   | **x-ms-content-length** | <*Délka obsahu*> | Integer | Celý obsah velikost v bajtech před dělením dat do bloků |
   ||||

2. Koncový bod odpoví "200" stavový kód úspěchu a této volitelné informace:

   | Pole hlavičky odpovědi koncového bodu | Typ | Požaduje se | Popis |
   |--------------------------------|------|----------|-------------|
   | **x-ms-hodnota chunk-size** | Integer | Ne | Velikost bloku navrhované dat v bajtech |
   | **Umístění** | Řetězec | Ne | Adresa URL umístění kam má odesílat zprávy HTTP PATCH |
   ||||

3. Aplikace logiky vytvoří a odešle zpracování zpráv HTTP PATCH - spolu tyto informace:

   * Na základě obsahu bloku **x-ms-hodnota chunk-size** nebo některé interně počítané velikosti až všechny obsahu sčítání **x-ms-content-length** postupně nahrání

   * Následující záhlaví obsahuje informace o obsahu bloku dat odeslaných v každé zprávě opravy:

     | Pole hlavičky požadavku Logic Apps | Hodnota | Typ | Popis |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*rozsah*> | Řetězec | Rozsah bajtů u aktuálního bloku obsahu, včetně počáteční hodnotu, koncová hodnota a celková velikost obsahu, například: "bajtů = 0-1023/10100" |
     | **Content-Type** | <*Typ obsahu*> | Řetězec | Typ bloku obsahu |
     | **Content-Length** | <*Délka obsahu*> | Řetězec | Délka velikost v bajtech aktuální blok dat |
     |||||

4. Po každém požadavku PATCH potvrdí koncový bod potvrzení pro každý blok reakcí "200" stavovým kódem.

Tato akce definice příkladu požadavek HTTP POST pro nahrávání obsahu bloku do koncového bodu. V rámci akce `runTimeConfiguration` vlastnost, `contentTransfer` sady vlastností `transferMode` k `chunked`:

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