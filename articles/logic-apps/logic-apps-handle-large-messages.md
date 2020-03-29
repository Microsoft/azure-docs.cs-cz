---
title: Zpracování velkých zpráv pomocí bloků dat
description: Zjistěte, jak zpracovat velké velikosti zpráv pomocí bloků v automatizovaných úlohách a pracovních postupech, které vytvoříte pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456553"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Zpracování velkých zpráv s bloků v Aplikacích Logika Azure

Při zpracování zpráv logic apps omezuje obsah zprávy na maximální velikost. Toto omezení pomáhá snížit režii vytvořené ukládáním a zpracováním velkých zpráv. Chcete-li zpracovat zprávy větší než tento limit, logic Apps můžete *bloku* velké zprávy do menších zpráv. Tímto způsobem můžete stále přenášet velké soubory pomocí logic apps za určitých podmínek. Při komunikaci s jinými službami prostřednictvím konektorů nebo protokolu HTTP mohou aplikace logiky využívat velké zprávy, ale *pouze* v blocích. Tato podmínka znamená, že konektory musí také podporovat bloků, nebo základní výměna zpráv HTTP mezi logic apps a tyto služby musí používat bloků.

Tento článek ukazuje, jak můžete nastavit bloků pro akce zpracování zpráv, které jsou větší než limit. Aktivační události aplikace logiky nepodporují bloků z důvodu zvýšené režie výměny více zpráv. 

## <a name="what-makes-messages-large"></a>Co dělá zprávy "velké"?

