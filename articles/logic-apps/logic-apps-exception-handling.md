---
title: "Chyba a zpracování výjimek pro Logic Apps v Azure | Microsoft Docs"
description: "Vzory pro chybové události a zpracování výjimek v Logic Apps."
services: logic-apps
documentationcenter: 
author: dereklee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 91819d0fba30dd2ada981435fa13b8ae0a7fcc45
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Zpracování chyb a výjimek v Logic Apps

Zpracování výpadku nebo problémy ze závislých systémů správně může představovat výzvu pro všechny Architektura integrace. Pokud chcete vytvořit robustní integrace, které jsou odolné proti problémy a chyby, Logic Apps nabízí prvotřídní prostředí pro zpracování chyb a výjimek. 

## <a name="retry-policies"></a>Opakujte zásady

Pro nejzákladnější výjimku a zpracování chyb můžete použít zásady opakování. Pokud počáteční žádosti vypršel časový limit nebo se nezdařilo, což je každá žádost, aby výsledkem 429 nebo 5xx odpovědi, tato zásada definuje, zda a jak akci opakovat žádost o. 

Existují čtyři typy zásady opakování: výchozí, žádná pevná intervalu a exponenciální intervalu. Pokud vaše definice pracovního postupu nemá zásady opakovaných pokusů, výchozí zásady podle definice služby, bude místo něj použita.

Pokud chcete nastavit zásady opakovaných pokusů, pokud je k dispozici, otevřete aplikaci návrháře logiku pro svou aplikaci logiky a přejděte na **nastavení** konkrétní akce v aplikaci logiky. Nebo můžete definovat zásady opakování v **vstupy** části pro určité akce nebo aktivační událost, pokud opakovatelné ve vaší definice pracovního postupu. Tady je Obecná syntaxe:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Další informace o syntaxi a **vstupy** tématu najdete [části zásady opakování akce pracovního postupu a aktivační události][retryPolicyMSDN]. Informace o omezeních zásady opakování najdete v tématu [Logic Apps omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Výchozí

Pokud nemáte definovat zásady opakování v **retryPolicy** části svou aplikaci logiky používá výchozí zásady, která je [exponenciální interval zásad](#exponential-interval) exponenciálnímu, odešle až čtyř opakovaných vyhledávání v zvýšení intervalech, které jsou škálovat podle 7.5 sekund. Interval je omezená od 5 do 45 sekund. Tato zásada je ekvivalentní zásad v tomto příkladu HTTP definice pracovního postupu:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Žádný

Pokud nastavíte **retryPolicy** k **žádné**, tato zásada neopakuje neúspěšných požadavků.

| Název elementu | Požaduje se | Typ | Popis | 
| ------------ | -------- | ---- | ----------- | 
| type | Ano | Řetězec | **None** | 
||||| 

### <a name="fixed-interval"></a>Pevně zadaném intervalu

Pokud nastavíte **retryPolicy** k **pevné**, tyto zásady opakování chybné žádosti tím, že zadaný interval času před odesláním další požadavek.

| Název elementu | Požaduje se | Typ | Popis |
| ------------ | -------- | ---- | ----------- |
| type | Ano | Řetězec | **Pevná** |
| počet | Ano | Integer | Počet opakování pokusů, které musí být mezi 1 a 90 | 
| interval | Ano | Řetězec | Interval opakování v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), která musí být mezi PT5S a PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponenciální intervalu

Pokud nastavíte **retryPolicy** k **exponenciální**, tyto zásady opakování po náhodném časovém intervalu z exponenciálnímu rostoucí rozsahu chybné žádosti. Zásady také zaručuje odeslat každým opakovaným pokusem v náhodném intervalu, který je větší než **minimumInterval** a menší než **maximumInterval**. Exponenciální zásady vyžadují **počet** a **interval**, při hodnoty pro **minimumInterval** a **maximumInterval** jsou volitelné. Pokud chcete přepsat výchozí hodnoty PT5S a PT1D v uvedeném pořadí, můžete přidat tyto hodnoty.

| Název elementu | Požaduje se | Typ | Popis |
| ------------ | -------- | ---- | ----------- |
| type | Ano | Řetězec | **Exponenciální** |
| počet | Ano | Integer | Počet opakování pokusů, které musí být mezi 1 a 90  |
| interval | Ano | Řetězec | Interval opakování v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), která musí být mezi PT5S a PT1D. |
| minimumInterval | Ne | Řetězec | Minimální interval opakování v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), která musí být mezi PT5S a **intervalu** |
| maximumInterval | Ne | Řetězec | Minimální interval opakování v [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), která musí být mezi **interval** a PT1D | 
||||| 

