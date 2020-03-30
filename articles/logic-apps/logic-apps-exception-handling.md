---
title: Zpracování chyb a výjimek v pracovních postupech
description: Zjistěte, jak zpracovat chyby a výjimky, ke kterým dochází v automatizovaných úlohách a pracovních postupech vytvořených pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284028"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Zpracování chyb a výjimek v Aplikacích Azure Logic Apps

Způsob, jakým jakákoli integrační architektura vhodně zpracovává prostoje nebo problémy způsobené závislými systémy, může představovat výzvu. Abychom vám pomohli vytvořit robustní a odolné integrace, které řádně zpracovávají problémy a selhání, logic apps poskytuje prvotřídní prostředí pro zpracování chyb a výjimek.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zásady opakování

Pro nejzákladnější výjimku a zpracování chyb můžete použít *zásadu opakování* v libovolné akci nebo aktivační události, kde je podporována, například naleznete v [tématu akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Zásady opakování určuje, zda a jak akce nebo aktivační událost opakování požadavku při původní požadavek časový limit nebo selhání, což je jakýkoli požadavek, který má za následek 408, 429 nebo 5xx odpověď. Pokud se nepoužívá žádná jiná zásada opakování, použije se výchozí zásada.

Tady jsou typy zásad opakování:

| Typ | Popis |
|------|-------------|
| **Výchozí** | Tato zásada odešle až čtyři opakování *v exponenciálně rostoucí* intervaly, které měřítko o 7,5 sekundy, ale jsou omezeny mezi 5 a 45 sekund. |
| **Exponenciální interval**  | Tato zásada čeká náhodný interval vybraný z exponenciálně rostoucí rozsah před odesláním další požadavek. |
| **Pevný interval**  | Tato zásada čeká zadaný interval před odesláním dalšího požadavku. |
| **Žádné**  | Neodesílejte žádost znovu. |
|||

Informace o omezení zásad opakování naleznete v [tématu Omezení a konfigurace aplikací logiky](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Změnit zásady opakování

Chcete-li vybrat jinou zásadu opakování, postupujte takto:

1. Otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Otevřete **nastavení** pro akci nebo aktivační událost.

1. Pokud akce nebo aktivační událost podporuje zásady opakování, vyberte v části **Zásady opakování**požadovaný typ.

Nebo můžete ručně zadat zásady opakování `inputs` v části pro akci nebo aktivační událost, která podporuje zásady opakování. Pokud nezadáte zásadu opakování, akce použije výchozí zásadu.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Požadováno*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*opakování typu zásad*> | Řetězec | Typ zásady opakování, který `default`chcete `none` `fixed`použít: , , , nebo`exponential` |
| <*interval opakování*> | Řetězec | Interval opakování, ve kterém musí hodnota používat [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Výchozí minimální interval `PT5S` je a `PT1D`maximální interval je . Při použití zásady exponenciálního intervalu můžete zadat různé minimální a maximální hodnoty. |
| <*pokusy o opakování*> | Integer | Počet pokusů o opakování, které musí být mezi 1 a 90 |
||||

*Volitelné*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*minimální interval*> | Řetězec | Pro zásadu exponenciálního intervalu nejmenší interval pro náhodně vybraný interval ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*maximální interval*> | Řetězec | Pro zásadu exponenciálního intervalu největší interval pro náhodně vybraný interval ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Zde je více informací o různých typech zásad.

<a name="default-retry"></a>

### <a name="default"></a>Výchozí

Pokud nezadáte zásadu opakování, akce použije výchozí zásadu, což je ve skutečnosti [exponenciální intervalová zásada,](#exponential-interval) která odešle až čtyři opakování v exponenciálně rostoucích intervalech, které jsou škálovány o 7,5 sekundy. Interval je omezen na 5 až 45 sekund.

Ačkoli není explicitně definována v akci nebo aktivační události, zde je, jak se chová výchozí zásady v příkladu akce HTTP:

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

Chcete-li určit, že akce nebo aktivační událost nezopakují neúspěšné požadavky, `none`nastavte <> *typu zásady opakování* na .

### <a name="fixed-interval"></a>Pevný interval

Chcete-li určit, že akce nebo aktivační událost čeká zadaný interval před odesláním `fixed`dalšího požadavku, nastavte <> *typu zásady opakování* na .

*Příklad*

Tato zásada opakování se pokusí získat nejnovější zprávy ještě dvakrát po první neúspěšný požadavek s 30sekundové zpoždění mezi jednotlivými pokusy:

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

Chcete-li určit, že akce nebo aktivační událost čeká náhodný interval před odesláním dalšího `exponential`požadavku, nastavte <> *retry-policy-type* na . Náhodný interval je vybrán z exponenciálně rostoucího rozsahu. Volitelně můžete také přepsat výchozí minimální a maximální intervaly zadáním vlastních minimálních a maximálních intervalů.

**Náhodné proměnné rozsahy**

Tato tabulka ukazuje, jak aplikace Logic Apps generuje jednotnou náhodnou proměnnou v zadaném rozsahu pro každý pokus až do počtu opakování včetně:

| Číslo opakování | Minimální interval | Maximální interval |
|--------------|------------------|------------------|
| 1 | max(0, <*minimální interval*>) | min (interval, <*> maximálního intervalu)* |
| 2 | max (interval, <*> minimálního intervalu)* | min(2 * interval, <*maximální interval*>) |
| 3 | max (2 * interval, <*> minimálního intervalu)* | min(4 * interval, <*maximální interval*>) |
| 4 | max (4 * interval, <*> minimálního intervalu)* | min(8 * interval, <*maximální interval*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Zachycení a zpracování selhání změnou "spustit po" chování

Při přidání akcí v Návrháři aplikace logiky implicitně deklarovat pořadí pro spuštění těchto akcí. Po dokončení akce je tato akce označena stavem `Failed` `Skipped`, `TimedOut`například `Succeeded`, , nebo . V každé definici `runAfter` akce vlastnost určuje předchůdce akce, která musí nejprve dokončit a stavy povolené pro tohoto předchůdce před následníkakce může spustit. Ve výchozím nastavení se akce, kterou přidáte do návrháře, spustí až po dokončení `Succeeded` stavu předchůdce.

Když akce vyvolá neošetřenou chybu nebo výjimku, akce je označena `Failed`a každá následnická akce je označena `Skipped`. Pokud se toto chování stane pro akci, která má paralelní větve, modul Logic Apps následuje ostatní větve k určení jejich stavy dokončení. Pokud například větev `Skipped` končí akcí, je stav dokončení této větve založen na stavu předchůdce této přeskočené akce. Po dokončení spuštění aplikace logiky modul určuje stav celého spuštění vyhodnocením všech stavů větve. Pokud některá větev končí selháním, `Failed`je označena celá aplikace logiky .

![Příklady, které ukazují, jak jsou vyhodnocovány stavy běhu](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Chcete-li se ujistit, že akce může být stále spuštěna i přes stav předchůdce, [přizpůsobte chování akce "spustit po",](#customize-run-after) aby bylo možné zpracovat neúspěšné stavy předchůdce.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Přizpůsobit chování "spustit po"

Chování akce "spustit po" můžete přizpůsobit tak, aby akce byla spuštěna, `Failed` `Skipped`když `TimedOut`je stav předchůdce buď `Succeeded`, , , nebo některý z těchto stavů. Chcete-li například odeslat e-mail po `Failed`označení `Succeeded`akce předchůdce aplikace Excel Online `Add_a_row_into_a_table` , změňte chování "spustit po" podle následujícího kroku:

* V návrhovém zobrazení vyberte tlačítko elipsy (**...**) a pak vyberte **Konfigurovat spuštění po .**

  ![Konfigurace chování "spustit po" pro akci](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Obrazec akce zobrazuje výchozí stav, který je vyžadován pro předchůdce akce, což je **Přidat řádek do tabulky** v tomto příkladu:

  ![Výchozí chování "spustit po" pro akci](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Změňte chování "spustit po" na požadovaný stav, který se v tomto příkladu **nezdařil:**

  ![Změnit chování "spustit po" na "selhalo"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Chcete-li určit, zda je akce `Failed`předchůdce `Skipped` `TimedOut`spuštěna, zda je akce předchůdce označena jako , nebo , vyberte další stavy:

  ![Změna chování "spustit po" mít jiný stav](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* V zobrazení kódu upravte v definici JSON akce `runAfter` vlastnost, která následuje podle této syntaxe:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  V tomto příkladu `runAfter` změňte vlastnost z `Succeeded` na `Failed`:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Chcete-li určit, že akce bude `Failed`spuštěna bez ohledu na to, zda je předchůdce označen jako , `Skipped` nebo `TimedOut`, přidejte další stavy:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Vyhodnocení akcí s obory a jejich výsledky

Podobně jako spuštění kroků po `runAfter` jednotlivých akcích s vlastností můžete seskupit akce společně uvnitř [oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Obory můžete použít, pokud chcete logicky seskupit akce, posoudit agregační stav oboru a provádět akce na základě tohoto stavu. Po dokončení všech akcí v oboru získá samotný obor svůj vlastní stav.

Chcete-li zkontrolovat stav oboru, můžete použít stejná kritéria, která používáte ke kontrole `Succeeded` `Failed`stavu spuštění aplikace logiky, například , a tak dále.

Ve výchozím nastavení, když jsou akce oboru úspěšné, je `Succeeded`označena stav oboru . Pokud konečná akce v oboru `Failed` `Aborted`výsledky jako nebo , `Failed`stav oboru je označen .

Chcete-li zachytit `Failed` výjimky v oboru a spustit akce, `runAfter` které `Failed` zpracovávají tyto chyby, můžete použít vlastnost pro tento obor. Tímto způsobem, pokud *všechny* akce v oboru `runAfter` nezdaří a použít vlastnost pro tento obor, můžete vytvořit jednu akci zachytit selhání.

Omezení oborů naleznete v tématu [Omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Získání kontextu a výsledků pro chyby

Přestože zachycení selhání z oboru je užitečné, můžete také chtít kontext, který vám pomůže přesně pochopit, které akce se nezdařilo plus všechny chyby nebo stavové kódy, které byly vráceny.

Funkce [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) poskytuje kontext o výsledcích ze všech akcí v oboru. Funkce `result()` přijme jeden parametr, což je název oboru, a vrátí pole, které obsahuje všechny výsledky akce z tohoto oboru. Tyto objekty akce obsahují stejné `actions()` atributy jako objekt, jako je například čas zahájení akce, čas ukončení, stav, vstupy, ID korelace a výstupy. Chcete-li odeslat kontext pro všechny akce, které `@result()` se `runAfter` nezdařily v rámci oboru, můžete snadno spárovat výraz s vlastností.

Chcete-li spustit akci pro každou akci `Failed` v oboru, který má výsledek, a filtrovat pole `@result()` výsledků až na neúspěšné akce, můžete spárovat výraz s akcí [**Pole filtru**](logic-apps-perform-data-operations.md#filter-array-action) a [**Pro každou**](../logic-apps/logic-apps-control-flow-loops.md) smyčku. Můžete vzít filtrované pole výsledků a provést akci `For_each` pro každou chybu pomocí smyčky.

Zde je příklad následovaný podrobným vysvětlením, které odešle požadavek HTTP POST s tělem odpovědi pro všechny akce, které se nezdařily v rámci oboru "My_Scope":

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

Tady je podrobný návod, který popisuje, co se děje v tomto příkladu:

1. Chcete-li získat výsledek ze všech akcí uvnitř "My_Scope", akce **Pole filtrů** používá tento výraz filtru:`@result('My_Scope')`

1. Podmínkou pro **pole** `@result()` filtru je libovolná `Failed`položka, která má stav rovný . Tato podmínka filtruje pole, které má všechny výsledky akce z "My_Scope" až do pole s pouze neúspěšnými výsledky akce.

1. Proveďte `For_each` akci smyčky na výstupech *filtrovaného pole.* Tento krok provede akci pro každý výsledek neúspěšné akce, který byl dříve filtrován.

   Pokud jedna akce v oboru selže, `For_each` akce ve smyčce spustit pouze jednou. Více neúspěšných akcí způsobí jednu akci na selhání.

1. Odeslat HTTP POST `For_each` na tělo odpovědi `@item()['outputs']['body']` na položku, což je výraz.

   Obrazec `@result()` položky je `@actions()` stejný jako obrazec a lze jej analyzovat stejným způsobem.

1. Zahrňte dvě vlastní záhlaví`@item()['name']`s názvem akce, která selhala ( ) a id sledování klienta při selhání (`@item()['clientTrackingId']`).

Pro referenci, zde je příklad `@result()` jedné položky, zobrazující `name`, a `body` `clientTrackingId` vlastnosti, které jsou analyzovány v předchozím příkladu. Mimo `For_each` akci `@result()` vrátí pole těchto objektů.

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

Chcete-li provést různé vzory zpracování výjimek, můžete použít výrazy popsané v tomto článku. Můžete provést jednu akci zpracování výjimky mimo obor, který přijímá celé filtrované `For_each` pole selhání a odebrat akci. Můžete také zahrnout další užitečné `\@result()` vlastnosti z odpovědi, jak bylo popsáno výše.

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitoru

Předchozí vzory jsou skvělý způsob, jak zpracovat chyby a výjimky v rámci spuštění, ale můžete také identifikovat a reagovat na chyby nezávislé na samotnéspuštění. [Azure Monitor](../azure-monitor/overview.md) poskytuje jednoduchý způsob, jak odeslat všechny události pracovního postupu, včetně všech stavů spuštění a akcí, do [pracovního prostoru Log Analytics](../azure-monitor/platform/data-platform-logs.md), [účtu úložiště Azure](../storage/blobs/storage-blobs-overview.md)nebo Azure Event [Hubs](../event-hubs/event-hubs-about.md).

Chcete-li vyhodnotit stavy spuštění, můžete sledovat protokoly a metriky nebo je publikovat do libovolného nástroje pro monitorování, který upřednostňujete. Jednou z možných možností je streamování všech událostí prostřednictvím centra událostí do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). V Stream Analytics můžete psát živé dotazy na základě všech anomálií, průměrů nebo selhání z diagnostických protokolů. Stream Analytics můžete použít k odesílání informací do jiných zdrojů dat, jako jsou fronty, témata, SQL, Azure Cosmos DB nebo Power BI.

## <a name="next-steps"></a>Další kroky

* [Podívejte se, jak zákazník vytváří zpracování chyb pomocí Aplikací logiky Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Další příklady a scénáře aplikací logiky](../logic-apps/logic-apps-examples-and-scenarios.md)
