---
title: Aktivita funkce Azure v Azure Data Factory | Microsoft Docs
description: Naučte se používat aktivitu Azure Functions ke spuštění funkce Azure ve Data Factoryovém kanálu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 292fe858b85faef69b9df2dbdf54e7061ed56fa2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142513"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure v Azure Data Factory

Aktivita funkce Azure umožňuje spouštět [Azure Functions](../azure-functions/functions-overview.md) v kanálu Data Factory. Pokud chcete spustit funkci Azure Functions, musíte vytvořit připojení propojené služby a aktivitu, která určuje funkci Azure, kterou plánujete spustit.

Po dobu osmi minut a ukázku této funkce se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Propojená služba Functions Azure

Návratový typ funkce Azure musí být platný `JObject`. (Mějte na paměti, že [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) není `JObject`.) Libovolný návratový typ jiný než `JObject` neúspěch a vyvolá obsah odpovědi na chyb uživatele není *platný JObject*.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type   | Vlastnost Type musí být nastavená na: **AzureFunction** | ano |
| Adresa URL aplikace Function App | Adresa URL pro Azure Function App. Formát je `https://<accountname>.azurewebsites.net`. Tato adresa URL je hodnota v sekci **URL** při zobrazení Function App v Azure Portal  | ano |
| klíč funkce | Přístupový klíč pro funkci Azure Functions Klikněte na část **Správa** příslušné funkce a zkopírujte buď **klíč funkce** , nebo **klíč hostitele**. Další informace najdete tady: [Azure Functions triggerů HTTP a vazeb](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ano |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivita funkce Azure

| **Vlastnost**  | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| name  | Název aktivity v kanálu  | Řetězec | ano |
| type  | Typ aktivity je "AzureFunctionActivity". | Řetězec | ano |
| propojená služba | Propojená služba funkce Azure pro odpovídající Function App Azure  | Odkaz na propojenou službu | ano |
| název funkce  | Název funkce v Azure Function App, kterou tato aktivita volá | Řetězec | ano |
| – metoda  | Metoda REST API pro volání funkce | Typy podporované řetězcem: "GET", "POST", "PUT"   | ano |
| záhlaví  | Hlavičky, které se odesílají do žádosti Například pro nastavení jazyka a typu na žádost: "hlavičky": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"} | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne |
| hlavní část  | tělo, které se odesílá spolu s požadavkem na metodu rozhraní API funkce  | Řetězec (nebo výraz s hodnotou resultType String) nebo objekt.   | Vyžadováno pro metody PUT/POST |
|   |   |   | |

Podívejte se na schéma datové části požadavku v části  [schéma datové části požadavku](control-flow-web-activity.md#request-payload-schema).

## <a name="routing-and-queries"></a>Směrování a dotazy

Aktivita funkce Azure podporuje **Směrování**. Pokud má například služba Azure Functions koncový bod `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, pak bude `functionName` pro použití v aktivitě `<functionName>/<value>`funkce Azure Functions. Tuto funkci můžete parametrizovat tak, aby poskytovala `functionName` požadovaný čas za běhu.

Aktivita funkce Azure také podporuje **dotazy**. Dotaz musí být zahrnut jako součást `functionName`. Například pokud je `HttpTriggerCSharp` název funkce a dotaz, který chcete `name=hello`zahrnout, můžete vytvořit `functionName` v aktivitě funkce Azure jako `HttpTriggerCSharp?name=hello`. Tato funkce může být Parametrizovaná, takže hodnotu je možné určit za běhu.

## <a name="timeout-and-long-running-functions"></a>Vypršení časového limitu a dlouho běžící funkce

Azure Functions časový limit po 230 sekundách bez ohledu na `functionTimeout` nastavení, které jste nakonfigurovali v nastavení. Další informace najdete v [tomto článku](../azure-functions/functions-versions.md#timeout). Chcete-li toto chování obejít, postupujte podle asynchronního vzoru nebo použijte Durable Functions. Výhodou Durable Functions je, že nabízí vlastní mechanismus pro sledování stavu, takže nebudete muset implementovat své vlastní.

Přečtěte si další informace o Durable Functions v [tomto článku](../azure-functions/durable/durable-functions-overview.md). Aktivitu funkce Azure můžete nastavit tak, aby volala trvalou funkci, která vrátí odpověď s jiným identifikátorem URI, jako je například [Tento příklad](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Vzhledem `statusQueryGetUri` k tomu, že vrátí stav HTTP 202, když je funkce spuštěná, můžete dotazovat stav funkce pomocí webové aktivity. Jednoduše nastavte aktivitu webu s `url` polem nastaveným na. `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` Po dokončení trvalé funkce bude výstupem funkce výstup webové aktivity.


## <a name="sample"></a>Ukázka

Ukázku Data Factory, který používá funkci Azure k extrakci obsahu souboru tar, najdete [tady](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o aktivitách v Data Factory v prokanálech [a aktivitách v Azure Data Factory](concepts-pipelines-activities.md).
