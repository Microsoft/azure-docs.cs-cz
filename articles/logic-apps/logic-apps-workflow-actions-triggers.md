---
title: "Pracovní postup triggery a akce - Azure Logic Apps | Microsoft Docs"
description: "Další informace o aktivační události a akce vytvoření automatizované pracovní postupy a procesy s logic apps"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: af30fd30f389cdc2070c45ae3b6e2cb1165239e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Triggery a akce pro pracovní postupy aplikace logiky

Všechny aplikace logiky začínat aktivační událost následuje akce. Tento článek popisuje typy triggerů a akce, které můžete použít pro vytváření systému integrace a automatizace pracovních firmy nebo procesy podle budovy aplikace logiky. 
  
## <a name="triggers-overview"></a>Přehled aktivační události 

Všechny aplikace logiky začínat aktivační událost, která určuje volání, které lze spustit spusťte aplikaci logiky. Toto jsou typy služby aktivačních událostí, které můžete použít:

* A *dotazování* aktivační události, které slouží k ověření koncového bodu protokolu HTTP služby v pravidelných intervalech
* A *nabízené* aktivovat, který volá [rozhraní API REST služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)
  
Žádná z aktivačních událostí obsahovat tyto prvky nejvyšší úrovně:  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Typy aktivační události a vstupy  

Každý typ aktivační událost má jiné rozhraní a jiné *vstupy* své chování, který definuje. 

| Typ aktivace | Popis | 
| ------------ | ----------- | 
| **Opakování** | Aktivuje se podle definovaného plánu. Můžete nastavit budoucí datum a čas pro ohlásí této aktivační události. Na základě četnosti, můžete také určit časy a dny pro spuštění pracovního postupu. | 
| **Požadavek**  | Umožňuje aplikaci logiky do koncový bod, který bude možné volat, také známé jako "Ruční" aktivační událost. | 
| **HTTP** | Kontroluje, nebo *hlasování*, koncový bod webové HTTP. Koncový bod HTTP musí odpovídat kontraktu konkrétní spouštěcí pomocí "202" asynchronní vzor nebo vrácením pole. | 
| **ApiConnection** | Dotazuje jako aktivační procedury protokolu HTTP, ale používá [Microsoft spravovaná rozhraní API](../connectors/apis-list.md). | 
| **HTTPWebhook** | Umožňuje aplikaci logiky do koncový bod volatelná aplikacemi, jako **požadavku** aktivovat, ale volá zadaná adresa URL pro registraci a zrušení registrace. |
| **ApiConnectionWebhook** | Funguje, jako **HTTPWebhook** aktivační událost, ale používá rozhraní API pro spravovaný společností Microsoft. | 
||| 

