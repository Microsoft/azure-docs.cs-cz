---
title: Zpracování velkých zpráv pomocí bloků dat
description: Naučte se zpracovávat velké velikosti zpráv pomocí bloků dat v automatizovaných úlohách a pracovních postupech, které vytvoříte pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 1b23c92ec70b80a6cd08fc42a05ffec1e5b43b31
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656763"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Zpracování velkých zpráv pomocí bloků dat v Azure Logic Apps

Při zpracování zpráv Logic Apps omezuje obsah zprávy na maximální velikost. Toto omezení pomáhá snižovat režie vytvořená ukládáním a zpracováním velkých zpráv. Pokud chcete zpracovávat zprávy větší, než je toto omezení, *Logic Apps může zaznamenat* velkou zprávu do menších zpráv. Tímto způsobem můžete dál přenášet velké soubory pomocí Logic Apps za určitých podmínek. Při komunikaci s jinými službami prostřednictvím konektorů nebo protokolu HTTP může Logic Apps využívat velké zprávy, ale *pouze* v blocích. Tento stav znamená, že konektory musí podporovat i bloky dat, nebo základní výměna zpráv HTTP mezi Logic Apps a tyto služby musí používat bloky dat.

V tomto článku se dozvíte, jak můžete nastavit vytváření bloků dat pro akce, které jsou větší, než je limit. Triggery aplikace logiky nepodporují bloky dat kvůli zvýšené režii při výměně více zpráv. 

## <a name="what-makes-messages-large"></a>Co dělá zprávy "velké"?

