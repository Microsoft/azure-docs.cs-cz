---
title: Pracovní postup triggery a akce - Azure Logic Apps | Microsoft Docs
description: Další informace o aktivační události a akce v definice pracovního postupu pro Azure Logic Apps
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886778"
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Triggery a akce pro definice pracovního postupu v Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md), všechny pracovní postupy aplikace logiky začínat následuje akce aktivační události. Tento článek popisuje triggery a akce, které můžete použít k vytváření aplikací logiky pro pracovní postupy automatizace firmy nebo procesy v integračních řešení. Můžete vytvořit aplikace logiky vizuálně pomocí návrháře aplikace logiky nebo základní definice pracovního postupu s vytvářením přímo [jazyk definic workflowů](../logic-apps/logic-apps-workflow-definition-language.md). Můžete vytvořit portálu Azure nebo Visual Studio. Zjistěte, jak [ceny funguje pro triggery a akce](../logic-apps/logic-apps-pricing.md).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Přehled aktivační události

Všechny aplikace logiky začínat aktivační událost, která definuje volání, které můžete vytvořit a spustit pracovní postup aplikace logiky. Toto jsou typy služby aktivačních událostí, které můžete použít:

* A *dotazování* aktivační události, které slouží k ověření koncového bodu protokolu HTTP služby v pravidelných intervalech
* A *nabízené* aktivovat, který volá [rozhraní API REST služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)
 