Tato tabulka ukazuje způsob, jakým jednoznačnou náhodnou proměnnou v uvedených rozsahu vygeneruje pro každý opakování včetně **počet**:

**Náhodná proměnná rozsahu**

| Opakujte číslo | Minimální interval | Maximální interval |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min (interval, **maximumInterval**) |
| 2 | Maximální počet (interval, **minimumInterval**) | Min (2 * interval **maximumInterval**) |
| 3 | Maximální počet (2 * interval **minimumInterval**) | Min (4 * interval **maximumInterval**) |
| 4 | Maximální počet (4 * interval **minimumInterval**) | Min (8 * interval **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Catch – a zpracování chyb s vlastností RunAfter

Každá akce logic app deklaruje akce, které musíte dokončit před zahájením této akce, podobně jako jak určit pořadí kroků v pracovním postupu. V definici akce **runAfter** vlastnost definuje toto uspořádání a je objekt, který popisuje, které akce a akce stavy provést akci.

Ve výchozím nastavení jsou všechny akce, které přidáte v návrháři aplikace logiky nastavení spustit po předchozím kroku, když je v předchozím kroku výsledek **úspěšné**. Ale můžete přizpůsobit **runAfter** hodnotu tak, aby akce vyvolat při předchozí akce způsobit jako **se nezdařilo**, **vynecháno**, nebo jejich kombinaci těchto hodnot. Například pro přidání položky do určité téma sběrnice po konkrétní **Insert_Row** akce selže, můžete použít v tomto příkladu **runAfter** definice:

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

**RunAfter** je nastavena na spustí, když **Insert_Row** stav akce je **se nezdařilo**. Akci spustit, pokud je stav akce **úspěšné**, **se nezdařilo**, nebo **vynecháno**, použijte následující syntaxi:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Akce, které spustit a úspěšně dokončit po předchozí akce se nezdařila, jsou označeny jako **úspěšné**. Toto chování znamená, že pokud jste úspěšně catch všechny chyby v pracovním postupu spustit samotné je označen jako **úspěšné**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Akce s obory a jejich výsledky vyhodnocení

Podobná spuštění po jednotlivé akce s kroky **runAfter** vlastnost, můžete seskupit akce uvnitř [oboru](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Pokud chcete logicky seskupit akce, posuzovat agregovaný stav oboru a provádět akce založené na stav, můžete pomocí oborů. Po všechny akce v oboru skončí, oboru, samotné získá svůj vlastní stav. 

Chcete-li zkontrolovat stav oboru, můžete použít stejné kritéria, která lze použít ke kontrole stavu spuštění aplikace logiky, jako například **úspěšné**, **se nezdařilo**a tak dále. 

Ve výchozím nastavení, když je akce všechny oboru úspěšné, stav oboru označena **úspěšné**. Pokud poslední akce v oboru výsledkem **se nezdařilo** nebo **bylo přerušeno.**, stav oboru je označena **se nezdařilo**. 

Zachytit výjimky v **se nezdařilo** oboru a spuštění akce, které zpracování těchto chyb, můžete použít **runAfter** vlastnost pro tento **se nezdařilo** oboru. Tímto způsobem, pokud *žádné* selhání akce v oboru, a použijete **runAfter** vlastnost pro daný obor, můžete vytvořit jednu akci k zachycení selhání.

Omezení u oborů, najdete v části [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Získání kontextu a výsledky pro selhání

I když zachytávání chyb z oboru je užitečné, můžete také kontextu, které vám pomohou pochopit, přesně plus žádné chyby nebo stavové kódy, které byly vráceny akce, které se nezdařilo.  **@result()** Funkce workflowu poskytuje kontext o výsledek všechny akce v oboru.

 **@result()** Funkce přijímá jeden parametr (oboru názvu) a vrátí pole všech akce výsledků v rámci tohoto oboru. Tyto objekty akce zahrnují stejné atributy, jako  **@actions()** objektu, například čas zahájení, čas ukončení, stav, vstupy, ID korelace a výstupy akce. Kontext pro všechny akce, které se nezdařilo odeslat v rámci oboru, můžete snadno spárujte  **@result()** fungovat s **runAfter** vlastnost.

Ke spuštění akce *pro každou* akce v oboru, který má **se nezdařilo** výsledek, a pokud chcete filtrovat pole výsledky dolů akce se nezdařila, pair  **@result()** s  **[pole filtru](../connectors/connectors-native-query.md)**  akce a  **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)**  smyčky. Můžete provést pole filtrované výsledek a provedení akce pro každé selhání pomocí **ForEach** smyčky. 

Tady je příklad, za nímž následuje podrobné vysvětlení, který odesílá požadavek HTTP POST s text odpovědi o všechny akce, které se nepodařilo v rámci oboru "My_Scope":

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Zde je podrobný návod, který popisuje, co se stane, že v tomto příkladu:

1. Chcete-li získat výsledek všechny akce v rámci "My_Scope", **pole filtru** filtrů Akce  **@result(My_Scope)**.

2. Podmínky pro **pole filtru** libovolnou  **@result()** položku, která je ve stavu, která je rovna **se nezdařilo**. Tuto podmínku filtrování pole všechny akce výsledky z "My_Scope" dolů pole s pouze výsledky akce se nezdařilo.

3. Provedení **pro každou** smyčka akce na *filtrované pole* výstupy. Tento krok provede akci *pro každou* výsledek akce, který byl dříve nefiltruje se nezdařilo.

   Pokud se nezdařila jednu akci v oboru, akce v **foreach** spustit jenom jednou. 
   Několik neúspěšných akce způsobí, že jednu akci za selhání.

4. Odeslání požadavku HTTP POST na **foreach** položky text odpovědi, což je  **@item() ['výstupy'] ['text']**.  **@result()** Tvar položka je stejný jako  **@actions()** utvářejí a lze analyzovat stejným způsobem.

5. Patří dva vlastní hlavičky s názvem selhání akce  **@item() [name]** a neúspěšný spusťte klienta, ID sledování  **@item() [clientTrackingId]**.

Pro referenci tady je příklad jednoho  **@result()** položky, zobrazuje **název**, **textu**, a **clientTrackingId** vlastnosti, které jsou analyzovány v předchozím příkladu. Mimo **foreach** akce,  **@result()** vrátí pole z těchto objektů.

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

K provedení různých zpracování vzory výjimek, můžete použít výrazy výše popsané v tomto článku. Můžete zvolit provedení jednoho výjimka zpracování akce mimo rozsah, který přijímá pole celý filtrované chyb a odebrat **foreach** akce. Můžete použít také další užitečné vlastnosti z  **@result()** odpovědi, jak se popisuje výš.

## <a name="azure-diagnostics-and-telemetry"></a>Azure diagnostiky a telemetrii

Předchozích jsou skvělý způsob, jak zpracování chyb a výjimek v rámci spuštění, ale můžete také určit a reagují na chyby, které jsou nezávislé na spuštění sám sebe. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) poskytuje jednoduchý způsob, jak odeslat všechny události pracovního postupu, včetně všech stavů spustit a akce, k účtu Azure Storage nebo centra událostí vytvořit s Azure Event Hubs. 

Pokud chcete vyhodnotit spuštění stavy, můžete sledování metrik a protokolování nebo publikovat je do jakýkoli monitorování nástroj, který preferujete. Jednou z možných možností je k vysílání datového proudu všechny události prostřednictvím centra událostí do [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Do služby Stream Analytics lze zapsat za provozu dotazy na základě jakékoli anomálie, průměry nebo selhání z diagnostické protokoly. Stream Analytics můžete použít k odeslání informací do jiných zdrojů dat, například fronty, témata, SQL, Azure Cosmos DB nebo Power BI.

## <a name="next-steps"></a>Další postup

* [V tématu Jak zákazník sestavení službou Azure Logic Apps zpracování chyb](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Najít další aplikace logiky příkladů a scénářů](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Naučte se vytvořit automatické nasazení pro logic apps](../logic-apps/logic-apps-create-deploy-template.md)
* [Vytvoření a nasazení aplikací logiky s využitím sady Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9