Zprávy jsou "velké" založené na službě, která tyto zprávy zpracovává. Přesné omezení velikosti velkých zpráv se v různých Logic Apps a konektorech liší. Logic Apps i konektory nemůžou přímo spotřebovávat velké zprávy, které musí být v bloku. Omezení velikosti zpráv Logic Apps najdete v tématu [omezení Logic Apps a konfigurace](../logic-apps/logic-apps-limits-and-config.md).
Pro omezení velikosti zpráv každého konektoru se podívejte na [konkrétní technické detaily konektoru](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Zpracování bloků zpráv pro Logic Apps

Logic Apps nemůže přímo používat výstupy ze zpráv v bloku, které jsou větší než limit velikosti zprávy. K obsahu zprávy v těchto výstupech mají přístup pouze akce, které podporují bloky dat. Akce, která zpracovává velké zprávy, musí splňovat *buď* tato kritéria:

* Nativně podporuje vytváření bloků dat v případě, že tato akce patří do konektoru. 
* Podpora bloků dat povolená v konfiguraci běhu této akce. 

V opačném případě dojde k chybě za běhu při pokusu o přístup k rozsáhlému výstupu obsahu. Pokud chcete povolit vytváření bloků dat, přečtěte si téma [nastavení podpory bloků dat](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Zpracování bloků zpráv pro konektory

Služby, které komunikují s Logic Apps, můžou mít vlastní omezení velikosti zpráv. Tato omezení jsou často menší než limit Logic Apps. Například za předpokladu, že konektor podporuje bloky dat, může konektor považovat za zprávu o velikosti 30 MB, zatímco Logic Apps ne. Aby bylo dosaženo dodržování limitu tohoto konektoru, Logic Apps rozdělí všechny zprávy větší než 30 MB na menší bloky dat.

Pro konektory, které podporují dělení na bloky dat, je podkladový protokol pro zpracování dat neviditelný pro koncové uživatele. Ale ne všechny konektory podporují vytváření bloků dat, takže tyto konektory generují chyby za běhu, když příchozí zprávy překračují omezení velikosti konektorů.

> [!NOTE]
> V případě akcí, které používají bloky dat, nelze předat text triggeru ani použít výrazy jako `@triggerBody()?['Content']` v těchto akcích. Místo toho můžete pro obsah souboru text nebo JSON zkusit použít [  akci](../logic-apps/logic-apps-perform-data-operations.md#compose-action) vytvořit nebo [vytvořit proměnnou](../logic-apps/logic-apps-create-variables-store-values.md) , která tento obsah zpracuje. Pokud tělo aktivační události obsahuje další typy obsahu, jako jsou třeba mediální soubory, musíte provést další kroky, abyste mohli tento obsah zpracovat.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Nastavení bloků dat přes protokol HTTP

V obecných scénářích protokolu HTTP můžete rozdělit velký objem stahování a nahrávání přes protokol HTTP, aby vaše aplikace logiky a koncový bod mohly vyměnit velké zprávy. Je však nutné zablokovat zprávy způsobem, který Logic Apps očekává. 

Pokud koncový bod povolil ukládání do bloků dat pro stahování nebo nahrávání, akce HTTP ve vaší aplikaci logiky automaticky zablokuje velké zprávy. V opačném případě musíte na koncovém bodu nastavit podporu bloků dat. Pokud nevlastníte nebo neovládáte koncový bod nebo konektor, možná nemáte možnost nastavit bloky dat.

Také pokud akce HTTP již nepovoluje vytváření bloků dat, je nutné nastavit také dělení na sebe ve `runTimeConfiguration` Vlastnosti akce. Tuto vlastnost můžete nastavit uvnitř akce, buď přímo v editoru zobrazení kódu, jak je popsáno dále, nebo v Návrháři Logic Apps, jak je popsáno zde:

1. V pravém horním rohu akce http zvolte tlačítko se třemi tečkami (**...**) a pak zvolte **Nastavení**.

   ![V akci otevřete nabídku nastavení.](./media/logic-apps-handle-large-messages/http-settings.png)

2. V části **přenos obsahu** nastavte možnost Zapnout vytváření **bloků dat** na **zapnuto**.

   ![Zapnout vytváření bloků dat](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Pokud chcete pokračovat v nastavování bloků dat pro stahování nebo nahrávání, pokračujte v následujících oddílech.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Stažení obsahu v blocích

Mnoho koncových bodů automaticky odesílá velké zprávy do bloků dat při stažení prostřednictvím požadavku HTTP GET. Chcete-li stáhnout blokové zprávy z koncového bodu přes protokol HTTP, musí koncový bod podporovat požadavky na částečný obsah nebo *stahování pomocí bloků dat*. Když aplikace logiky odešle požadavek HTTP GET na koncový bod pro stažení obsahu a koncový bod odpoví stavovým kódem "206", odpověď obsahuje obsah v bloku. Logic Apps nemůže řídit, jestli koncový bod podporuje částečné požadavky. Nicméně, když aplikace logiky získá první odpověď "206", aplikace logiky automaticky pošle více požadavků na stažení veškerého obsahu.

Pokud chcete zjistit, jestli koncový bod může podporovat částečný obsah, pošlete žádost o hlavu. Tato žádost vám pomůže určit, jestli odpověď obsahuje `Accept-Ranges` hlavičku. V takovém případě, pokud koncový bod podporuje stahování pomocí bloků dat, ale neodesílá obsah v bloku dat, můžete tuto možnost *navrhnout* nastavením `Range` HLAVIČKY v žádosti HTTP GET. 

Tyto kroky popisují podrobný Logic Apps procesu, který se používá ke stahování obsahu v bloku dat z koncového bodu do vaší aplikace logiky:

1. Vaše aplikace logiky odešle koncový bod požadavek HTTP GET.

   Hlavička žádosti může volitelně zahrnovat `Range` pole, které popisuje rozsah bajtů pro vyžádání bloků obsahu.

2. Koncový bod odpoví kódem stavu "206" a textem zprávy HTTP.

    Podrobnosti o obsahu v tomto bloku se zobrazí v `Content-Range` hlavičce odpovědi, včetně informací, které pomáhají Logic Apps určení začátku a konce bloku a také celkové velikosti celého obsahu před vytvořením bloků dat.

3. Vaše aplikace logiky automaticky posílá následné požadavky HTTP GET.

    Vaše aplikace logiky odešle požadavky na následné získání, dokud se nenačte celý obsah.

Například tato definice akce zobrazuje požadavek HTTP GET, který nastaví `Range` hlavičku. Záhlaví *naznačuje* , že koncový bod by měl odpovídat obsahu v bloku:

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

Požadavek GET nastaví hlavičku "Range" na "byte = 0-1023", což je rozsah bajtů. Pokud koncový bod podporuje požadavky na částečný obsah, koncový bod odpoví obsahem bloku dat z požadovaného rozsahu. V závislosti na koncovém bodu se může přesný formát pole s hlavičkou "rozsah" lišit.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Nahrávání obsahu v blocích

Aby bylo možné nahrát obsah v bloku z akce HTTP, musí mít tato akce povolenou podporu bloků dat prostřednictvím `runtimeConfiguration` Vlastnosti akce. Toto nastavení povoluje akci spuštění protokolu pro zpracování dat. Vaše aplikace logiky pak může poslat počáteční zprávu POST nebo PUT do cílového koncového bodu. Po reakci koncového bodu s navrhovanou velikostí bloku se aplikace logiky dokončí odesláním požadavků na opravu HTTP, které obsahují bloky obsahu.

Tyto kroky popisují podrobný Logic Apps procesu, který se používá pro nahrávání obsahu v bloku z aplikace logiky do koncového bodu:

1. Vaše aplikace logiky pošle počáteční příspěvek HTTP nebo žádost o vložení s prázdným textem zprávy. Hlavička žádosti obsahuje tyto informace o obsahu, který vaše aplikace logiky chce nahrát do bloků:

   | Logic Apps pole s hlavičkou žádosti | Hodnota | Typ | Popis |
   |---------------------------------|-------|------|-------------|
   | **x-MS-Transfer-Mode** | blokové | Řetězec | Indikuje, že se obsah nahrává do bloků dat. |
   | **x-MS-Content-Length** | <*Content-Length*> | Integer | Celá velikost obsahu v bajtech před vytvořením bloku dat |
   ||||

2. Koncový bod odpoví kódem stavu úspěch "200" a tyto volitelné informace:

   | Pole hlavičky odpovědi koncového bodu | Typ | Povinné | Popis |
   |--------------------------------|------|----------|-------------|
   | **x-MS-bloková velikost** | Integer | Ne | Navrhovaná velikost bloku v bajtech |
   | **Umístění** | Řetězec | Ano | Umístění adresy URL, kam se mají odeslat zprávy opravy HTTP |
   ||||

3. Vaše aplikace logiky vytvoří a pošle následné zprávy opravy HTTP – každou s těmito informacemi:

   * Blok obsahu založený na **velikosti x-MS-bloků dat** nebo některé interní počítané velikosti, dokud se veškerý obsah celkově **x-MS-Content-Length** postupně nahrává

   * Tato hlavička podrobně popisuje blok obsahu odeslaný v každé zprávě opravy:

     | Logic Apps pole s hlavičkou žádosti | Hodnota | Typ | Popis |
     |---------------------------------|-------|------|-------------|
     | **Rozsah obsahu** | <*oblasti*> | Řetězec | Rozsah bajtů pro aktuální blok obsahu, včetně počáteční hodnoty, koncové hodnoty a celkové velikosti obsahu, například: "bajty = 0-1023/10100" |
     | **Typ obsahu** | <*typ obsahu*> | Řetězec | Typ obsahu v bloku |
     | **Délka obsahu** | <*Content-Length*> | Řetězec | Délka velikosti v bajtech pro aktuální blok dat |
     |||||

4. Po každé žádosti o opravu koncový bod potvrdí příjem pro jednotlivé bloky, a to tak, že odpoví na stavový kód "200" a následující hlavičky odpovědí:

   | Pole hlavičky odpovědi koncového bodu | Typ | Povinné | Popis |
   |--------------------------------|------|----------|-------------|
   | **Rozsah** | Řetězec | Ano | Rozsah bajtů pro obsah, který byl přijat koncovým bodem, například: "bytes = 0-1023" |   
   | **x-MS-bloková velikost** | Integer | Ne | Navrhovaná velikost bloku v bajtech |
   ||||

Například tato definice akce zobrazuje požadavek HTTP POST pro nahrání obsahu v bloku do koncového bodu. Ve `runTimeConfiguration` Vlastnosti akce `contentTransfer` sada vlastností nastaví `transferMode` na `chunked` :

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
