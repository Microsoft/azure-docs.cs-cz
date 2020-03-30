---
title: Metadata OpenAPI ve funkcích Azure
description: Přehled podpory OpenAPI ve funkcích Azure
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: a80079574dc29c54de89f5275c65637b205742d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227413"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Podpora metadat OpenAPI 2.0 ve funkcích Azure (preview)
Podpora metadat OpenAPI 2.0 (dříve Swagger) ve službě Azure Functions je funkce náhledu, kterou můžete použít k zápisu definice OpenAPI 2.0 do aplikace funkcí. Potom můžete hostovat tento soubor pomocí aplikace funkce.

> [!IMPORTANT]
> Funkce OpenAPI ve verzi Preview je v současné době k dispozici pouze v modulu runtime verze 1.x. Informace o tom, jak vytvořit aplikaci funkcí 1.x [najdete tady](./functions-versions.md#creating-1x-apps).

[Metadata OpenAPI](https://swagger.io/) umožňují, aby funkce, která je hostitelem rozhraní REST API, byla spotřebována širokou škálou dalšího softwaru. Tento software zahrnuje nabídky microsoftu, jako jsou PowerApps a [funkce API Apps azure app service](../app-service/overview.md), vývojářské nástroje třetích stran, jako je [Postman](https://www.getpostman.com/docs/importing_swagger), a [mnoho dalších balíčků](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Doporučujeme začít s [kurzem Začínáme](./functions-api-definition-getting-started.md) a potom se vrátit k tomuto dokumentu, abyste se dozvěděli další informace o konkrétních funkcích.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Povolit podporu definice OpenAPI
Všechna nastavení OpenAPI můžete nakonfigurovat na stránce **Definice rozhraní API** ve **funkcích platformy vaší**aplikace pro funkci .

> [!NOTE]
> Funkce definice rozhraní API není pro modul runtime beta aktuálně podporována.

Chcete-li povolit generování hostované definice OpenAPI a definici rychlého startu, nastavte **zdroj definice rozhraní API** na funkci **(Preview).** **Externí adresa URL** umožňuje vaší funkci používat definici OpenAPI, která je hostována jinde.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Vygenerujte kostru Swagger u metadat vaší funkce
Šablona vám může pomoci začít psát první definici OpenAPI. Funkce šablony definice vytvoří řídkou definici OpenAPI pomocí všech metadat v souboru function.json pro každou z aktivačních funkcí HTTP. Budete muset vyplnit další informace o vašem rozhraní API ze [specifikace OpenAPI](https://swagger.io/specification/), jako jsou šablony požadavků a odpovědí.

Podrobné pokyny naleznete v kurzu [Začínáme](./functions-api-definition-getting-started.md).

### <a name="available-templates"></a><a name="templates"></a>Dostupné šablony

|Name (Název)| Popis |
|:-----|:-----|
|Vygenerovaná definice|Definice OpenAPI s maximálním množstvím informací, které lze odvodit z existujících metadat funkce.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Zahrnutá metadata do generované definice

Následující tabulka představuje nastavení portálu Azure a odpovídající data v function.json, jak je mapována na generované Kostra Swagger.

|Swagger.json|Portál ui|Function.json|
|:----|:-----|:-----|
|[Hostitel](https://swagger.io/specification/#fixed-fields-15)|**Nastavení aplikace** > **Funkce Nastavení** > služby App Service – adresa URL**přehledu** > **URL**|*Není k dispozici*
|[Cesty](https://swagger.io/specification/#paths-object-29)|**Integrace** > **vybraných metod HTTP**|Vázání: Trasa
|[Položka cesty](https://swagger.io/specification/#path-item-object-32)|**Integrovat** > **šablonu trasy**|Vazby: Metody
|[Zabezpečení](https://swagger.io/specification/#security-scheme-object-112)|**Klíče**|*Není k dispozici*|
|operationID*|**Trasa + Povolená slovesa**|Trasa + Povolená slovesa|

\*ID operace je vyžadováno pouze pro integraci s PowerApps a Flow.
> [!NOTE]
> Rozšíření x-ms-summary poskytuje zobrazovaný název v Logic Apps, PowerApps a Flow.
>
> Další informace najdete [v tématu Přizpůsobení definice Swaggeru pro PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Nastavení definice rozhraní API pomocí CI/CD

 Před povolením správy zdrojového kódu k úpravě definice rozhraní API ze správy zdrojového kódu je nutné povolit hostování definic rozhraní API na portálu. Postupujte podle těchto pokynů:

1. Přejděte do **definice rozhraní API (preview)** v nastavení aplikace funkce.
   1. Nastavte **zdroj definice rozhraní API** na **funkci**.
   1. Klikněte na **Generovat šablonu definice rozhraní API** a potom na **Uložit** vytvořte definici šablony pro pozdější úpravy.
   1. Poznamenejte si adresu URL a klíč definice rozhraní API.
1. [Nastavte průběžnou integraci/průběžné nasazování (CI/CD).](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment)
2. Upravte swagger.json ve svatou novému zdroji na adrese \site\wwwroot\.azurefunctions\swagger\swagger.json.

Nyní jsou změny swagger.json ve vašem úložišti hostovány vaší funkční aplikací na adrese URL a klíči definice rozhraní API, které jste zaznamenali v kroku 1.c.

## <a name="next-steps"></a>Další kroky
* [Začínáme výukový program](functions-api-definition-getting-started.md). Vyzkoušejte náš návod, abyste viděli definici OpenAPI v akci.
* [Azure Functions GitHub úložiště](https://github.com/Azure/Azure-Functions/). Podívejte se na úložiště Funkce, abyste nám poskytli zpětnou vazbu k náhledu podpory definice rozhraní API. Vytvořte problém GitHub u všeho, co chcete vidět aktualizované.
* [Azure Functions odkaz na vývojáře](functions-reference.md). Další informace o funkcích kódování a definování aktivačních událostí a vazeb.
