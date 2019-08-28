---
title: OpenAPI metadata v Azure Functions | Microsoft Docs
description: Přehled podpory OpenAPI v Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 00dedd069914384a1883a54bfb433857286fb49c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097575"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Podpora metadat OpenAPI 2,0 v Azure Functions (Preview)
Podpora metadat OpenAPI 2,0 (dříve Swagger) v Azure Functions je funkce verze Preview, kterou můžete použít k zápisu definice OpenAPI 2,0 do aplikace Function App. Tento soubor pak můžete hostovat pomocí aplikace Function App.

> [!IMPORTANT]
> Funkce OpenAPI ve verzi Preview je v současné době k dispozici pouze v modulu runtime verze 1.x. Informace o tom, jak vytvořit aplikaci funkcí 1.x [najdete tady](./functions-versions.md#creating-1x-apps).

[Openapi metadata](https://swagger.io/) umožňují funkci, která je hostitelem REST API, aby byla spotřebována širokou škálou jiného softwaru. Tento software zahrnuje nabídky Microsoftu, jako je PowerApps, a [funkce API Apps Azure App Service](../app-service/overview.md), vývojářské nástroje třetích stran, jako je například [post](https://www.getpostman.com/docs/importing_swagger)a [spousta dalších balíčků](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Doporučujeme začít s kurzem [Začínáme](./functions-api-definition-getting-started.md) a pak se vrátit do tohoto dokumentu a získat další informace o konkrétních funkcích.

## <a name="enable"></a>Povolit podporu definice OpenAPI
Všechna nastavení OpenAPI můžete nakonfigurovat na stránce **definice rozhraní API** ve **funkcích platformy**aplikace Function App.

> [!NOTE]
> Funkce definice rozhraní API funkce se v současnosti nepodporuje pro modul runtime beta verze.

Pokud chcete povolit generování definice hostovaného OpenAPI a definice pro rychlý Start, nastavte **zdroj definice rozhraní API** na **funkci (Preview)** . **Externí adresa URL** umožňuje, aby funkce používala definici openapi, která je hostována jinde.

## <a name="generate-definition"></a>Generování kostry Swagger z metadat vaší funkce
Šablona vám může pomáhat začít s psaním první definice OpenAPI. Funkce šablony definice vytvoří zhuštěnou definici OpenAPI pomocí všech metadat v souboru Function. JSON pro každou z vašich funkcí triggeru protokolu HTTP. Budete muset zadat další informace o rozhraní API ze [specifikace openapi](https://swagger.io/specification/), jako jsou šablony žádostí a odpovědí.

Podrobné pokyny najdete v [úvodním kurzu](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Dostupné šablony

|Name| Popis |
|:-----|:-----|
|Vygenerovaná definice|Definice OpenAPI s maximálním množstvím informací, které lze odvodit z existujících metadat funkce.|

### <a name="quickstart-details"></a>Zahrnutá metadata ve vygenerované definici

Následující tabulka představuje nastavení Azure Portal a odpovídající data v Function. JSON, protože je namapována na vygenerovaný kostru Swagger.

|Swagger.json|Uživatelské rozhraní portálu|Function.json|
|:----|:-----|:-----|
|[Hostitel](https://swagger.io/specification/#fixed-fields-15)|**Nastavení** >  >  aplikace Function App App Service adresa URL přehledu nastavení > |*Nepřítomno*
|[Ruky](https://swagger.io/specification/#paths-object-29)|Integrace > **vybraných metod http**|Vazeb Trasa
|[Položka cesty](https://swagger.io/specification/#path-item-object-32)|**Integrovat** > **šablonu směrování**|Vazeb Metody
|[Zabezpečení](https://swagger.io/specification/#security-scheme-object-112)|**Klíče**|*Nepřítomno*|
|operationId|**Trasy a povolené operace**|Trasy a povolené operace|

\*ID operace se vyžaduje jenom pro integraci s PowerApps a Flowem.
> [!NOTE]
> Rozšíření x-MS-Summary poskytuje zobrazované jméno v Logic Apps, PowerApps a flow.
>
> Další informace najdete v tématu [přizpůsobení definice Swagger pro PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Použití CI/CD k nastavení definice rozhraní API

 Než povolíte správu zdrojového kódu, musíte na portálu povolit hostování definice rozhraní API, aby se změnila definice rozhraní API ze správy zdrojového kódu. Postupujte podle těchto pokynů:

1. V nastavení aplikace Function App přejděte do **definice rozhraní API (Preview)** .
   1. Nastavte **zdroj definice rozhraní API** na **funkci**.
   1. Klikněte na **vygenerovat šablonu definic rozhraní API** a pak klikněte na **Uložit** a vytvořte definici šablony pro pozdější úpravu.
   1. Poznamenejte si adresu URL a klíč definice rozhraní API.
1. [Nastavení průběžné integrace/průběžného nasazování (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Úprava Swagger. JSON ve správě zdrojového kódu na \site\wwwroot\.azurefunctions\swagger\swagger.JSON.

Změny ve službě Swagger. JSON ve vašem úložišti teď hostuje aplikace Function App na adrese URL a klíči definice rozhraní API, které jste si poznamenali v kroku 1. c.

## <a name="next-steps"></a>Další postup
* [Úvodní kurz](functions-api-definition-getting-started.md). Vyzkoušejte si náš Názorný postup pro zobrazení definice OpenAPI v akci.
* [Azure Functions úložiště GitHubu](https://github.com/Azure/Azure-Functions/). Podívejte se na úložiště funkcí a sdělte nám svůj názor na podporu definice rozhraní API ve verzi Preview. Udělejte problém GitHubu pro cokoli, co chcete zobrazit aktualizované.
* [Azure Functions referenční informace pro vývojáře](functions-reference.md). Přečtěte si o funkcích kódování a definování triggerů a vazeb.
