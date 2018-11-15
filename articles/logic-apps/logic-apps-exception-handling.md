---
title: Zpracování chyb a výjimek – Azure Logic Apps | Dokumentace Microsoftu
description: Seznamte se s vzory pro chyb a zpracování výjimek v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 19a715812f1250523fd050ac8b80dee9ec664be4
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686258"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Zpracování chyb a výjimek v Azure Logic Apps

Tak, aby všechny Architektura integrace správně zpracovává výpadky nebo problémy způsobené závislých systémů může představovat výzvu. Můžete vytvořit robustní a odolná proti selháním integrace, které pohodlně zvládnout problémy a chyby, Logic Apps nabízí prvotřídní prostředí pro zpracování chyb a výjimek. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zásady opakování

Většina základních výjimek a zpracování chyb, můžete použít *zásady opakování* v libovolné akci nebo aktivační události, kde je podporován. Zásady opakování určuje, zda a jak akci nebo triggeru opakování žádosti při původní žádost vyprší časový limit nebo selže, tedy všechny požadavky, které vede 408, 429 nebo odpověď 5xx. Pokud žádné jiné zásady opakování se používá, použije se výchozí zásady. 

Toto jsou typy zásad opakování: 

| Typ | Popis | 
|------|-------------| 
| [**Výchozí**](#default-retry) | Tato zásada odešle až čtyři opakované pokusy probíhaly v [ *exponenciálně rostoucím* ](#exponential-retry) intervalech, které můžete škálovat 7.5 sekund, ale jsou omezené mezi 5 a nejvýše 45 sekund. | 
| [**Exponenciální interval**](#exponential-retry)  | Tyto zásady vyčká náhodném intervalu vybrali geometrickou řadou rostoucí rozsah než pošle další požadavek. | 
| [**Pevný interval**](#fixed-retry)  | Tato zásada počká zadaný interval, než pošle další požadavek. | 
| [**None**](#no-retry)  | Není odešlete požadavek znovu. | 
||| 

Informace o omezeních zásady opakování, naleznete v tématu [Logic Apps omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Změnit zásady opakovaných pokusů

Pokud chcete vybrat jiné zásady opakování, postupujte takto: 

1. Otevření aplikace logiky v návrháři aplikace logiky. 

2. Otevřít **nastavení** akce nebo triggeru.

3. Pokud tato akce nebo triggeru podporuje zásady opakování, v části **zásady opakování**, vyberte požadovaný typ. 

Nebo můžete ručně zadat zásady opakovaných pokusů v `inputs` část pro akci nebo trigger, který podporuje zásady opakování. Pokud nechcete zadat zásady opakování, akce využívá výchozí zásady.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Vyžaduje*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*Typ zásad opakování*> | Řetězec | Typ zásad opakování, který chcete použít: `default`, `none`, `fixed`, nebo `exponential` | 
| <*interval opakování*> | Řetězec | Interval opakování, ve kterém musí používat hodnotu [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Výchozí minimální interval `PT5S` a maximální interval je `PT1D`. Při použití zásady s exponenciálním intervalem můžete určit různé minimální a maximální hodnoty. | 
| <*opakované pokusy*> | Integer | Počet opakovaných pokusů, které musí být mezi 1 a 90 | 
||||

*Volitelné*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*minimální interval*> | Řetězec | Pro zásady s exponenciálním intervalem, nejmenší interval intervalu namátkou vybraného v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*maximální interval*> | Řetězec | Pro zásady s exponenciálním intervalem, největší interval intervalu namátkou vybraného v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Tady je další informace o typech jiné zásady.

<a name="default-retry"></a>

### <a name="default"></a>Výchozí

Pokud nechcete zadat zásady opakování, akce využívá výchozí zásady, která je ve skutečnosti k [zásady s exponenciálním intervalem](#exponential-interval) , která odešle až čtyři opakované pokusy probíhaly v exponenciálně prodlužuje intervalech, které jsou měřítkem řídit 7.5 sekund. Interval je uzavřeny mezi 5 a nejvýše 45 sekund. 

I když nejsou explicitně definovány v triggeru nebo akce, tady je chování výchozí zásada v příklad akce HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Žádný

Chcete-li zadat akci nebo triggeru se nebude opakovat neúspěšné požadavky, nastavte <*typ zásad opakování*> do `none`.

### <a name="fixed-interval"></a>Pevný interval

Chcete-li určit, že akce nebo triggeru počká zadaný interval pošle další požadavek, nastavte <*typ zásad opakování*> do `fixed`.

*Příklad*

Takové zásady opakování, pokusí se získat nejnovější informace o dvou víckrát od prvního požadavku se nezdařilo s 30sekundovém zpoždění mezi jednotlivými pokusy o:

```json
"Get_latest_news": {
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

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponenciální interval

Chcete-li určit, že akce nebo triggeru počká náhodném intervalu pošle další požadavek, nastavte <*typ zásad opakování*> do `exponential`. Náhodném intervalu je vybrat v exponenciálně rostoucím rozsahu. Volitelně můžete také přepsat výchozí minimální a maximální intervaly tak, že zadáte vlastní minimální a maximální hodnotou.

**Náhodná proměnná rozsahu**

Tato tabulka ukazuje, jak Logic Apps generuje jednotné náhodná proměnná v zadaném rozsahu pro každého opakování až po a včetně počet opakovaných pokusů:

| Opakujte číslo | Minimální interval | Maximální interval |
|--------------|------------------|------------------|
| 1 | maximální počet (0, <*minimální interval*>) | min (interval <*maximální interval*>) |
| 2 | maximální počet (interval <*minimální interval*>) | min (2 * interval <*maximální interval*>) |
| 3 | maximální počet (2 * interval <*minimální interval*>) | min (4 * interval <*maximální interval*>) |
| 4 | maximální počet (4 * interval <*minimální interval*>) | min (8 * interval <*maximální interval*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Zachytit a zpracovat selhání se vlastnost RunAfter

Každá akce aplikace logiky deklaruje akce, které musíte dokončit před zahájením této akce, podobně jako jak určit pořadí kroků v pracovním postupu. V definici akce **runAfter** vlastnost definuje toto uspořádání a je objekt, který popisuje, jaké akce a stavy akce provede akci.

Ve výchozím nastavení jsou všechny akce, které se v návrháři aplikace logiky přidáte nastavené spustit po dokončení předchozího kroku, když je výsledkem předchozího kroku **Succeeded**. Ale můžete přizpůsobit **runAfter** hodnotu tak, aby akce aktivují, když výsledek předchozí akce jako **neúspěšné**, **vynecháno**, nebo kombinaci těchto hodnot. Například pro přidání položky do konkrétního tématu služby Service Bus po konkrétní **Insert_Row** akce selže, můžete použít v tomto příkladu **runAfter** definice:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**RunAfter** je nastavena na spustí, když **Insert_Row** je stav akce **neúspěšné**. Akci spustit, pokud je stav akce **Succeeded**, **neúspěšné**, nebo **vynecháno**, použijte následující syntaxi:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Akce, které spustit a dokončit úspěšně po předchozí akce se nezdařila, jsou označeny jako **Succeeded**. Toto chování znamená, že pokud jste úspěšně catch, všechny chyby v pracovním postupu spustit samotného je označen jako **Succeeded**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Akce s obory a jejich výsledky vyhodnocení

Podobně jako kroky spuštění po jednotlivých akcí s **runAfter** vlastností, můžete seskupit akce uvnitř [oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Pokud chcete logicky seskupit akce, vyhodnocení agregovaný stav oboru a provádět akce na základě tohoto stavu můžete pomocí oborů. Jakmile skončí všechny akce v oboru, rozsah samotného získá svůj vlastní stav. 

Pokud chcete zkontrolovat stav oboru, můžete použít stejné kritéria, která umožňují kontrolovat stav spuštění aplikace logiky, například **úspěch**, **se nezdařilo**, a tak dále. 

Ve výchozím nastavení, když všechny oboru akce úspěšná, stav oboru označí se **Succeeded**. Pokud poslední akce v oboru výsledky jako **se nezdařilo** nebo **přerušeno**, je označen stav oboru **neúspěšné**. 

Jak zachytávat výjimky v **neúspěšné** oboru a spuštění akce, které zpracování těchto chyb, můžete použít **runAfter** vlastnost, která **neúspěšné** oboru. Tímto způsobem, pokud *jakékoli* selhání akce v oboru a použít **runAfter** vlastnost pro daný obor, můžete vytvořit jednu akci pro zachycení chyb.

Omezení u oborů najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Získání kontextu a výsledky pro selhání

I když zachytávání chyb z oboru je užitečné, můžete také kontext vám pomohou pochopit, přesně plus všechny chyby nebo stavové kódy, které byly vráceny akce, které se nezdařilo. `@result()` Výraz poskytuje kontext, o výsledku všechny akce v oboru.

`@result()` Výraz přijímá jeden parametr (název oboru) a vrátí pole všechny akce výsledky z v rámci tohoto oboru. Mezi tyto akce objekty patří stejné atributy, jako  **@actions()** objektu, například čas zahájení, čas ukončení, stav, vstupů, ID korelace a výstupy akce. Kontext pro všechny akce, které se nepodařilo odeslat v rámci oboru, je jasné,  **@result()** pracovat **runAfter** vlastnost.

Spustit akci pro každou akci v oboru, který má **neúspěšné** výsledek, a pro filtrování pole výsledky dolů nezdařené akce, spárujte  **@result()** s **[Filtrování pole](../connectors/connectors-native-query.md)** akce a [ **pro každou** ](../logic-apps/logic-apps-control-flow-loops.md) smyčky. Můžete využít pole filtrovaných výsledků a provést akci pro každý selhání pomocí **pro každou** smyčky. 

Tady je příklad, za nímž následuje podrobné vysvětlení, který pošle požadavek HTTP POST s tělo odpovědi pro všechny akce, které se nepodařilo v rámci oboru "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Tady je podrobný návod, který popisuje, co se stane v tomto příkladu:

1. Chcete-li získat výsledek ze všech akcí uvnitř "My_Scope" **filtrování pole** akce používá tento výraz filtru: `@result('My_Scope')`

2. Podmínka pro **filtrování pole** libovolnou `@result()` položku, která je ve stavu, který je roven **neúspěšné**. Tato podmínka filtrování pole, které obsahuje všechny akce výsledky z "My_Scope" na pole s pouze výsledky neúspěšných akcí.

3. Provést **pro každou** opakovat na akce *filtrovaného pole* výstupy. Tento krok provádí akci pro každý neúspěšná akce výsledek, který byl dříve filtrovat.

   Pokud se nezdařilo jednu akci v oboru, akce v **pro každou** smyčky spustit jenom jednou. 
   Několik neúspěšných akce způsobí, že jednu akci za chyby.

4. Odeslání požadavku HTTP POST **pro každou** položky text odpovědi, která je `@item()['outputs']['body']` výrazu. 

   `@result()` Obrazec položky, je stejné jako `@actions()` obrazce a může být analyzován stejným způsobem.

5. Zahrnout dvě vlastní záhlaví s názvem neúspěšná akce (`@item()['name']`) a se spusťte klienta, ID sledování (`@item()['clientTrackingId']`).

Pro referenci tady je příklad jednoho `@result()` položky, zobrazuje **název**, **tělo**, a **clientTrackingId** vlastnosti, které jsou analyzovány v předchozí Příklad. Mimo **pro každou** akce, `@result()` vrátí pole z těchto objektů.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

K provádění různých vzorů pro zpracování výjimek, můžete použít výrazy výše popsaný v tomto článku. Může zvolit ke spuštění jedné výjimky zpracování akce mimo rozsah, který přijímá celé filtrovaného pole selhání a odebrat **pro každou** akce. Můžete použít také další užitečné vlastnosti z  **@result()** odpověď, jak je uvedeno výše.

## <a name="azure-diagnostics-and-metrics"></a>Azure Diagnostika a metriky

Předchozí vzorce jsou skvělé až po zpracování chyb a výjimek v rámci běhu, ale také identifikovat a reakce na chyby, které jsou nezávislé na spuštění samotný. 
[Diagnostika Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) poskytuje jednoduchý způsob, jak odeslat všechny pracovní postup události, včetně všech stavů běh a akci, do účtu služby Azure Storage nebo Centrum událostí vytvořené pomocí Azure Event Hubs. 

K vyhodnocení spuštění stavy, můžete monitorovat metriky a protokolování nebo publikovat je do monitorovacího nástroje, které dáváte přednost. Jednou z možností potenciální je ke streamování všechny události prostřednictvím služby Event Hubs do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Ve službě Stream Analytics můžete napsat živých dotazů založených na anomálie, průměry nebo selhání z diagnostické protokoly. Stream Analytics můžete použít k odesílání informací do jiných zdrojů dat, jako je například front, témat, SQL, Azure Cosmos DB nebo Power BI.

## <a name="next-steps"></a>Další postup

* [Naleznete v tématu Postupy: sestavení zákazník s Azure Logic Apps pro zpracování chyb](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Najít další příklady Logic Apps a scénáře](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