Další informace najdete v tématu [jazyk definic workflowů](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Aktivační událost opakování  

Této aktivační události běží podle plánu a způsob opakování, které zadáte a poskytuje snadný způsob pro pravidelné spouštění pracovního postupu. 

Zde je příklad aktivační událost základní opakování, který spouští každý den:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Můžete také naplánovat počáteční datum a čas pro ohlásí aktivační událost. Například spusťte sestavu týdenní každé pondělí, můžete naplánovat aplikaci logiky ke spuštění na konkrétní pondělí jako tento ukázkový: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Zde je definice této aktivační události:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Ano | Řetězec | Jednotka času pro jak často se aktivuje aktivační událost. Použít pouze jednu z těchto hodnot: "druhý", "minutu", "hodina", "dne", "týden" nebo "měsíc" | 
| interval | Ano | Integer | Kladné celé číslo, které popisuje, jak často pracovní postup bude spuštěn na základě četnosti. <p>Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "měsíc", pak opakování je každých 6 měsíců. | 
| timeZone | Ne | Řetězec | Platí, pouze pokud zadáte počáteční čas protože této aktivační události není přijmout [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Zadejte časové pásmo, které chcete použít. | 
| startTime | Ne | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p>RRRR-MM-ddTHH Pokud zadáte časové pásmo <p>-nebo- <p>RRRR-MM-ddTHH Pokud nezadáte časové pásmo <p>Tak například, pokud chcete 18 září 2017 na 2:00 PM, zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, jako je například "Tichomoří (běžný čas). Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** tento čas zahájení musí následovat [ISO 8601 datum čas specifikace](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nezadáte časové pásmo, musíte přidat písmeno "Z" bez žádné mezery na konci. Tento "Z" odkazuje na ekvivalent [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány, čas spuštění je první výskyt, zatímco pro komplexní plány, aktivační událost není fire všechny dřív, než čas spuštění. Další informace o počáteční data a časy, najdete v tématu [vytvořit a plán pravidelně spuštěné úkoly](../connectors/connectors-native-recurrence.md). | 
| weekDays | Ne | Řetězec nebo pole řetězců | Pokud zadáte "Týden" pro `frequency`, můžete zadat jeden nebo více dní, oddělených čárkami, pokud chcete spustit pracovní postup: "Pondělí", "Úterý", "Středa", "Čtvrtek", "Pátek", "Sobota" a "Neděle" | 
| hours | Ne | Celé číslo nebo číslo pole | Pokud zadáte "Dne" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 23, oddělených čárkami, jako hodin dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "10", "12" a "14", získáte 10 AM, 12 PM a 14: 00 jako značky hodinu. | 
| minutes | Ne | Celé číslo nebo číslo pole | Pokud zadáte "Den" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 59, oddělených čárkami, jako počet minut za hodinu, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako minutu značky a použijeme předchozí příklad hodin dne, získáte 10:30 AM, 12:30:00 a 14:30:00. | 
||||| 

Například této aktivační události opakování určuje, že aplikace logiky spouští každý týden každé pondělí v 10:30 AM, 12:30:00 a 14:30:00 Tichomoří (běžný čas), od dříve než 9 září 2017 ve 2:00:

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

Další informace se opakování a příklady čas spuštění této aktivační události najdete v tématu [vytvořit a plán pravidelně spuštěné úkoly](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Žádost o aktivační události

Této aktivační události slouží jako koncový bod, který můžete použít pro volání aplikace logiky prostřednictvím požadavku HTTP. Aktivační událost požadavku vypadá v tomto příkladu:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Této aktivační události je volitelná vlastnost s názvem `schema`:
  
| Název elementu | Požaduje se | Typ | Popis |
| ------------ | -------- | ---- | ----------- |
| schema | Ne | Objekt | Schéma JSON, který ověří příchozí žádost. Užitečné pomáháte kroky následné pracovního postupu vědět, vlastnosti, které chcete odkazovat. | 
||||| 

K vyvolání této aktivační události jako koncový bod, je třeba volat `listCallbackUrl` rozhraní API. V tématu [rozhraní API REST služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Trigger HTTP  

Této aktivační události dotazuje zadaný koncový bod a zkontroluje odpověď na zjistit, zda by měl pracovní postup spuštěn, nebo ne. Zde `inputs` objekt trvá těchto parametrů požadovaných pro tvorbu volání protokolu HTTP: 

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| metoda | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| identifikátor uri | Ano| Řetězec | Protokolu HTTP nebo HTTPs koncový bod, který kontroluje, aktivační události. Maximální velikost řetězce: 2 KB | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| Ověřování | Ne | Objekt | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). <p>Nad Scheduler, existuje více podporované jednu vlastnost: `authority`. Ve výchozím nastavení, tato hodnota je `https://login.windows.net` není-li zadána, ale můžete použít jinou hodnotu, jako například`https://login.windows\-ppe.net`. | 
||||| 

A *zásady opakování* platí pro náhodnými poruchami, vyznačují jako stavové kódy HTTP 408 429 a 5xx kromě všechny výjimky připojení. Můžete definovat tyto zásady se `retryPolicy` objektu, jak je vidět tady:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Chcete-li funkce fungují dobře u aplikace logiky, triggeru protokolu HTTP vyžaduje rozhraní API HTTP tak, aby odpovídala pomocí specifického vzoru. Aktivační událost rozpoznává tyto vlastnosti:  
  
| Odpověď | Požaduje se | Popis | 
| -------- | -------- | ----------- |  
| Stavový kód | Ano | Spuštění způsobí, že stavovým kódem 200 ("OK"). Další kód stavu nezpůsobí spustit. | 
| Opakovat po záhlaví | Ne | Počet sekund do aplikace logiky dotazuje koncový bod znovu. | 
| Hlavička umístění | Ne | Adresa URL pro volání při dalším intervalu dotazování. Pokud není zadaný, použije se původní adresu URL. | 
|||| 

Zde jsou některé příklad chování pro různé typy požadavků:
  
| Kód odpovědi | Opakovat po | Chování | 
| ------------- | ----------- | -------- | 
| 200 | {none} | Spusťte pracovní postup, a poté znovu zkontrolujte pro další data po definované opakování. | 
| 200 | 10 sekund | Spusťte pracovní postup, a poté znovu zkontrolujte pro další data po 10 sekundách. |  
| 202 | 60 sekund | Nemáte spustit pracovní postup. Další pokus se stane za jednu minutu, podstoupí definované opakování. Pokud je definovaný opakování je menší než jedna minuta, záhlaví opakovat po přednost. Jinak je použít definované opakování. | 
| 400 | {none} | Chybný požadavek, nespouštět pracovního postupu. Pokud žádné `retryPolicy` je definován, je použita výchozí zásady. Po překročení počet opakovaných pokusů, aktivační událost zjišťuje znovu data po definované opakování. | 
| 500 | {none}| Chyba serveru nelze spustit pracovní postup. Pokud žádné `retryPolicy` je definován, je použita výchozí zásady. Po překročení počet opakovaných pokusů, aktivační událost zjišťuje znovu data po definované opakování. | 
|||| 

Zde jsou výstupy aktivace protokolu HTTP: 
  
| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt | Hlavičky HTTP odpovědi | 
| hlavní část | Objekt | Text odpovědi HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Aktivační událost APIConnection  

V základních funkcí této aktivační události funguje jako triggeru protokolu HTTP. Parametry pro identifikaci akce se ale liší. Zde naleznete příklad:   
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| hostitel | Ano | Objekt | Hostované brány a ID pro aplikaci API | 
| metoda | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| Ověřování | Ne | Objekt | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Pro `host` objektu, tady jsou vlastnosti:  
  
| Název elementu | Požaduje se | Popis | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | Ano | Koncový bod pro spravované rozhraní API | 
| Název připojení |  | Název připojení spravované rozhraní API, které používá pracovní postup. Parametr s názvem musí odkazovat na `$connection`. |
|||| 

A *zásady opakování* platí pro náhodnými poruchami, vyznačují jako stavové kódy HTTP 408 429 a 5xx kromě všechny výjimky připojení. Můžete definovat tyto zásady se `retryPolicy` objektu, jak je vidět tady:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Zde jsou výstupy pro aktivační procedury pro připojení k rozhraní API:
  
| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt | Hlavičky HTTP odpovědi | 
| hlavní část | Objekt | Text odpovědi HTTP | 
|||| 

Další informace o [jak ceny funguje pro připojení k rozhraní API aktivuje](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>Aktivační událost HTTPWebhook  

Této aktivační události obsahuje koncový bod, podobně jako `Request` aktivační událost, ale aktivační událost HTTPWebhook také voláním zadaná adresa URL pro registraci a zrušení registrace. Tady je příklad, jak může vypadat aktivační procedury HTTPWebhook:

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

Mnoho z těchto částí je volitelné a chování aktivační událost HTTPWebhook závisí na oddíly, které poskytují nebo vynechejte. Zde jsou vlastnosti pro aktivační událost HTTPWebhook:
  
| Název elementu | Požaduje se | Popis | 
| ------------ | -------- | ----------- |  
| přihlášení k odběru | Ne | Určuje odchozí žádost volat při aktivační událost se vytvoří a provede počáteční registrace. | 
| Odhlásit | Ne | Určuje odchozí žádost volat při aktivační událost je odstranit. | 
|||| 

Můžete zadat omezení na aktivační události webhooku stejným způsobem jako [HTTP asynchronní omezení](#asynchronous-limits). Zde jsou další informace o `subscribe` a `unsubscribe` akce:

* `subscribe` je volána, aby aktivační událost můžete zahájit naslouchání na události. Toto volání odchozí spustí se stejnými parametry jako standardní akce HTTP. Toto volání se stane, když pracovní postup změny žádným způsobem, například když přihlašovací údaje jsou vráceny, nebo změňte vstupní parametry aktivační události. 
  
  Pro podporu tohoto volání `@listCallbackUrl()` funkce vrátí jedinečnou adresu URL pro tuto konkrétní aktivační událost v pracovním postupu. Tato adresa URL představuje jedinečný identifikátor pro koncové body, které používají rozhraní API služby REST.
  
* `unsubscribe` je volána automaticky, když operace vykreslí této aktivační události neplatná, včetně těchto operací:

  * Odstranit nebo zakázat aktivační událost. 
  * Odstranit nebo zakázat pracovní postup. 
  * Odstranit nebo zakázat odběr. 
  
  Parametry této funkce jsou stejné jako triggeru protokolu HTTP.

Tady jsou výstupy z HTTPWebhook aktivovat a jsou příchozí žádost o:
  
| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt | Hlavičky HTTP odpovědi | 
| hlavní část | Objekt | Text odpovědi HTTP | 
|||| 

## <a name="triggers-conditions"></a>Aktivační události: podmínky

Pro všechny aktivační události můžete jednu nebo víc podmínek k určení, zda se má pracovní postup spustit nebo ne. V tomto příkladu pouze aktivační události sestavy při pracovního postupu `sendReports` parametr je nastaven na hodnotu true. 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Nakonec můžete odkazovat na podmínky stavový kód aktivační události. Můžete například spustit pracovní postup, pouze v případě, že váš web vrátí stavový kód 500:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> Ve výchozím nastavení, aktivační událost aktivuje pouze na příjem "200 OK" odpovědi. Když výraz odkazuje aktivační událost stavový kód žádným způsobem, se nahradí výchozí chování aktivační události. Ano Pokud chcete, aby aktivační událost podle víc stavových kódů, třeba ještě efektivněji, stavovým kódem 200 a stavový kód 201, musí obsahovat tento příkaz jako vaše podmínky: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Aktivační události: Pole s více spustí zpracovat

Pokud aktivační událost vrátí pole pro svou aplikaci logiky ke zpracování, někdy smyčka "pro každou" může trvat příliš dlouho zpracování každé položky pole. Místo toho můžete použít **SplitOn** vlastnost ve vaší aktivační *debatch* pole. 

Debatching rozdělí si položky pole a spustí novou instanci aplikace logiky spuštěná pro každou položku pole. Tento přístup je užitečné, například pokud chcete dotazování koncový bod, který může vrátit více nové položky mezi intervaly cyklického dotazování.
Pro maximální počet pole položek, které **SplitOn** můžete zpracovat spuštění jedné logiku aplikace najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Můžete přidat **SplitOn** pouze pro aktivační události ruční definování nebo přepsání v zobrazení kódu pro definici aplikace logiky JSON. Nemůžete použít **SplitOn** když chcete implementovat vzor synchronní odpovědi. Jakýkoli pracovní postup, který používá **SplitOn** a obsahuje odpovědi na akce spustí asynchronně a, ihned odešle `202 ACCEPTED` odpovědi.

Pokud soubor Swagger vaše aktivační událost popisuje datové části, která je pole, **SplitOn** vlastnost se automaticky přidá do vašeho aktivační události. Jinak přidejte tuto vlastnost uvnitř datové části odpovědi, která má pole, které chcete debatch. 

Předpokládejme například, že máte rozhraní API, které vrátí této odpovědi: 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
Aplikace logiky, stačí obsah z `Rows`, takže si můžete vytvořit aktivační událost jako tento ukázkový.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Pokud použijete `SplitOn` příkaz, nelze získat vlastnosti, které jsou mimo pole. Takže například nelze získat `status` vrácena vlastnost v odpovědi z rozhraní API.
> 
> Aby nedošlo k selhání, pokud `Rows` vlastnost neexistuje, tento příklad používá `?` operátor.

Teď můžete použít svou definici pracovního postupu `@triggerBody().name` získat `customer-name-one` z během prvního spuštění a `customer-name-two` z druhé spustit. Ano, aktivační událost výstupy vzhled jako tyto příklady:

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Aktivační události: Ještě efektivněji, které dokončit pouze po všechny aktivní spustí

Aktivuje opakování můžete nakonfigurovat tak, aby se aktivují, pouze když byly dokončeny všechny aktivní spustí. Chcete-li nakonfigurovat tato nastavení, nastavte `operationOptions` vlastnost `singleInstance`:

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

Pokud naplánované opakování se stane, když je spuštěna instance pracovního postupu, aktivační událost přeskočí a čeká, až další interval opakování naplánované zkontrolujte znovu.

## <a name="actions-overview"></a>Akce – přehled

Existuje mnoho typů akcí, každý s jedinečný chování. Každý typ akce má jiné vstupy, které definují chování akci. Akce kolekce může obsahovat mnoho dalších akcí v rámci sami. 

### <a name="standard-actions"></a>Standardní akce  

| Typ akce | Popis | 
| ----------- | ----------- | 
| **HTTP** | Zavolá koncový bod webové HTTP. | 
| **ApiConnection**  | Funguje jako akce HTTP, ale používá [Microsoft spravovaná rozhraní API](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Funguje jako HTTPWebhook, ale používá rozhraní API pro spravovaný společností Microsoft. | 
| **Odpověď** | Definuje odpověď pro příchozí volání. | 
| **Vytvoření** | Vytvoří objekt libovolný z akce vstupy. | 
| **Funkce** | Představuje Azure funkce. | 
| **Počkej** | Počká pevnou hodnotu čas, nebo dokud určitý čas. | 
| **Workflow** | Představuje vnořený pracovní postup. | 
| **Vytvoření** | Vytvoří objekt libovolný z akce vstupy. | 
| **Dotaz** | Filtry založené na podmínce pole. | 
| **Výběr** | Projekty každý element pole na novou hodnotu. Například můžete převést pole čísla do pole objektů. | 
| **Tabulka** | Převede pole položek do tabulky CSV nebo HTML. | 
| **Ukončení** | Zastaví pracovní postup. | 
| **Počkej** | Počká pevnou hodnotu čas, nebo dokud určitý čas. | 
| **Workflow** | Představuje vnořený pracovní postup. | 
||| 

### <a name="collection-actions"></a>Kolekce akce

| Typ akce | Popis | 
| ----------- | ----------- | 
| **If** | Vyhodnocení výrazu a na základě výsledku, spustí odpovídající firemní pobočky. | 
| **Switch** | Proveďte různé akce, které jsou založené na konkrétní hodnoty objektu. | 
| **ForEach** | Tato akce opakování iteruje v rámci pole a provede vnitřní akce na každou položku pole. | 
| **dokud** | Tato opakování akce provede vnitřní akce, dokud podmínku výsledků na hodnotu true. | 
| **Rozsah** | Použití pro logicky seskupování dalších akcí. | 
|||  

## <a name="http-action"></a>Akce HTTP  

Akce HTTP volá zadaný koncový bod a zkontroluje odpověď na zjistit, zda by měl pracovní postup spuštěn, nebo ne. Příklad:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

Zde `inputs` objekt trvá těchto parametrů požadovaných pro tvorbu volání protokolu HTTP: 

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| metoda | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| identifikátor uri | Ano| Řetězec | Protokolu HTTP nebo HTTPs koncový bod, který kontroluje, aktivační události. Maximální velikost řetězce: 2 KB | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). <p>Nad Scheduler, existuje více podporované jednu vlastnost: `authority`. Ve výchozím nastavení, tato hodnota je `https://login.windows.net` není-li zadána, ale můžete použít jinou hodnotu, jako například`https://login.windows\-ppe.net`. | 
||||| 

Akce HTTP a APIConnection akce, které podporují *opakujte zásady*. Zásady opakování platí pro náhodnými poruchami, vyznačují jako stavové kódy HTTP 408 429 a 5xx kromě všechny výjimky připojení. Můžete definovat tyto zásady se `retryPolicy` objektu, jak je vidět tady:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Tento příklad akce HTTP opakovat, pokud existují občasné chyby celkem tři spuštěních a 30 sekund zpoždění mezi jednotlivými pokusy o načítání dvakrát nejnovější informace:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

Interval opakování je uveden v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Tento interval má výchozí a minimální hodnotu 20 sekund, zatímco maximální hodnota je jedna hodina. Výchozí a maximální počet opakování je čtyři hodiny. Pokud nezadáte definici zásady opakování `fixed` strategie se používá s výchozí hodnoty a interval opakování. Chcete-li zakázat zásady opakovaných pokusů, nastavte její typ na `None`.

### <a name="asynchronous-patterns"></a>Asynchronními vzory

Ve výchozím nastavení všechny akce založené na protokolu HTTP podporují vzor standardní asynchronní operaci. Takže pokud vzdáleného serveru znamená, že je požadavek přijaté ke zpracování odpovědi "202 platných", modul Logic Apps udržuje dotazování adresa URL zadaná v odpovědi na umístění záhlaví až do dosažení stavu terminálu, který je odpovědí bez 202.
  
Chcete-li zakázat asynchronní chování se popisuje výš, nastavte `operationOptions` k `DisableAsyncPattern` v vstupy akce. Výstup akce v tomto případě je založená na počáteční 202 odpověď ze serveru. Příklad:
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Asynchronní omezení

Můžete omezit dobu trvání pro asynchronní vzor na určitém časovém intervalu. Pokud časový interval uplynutí bez dosažení stavu terminálu, že stav akce označená `Cancelled` s `ActionTimedOut` kódu. Časový limit omezení je zadána ve formátu ISO 8601. Tento příklad ukazuje, jak můžete zadat omezení:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection akce

Tato akce odkazuje konektor spravovaný společností Microsoft, vyžadování odkaz na platné připojení a informace o rozhraní API a parametry. Tady je příklad akce APIConnection:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| hostitel | Ano | Objekt | Představuje informace o konektoru, jako `runtimeUrl` a odkaz na objekt připojení. | 
| metoda | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| path | Ano | Řetězec | Cesta pro operace rozhraní API | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Zásady opakování platí pro náhodnými poruchami, vyznačují jako stavové kódy HTTP 408 429 a 5xx kromě všechny výjimky připojení. Můžete definovat tyto zásady se `retryPolicy` objektu, jak je vidět tady:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>Akce webhooku APIConnection

Akce APIConnectionWebhook odkazuje konektor spravovaný společností Microsoft. Tato akce vyžaduje odkaz na platné připojení a informace o rozhraní API a parametry. Můžete zadat omezení na akce webhooku stejným způsobem jako [HTTP asynchronní omezení](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| hostitel | Ano | Objekt | Představuje informace o konektoru, jako `runtimeUrl` a odkaz na objekt připojení. | 
| path | Ano | Řetězec | Cesta pro operace rozhraní API | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Akce odpovědi  

Tato akce obsahuje datové části celé odpovědi z požadavku HTTP a zahrnuje `statusCode`, `body`, a `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

Akce odpovědi má zvláštní omezení, která se nevztahují na jiné akce, konkrétně:  
  
* Akce reagující nemůže mít v paralelních větvích v definici aplikace logiky, protože příchozí požadavek vyžaduje deterministickou odpověď.
  
* Pokud pracovní postup dosáhne akce odpovědi po příchozí žádost již přijata odpověď, akce odpovědi považuje za neúspěšné nebo je v konfliktu. V důsledku toho je označena spusťte aplikaci logiky `Failed`.
  
* Nelze použít pracovního postupu s akce reagující `splitOn` příkazů v definici aktivační událost, protože volání vytvoří několik spustí. V důsledku toho zkontrolujte pro tento případ, když je pracovní postup operaci PUT a vrátí odpověď "Chybný požadavek".

## <a name="compose-action"></a>Vytvořit akce

Tato akce umožňuje vytvořit libovolný objekt a výstupem je výsledkem vyhodnocení akce vstupy. 

> [!NOTE]
> Můžete použít `Compose` akce pro tvorbu žádný výstup, včetně objektů, pole a jiný typ nativně podporuje logiku aplikace jako soubor XML a binární.

Například můžete použít `Compose` akce pro slučování výstupy z více akcí:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Akce – funkce

Tato akce vám umožní představují a volání [Azure funkce](../azure-functions/functions-overview.md), například:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- |  
| id – funkce | Ano | Řetězec | ID prostředku pro funkci Azure, kterou chcete volat. | 
| metoda | Ne | Řetězec | Metoda HTTP, který se používá k volání funkce. Pokud není zadaný, "POST" je výchozí metodou. | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
|||||

Při ukládání aplikace logiky, modul Logic Apps provádí některé kontroly na odkazované funkce:

* Musíte mít přístup k funkci.
* Můžete použít pouze standardní triggeru protokolu HTTP nebo obecné aktivační události Webhooku JSON.
* Funkce by neměl mít všechny trasy definované.
* Jsou povoleny pouze "funkce" a "anonymní" autorizace úrovně.

> [!NOTE]
> Modul Logic Apps načítá a ukládá do mezipaměti adresu URL aktivační událost, která se používá za běhu. Takže pokud všechny operace by způsobila neplatnost adresu URL v mezipaměti, akce selže za běhu. Chcete-li tento problém obejít, uložte aplikaci logiky znovu, což způsobí, že aplikace logiky načíst a znovu mezipaměti adresu URL aktivační událost.

## <a name="select-action"></a>Vyberte akci

Tato akce umožňuje projektu každý element pole na novou hodnotu. Tento příklad převede pole čísel na pole objektů:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| od | Ano | Pole | Zdrojové pole |
| vybrat | Ano | Všechny | Projekce použít pro každý prvek v poli zdroje |
||||| 

Výstup `select` pole, které má stejné mohutnost jako vstupní pole není akce. Každý prvek převede podle definice `select` vlastnost. Pokud vstup je prázdné pole, je výstup také prázdné pole.

## <a name="terminate-action"></a>Ukončit akce

Tato akce zastaví spuštění pracovního postupu zrušení všechny akce v průběhu a přeskočení všechny zbývající akce. Akce ukončení neovlivní již dokončených akcí.

Chcete-li například zastavit spuštění, který má `Failed` stavu:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Ano | Řetězec | Cílových spouštění je stav, který je buď `Failed` nebo `Cancelled` |
| runError | Ne | Objekt | Podrobnosti o chybě. Podporované pouze tehdy, když `runStatus` je nastaven na `Failed`. |
| runError kódu | Ne | Řetězec | Kód chyby spuštění |
| zpráva runError | Ne | Řetězec | Chybová zpráva spuštění | 
||||| 

## <a name="query-action"></a>Akce dotazu

Tato akce umožňuje filtrovat pole na základě podmínky. 

> [!NOTE]
> Vytvářené akci nelze použít k vytvoření žádný výstup, včetně objektů, pole a jiný typ nativně podporuje logiku aplikace jako soubor XML a binární.

Chcete-li například vyberte čísla větší než 2:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| od | Ano | Pole | Zdrojové pole |
| kde | Ano | Řetězec | Podmínku, která se použije pro každý element ze zdrojové pole. Pokud žádné hodnoty odpovídají `where` podmínka, výsledek je prázdné pole. |
||||| 

Výstup `query` akce je pole, které má elementy ze vstupní pole, které splňují zadanou podmínku.

## <a name="table-action"></a>Tabulka akcí

Tato akce umožňuje převod pole do tabulky CSV nebo HTML. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| od | Ano | Pole | Zdrojové pole. Pokud `from` hodnota vlastnosti je prázdné pole, výstup je prázdná tabulka. | 
| Formát | Ano | Řetězec | Formát tabulky, který chcete, "CSV" nebo "HTML" | 
| sloupce | Ne | Pole | Sloupce tabulky, které chcete. Slouží k přepisu výchozí tabulky tvar. | 
| záhlaví sloupce | Ne | Řetězec | Záhlaví sloupce | 
| Hodnota sloupce | Ano | Řetězec | Hodnota sloupce | 
||||| 

Předpokládejme, že definujete tabulka akcí, jako v tomto příkladu:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

A používat toto pole pro `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Toto je výstup z tohoto příkladu:

<table><thead><tr><th>ID</th><th>Název</th></tr></thead><tbody><tr><td>0</td><td>jablka</td></tr><tr><td>1</td><td>Pomeranče</td></tr></tbody></table>

Chcete-li přizpůsobit tuto tabulku, můžete určit sloupce, které explicitně, například:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Toto je výstup z tohoto příkladu:

<table><thead><tr><th>Vytvoření ID</th><th>Popis</th></tr></thead><tbody><tr><td>0</td><td>Čerstvá jablka</td></tr><tr><td>1</td><td>čerstvé pomeranče</td></tr></tbody></table>

## <a name="wait-action"></a>Počkejte akce  

Tato akce pozastaví spuštění pracovního postupu po zadanou dobu. Tento příklad pracovní postup se 15 minut, než:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Alternativně počkejte chvilku konkrétní v čase, můžete v tomto příkladu:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> U buď můžete určit dobu čekání `interval` objekt nebo `until` objektu, ale ne obojí.

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| do | Ne | Objekt | Doba čekání na základě v bodě v čase | 
| dokud časové razítko | Ano | Řetězec | Do bodu v čase v [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) vypršení platnosti čekání | 
| interval | Ne | Objekt | Doba čekání na základě jednotku intervalu a počet | 
| jednotku intervalu | Ano | Řetězec | Jednotka času. Použít pouze jednu z těchto hodnot: "druhý", "minutu", "hodina", "dne", "týden" nebo "měsíc" | 
| počet intervalu | Ano | Integer | Kladné celé číslo představující počet jednotek intervalu používat po celou dobu čekání | 
||||| 

## <a name="workflow-action"></a>Akce pracovního postupu

Tato akce umožňuje vnořit pracovního postupu. Modul Logic Apps provede kontrolu přístupu na podřízené pracovní postup, přesněji řečeno, aktivační událost, takže musíte mít přístup k podřízeného pracovního postupu. Příklad:

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- |  
| id hostitele | Ano | Řetězec| ID prostředku pro pracovní postup, který chcete volání | 
| Název aktivační události hostitele | Ano | Řetězec | Název aktivační události, kterou chcete vyvolání | 
| Dotazy | Ne | Objekt | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt | Představuje datovou část, která je odeslána koncovému bodu. | 
||||| 

Tato akce výstupy jsou založené na definovat v `Response` akce pro podřízené pracovní postup. Pokud se nedefinuje podřízeného pracovního postupu `Response` akce, výstupy jsou prázdné.

## <a name="collection-actions-overview"></a>Přehled akce kolekce

Abyste řízení provádění pracovního postupu, akce kolekce může obsahovat další akce. Můžete odkazovat přímo k odkazování na akce v kolekci mimo kolekce. Například pokud definujete `Http` akce v oboru, pak `@body('http')` je stále platný kdekoli v pracovním postupu. Také akce v kolekci můžete jenom "spustit" dalších akcí ve stejné kolekci.

## <a name="if-action"></a>Pokud se akce

Tato akce, která je podmíněného prohlášení, umožňuje vyhodnotit podmínku a provést větev založené na tom, jestli se výraz vyhodnotí jako true. Pokud je podmínka vyhodnocena jako true úspěšně, podmínky je označen "Succeeded". Akce, které jsou v `actions` nebo `else` objekty být tyto hodnoty:

* "Bylo úspěšně dokončeno" při jejich spuštění a úspěšné
* "Se nezdařilo" při jejich spuštění a selhání
* "" Při přeskočen nefunguje odpovídající firemní pobočky

Další informace o [podmíněné příkazy v aplikacích logiky](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| Akce | Ano | Objekt | Vnitřní akce, které spustí, když `expression` vyhodnocen `true` | 
| výraz | Ano | Řetězec | Výraz k vyhodnocení |
| else | Ne | Objekt | Vnitřní akce, které spustí, když `expression` vyhodnocen `false` |
||||| 

Příklad:

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Jak podmínky použití výrazů v akce

Zde jsou některé příklady, které ukazují, jak můžete použít výrazy v podmínkách:
  
| Výraz JSON | Výsledek | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Libovolná hodnota, která vyhodnotí jako true způsobí, že tato podmínka předat. Podporuje pouze výrazy logických hodnot. Chcete-li jiné typy převést na logickou hodnotu, použijte tyto funkce: `empty` nebo `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Podporuje funkce porovnání. V tomto příkladu akci spustí pouze v případě, že výstupem action1 je větší než prahová hodnota. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Podporuje funkce logiku pro vytvoření vnořené výrazy logických hodnot. V tomto příkladu spustí akci v případě, že výstupem action1 je více než prahová hodnota nebo pod 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Pokud chcete zkontrolovat, zda pole má všechny položky, můžete použít funkce pole. V tomto příkladu spustí akci při chybách pole je prázdné. | 
| `"expression": "parameters('hasSpecialAction')"` | Tento výraz způsobí chybu a není platný stav. Musíte použít podmínek "@" symbol. | 
||| 

## <a name="switch-action"></a>Přepínač akce

Tato akce, který je příkaz switch, provádí různé akce podle konkrétní hodnoty objektu, výraz nebo token. Tato akce vyhodnocena jako objekt, výraz nebo token, vybere případu, který odpovídá výsledek a spouští akce pro pouze v takovém případě. Když žádném případě odpovídá výsledek, spustí se výchozí akci. Po spuštění příkazu switch pouze jeden případ by měl odpovídat výsledek. Další informace o [přepínač příkazy ve aplikace logiky](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| výraz | Ano | Řetězec | Objekt, výraz nebo tokenu k vyhodnocení | 
| Případy | Ano | Objekt | Obsahuje sadu vnitřní akce, které běží na základě na výsledek výrazu. | 
| Případ | Ano | Řetězec | Hodnota, která má odpovídat výsledek | 
| Akce | Ano | Objekt | Vnitřní akce, které spustit pro případ odpovídající výsledek výrazu | 
| výchozí | Ne | Objekt | Vnitřní akce, které spustit, když žádné případy odpovídají výsledku | 
||||| 

Příklad:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Akce foreach

Tato akce opakování iteruje v rámci pole a provede vnitřní akce na každou položku pole. Ve výchozím nastavení smyčka typu Foreach běží paralelně. Pro maximální počet paralelní cyklů, který "pro každou" smyčky můžete spustit najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). Chcete-li spustit každý cyklus postupně, nastavte `operationOptions` parametru `Sequential`. Další informace o [Foreach cyklu v aplikacích logiky](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| Akce | Ano | Objekt | Vnitřní akce pro spuštění uvnitř smyčky | 
| foreach | Ano | Řetězec | Pole k iteraci v rámci | 
| operationOptions | Ne | Řetězec | Určuje operaci možnosti přizpůsobení chování. Aktuálně podporuje pouze `Sequential` postupně spuštění iterací kde je paralelní výchozí chování. |
||||| 

Příklad:

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Dokud akce

Tato opakování akce spustí vnitřní akce, dokud je podmínka vyhodnocena jako jako true. Další informace o ["do" smyčky v aplikacích logiky](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- | 
| Akce | Ano | Objekt | Vnitřní akce pro spuštění uvnitř smyčky | 
| výraz | Ano | Řetězec | Výraz k vyhodnocení po každé iteraci | 
| Limit | Ano | Objekt | Limity pro smyčky. Musíte definovat alespoň jeden limit. | 
| počet | Ne | Integer | Limit počtu opakování k provedení | 
| timeout | Ne | Řetězec | Časový limit v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) který určuje, jak dlouho má spustit smyčky |
||||| 

Příklad:

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Akce oboru

Tato akce umožňuje logicky akce skupiny v pracovním postupu. Oboru také získá svůj vlastní stav po všechny akce v dokončit tento obor systémem. Další informace o [obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Název | Požaduje se | Typ | Popis | 
| ---- | -------- | ---- | ----------- |  
| Akce | Ano | Objekt | Vnitřní akce, které jsou spouštěny v rámci oboru |
||||| 

## <a name="next-steps"></a>Další postup

* Další informace o [jazyk definic workflowů funkce](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [pracovního postupu REST API](https://docs.microsoft.com/rest/api/logic/workflows)