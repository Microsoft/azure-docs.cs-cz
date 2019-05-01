---
title: Aktivita funkce Azure Functions ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití funkce Azure Functions aktivity ke spuštění funkce Azure Functions v kanálech Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727063"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure ve službě Azure Data Factory

Aktivita funkce Azure vám umožní spustit [Azure Functions](../azure-functions/functions-overview.md) v kanálech Data Factory. Ke spuštění funkce Azure, musíte vytvořit propojenou službu připojení a aktivitu, která určuje, kterou chcete použít ke spuštění funkce Azure functions.

Úvod osm minut a ukázku této funkce podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Služba Azure propojené – funkce

Návratový typ funkce Azure musí být platný `JObject`. (Mějte na paměti, která [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) je *není* `JObject`.) Žádný návratový typ jiný než `JObject` selže a vyvolá chybu uživatele *obsah odpovědi není platný JObject*.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type   | Vlastnost type musí být nastavená na: **AzureFunction** | ano |
| Adresa url aplikace – funkce | Adresa URL pro aplikaci Azure Function App. Formát je `https://<accountname>.azurewebsites.net`. Tato adresa URL je hodnoty v rámci **URL** části při prohlížení aplikace Function App na webu Azure Portal  | ano |
| klíč funkce | Přístupový klíč pro funkce Azure functions. Klikněte na **spravovat** oddílu pro odpovídající funkci a zkopírovat buď **klíč funkce** nebo **klíč hostitele**. Další informace najdete tady: [Azure Functions HTTP aktivačními událostmi a vazbami](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ano |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivit v Azure – funkce

| **Vlastnost**  | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| jméno  | Název aktivity v kanálu  | String | ano |
| type  | Typ aktivity je "AzureFunctionActivity. | String | ano |
| Propojená služba | Funkce Azure, propojené služby pro odpovídající aplikaci funkcí Azure  | Odkaz na propojenou službu | ano |
| Název funkce  | Název funkce v aplikaci Azure Function App, která volá tuto aktivitu | String | ano |
| method  | Metoda rozhraní REST API pro volání funkce | Řetězec podporované typy: "GET", "POST", "UMÍSTĚNÍ"   | ano |
| záhlaví  | Hlavičky, které se odesílají na požadavek. Například nastavení jazyka a typu na vyžádání: "záhlaví": {"Accept-Language": "en-us", "Content-Type": "application/json"} | Řetězec (nebo výraz s hodnotu resultType řetězec) | Ne |
| hlavní část  | text, který se zasílá společně s žádost na metodu api – funkce  | Řetězec (nebo výraz s hodnotu resultType řetězec) nebo objekt.   | Vyžaduje se pro metody PUT/POST |
|   |   |   | |

Zobrazit schéma datové části požadavku v [schématu datové části požadavku](control-flow-web-activity.md#request-payload-schema) oddílu.

## <a name="routing-and-queries"></a>Směrování a dotazy

Aktivita funkce Azure podporuje **směrování**. Například, pokud má koncový bod vaše funkce Azure Functions `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, pak bude `functionName` použít v aktivitě funkce Azure je `<functionName>/<value>`. Tato funkce se požadovaný můžete parametrizovat `functionName` za běhu.

Aktivita funkce Azure podporuje také **dotazy**. Dotaz musí být součástí `functionName`. Například, pokud je název funkce `HttpTriggerCSharp` a dotaz, který chcete zahrnout `name=hello`, pak můžete vytvořit `functionName` v aktivitě funkce Azure jako `HttpTriggerCSharp?name=hello`. Tato funkce může být parametrizován tak můžete určit hodnotu za běhu.

## <a name="timeout-and-long-running-functions"></a>Časový limit a dlouho běžící funkce

Azure Functions vyprší po sekundách 230 bez ohledu `functionTimeout` nastavení, které jste nakonfigurovali v nastavení. Další informace najdete v [tomto článku](../azure-functions/functions-versions.md#timeout). Chcete-li tento problém vyřešit, postupujte podle asynchronní vzorek nebo použít Durable Functions. Výhodou odolná služba Functions je, že nabízejí vlastní mechanismus sledování stavu, takže nebudete muset implementovat vlastní.

Další informace o odolná služba Functions v [v tomto článku](../azure-functions/durable/durable-functions-overview.md). Aktivita funkce Azure můžete nastavit pro volání funkce trvalý, který vrátí odpověď se jiný identifikátor URI, například [v tomto příkladu](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Protože `statusQueryGetUri` vrátí 202 stav protokolu HTTP při funkce běží, stav funkce můžete dotazovat pomocí aktivitu webu. Stačí nastavit aktivitu webu s `url` pole nastaveno `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Po dokončení funkce trvalý výstup funkce bude výstup aktivity webu.


## <a name="next-steps"></a>Další postup

Další informace o aktivity ve službě Data Factory v [kanály a aktivity ve službě Azure Data Factory](concepts-pipelines-activities.md).