Zprávy jsou "velké" na základě služby zpracování těchto zpráv. Přesný limit velikosti u velkých zpráv se liší mezi aplikacemi logiky a konektory. Aplikace logiky a konektory nelze přímo využívat velké zprávy, které musí být bloků dat. Omezení velikosti zprávy aplikace Logic Apps najdete v tématu [omezení a konfigurace aplikací logiky](../logic-apps/logic-apps-limits-and-config.md).
Pro každý konektor omezení velikosti zprávy, naleznete [v konkrétní technické podrobnosti konektoru](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Zpracování blokových zpráv pro logicaplikace

Logic Apps nelze přímo použít výstupy z bloků zpráv, které jsou větší než limit velikosti zprávy. K obsahu zprávy v těchto výstupech mají přístup pouze akce, které podporují bloků. Takže akce, která zpracovává velké zprávy, musí splňovat *buď* tato kritéria:

* Nativně podporují bloků, když tato akce patří do konektoru. 
* Mít blokovací podporu povolenou v konfiguraci runtime této akce. 

V opačném případě se při pokusu o přístup k výstupu velkého obsahu zobrazí chyba za běhu. Informace o povolení bloků dat naleznete v tématu [Nastavení podpory bloků](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Zpracování blokových zpráv pro konektory

Služby, které komunikují s Logic Apps může mít své vlastní omezení velikosti zprávy. Tato omezení jsou často menší než limit logic apps. Například za předpokladu, že konektor podporuje bloků, konektor může považovat zprávu 30 MB za velkou, zatímco logic Apps není. Chcete-li dodržet limit tohoto konektoru, logic Apps rozdělí všechny zprávy větší než 30 MB na menší bloky.

Pro konektory, které podporují bloků, základní blokování protokol je neviditelný pro koncové uživatele. Však ne všechny konektory podporují bloků, takže tyto konektory generovat chyby za běhu při příchozí zprávy překračují omezení velikosti konektorů.

> [!NOTE]
> U akcí, které používají bloků, nelze předat tělo aktivační události `@triggerBody()?['Content']` nebo použít výrazy, například v těchto akcích. Místo toho pro obsah textu nebo souboru JSON můžete zkusit použít akci [ **Napsat** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) nebo [vytvořit proměnnou](../logic-apps/logic-apps-create-variables-store-values.md) pro zpracování tohoto obsahu. Pokud tělo aktivační události obsahuje jiné typy obsahu, například mediální soubory, je třeba provést další kroky ke zpracování tohoto obsahu.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Nastavení bloků přes protokol HTTP

V obecných scénářích HTTP můžete rozdělit velké stahování obsahu a nahrávání přes HTTP, takže aplikace logiky a koncový bod můžete vyměňovat velké zprávy. Však musíte bloku zprávy tak, že logic Apps očekává. 

Pokud koncový bod povolil bloků pro stahování nebo nahrávání, akce HTTP v aplikaci logiky automaticky bloku velké zprávy. V opačném případě je nutné nastavit podporu bloků v koncovém bodě. Pokud nevlastníte nebo neovládáte koncový bod nebo spojnici, pravděpodobně nemáte možnost nastavit bloků.

Také pokud akce HTTP již nepovoluje bloků, musíte také nastavit bloků `runTimeConfiguration` ve vlastnosti akce. Tuto vlastnost můžete nastavit uvnitř akce, buď přímo v editoru zobrazení kódu, jak je popsáno později, nebo v Návrháři aplikací logiky, jak je popsáno zde:

1. V pravém horním rohu akce HTTP zvolte tlačítko se třemi tečkami (**...**) a pak zvolte **Nastavení**.

   ![V akci otevřete nabídku nastavení](./media/logic-apps-handle-large-messages/http-settings.png)

2. V části **Přenos obsahu**nastavte povolit bloků na **zapnuto** . **On**

   ![Zapnutí bloků](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Chcete-li pokračovat v nastavování bloků pro stahování nebo nahrávání, pokračujte v následujících částech.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Stahování obsahu v blocích

Mnoho koncových bodů automaticky odesílá velké zprávy v blocích při stahování prostřednictvím požadavku HTTP GET. Chcete-li stáhnout blokové zprávy z koncového bodu přes protokol HTTP, musí koncový bod podporovat požadavky na částečný obsah nebo *soubory ke stažení*. Když aplikace logiky odešle požadavek HTTP GET do koncového bodu pro stahování obsahu a koncový bod odpoví stavovým kódem "206", odpověď obsahuje blokový obsah. Logic Apps nelze řídit, zda koncový bod podporuje částečné požadavky. Však když vaše aplikace logiky získá první odpověď "206", aplikace logiky automaticky odešle více požadavků na stažení veškerého obsahu.

Chcete-li zkontrolovat, zda koncový bod může podporovat částečný obsah, odešlete požadavek HEAD. Tento požadavek vám pomůže určit, zda odpověď obsahuje `Accept-Ranges` záhlaví. Tímto způsobem pokud koncový bod podporuje blokové stahování, ale neodesílá blokovaný obsah, můžete tuto možnost *navrhnout* nastavením `Range` záhlaví v požadavku HTTP GET. 

Tyto kroky popisují podrobný proces, který aplikace Logika používá ke stahování obsahu bloku z koncového bodu do aplikace logiky:

1. Vaše aplikace logiky odešle požadavek HTTP GET do koncového bodu.

   Hlavička požadavku může volitelně obsahovat `Range` pole, které popisuje rozsah bajtů pro vyžádání bloků obsahu.

2. Koncový bod odpoví stavovým kódem "206" a textem zprávy HTTP.

    Podrobnosti o obsahu v tomto bloku `Content-Range` se zobrazí v záhlaví odpovědi, včetně informací, které pomáhá Logic Apps určit začátek a konec pro blok, plus celková velikost celého obsahu před bloků.

3. Vaše aplikace logiky automaticky odešle následné požadavky HTTP GET.

    Vaše aplikace logiky odesílá následné požadavky GET, dokud se nenačte celý obsah.

Například tato definice akce zobrazuje požadavek `Range` HTTP GET, který nastavuje záhlaví. Záhlaví *naznačuje,* že koncový bod by měl reagovat s blokovaným obsahem:

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

Požadavek GET nastaví hlavičku "Range" na "bytes=0-1023", což je rozsah bajtů. Pokud koncový bod podporuje požadavky na částečný obsah, koncový bod odpoví s blokem obsahu z požadované oblasti. Na základě koncového bodu se může lišit přesný formát pole záhlaví "Rozsah".

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Nahrání obsahu v blocích

Chcete-li odeslat blokový obsah z akce HTTP, musí mít `runtimeConfiguration` akce povolenou podporu bloků prostřednictvím vlastnosti akce. Toto nastavení umožňuje akci spustit protokol bloků. Vaše aplikace logiky pak můžete odeslat počáteční post nebo PUT zprávu do cílového koncového bodu. Poté, co koncový bod odpoví s navrhovanou velikost bloku dat, aplikace logiky následuje odesláním http patch požadavky, které obsahují bloky obsahu.

Tyto kroky popisují podrobný proces, který aplikace Logika používá pro nahrávání obsahu bloků z aplikace logiky do koncového bodu:

1. Aplikace logiky odešle počáteční požadavek HTTP POST nebo PUT s prázdným textem zprávy. Hlavička požadavku obsahuje tyto informace o obsahu, který chce aplikace logiky nahrát v blocích:

   | Pole záhlaví požadavku aplikace Logika | Hodnota | Typ | Popis |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | Blokového | Řetězec | Označuje, že obsah je odeslán v blocích. |
   | **x-ms-content-length** | <*délka obsahu*> | Integer | Celá velikost obsahu v bajtech před bloků |
   ||||

2. Koncový bod reaguje se stavovým kódem úspěchu "200" a těmito volitelnými informacemi:

   | Pole hlavičky odpovědi koncového bodu | Typ | Požaduje se | Popis |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Ne | Navrhovaná velikost bloku v bajtech |
   | **Umístění** | Řetězec | Ano | Umístění adresy URL, kam má být odesláno zprávy HTTP PATCH |
   ||||

3. Aplikace logiky vytvoří a odešle následné zprávy HTTP PATCH – každá s těmito informacemi:

   * Blok dat obsahu založený na **velikosti x-ms-chunk** nebo některé interně vypočtené velikosti, dokud není postupně odeslán veškerý obsah o **celkové délce obsahu x-ms**

   * Tyto podrobnosti záhlaví o bloku obsahu odeslané v každé zprávě PATCH:

     | Pole záhlaví požadavku aplikace Logika | Hodnota | Typ | Popis |
     |---------------------------------|-------|------|-------------|
     | **Rozsah obsahu** | <*Rozsah*> | Řetězec | Rozsah bajtů pro aktuální blok obsahu, včetně počáteční hodnoty, koncové hodnoty a celkové velikosti obsahu, například: "bytes=0-1023/10100" |
     | **Typ obsahu** | <*typ obsahu*> | Řetězec | Typ blokovaného obsahu |
     | **Délka obsahu** | <*délka obsahu*> | Řetězec | Délka velikosti v bajtů aktuálního bloku dat |
     |||||

4. Po každém požadavku PATCH koncový bod potvrdí příjem pro každý blok tím, že odpoví pomocí stavového kódu "200" a následující chouje na hlavičky odpovědí:

   | Pole hlavičky odpovědi koncového bodu | Typ | Požaduje se | Popis |
   |--------------------------------|------|----------|-------------|
   | **Rozsah** | Řetězec | Ano | Rozsah bajtů pro obsah, který byl přijat koncovým bodem, například: "bytes=0-1023" |   
   | **x-ms-chunk-size** | Integer | Ne | Navrhovaná velikost bloku v bajtech |
   ||||

Tato definice akce například zobrazuje požadavek HTTP POST pro nahrávání blokovaného obsahu do koncového bodu. Ve `runTimeConfiguration` vlastnosti akce vlastnost `contentTransfer` nastaví `transferMode` `chunked`na :

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
