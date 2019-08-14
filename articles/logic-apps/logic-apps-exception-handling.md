---
title: Zpracování chyb a výjimek – Azure Logic Apps | Microsoft Docs
description: Přečtěte si o vzorech pro zpracování chyb a výjimek v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60996576"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Zpracování chyb a výjimek v Azure Logic Apps

Způsob, jakým každá architektura integrace vhodně zpracovává výpadky nebo problémy způsobené závislými systémy, může představovat výzvu. Abychom vám pomohli vytvořit robustní a odolnou integraci, která řádně vyřeší problémy a chyby, Logic Apps poskytuje prvotřídní prostředí pro zpracování chyb a výjimek. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Zásady opakování pokusů

Pro většinu základních výjimek a zpracování chyb můžete použít *zásady opakování* v jakékoli akci nebo triggeru, kde se podporuje. Zásada opakování určuje, jestli se akce nebo aktivační procedura znovu pokusí o odeslání žádosti, když vyprší časový limit původní žádosti, což je jakákoli žádost, která má za následek odpověď 408, 429 nebo 5xx. Pokud se nepoužijí žádné jiné zásady opakování, použije se výchozí zásada. 

Tady jsou typy zásad opakování: 

| type | Popis | 
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

2. Otevřete **Nastavení** akce nebo triggeru.

3. Pokud akce nebo Trigger podporuje zásady opakování, vyberte v části **zásady opakování**možnost požadovaný typ. 

Případně můžete zásady opakování zadat ručně v `inputs` části pro akci nebo aktivační událost, která podporuje zásady opakování. Pokud nezadáte zásadu opakování, akce použije výchozí zásady.

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

*Požadováno*

