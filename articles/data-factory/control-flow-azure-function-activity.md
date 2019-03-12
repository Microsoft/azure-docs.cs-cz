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
ms.openlocfilehash: b98d20a1f96a6ab4a0dc72330e85fdc98ba04eae
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576374"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure ve službě Azure Data Factory

Aktivita funkce Azure vám umožní spustit [Azure Functions](../azure-functions/functions-overview.md) v kanálech Data Factory. Ke spuštění funkce Azure, musíte vytvořit propojenou službu připojení a aktivitu, která určuje, kterou chcete použít ke spuštění funkce Azure functions.

Úvod osm minut a ukázku této funkce podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Služba Azure propojené – funkce

Návratový typ funkce Azure musí být platný `JObject`. (Mějte na paměti, která [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) je *není* `JObject`.) Žádný návratový typ jiný než `JObject` selže a vyvolá chybu obecný uživatel *Chyba volání koncového bodu*.

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

## <a name="more-info"></a>Další informace

Aktivita funkce Azure podporuje **směrování**. Například, pokud vaše aplikace používá následující směrování - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` - pak bude `functionName` je `functionName/{value}`, které můžete parametrizovat poskytnout požadovaný `functionName` za běhu.

Aktivita funkce Azure podporuje také **dotazy**. Dotaz musí být součástí `functionName` – například `HttpTriggerCSharp2?name=hello` – kde `function name` je `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Další postup

Další informace o aktivity ve službě Data Factory v [kanály a aktivity ve službě Azure Data Factory](concepts-pipelines-activities.md).
