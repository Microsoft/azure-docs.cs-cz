---
title: Aktivita funkce Azure v Azure Data Factory
description: Zjistěte, jak pomocí aktivity Funkce Azure spustit funkci Azure v kanálu datové továrny.
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
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207021"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivita funkce Azure ve Službě Azure Data Factory

Aktivita funkce Azure umožňuje spouštět [funkce Azure](../azure-functions/functions-overview.md) v kanálu datové továrny. Chcete-li spustit funkci Azure, musíte vytvořit připojení propojené služby a aktivitu, která určuje funkci Azure, kterou chcete spustit.

Osmiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Propojená služba Azure Function

Návratový typ funkce Azure musí být `JObject`platný . (Mějte na paměti, *not* že `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) není .) Jakýkoli návratový `JObject` typ jiný než selže a vyvolá chybu uživatele *Obsah odpovědi není platný JObject*.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type   | Vlastnost type musí být nastavena na: **AzureFunction.** | ano |
| adresa URL aplikace funkce | Adresa URL aplikace Azure Function App. Formát `https://<accountname>.azurewebsites.net`je . Tato adresa URL je hodnota v části **ADRESA URL** při zobrazení aplikace funkce na webu Azure Portal  | ano |
| funkční klávesa | Přístupový klíč pro funkci Azure. Klikněte na sekci **Spravovat** pro příslušnou funkci a zkopírujte funkční **klíč** nebo **klíč Hostitele**. Další informace najdete tady: [Aktivační události a vazby http funkce Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ano |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivita funkce Azure

| **Vlastnost**  | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| jméno  | Název aktivity v kanálu  | Řetězec | ano |
| type  | Typ aktivity je "AzureFunctionActivity" | Řetězec | ano |
| propojená služba | Propojená služba Azure Function pro odpovídající aplikaci Azure Function App  | Odkaz na propojenou službu | ano |
| název funkce  | Název funkce v aplikaci Azure Function App, kterou tato aktivita volá | Řetězec | ano |
| method  | Metoda ROZHRANÍ REST API pro volání funkce | Typy podporované řetězce: "GET", "POST", "PUT"   | ano |
| záhlaví  | Hlavičky, které jsou odeslány do požadavku. Chcete-li například nastavit jazyk a typ na žádost: "záhlaví": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Řetězec (nebo výraz s resultType řetězce) | Ne |
| text  | subjekt, který je odeslán spolu s požadavkem na metodu api funkce  | Řetězec (nebo výraz s resultType řetězce) nebo objekt.   | Požadováno pro metody PUT/POST |
|   |   |   | |

Podívejte se na schéma datové části požadavku v části [Požadavek na schéma datové části.](control-flow-web-activity.md#request-payload-schema)

## <a name="routing-and-queries"></a>Směrování a dotazy

Aktivita funkce Azure podporuje **směrování**. Například pokud vaše funkce Azure `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`má koncový `functionName` bod , pak se `<functionName>/<value>`má použít v aktivitě funkce Azure je . Tuto funkci můžete parametrizovat a `functionName` poskytnout tak požadovaný za běhu.

Aktivita funkce Azure také podporuje **dotazy**. Dotaz musí být zahrnutjako součást `functionName`. Například pokud `HttpTriggerCSharp` je název funkce a dotaz, který `name=hello`chcete zahrnout je `functionName` , pak můžete `HttpTriggerCSharp?name=hello`vytvořit v aktivitě funkce Azure jako . Tato funkce může být parametrizována, takže hodnota může být určena za běhu.

## <a name="timeout-and-long-running-functions"></a>Funkce časového oběhu a dlouhého běhu

Časový rozsah funkcí Azure po 230 `functionTimeout` sekundách bez ohledu na nastavení, které jste nakonfigurovali v nastavení. Další informace najdete v [tomto článku](../azure-functions/functions-versions.md#timeout). Chcete-li toto chování obejít, postupujte podle asynchronního vzoru nebo použijte trvalé funkce. Výhodou trvalých funkcí je, že nabízejí svůj vlastní mechanismus sledování stavu, takže nebudete muset implementovat vlastní.

Další informace o trvanlivé funkce v [tomto článku](../azure-functions/durable/durable-functions-overview.md). Můžete nastavit aktivitu funkce Azure pro volání trvalé funkce, která vrátí odpověď s jiným identifikátorem URI, jako je [například tento příklad](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Protože `statusQueryGetUri` vrátí stav PROTOKOLU HTTP 202, když je funkce spuštěna, můžete dotazování na stav funkce pomocí webové aktivity. Jednoduše nastavte webovou aktivitu s polem `url` nastaveným na `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. Po dokončení trvalé funkce výstup funkce bude výstup webové aktivity.


## <a name="sample"></a>Ukázka

Můžete najít ukázku Data Factory, který používá funkci Azure extrahovat obsah souboru tar [zde](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Další kroky

Další informace o aktivitách v Data Factory v [kanálech a aktivitách v Azure Data Factory](concepts-pipelines-activities.md).