Všechny aktivační události mají tyto prvky nejvyšší úrovně, i když některé jsou volitelné:  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Název aktivační události*> | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Zadejte aktivační události, například: "Http" nebo "ApiConnection" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| recurrence | Objekt JSON | Četnost a interval, který popisuje, jak často se aktivuje aktivační události |  
| frequency | Řetězec | Jednotka času, který popisuje, jak často se aktivuje aktivační událost: "Druhý", "minut", "Hodina", "Den", "Týden" nebo "Měsíc" | 
| interval | Integer | Kladné celé číslo, které popisují, jak často se aktivuje aktivační události na základě četnosti. <p>Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "měsíc", pak opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [Podmínky](#trigger-conditions) | Pole | Jednu nebo víc podmínek, které určují, zda se má spustit pracovní postup | 
| [splitOn](#split-on-debatch) | Řetězec | Výraz, který rozdělí, nebo *debatches*, pole položek do více instancí pracovního postupu pro zpracování. Tato možnost je k dispozici pro aktivační události, které vrací pole a pouze při práci se přímo v kódu zobrazení. | 
| [operationOptions](#trigger-operation-options) | Řetězec | Některé aktivační události poskytují další možnosti, které umožňují změnit výchozí chování aktivační události | 
||||| 

## <a name="trigger-types-and-details"></a>Typy aktivační události a podrobnosti  

Každý typ aktivační událost má jiné rozhraní a vstupních hodnot, které definují chování aktivační události. 

| Typ aktivace | Popis | 
| ------------ | ----------- | 
| [**Opakování**](#recurrence-trigger) | Aktivuje se podle definovaného plánu. Můžete nastavit budoucí datum a čas pro ohlásí této aktivační události. Na základě četnosti, můžete také určit časy a dny pro spuštění pracovního postupu. | 
| [**Požadavek**](#request-trigger)  | Umožňuje aplikaci logiky do s koncový bod, také známé jako "Ruční" aktivační událost. Například v tématu [volat, aktivaci nebo vnořit pracovních s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
| [**HTTP**](#http-trigger) | Kontroluje, nebo *hlasování*, koncový bod webové HTTP. Koncový bod HTTP musí odpovídat kontraktu konkrétní aktivační událost pomocí "202" asynchronní vzor nebo vrácením pole. | 
| [**ApiConnection**](#apiconnection-trigger) | Funguje jako triggeru protokolu HTTP, ale používá [Microsoft spravovaná rozhraní API](../connectors/apis-list.md). | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Funguje jako aktivační událost požadavku, ale volá zadaná adresa URL pro registraci a zrušení registrace. |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Funguje jako HTTPWebhook aktivační událost, ale používá [Microsoft spravovaná rozhraní API](../connectors/apis-list.md). | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Aktivační událost opakování  

Této aktivační události běží na základě zadané opakování a plán a poskytuje snadný způsob pro pravidelné spouštění pracovního postupu. 

Zde je definice aktivační události:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Opakování | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Typ aktivace, kterým je "Recurrence" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| recurrence | Objekt JSON | Četnost a interval, který popisuje, jak často se aktivuje aktivační události |  
| frequency | Řetězec | Jednotka času, který popisuje, jak často se aktivuje aktivační událost: "Druhý", "minut", "Hodina", "Den", "Týden" nebo "Měsíc" | 
| interval | Integer | Kladné celé číslo, které popisují, jak často se aktivuje aktivační události na základě četnosti. <p>Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "měsíc", pak opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| startTime | Řetězec | Počáteční datum a čas v tomto formátu: <p>RRRR-MM-ddTHH Pokud zadáte časové pásmo <p>-nebo- <p>RRRR-MM-ddTHH Pokud nezadáte časové pásmo <p>Tak například, pokud chcete 18 září 2017 na 2:00 PM, zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, jako je například "Tichomoří (běžný čas), nebo zadejte" 2017-09-18T14:00:00Z "bez časového pásma. <p>**Poznámka:** tento čas zahájení musí následovat [ISO 8601 datum čas specifikace](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nezadáte časové pásmo, musíte přidat písmeno "Z" bez žádné mezery na konci. Tento "Z" odkazuje na ekvivalent [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány, čas spuštění je první výskyt, zatímco pro komplexní plány, aktivační událost není fire všechny dřív, než čas spuštění. Další informace o počáteční data a časy, najdete v tématu [vytvořit a plán pravidelně spuštěné úkoly](../connectors/connectors-native-recurrence.md). | 
| timeZone | Řetězec | Platí, pouze pokud zadáte počáteční čas protože této aktivační události není přijmout [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Zadejte časové pásmo, které chcete použít. | 
| hours | Celé číslo nebo číslo pole | Pokud zadáte "Dne" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 23, oddělených čárkami, jako hodin dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "10", "12" a "14", získáte 10 AM, 12 PM a 14: 00 jako značky hodinu. | 
| minutes | Celé číslo nebo číslo pole | Pokud zadáte "Den" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 59, oddělených čárkami, jako počet minut za hodinu, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako minutu značky a použijeme předchozí příklad hodin dne, získáte 10:30 AM, 12:30:00 a 14:30:00. | 
| weekDays | Řetězec nebo pole řetězců | Pokud zadáte "Týden" pro `frequency`, můžete zadat jeden nebo více dní, oddělených čárkami, pokud chcete spustit pracovní postup: "Pondělí", "Úterý", "Středa", "Čtvrtek", "Pátek", "Sobota" a "Neděle" | 
| Souběžnosti | Objekt JSON | Tento objekt pro opakování a dotazování aktivační procedury určuje maximální počet instancí pracovního postupu, které můžou běžet současně. Tato hodnota se používá k omezení požadavků, které přijímají back-end systémy. <p>Tato hodnota například nastaví limit souběžnosti 10 instancí: `"concurrency": { "runs": 10 }` | 
| operationOptions | Řetězec | `singleInstance` Možnost určuje, že aktivační událost aktivuje až po dokončení všech active spustí. V tématu [aktivační události: provést až po aktivní spouští Dokončit](#single-instance). | 
|||| 

*Příklad 1*

Této aktivační události základní opakování spouštěna každý den:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Příklad 2*

Můžete zadat počáteční datum a čas pro ohlásí aktivační událost. Této aktivační události opakování začíná v zadané datum a potom aktivuje každý den:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Příklad 3*

Této aktivační události opakování zahájí na 9 září 2017 ve 14:00:00 a aktivuje se každý týden každé pondělí v 10:30 AM, 12:30:00 a 14:30:00 Tichomoří (běžný čas):

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Další informace a příklady této aktivační události, najdete v části [vytvořit a plán pravidelně spuštěné úkoly](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

## <a name="request-trigger"></a>Žádost o aktivační události

Této aktivační události díky aplikaci logiky s tím, že vytvoříte koncový bod, který může přijmout příchozí požadavky HTTP. K této aktivační události zavolat, je nutné použít `listCallbackUrl` rozhraní API v [rozhraní API REST služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Další informace o použití této aktivační události jako koncový bod protokolu HTTP, najdete v tématu [volat, aktivaci nebo vnořit pracovních s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Ruční | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Aktivační událost typu, který je "Vyžádat" | 
| Typ | Řetězec | Typ požadavku, což je "Http" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| method | Řetězec | Metoda, která požadavky musí používat pro volání aktivační události: "GET", "PUT", "POST", "OPRAVIT", "Odstranit" nebo "HEAD" |
| RelativePath | Řetězec | Relativní cesta pro parametr, který přijímá váš koncový bod HTTP adresa URL | 
| Schéma | Objekt JSON | Schéma JSON, který popisuje a ověří datové části, nebo vstupních hodnot, které obdrží aktivační události z příchozího požadavku. Toto schéma pomáhá akce následné pracovního postupu vědět vlastnosti odkazovat. | 
| properties | Objekt JSON | Jeden nebo více vlastností ve schématu JSON, který popisuje datové části | 
| Požadované | Pole | Jeden nebo více vlastností, které vyžadují hodnoty | 
|||| 

*Příklad*

Této aktivační události požadavku určuje, že příchozí žádosti používat metodu POST protokolu HTTP k volání aktivační události a schéma, které ověří vstup z příchozího požadavku: 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>Trigger HTTP  

Této aktivační události dotazuje zadaný koncový bod a zkontroluje odpověď. Odpověď určuje, zda by měl pracovní postup spustit nebo ne. `inputs` Objekt JSON obsahuje a vyžaduje `method` a `uri` parametrů požadovaných pro tvorbu volání protokolu HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| HTTP | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Typ aktivace, kterým je "Http" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| method | Ano | Řetězec | Metoda HTTP pro dotazování zadaný koncový bod: "GET", "PUT", "POST", "OPRAVIT", "Odstranit" nebo "HEAD" | 
| identifikátor uri | Ano| Řetězec | Adresu URL koncového bodu protokolu HTTP nebo HTTPS, která kontroluje aktivační události nebo dotazuje <p>Maximální velikost řetězce: 2 KB | 
| recurrence | Objekt JSON | Četnost a interval, který popisuje, jak často se aktivuje aktivační události |  
| frequency | Řetězec | Jednotka času, který popisuje, jak často se aktivuje aktivační událost: "Druhý", "minut", "Hodina", "Den", "Týden" nebo "Měsíc" | 
| interval | Integer | Kladné celé číslo, které popisují, jak často se aktivuje aktivační události na základě četnosti. <p>Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "měsíc", pak opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Dotazy | Objekt JSON | Všechny parametry dotazu, které chcete zahrnout s adresou URL <p>Například přidá tento element `?api-version=2015-02-01` na adresu URL, řetězec dotazu: <p>`"queries": { "api-version": "2015-02-01" }` <p>Výsledek: `https://contoso.com?api-version=2015-02-01` | 
| hlavičky | Objekt JSON | Nejméně jedno záhlaví odeslat s požadavkem <p>Chcete-li například nastavit jazyk a typ pro žádost: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Objekt JSON | Datová část (data) k odeslání do koncového bodu | 
| Ověřování | Objekt JSON | Metoda, která příchozí požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). Nad rámec Scheduler `authority` vlastnost je podporována. Pokud není zadáno, výchozí hodnota je `https://login.windows.net`, ale můžete použít jinou hodnotu, jako například`https://login.windows\-ppe.net`. | 
| retryPolicy | Objekt JSON | Tento objekt přizpůsobuje chování opakování občasné chyby, které mají 4xx nebo 5xx stavové kódy. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| Souběžnosti | Objekt JSON | Tento objekt pro opakování a dotazování aktivační procedury určuje maximální počet instancí pracovního postupu, které můžou běžet současně. Tato hodnota se používá k omezení požadavků, které přijímají back-end systémy. <p>Tato hodnota například nastaví limit souběžnosti 10 instancí: <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | Řetězec | `singleInstance` Možnost určuje, že aktivační událost aktivuje až po dokončení všech active spustí. V tématu [aktivační události: provést až po aktivní spouští Dokončit](#single-instance). | 
|||| 

Aby dobře pracoval s svou aplikaci logiky, triggeru protokolu HTTP vyžaduje, že rozhraní API HTTP odpovídat určitému vzoru. Aktivace protokolu HTTP rozpoznává tyto vlastnosti:  
  
| Odpověď | Požaduje se | Popis | 
| -------- | -------- | ----------- |  
| Stavový kód | Ano | "200 OK" stavový kód spustí spustit. Další kód stavu nelze spustit spustit. | 
| Opakovat po záhlaví | Ne | Počet sekund do aplikace logiky dotazuje koncový bod znovu | 
| Hlavička umístění | Ne | Adresa URL pro volání při dalším intervalu dotazování. Pokud není zadaný, použije se původní adresu URL. | 
|||| 

*Příklad chování pro jiné požadavky*

| Stavový kód | Opakovat po | Chování | 
| ----------- | ----------- | -------- | 
| 200 | {none} | Spusťte pracovní postup, a poté znovu zkontrolujte pro další data po definované opakování. | 
| 200 | 10 sekund | Spusťte pracovní postup, a poté znovu zkontrolujte pro další data po 10 sekundách. |  
| 202 | 60 sekund | Nemáte spustit pracovní postup. Další pokus se stane za jednu minutu, podstoupí definované opakování. Pokud je definovaný opakování je menší než jedna minuta, záhlaví opakovat po přednost. Jinak je použít definované opakování. | 
| 400 | {none} | Chybný požadavek, nespouštět pracovního postupu. Pokud žádné `retryPolicy` je definován, je použita výchozí zásady. Po překročení počet opakovaných pokusů, aktivační událost zjišťuje znovu data po definované opakování. | 
| 500 | {none}| Chyba serveru nelze spustit pracovní postup. Pokud žádné `retryPolicy` je definován, je použita výchozí zásady. Po překročení počet opakovaných pokusů, aktivační událost zjišťuje znovu data po definované opakování. | 
|||| 

### <a name="http-trigger-outputs"></a>Výstupy triggeru protokolu HTTP

| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt JSON | Hlavičky z odpovědi HTTP | 
| hlavní část | Objekt JSON | Text z odpovědi HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Aktivační událost APIConnection  

Této aktivační události pracuje stejně jako [triggeru protokolu HTTP](#http-trigger), ale používá [Microsoft spravovaná rozhraní API](../connectors/apis-list.md) tak liší parametry této aktivační události. 

Zde je definice aktivační události, i když mnoho oddíly jsou volitelné, takže má aktivační procedura chování závisí na oddíly, jestli jsou zahrnuté:

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Typ aktivace, kterým je "ApiConnection" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| hostitel | Objekt JSON | Objekt JSON, který popisuje hostitele brány a ID pro spravované rozhraní API <p>`host` Objekt JSON má tyto prvky: `api` a `connection` | 
| rozhraní api | Objekt JSON | Adresa URL koncového bodu pro spravované rozhraní API: <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| připojení | Objekt JSON | Název pro spravované rozhraní API spojení, které používá pracovní postup, který musí obsahovat odkaz na parametr s názvem `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| method | Řetězec | Metoda HTTP pro komunikaci s spravované rozhraní API: "GET", "PUT", "POST", "OPRAVIT", "Odstranit" nebo "HEAD" | 
| recurrence | Objekt JSON | Četnost a interval, který popisuje, jak často se aktivuje aktivační události |  
| frequency | Řetězec | Jednotka času, který popisuje, jak často se aktivuje aktivační událost: "Druhý", "minut", "Hodina", "Den", "Týden" nebo "Měsíc" | 
| interval | Integer | Kladné celé číslo, které popisují, jak často se aktivuje aktivační události na základě četnosti. <p>Toto jsou minimální a maximální hodnotou: <p>-Měsíc: 1-16 měsíců </br>-Den: 1-500 dnů </br>-Hodina: 1-12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Druhý: 1-9,999,999 sekund<p>Například pokud je interval 6 a frekvenci "měsíc", pak opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Dotazy | Objekt JSON | Všechny parametry dotazu, které chcete zahrnout s adresou URL <p>Například přidá tento element `?api-version=2015-02-01` na adresu URL, řetězec dotazu: <p>`"queries": { "api-version": "2015-02-01" }` <p>Výsledek: `https://contoso.com?api-version=2015-02-01` | 
| hlavičky | Objekt JSON | Nejméně jedno záhlaví odeslat s požadavkem <p>Chcete-li například nastavit jazyk a typ pro žádost: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Objekt JSON | Objekt JSON, který popisuje datová část (data) k odeslání do spravované rozhraní API | 
| Ověřování | Objekt JSON | Metoda, kterou příchozí žádosti by měla použít pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Objekt JSON | Tento objekt přizpůsobuje chování opakování občasné chyby, které mají 4xx nebo 5xx stavové kódy: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| Souběžnosti | Objekt JSON | Tento objekt pro opakování a dotazování aktivační procedury určuje maximální počet instancí pracovního postupu, které můžou běžet současně. Tato hodnota se používá k omezení požadavků, které přijímají back-end systémy. <p>Tato hodnota například nastaví limit souběžnosti 10 instancí: `"concurrency": { "runs": 10 }` | 
| operationOptions | Řetězec | `singleInstance` Možnost určuje, že aktivační událost aktivuje až po dokončení všech active spustí. V tématu [aktivační události: provést až po aktivní spouští Dokončit](#single-instance). | 
||||

*Příklad*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>Výstupy APIConnection aktivační události
 
| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt JSON | Hlavičky z odpovědi HTTP | 
| hlavní část | Objekt JSON | Text z odpovědi HTTP | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>Aktivační událost HTTPWebhook  

Této aktivační události pracuje stejně jako [aktivační událost požadavku](#request-trigger) vytvořením s koncový bod pro svou aplikaci logiky. Této aktivační události však také voláním zadaný koncový bod adresy URL pro registraci nebo rušení registrace předplatného. Můžete zadat omezení na aktivační události webhooku stejným způsobem jako [HTTP asynchronní omezení](#asynchronous-limits). 

Zde je definice aktivační událost, když mnoho oddíly jsou volitelné a aktivační události chování závisí na oddíly, které můžete použít nebo vynechejte:

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Typ aktivace, kterým je "HttpWebhook" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| přihlášení k odběru | Objekt JSON| Odchozí požadavek volání a provedení počáteční registrace, když se vytvoří aktivační událost. Toto volání se provede tak, aby aktivační událost můžete zahájit naslouchání na události na koncový bod. Další informace najdete v tématu [přihlášení k odběru a odhlášení](#subscribe-unsubscribe). | 
| method | Řetězec | Metoda protokolu HTTP použitou pro danou žádost předplatné: "GET", "PUT", "POST", "OPRAVIT", "Odstranit" nebo "HEAD" | 
| identifikátor uri | Řetězec | Adresa URL koncového bodu pro kam má posílat žádosti o odběr | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Odhlásit | Objekt JSON | Odchozích žádost automaticky volání a zrušit předplatné, pokud operace provede aktivační události neplatný. Další informace najdete v tématu [přihlášení k odběru a odhlášení](#subscribe-unsubscribe). | 
| method | Řetězec | Metoda HTTP pro žádost o zrušení: "GET", "PUT", "POST", "OPRAVIT", "Odstranit" nebo "HEAD" | 
| identifikátor uri | Řetězec | Adresa URL koncového bodu, kam můžete odeslat žádost o zrušení | 
| hlavní část | Objekt JSON | Objekt JSON, který popisuje datová část (data) pro požadavek na předplatné nebo zrušení | 
| Ověřování | Objekt JSON | Metoda, kterou příchozí žádosti by měla použít pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Objekt JSON | Tento objekt přizpůsobuje chování opakování občasné chyby, které mají 4xx nebo 5xx stavové kódy: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
|||| 

*Příklad*

```json
"myAppSpotTrigger": {
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
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` a `unsubscribe`

`subscribe` Volání se stane, když pracovní postup změny žádným způsobem, například když obnovily přihlašovací údaje nebo aktivační událost vstup změnu parametry. Volání používá stejné parametry jako standardní akce HTTP. 
 
`unsubscribe` Volání automaticky se stane, když operace umožňuje aktivační událost HTTPWebhook neplatná, třeba:

* Odstranit nebo zakázat aktivační událost. 
* Odstranit nebo zakázat pracovní postup. 
* Odstranit nebo zakázat odběr. 

Pro podporu těchto volání `@listCallbackUrl()` funkce vrátí jedinečný "zpětného volání URL" této aktivační události. Tato adresa URL představuje jedinečný identifikátor pro koncové body, které používají rozhraní API služby REST. Parametry této funkce jsou stejné jako triggeru protokolu HTTP.

### <a name="httpwebhook-trigger-outputs"></a>Výstupy HTTPWebhook aktivační události

| Název elementu | Typ | Popis |
| ------------ | ---- | ----------- |
| hlavičky | Objekt JSON | Hlavičky z odpovědi HTTP | 
| hlavní část | Objekt JSON | Text z odpovědi HTTP | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>Aktivační událost ApiConnectionWebhook

Této aktivační události pracuje stejně jako [aktivační událost HTTPWebhook](#httpwebhook-trigger), ale používá [Microsoft spravovaná rozhraní API](../connectors/apis-list.md). 

Zde je definice aktivační události:

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*Požadované*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | Objekt JSON | Název aktivační události, který je objektem popsané ve formátu Javascript Object Notation (JSON)  | 
| type | Řetězec | Typ aktivace, kterým je "ApiConnectionWebhook" | 
| Vstupy | Objekt JSON | Vstupy aktivační události, které definují chování aktivační události | 
| hostitel | Objekt JSON | Objekt JSON, který popisuje hostitele brány a ID pro spravované rozhraní API <p>`host` Objekt JSON má tyto prvky: `api` a `connection` | 
| připojení | Objekt JSON | Název pro spravované rozhraní API spojení, které používá pracovní postup, který musí obsahovat odkaz na parametr s názvem `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| hlavní část | Objekt JSON | Objekt JSON, který popisuje datová část (data) k odeslání do spravované rozhraní API | 
| NotificationUrl | Řetězec | Vrátí jedinečný "zpětného volání URL" této aktivační události, můžete použít spravované rozhraní API | 
|||| 

*Volitelné*

| Název elementu | Typ | Popis | 
| ------------ | ---- | ----------- | 
| Dotazy | Objekt JSON | Všechny parametry dotazu, které chcete zahrnout s adresou URL <p>Například přidá tento element `?folderPath=Inbox` na adresu URL, řetězec dotazu: <p>`"queries": { "folderPath": "Inbox" }` <p>Výsledek: `https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>Aktivační události: podmínky

Pro všechny aktivační události můžete zahrnout pole s jednu nebo víc podmínek, které určují, zda se má pracovní postup spustit nebo ne. V tomto příkladu sestavy aktivační událost aktivuje pouze během pracovního postupu `sendReports` parametr je nastaven na hodnotu true. 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

Podmínky můžete taky odkazovat aktivační události stavový kód. Předpokládejme například, že chcete spustit pracovní postup pouze v případě, že váš web vrátí "500" stavový kód:

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> Ve výchozím nastavení, aktivační událost aktivuje pouze na příjem "200 OK" odpovědi. Když výraz odkazuje aktivační událost stavový kód žádným způsobem, se nahradí výchozí chování aktivační události. Ano Pokud chcete, aby aktivační událost má provést pro víc stavových kódů, třeba stavovým kódem 200 a stavový kód 201, musí obsahovat tento příkaz jako vaše podmínky: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>Aktivační události: Rozdělit pole na více běží

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
        "interval": 1
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

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>Aktivační události: Možnosti operace

Tyto triggery poskytují další možnosti, které umožňují změnit výchozí chování.

| Trigger | Možnost operace | Popis |
|---------|------------------|-------------|
| [Opakování](#recurrence-trigger), <br>[HTTP](#http-trigger), <br>[ApiConnection](#apiconnection-trigger) | singleInstance | Dokončení ještě efektivněji, které spouští pouze po všechny aktivní aktivační událost. |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>Aktivační události: Provést až po aktivní spouští dokončit

Aktivačních událostí, kde můžete nastavit opakování můžete určit, že byl dokončen ještě efektivněji aktivační událost pouze po všechny aktivní spustí. Pokud naplánované opakování se stane, když je spuštěna instance pracovního postupu, aktivační událost přeskočí a čeká na další naplánované opakování před opětovnou kontrolu. Příklad:

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

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
| **pracovní postup** | Představuje vnořený pracovní postup. | 
| **Vytvoření** | Vytvoří objekt libovolný z akce vstupy. | 
| **Dotaz** | Filtry založené na podmínce pole. | 
| **Výběr** | Projekty každý element pole na novou hodnotu. Například můžete převést pole čísla do pole objektů. | 
| **Tabulka** | Převede pole položek do tabulky CSV nebo HTML. | 
| **Ukončení** | Zastaví pracovní postup. | 
| **Počkej** | Počká pevnou hodnotu čas, nebo dokud určitý čas. | 
| **pracovní postup** | Představuje vnořený pracovní postup. | 
||| 

### <a name="collection-actions"></a>Kolekce akce

| Typ akce | Popis | 
| ----------- | ----------- | 
| **Pokud** | Vyhodnocení výrazu a na základě výsledku, spustí odpovídající firemní pobočky. | 
| **přepínače** | Proveďte různé akce, které jsou založené na konkrétní hodnoty objektu. | 
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
| method | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| identifikátor uri | Ano| Řetězec | Protokolu HTTP nebo HTTPs koncový bod, který kontroluje, aktivační události. Maximální velikost řetězce: 2 KB | 
| Dotazy | Ne | Objekt JSON | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt JSON | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt JSON | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt JSON | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt JSON | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). <p>Nad Scheduler, existuje více podporované jednu vlastnost: `authority`. Ve výchozím nastavení, tato hodnota je `https://login.windows.net` není-li zadána, ale můžete použít jinou hodnotu, jako například`https://login.windows\-ppe.net`. | 
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
| hostitel | Ano | Objekt JSON | Představuje informace o konektoru, jako `runtimeUrl` a odkaz na objekt připojení. | 
| method | Ano | Řetězec | Použije jeden z těchto metod HTTP: "GET", "POST", "PUT", "Odstranit", "OPRAVIT" nebo "HEAD" | 
| path | Ano | Řetězec | Cesta pro operace rozhraní API | 
| Dotazy | Ne | Objekt JSON | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt JSON | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt JSON | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt JSON | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt JSON | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
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
| hostitel | Ano | Objekt JSON | Představuje informace o konektoru, jako `runtimeUrl` a odkaz na objekt připojení. | 
| path | Ano | Řetězec | Cesta pro operace rozhraní API | 
| Dotazy | Ne | Objekt JSON | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt JSON | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt JSON | Představuje datovou část, která je odeslána koncovému bodu. | 
| retryPolicy | Ne | Objekt JSON | Tento objekt použijte k přizpůsobení chování opakování 4xx nebo 5xx chyby. Další informace najdete v tématu [opakujte zásady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Ne | Řetězec | Definuje sadu zvláštní chování potlačit. | 
| Ověřování | Ne | Objekt JSON | Představuje metodu, která požadavek by měl používat pro ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
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
| method | Ne | Řetězec | Metoda HTTP, který se používá k volání funkce. Pokud není zadaný, "POST" je výchozí metodou. | 
| Dotazy | Ne | Objekt JSON | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt JSON | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt JSON | Představuje datovou část, která je odeslána koncovému bodu. | 
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
| runError | Ne | Objekt JSON | Podrobnosti o chybě. Podporované pouze tehdy, když `runStatus` je nastaven na `Failed`. |
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
| do | Ne | Objekt JSON | Doba čekání na základě v bodě v čase | 
| dokud časové razítko | Ano | Řetězec | Do bodu v čase v [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) vypršení platnosti čekání | 
| interval | Ne | Objekt JSON | Doba čekání na základě jednotku intervalu a počet | 
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
| Dotazy | Ne | Objekt JSON | Představuje všechny parametry dotazu, které chcete zahrnout do adresy URL. <p>Například `"queries": { "api-version": "2015-02-01" }` přidá `?api-version=2015-02-01` na adresu URL. | 
| hlavičky | Ne | Objekt JSON | Představuje každá hlavička odeslaný v požadavku. <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| hlavní část | Ne | Objekt JSON | Představuje datovou část, která je odeslána koncovému bodu. | 
||||| 

Tato akce výstupy jsou založené na definovat v `Response` akce pro podřízené pracovní postup. Pokud se nedefinuje podřízeného pracovního postupu `Response` akce, výstupy jsou prázdné.

## <a name="collection-actions-overview"></a>Přehled akce kolekce

Abyste řízení provádění pracovního postupu, akce kolekce může obsahovat další akce. Můžete odkazovat přímo k odkazování na akce v kolekci mimo kolekce. Například pokud definujete `Http` akce v oboru, pak `@body('http')` je stále platný kdekoli v pracovním postupu. Také akce v kolekci můžete jenom "spustit" dalších akcí ve stejné kolekci.

## <a name="if-action"></a>Pokud se akce

Tato akce, která je podmíněného prohlášení, umožňuje vyhodnotit podmínku a provést větev založené na tom, jestli se výraz vyhodnotí jako true. Pokud podmínka vyhodnocena jako true úspěšně, podmínky je označen stavem "Succeeded". Akce, které jsou v `actions` nebo `else` objekty být tyto hodnoty:

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
| Akce | Ano | Objekt JSON | Vnitřní akce, které spustí, když `expression` vyhodnocen `true` | 
| výraz | Ano | Řetězec | Výraz k vyhodnocení |
| else | Ne | Objekt JSON | Vnitřní akce, které spustí, když `expression` vyhodnocen `false` |
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
| `"expression": "parameters('hasSpecialAction')"` | Tento výraz způsobí chybu a není platný stav. Musíte použít podmínek "\@" symbol. | 
||| 

## <a name="switch-action"></a>Přepínač akce

Tato akce, který je příkaz switch, provádí různé akce podle konkrétní hodnoty objektu, výraz nebo token. Tato akce vyhodnocena jako objekt, výraz nebo token, vybere případu, který odpovídá výsledek a spouští akce pro pouze v takovém případě. Když žádném případě odpovídá výsledek, spustí se výchozí akci. Po spuštění příkazu switch pouze jeden případ by měl odpovídat výsledek. Další informace o [přepínač příkazy ve aplikace logiky](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
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
| Případy | Ano | Objekt JSON | Obsahuje sadu vnitřní akce, které běží na základě na výsledek výrazu. | 
| Případ | Ano | Řetězec | Hodnota, která má odpovídat výsledek | 
| Akce | Ano | Objekt JSON | Vnitřní akce, které spustit pro případ odpovídající výsledek výrazu | 
| výchozí | Ne | Objekt JSON | Vnitřní akce, které spustit, když žádné případy odpovídají výsledku | 
||||| 

Příklad:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
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
| Akce | Ano | Objekt JSON | Vnitřní akce pro spuštění uvnitř smyčky | 
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
| Akce | Ano | Objekt JSON | Vnitřní akce pro spuštění uvnitř smyčky | 
| výraz | Ano | Řetězec | Výraz k vyhodnocení po každé iteraci | 
| Limit | Ano | Objekt JSON | Limity pro smyčky. Musíte definovat alespoň jeden limit. | 
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
| Akce | Ano | Objekt JSON | Vnitřní akce, které jsou spouštěny v rámci oboru |
||||| 

## <a name="next-steps"></a>Další postup

* Další informace o [jazyk definic workflowů funkce](../logic-apps/logic-apps-workflow-definition-language.md)
* Další informace o [pracovního postupu REST API](https://docs.microsoft.com/rest/api/logic/workflows)