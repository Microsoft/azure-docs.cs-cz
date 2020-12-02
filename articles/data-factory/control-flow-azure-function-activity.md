---
title: Aktivita funkce Azure v Azure Data Factory
description: Naučte se používat aktivitu Azure Functions ke spuštění funkce Azure ve Data Factoryovém kanálu.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: f7c8f1e5ae0da9e7c404a942fcb4f554281486a7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500034"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Aktivita funkce Azure umožňuje spouštět [Azure Functions](../azure-functions/functions-overview.md) v kanálu Data Factory. Pokud chcete spustit funkci Azure Functions, musíte vytvořit připojení propojené služby a aktivitu, která určuje funkci Azure, kterou plánujete spustit.

Po dobu osmi minut a ukázku této funkce se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Propojená služba Functions Azure

Návratový typ funkce Azure musí být platný `JObject` . (Mějte na paměti, [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) že JArray *není* `JObject` .) Libovolný návratový typ jiný než `JObject` neúspěch a vyvolá *obsah odpovědi* na chyb uživatele není platný JObject.

| **Vlastnost** | **Popis** | **Povinné** |
| --- | --- | --- |
| typ   | Vlastnost Type musí být nastavená na: **AzureFunction** . | ano |
| Adresa URL aplikace Function App | Adresa URL pro Azure Function App. Formát je `https://<accountname>.azurewebsites.net` . Tato adresa URL je hodnota v sekci **URL** při zobrazení Function App v Azure Portal  | ano |
| klíč funkce | Přístupový klíč pro funkci Azure Functions Klikněte na část **Správa** příslušné funkce a zkopírujte buď **klíč funkce** , nebo **klíč hostitele**. Další informace najdete tady: [aktivační události a vazby HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) . | ano |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivita funkce Azure

| **Vlastnost**  | **Popis** | **Povolené hodnoty** | **Povinné** |
| --- | --- | --- | --- |
| name  | Název aktivity v kanálu  | Řetězec | ano |
| typ  | Typ aktivity je "AzureFunctionActivity". | Řetězec | ano |
| propojená služba | Propojená služba funkce Azure pro odpovídající Function App Azure  | Odkaz na propojenou službu | ano |
| název funkce  | Název funkce v Azure Function App, kterou tato aktivita volá | Řetězec | ano |
| method  | Metoda REST API pro volání funkce | Typy podporované řetězcem: "GET", "POST", "PUT"   | ano |
| header  | Hlavičky, které se odesílají do žádosti Například pro nastavení jazyka a typu na žádost: "hlavičky": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"} | Řetězec (nebo výraz s hodnotou resultType řetězce) | No |
| text  | tělo, které se odesílá spolu s požadavkem na metodu rozhraní API funkce  | Řetězec (nebo výraz s hodnotou resultType String) nebo objekt.   | Vyžadováno pro metody PUT/POST |
|   |   |   | |

Podívejte se na schéma datové části požadavku v části [schéma datové části požadavku](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Směrování a dotazy

Aktivita funkce Azure podporuje **Směrování**. Pokud má například služba Azure Functions koncový bod  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` , pak bude `functionName` pro použití v aktivitě funkce Azure Functions `<functionName>/<value>` . Tuto funkci můžete parametrizovat tak, aby poskytovala požadovaný čas `functionName` za běhu.

Aktivita funkce Azure také podporuje **dotazy**. Dotaz musí být zahrnut jako součást `functionName` . Například pokud je název funkce `HttpTriggerCSharp` a dotaz, který chcete zahrnout `name=hello` , můžete vytvořit `functionName` v aktivitě funkce Azure jako `HttpTriggerCSharp?name=hello` . Tato funkce může být Parametrizovaná, takže hodnotu je možné určit za běhu.

## <a name="timeout-and-long-running-functions"></a>Vypršení časového limitu a dlouho běžící funkce

Azure Functions časový limit po 230 sekundách bez ohledu na `functionTimeout` nastavení, které jste nakonfigurovali v nastavení. Další informace najdete v [tomto článku](../azure-functions/functions-versions.md#timeout). Chcete-li toto chování obejít, postupujte podle asynchronního vzoru nebo použijte Durable Functions. Výhodou Durable Functions je, že nabízí vlastní mechanismus pro sledování stavu, takže nebudete muset implementovat své vlastní.

Přečtěte si další informace o Durable Functions v [tomto článku](../azure-functions/durable/durable-functions-overview.md). Aktivitu funkce Azure můžete nastavit tak, aby volala trvalou funkci, která vrátí odpověď s jiným identifikátorem URI, jako je například [Tento příklad](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Vzhledem k tomu `statusQueryGetUri` , že vrátí stav HTTP 202, když je funkce spuštěná, můžete dotazovat stav funkce pomocí webové aktivity. Jednoduše nastavte aktivitu webu s `url` polem nastaveným na `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Po dokončení trvalé funkce bude výstupem funkce výstup webové aktivity.


## <a name="sample"></a>Ukázka

Ukázku Data Factory, který používá funkci Azure k extrakci obsahu souboru tar, najdete [tady](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o aktivitách v Data Factory v [Prokanálech a aktivitách v Azure Data Factory](concepts-pipelines-activities.md).
