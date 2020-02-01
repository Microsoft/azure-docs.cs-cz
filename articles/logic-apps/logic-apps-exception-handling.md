---
title: Zpracování chyb a výjimek v pracovních postupech
description: Naučte se zpracovávat chyby a výjimky, ke kterým dochází v automatizovaných úlohách a pracovních postupech vytvořených pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906695"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Zpracování chyb a výjimek v Azure Logic Apps

Způsob, jakým každá architektura integrace vhodně zpracovává výpadky nebo problémy způsobené závislými systémy, může představovat výzvu. Abychom vám pomohli vytvořit robustní a odolnou integraci, která řádně vyřeší problémy a chyby, Logic Apps poskytuje prvotřídní prostředí pro zpracování chyb a výjimek.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zásady opakování pokusů

U většiny základních výjimek a zpracování chyb můžete použít *zásady opakování* v jakékoli akci nebo triggeru, kde se podporuje, například v tématu [akce http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Zásada opakování určuje, jestli se akce nebo aktivační procedura znovu pokusí o odeslání žádosti, když vyprší časový limit původní žádosti, což je jakákoli žádost, která má za následek odpověď 408, 429 nebo 5xx. Pokud se nepoužijí žádné jiné zásady opakování, použije se výchozí zásada.

Tady jsou typy zásad opakování:

| Typ | Popis |
|------|-------------|
| **Výchozí** | Tato zásada odesílá až čtyři opakované pokusy ve *exponenciálně rostoucích* intervalech, které se škálují po 7,5 sekund, ale jsou omezené mezi 5 a 45 sekundami. |
| **Exponenciální interval**  | Tato zásada počká náhodný interval vybraný z exponenciálně rostoucího rozsahu před odesláním dalšího požadavku. |
| **Pevný interval**  | Tato zásada počká zadaný interval před odesláním dalšího požadavku. |
| **Žádné**  | Neodešlete požadavek znovu. |
|||

Informace o omezeních zásad opakování najdete v tématu [omezení Logic Apps a konfigurace](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Změnit zásady opakování

Pokud chcete vybrat jiné zásady opakování, postupujte následovně:

1. Otevřete aplikaci logiky v návrháři aplikace logiky.

1. Otevřete **Nastavení** akce nebo triggeru.

1. Pokud akce nebo Trigger podporuje zásady opakování, vyberte v části **zásady opakování**možnost požadovaný typ.

Případně můžete zásady opakování zadat ručně v části `inputs` pro akci nebo aktivační událost, která podporuje zásady opakování. Pokud nezadáte zásadu opakování, akce použije výchozí zásady.

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

*Vyžaduje*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*retry-policy-type*> | Řetězec | Typ zásady opakování, který chcete použít: `default`, `none`, `fixed`nebo `exponential` |
| <*retry-interval*> | Řetězec | Interval opakování, ve kterém hodnota musí používat [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Výchozí interval minimální hodnoty je `PT5S` a maximální interval je `PT1D`. Když použijete exponenciální zásadu intervalu, můžete zadat jiné minimální a maximální hodnoty. |
| <*retry-attempts*> | Integer | Počet pokusů o opakování, který musí být mezi 1 a 90. |
||||

*Volitelné*

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*minimum-interval*> | Řetězec | Pro pravidlo exponenciálního intervalu, nejmenší interval náhodně vybraného intervalu ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*minimum-interval*> | Řetězec | Pro pravidlo exponenciálního intervalu, nejdelší interval pro náhodně vybraný interval ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Zde jsou další informace o různých typech zásad.

<a name="default-retry"></a>

### <a name="default"></a>Výchozí

Pokud nezadáte zásadu opakování, akce použije výchozí zásadu, která je ve skutečnosti [exponenciálního intervalu](#exponential-interval) , který ve skutečnosti pošle čtyři opakované pokusy, a to v exponenciálně stoupajících intervalů, které se škálují po 7,5 sekund. Interval je omezené mezi 5 a 45 sekundami.

I když v akci nebo triggeru není explicitně definované, tady je způsob, jakým se v ukázkové akci HTTP chovají výchozí zásady:

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

### <a name="none"></a>Žádné

Pokud chcete určit, že akce nebo Trigger neopakuje neúspěšné požadavky, nastavte <*Opakovat – typ zásad*> na `none`.

### <a name="fixed-interval"></a>Pevný interval

Chcete-li určit, že akce nebo aktivační událost před odesláním dalšího požadavku počká zadaný interval, nastavte <*Opakovat – typ zásad*> na `fixed`.

*Příklad*

Tato zásada opakování se pokusí získat nejnovější novinky po prvním neúspěšném požadavku s 30 sekundovým zpožděním mezi jednotlivými pokusy:

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

Chcete-li určit, že akce nebo aktivační událost před odesláním dalšího požadavku počká na náhodný interval, nastavte < >*typu opakovat-zásady*na `exponential`. Náhodný interval je vybrán z exponenciálního rostoucího rozsahu. Volitelně můžete také přepsat výchozí minimální a maximální intervaly zadáním vlastního minimálního a maximálního intervalu.

**Rozsahy náhodných proměnných**

Tato tabulka ukazuje, jak Logic Apps generuje v zadaném rozsahu jednotnou náhodnou proměnnou pro každý pokus o opakování až do a včetně počtu opakovaných pokusů:

| Číslo opakování | Minimální interval | Maximální interval |
|--------------|------------------|------------------|
| 1\. místo | Max (0, >*minimálního intervalu*<) | min (interval <*Maximum-interval*>) |
| 2 | Max (interval, < >*minimálního intervalu*) | min (2 * interval, <*Maximum-interval*>) |
| 3 | Max (2 * interval, < >*minimálního intervalu*) | min (4 * interval, <*Maximum-interval*>) |
| 4 | Max (4 * interval, < >*minimální interval*) | min (8 * interval, <*Maximum-interval*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Zachycení a zpracování selhání změnou chování spustit po

Když přidáte akce v návrháři aplikace logiky, implicitně deklarujete pořadí, které se má použít pro spuštění těchto akcí. Po dokončení akce bude tato akce označena stavem, například `Succeeded`, `Failed`, `Skipped`nebo `TimedOut`. V každé definici akce vlastnost `runAfter` Určuje akci předchůdce, která musí být nejprve dokončena, a stav povolený pro dané předchůdce, než bude možné spustit akci následníka. Ve výchozím nastavení se akce, kterou přidáte v návrháři, spustí až po dokončení předchůdce se stavem `Succeeded`.

Pokud akce vyvolá neošetřenou chybu nebo výjimku, je akce označena `Failed`a všechny akce následníka jsou označeny `Skipped`. Pokud k tomuto chování dochází pro akci, která má paralelní větve, modul Logic Apps se řídí ostatními větvemi k určení stavu jejich dokončení. Pokud například větev končí akcí `Skipped`, je stav dokončení této větve založený na tomto stavu přeskočené akce. Po dokončení spuštění aplikace logiky určí modul celý stav spuštění vyhodnocením všech stavů větví. Pokud dojde k výpadku jakékoli větve, je celý běh aplikace logiky označený `Failed`.

![Příklady, které ukazují, jak se vyhodnocují stavy spuštění](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Aby se zajistilo, že akce může stále běžet navzdory jejímu stavu předchůdce, [Přizpůsobte chování akce spustit po](#customize-run-after) pro zpracování neúspěšných stavů předchůdce.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Přizpůsobení chování "spustit po"

Chování akce "spustit po" můžete přizpůsobit tak, aby se akce spustila, když je stav předchůdce buď `Succeeded`, `Failed`, `Skipped`, `TimedOut`nebo kterýkoli z těchto stavů. Pokud například chcete odeslat e-mail po `Add_a_row_into_a_table` akci předchůdce Excelu online `Failed`namísto `Succeeded`, změňte chování "spustit po" podle kroků v jednom kroku:

* V zobrazení Návrh vyberte tlačítko se třemi tečkami ( **...** ) a pak vyberte **Konfigurovat spustit po**.

  ![Konfigurace chování "spustit po" pro akci](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  V obrazci akce se zobrazuje výchozí stav, který je požadován pro akci předchůdce, což je **Přidání řádku do tabulky** v tomto příkladu:

  ![Výchozí chování "spustit po" pro akci](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Změňte chování "spustit po" na stav, který chcete, což se v tomto příkladu **nezdařilo** :

  ![Změnit chování "spustit po" na "selhalo"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Pokud chcete určit, že se akce spustí, pokud je akce předchůdce označená jako `Failed`, `Skipped` nebo `TimedOut`, vyberte další stavy:

  ![Změnit chování spustit po po, aby měl jakýkoliv jiný stav](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* V zobrazení kódu v definici JSON akce upravte vlastnost `runAfter`, která následuje za touto syntaxí:

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

  V tomto příkladu změňte vlastnost `runAfter` z `Succeeded` na `Failed`:

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

  Pokud chcete určit, že se akce spustí, pokud je akce předchůdce označená jako `Failed`, `Skipped` nebo `TimedOut`, přidejte další stavy:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Vyhodnotit akce s obory a jejich výsledky

Podobně jako u jednotlivých akcí s vlastností `runAfter` můžete seskupit akce do skupin společně v rámci [oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Obory můžete použít, když chcete logicky seskupit akce společně, vyhodnotit agregovaný stav oboru a provádět akce na základě tohoto stavu. Po dokončení všech akcí v oboru, který je spuštěný, získá samotný rozsah svůj vlastní stav.

Chcete-li zjistit stav oboru, můžete použít stejná kritéria, jako je například `Succeeded`, `Failed`a tak dále, pomocí kterých můžete kontrolovat stav spuštění aplikace logiky.

Ve výchozím nastavení platí, že po úspěšném dokončení všech akcí oboru je stav oboru označený `Succeeded`. Pokud je poslední akce v oboru výsledkem `Failed` nebo `Aborted`, stav oboru je označen jako `Failed`.

Chcete-li zachytit výjimky v oboru `Failed` a spustit akce, které tyto chyby zpracovávají, můžete pro daný `Failed` obor použít vlastnost `runAfter`. Tímto způsobem, pokud *některé* akce v oboru selžou a použijete vlastnost `runAfter` pro tento obor, můžete vytvořit jedinou akci pro zachycení selhání.

Omezení pro rozsahy najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Získání kontextu a výsledků pro selhání

I když je užitečné zachycení selhání z oboru, můžete také chtít, aby byl kontext, který vám pomůže pochopit přesně to, které akce se nezdařily, a všechny chyby nebo stavové kódy, které byly vráceny.

Funkce [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) poskytuje kontext o výsledcích ze všech akcí v oboru. Funkce `result()` přijímá jeden parametr, což je název oboru, a vrátí pole, které obsahuje všechny výsledky akce v rámci daného oboru. Tyto objekty akcí zahrnují stejné atributy jako objekt `actions()`, jako je čas spuštění akce, čas ukončení, stav, vstupy, ID korelace a výstupy. Chcete-li odeslat kontext pro všechny akce, které se v rámci oboru nezdařily, můžete snadno spárovat výraz `@result()` s vlastností `runAfter`.

Chcete-li spustit akci pro každou akci v oboru, který má výsledek `Failed` a filtrovat pole výsledků dolů na neúspěšné akce, můžete spárovat `@result()` výraz s akcí [**filtru pole**](logic-apps-perform-data-operations.md#filter-array-action) a a [**pro každou**](../logic-apps/logic-apps-control-flow-loops.md) smyčku. Filtrované pole výsledků můžete převzít a provést akci při každém selhání pomocí smyčky `For_each`.

Tady je příklad následovaný detailním vysvětlením, který pošle požadavek HTTP POST s textem odpovědi pro všechny akce, které selhaly v rámci oboru "My_Scope":

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

1. Pro získání výsledku ze všech akcí uvnitř "My_Scope" akce **pole filtru** používá tento výraz filtru: `@result('My_Scope')`

1. Podmínka pro **pole filtru** je jakákoli `@result()` položka, která má stav roven `Failed`. Tato podmínka filtruje pole, které má všechny výsledky akce z "My_Scope" dolů do pole, které obsahuje pouze výsledky neúspěšných akcí.

1. Proveďte akci `For_each` smyčky u *filtrovaných* výstupů polí. Tento krok provádí akci u každého neúspěšného výsledku akce, který byl dříve filtrován.

   Pokud se jedna akce v oboru nezdařila, akce ve smyčce `For_each` spustit pouze jednou. Několik neúspěšných akcí způsobuje jednu akci za selhání.

1. Odešle příspěvek HTTP na text odpovědi `For_each` položky, což je výraz `@item()['outputs']['body']`.

   Obrazec `@result()` položky je stejný jako obrazec `@actions()` a lze ho analyzovat stejným způsobem.

1. Zahrňte dvě vlastní hlavičky s názvem nezdařené akce (`@item()['name']`) a neúspěšného spuštění ID sledování klienta (`@item()['clientTrackingId']`).

Tady je příklad jedné položky `@result()`, která ukazuje vlastnosti `name`, `body`a `clientTrackingId`, které jsou analyzovány v předchozím příkladu. Mimo `For_each` akci `@result()` vrátí pole těchto objektů.

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

Chcete-li provádět různé vzory zpracování výjimek, můžete použít výrazy, které jsou popsány v tomto článku. Můžete se rozhodnout spustit jedinou akci zpracování výjimek mimo obor, který přijímá celé filtrované pole selhání, a odebrat akci `For_each`. Můžete také zahrnout další užitečné vlastnosti z odpovědi `\@result()`, jak je popsáno výše.

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitor

Předchozí vzory představují skvělý způsob zpracování chyb a výjimek v rámci spuštění, ale můžete také identifikovat a reagovat na chyby nezávisle na samotném spuštění. [Azure monitor](../azure-monitor/overview.md) poskytuje jednoduchý způsob, jak odeslat všechny události pracovního postupu, včetně všech stavů spuštění a akce, do [pracovního prostoru Log Analytics](../azure-monitor/platform/data-platform-logs.md), [účtu Azure Storage](../storage/blobs/storage-blobs-overview.md)nebo [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Chcete-li vyhodnotit stavy spuštění, můžete monitorovat protokoly a metriky nebo je publikovat do libovolného nástroje pro monitorování, které dáváte přednost. Jednou z možných možností je streamování všech událostí prostřednictvím Event Hubs do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). V Stream Analytics můžete psát živé dotazy na základě jakýchkoli anomálií, průměrů nebo chyb z diagnostických protokolů. K posílání informací do jiných zdrojů dat, jako jsou fronty, témata, SQL, Azure Cosmos DB nebo Power BI, můžete použít Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Podívejte se, jak zákazník sestavuje zpracování chyb pomocí Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Najít další Logic Apps příklady a scénáře](../logic-apps/logic-apps-examples-and-scenarios.md)