| Value | type | Popis |
|-------|------|-------------|
| <*retry-policy-type*> | Řetězec | Typ zásady opakování, který chcete použít: `default`, `none`, `fixed`nebo`exponential` | 
| <*retry-interval*> | Řetězec | Interval opakování, ve kterém hodnota musí používat [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Výchozí minimální interval je `PT5S` a maximální interval je. `PT1D` Když použijete exponenciální zásadu intervalu, můžete zadat jiné minimální a maximální hodnoty. | 
| <*retry-attempts*> | Integer | Počet pokusů o opakování, který musí být mezi 1 a 90. | 
||||

*Volitelné*

| Value | type | Popis |
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

Pokud chcete určit, že akce nebo Trigger neopakuje neúspěšné požadavky, nastavte <*Opakovat-zásady-typ*> na `none`.

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

Chcete-li určit, že akce nebo aktivační událost před odesláním dalšího požadavku počká na náhodný interval, nastavte <*Opakovat – typ zásad*> na `exponential`. Náhodný interval je vybrán z exponenciálního rostoucího rozsahu. Volitelně můžete také přepsat výchozí minimální a maximální intervaly zadáním vlastního minimálního a maximálního intervalu.

**Rozsahy náhodných proměnných**

Tato tabulka ukazuje, jak Logic Apps generuje v zadaném rozsahu jednotnou náhodnou proměnnou pro každý pokus o opakování až do a včetně počtu opakovaných pokusů:

| Číslo opakování | Minimální interval | Maximální interval |
|--------------|------------------|------------------|
| 1 | Max (0, >*minimálního intervalu*<) | min (interval <*Maximum-interval*>) |
| 2 | Max (interval, < >*minimálního intervalu*) | min (2 * interval, <*Maximum-interval*>) |
| 3 | Max (2 * interval, < >*minimálního intervalu*) | min (4 * interval, <*Maximum-interval*>) |
| 4 | Max (4 * interval, < >*minimální interval*) | min (8 * interval, <*Maximum-interval*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Zachycení a zpracování selhání s vlastností Runafter šablonové

Každá akce aplikace logiky deklaruje akce, které je třeba dokončit před zahájením této akce, podobně jako určení pořadí kroků v pracovním postupu. V definici akce definuje vlastnost **runafter šablonové** toto pořadí a je objekt, který popisuje, které akce a stavy akcí provádějí akci.

Ve výchozím nastavení se všechny akce, které přidáte v návrháři aplikace logiky, nastaví tak, aby se spouštěly po předchozím kroku, kdy je výsledek předchozího kroku **úspěšný**. Můžete ale přizpůsobit hodnotu **runafter šablonové** , aby se akce aktivovaly v případě, že předchozí akce vyplývají z **chyb**, přeskočily nebo některé kombinace těchto hodnot. Pokud například chcete přidat položku do konkrétního tématu Service Bus po chybě konkrétní akce **Insert_Row** , můžete použít tuto ukázku definice **runafter šablonové** :

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

Vlastnost **runafter šablonové** je nastavena na hodnotu spustit, když se stav akce Insert_Row **nezdařil**. Chcete-li spustit akci, pokud je stav akce **úspěch**, **selhalo**nebo **vynecháno**, použijte tuto syntaxi:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Akce, které byly úspěšně spuštěny a dokončeny po úspěšném provedení předchozí akce, jsou označeny jako **úspěšné**. Toto chování znamená, že pokud úspěšně zachytíte všechny chyby v pracovním postupu, bude se samostatně označit jako **úspěšný**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Vyhodnotit akce s obory a jejich výsledky

Podobně jako při provádění jednotlivých akcí s vlastností **runafter šablonové** můžete seskupit akce v rámci [oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Obory můžete použít, když chcete logicky seskupit akce společně, vyhodnotit agregovaný stav oboru a provádět akce na základě tohoto stavu. Po dokončení všech akcí v oboru, který je spuštěný, získá samotný rozsah svůj vlastní stav. 

Chcete-li zjistit stav oboru, můžete použít stejná kritéria, která používáte ke kontrole stavu spuštění aplikace logiky, jako je například **úspěch**, **selhání**a tak dále. 

Ve výchozím nastavení platí, že po úspěšném dokončení všech akcí oboru je stav oboru označen jako **úspěšný**. Pokud se poslední akce v oboru vyplní jako **neúspěšná** nebo **přerušená**, stav tohoto oboru se označí jako neúspěšné. 

Chcete-li zachytit výjimky v neúspěšném rozsahu a spustit akce, které tyto chyby zpracovávají, můžete použít vlastnost **runafter šablonové** pro daný obor, u kterého **došlo k selhání** . Tímto způsobem, pokud dojde k selhání *jakýchkoli* akcí v oboru, a pro tento obor použijete vlastnost **runafter šablonové** , můžete vytvořit jednu akci pro zachycení selhání.

Omezení pro rozsahy najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Získání kontextu a výsledků pro selhání

I když je užitečné zachycení selhání z oboru, můžete také chtít, aby byl kontext, který vám pomůže pochopit přesně to, které akce se nezdařily, a všechny chyby nebo stavové kódy, které byly vráceny. `@result()` Výraz poskytuje kontext pro výsledek všech akcí v oboru.

`@result()` Výraz přijímá jeden parametr (název oboru) a vrací pole všech výsledků akce v rámci daného oboru. Tyto objekty akcí zahrnují stejné atributy jako  **\@objekt Actions ()** , jako je čas spuštění akce, čas ukončení, stav, vstupy, ID korelace a výstupy. Chcete-li odeslat kontext pro všechny akce, které selhaly v rámci rozsahu, můžete snadno spárovat  **\@funkci Result ()** s vlastností **runafter šablonové** .

Chcete-li spustit akci pro každou akci v oboru, který má **neúspěšný** výsledek, a filtrovat pole výsledků dolů na neúspěšné akce, můžete spárovat  **\@výsledek ()** s akcí **[filtru pole](../connectors/connectors-native-query.md)** a a [**pro každou**](../logic-apps/logic-apps-control-flow-loops.md) smyčku. Filtrované pole výsledků můžete převzít a provést akci pro každou chybu pomocí příkazu **pro každou** smyčku. 

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

1. Pro získání výsledku ze všech akcí uvnitř "My_Scope" akce **pole filtru** používá tento výraz filtru:`@result('My_Scope')`

2. Podmínka pro **pole filtru** je jakákoli `@result()` položka, která má stav rovno **selhání**. Tato podmínka filtruje pole, které má všechny výsledky akce z "My_Scope" dolů do pole, které obsahuje pouze výsledky neúspěšných akcí.

3. Provede akci **pro každou** smyčku u filtrovaných výstupů *polí* . Tento krok provádí akci u každého neúspěšného výsledku akce, který byl dříve filtrován.

   Pokud se jedna akce v oboru nezdařila, akce v **pro každou** smyčku se spustí pouze jednou. 
   Několik neúspěšných akcí způsobuje jednu akci za selhání.

4. Odešle příspěvek http na **pro každé** tělo odpovědi položky, což je `@item()['outputs']['body']` výraz. 

   Obrazec položky je stejný jako tvar a lze ho analyzovat stejným způsobem. `@actions()` `@result()`

5. Přidejte dvě vlastní hlavičky s názvem akce při selhání (`@item()['name']`) a neúspěšném spuštěným spuštěním ID`@item()['clientTrackingId']`sledování klienta ().

Tady je příklad `@result()` jedné položky, která zobrazuje **název**, **text**a vlastnosti **clientTrackingId** , které jsou analyzovány v předchozím příkladu. Mimo a **pro každou** akci `@result()` vrátí pole těchto objektů.

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

Chcete-li provádět různé vzory zpracování výjimek, můžete použít výrazy, které jsou popsány v tomto článku. Můžete se rozhodnout spustit jedinou akci zpracování výjimek mimo obor, který přijímá celé filtrované pole selhání, a **pro každou** akci odebrat. Můžete také zahrnout další užitečné vlastnosti z  **\@odpovědi Result ()** , jak je popsáno výše.

## <a name="azure-diagnostics-and-metrics"></a>Azure Diagnostics a metriky

Předchozí vzory představují skvělý způsob zpracování chyb a výjimek v rámci spuštění, ale můžete také identifikovat a reagovat na chyby nezávisle na samotném spuštění. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) poskytuje jednoduchý způsob, jak odeslat všechny události pracovního postupu, včetně všech stavů spuštění a akce, do účtu Azure Storage nebo centra událostí vytvořeného pomocí Azure Event Hubs. 

Chcete-li vyhodnotit stavy spuštění, můžete monitorovat protokoly a metriky nebo je publikovat do libovolného nástroje pro monitorování, které dáváte přednost. Jednou z možných možností je streamování všech událostí prostřednictvím Event Hubs do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). V Stream Analytics můžete psát živé dotazy na základě jakýchkoli anomálií, průměrů nebo chyb z diagnostických protokolů. K posílání informací do jiných zdrojů dat, jako jsou fronty, témata, SQL, Azure Cosmos DB nebo Power BI, můžete použít Stream Analytics.

## <a name="next-steps"></a>Další postup

* [Podívejte se, jak zákazník sestavuje zpracování chyb pomocí Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Najít další Logic Apps příklady a scénáře](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
