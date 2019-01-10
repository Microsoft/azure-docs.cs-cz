---
title: Aktivita funkce Azure Functions ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití funkce Azure Functions aktivity ke spuštění funkce Azure Functions v kanálech Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: douglasl
ms.openlocfilehash: 1b9821a0115aba96f4588049273bd7a157121ded
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156625"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure ve službě Azure Data Factory

Aktivita funkce Azure vám umožní spustit [Azure Functions](../azure-functions/functions-overview.md) v kanálech Data Factory. Ke spuštění funkce Azure, musíte vytvořit propojenou službu připojení a aktivitu, která určuje, kterou chcete použít ke spuštění funkce Azure functions.

## <a name="azure-function-linked-service"></a>Služba Azure propojené – funkce

Návratový typ funkce Azure musí být platný JObject. (Mějte na paměti, která [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) je *není* JObject.) Cokoli, se nezdaří a vyvolá chybu obecný uživatel *Chyba volání koncového bodu*.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type   | Vlastnost type musí být nastavená na: **AzureFunction** | ano |
| Adresa url aplikace – funkce | Adresa URL pro aplikaci Azure Function App. Formát je `https://<accountname>.azurewebsites.net`. Tato adresa URL je hodnoty v rámci **URL** části při prohlížení aplikace Function App na webu Azure Portal  | ano |
| klíč funkce | Přístupový klíč pro funkce Azure functions. Klikněte na **spravovat** oddílu pro odpovídající funkci a zkopírovat buď **klíč funkce** nebo **klíč hostitele**. Další informace najdete tady: [Azure Functions HTTP aktivačními událostmi a vazbami](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ano |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivit v Azure – funkce

| **Vlastnost**  | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| jméno  | Název aktivity v kanálu  | Řetězec | ano |
| type  | Typ aktivity je "AzureFunctionActivity. | Řetězec | ano |
| Propojená služba | Funkce Azure, propojené služby pro odpovídající aplikaci funkcí Azure  | Odkaz na propojenou službu | ano |
| Název funkce  | Název funkce v aplikaci Azure Function App, která volá tuto aktivitu | Řetězec | ano |
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
