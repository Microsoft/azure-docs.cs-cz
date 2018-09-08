---
title: Metadata OpenAPI ve službě Azure Functions | Dokumentace Microsoftu
description: Přehled podpory OpenAPI ve službě Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 68077e576fe3451627d8a5c8e1ff1b26d06d96b7
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091834"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Podpora metadat OpenAPI 2.0 ve službě Azure Functions (preview)
OpenAPI 2.0 (dříve Swagger) podpora metadat ve službě Azure Functions je funkce ve verzi preview, můžete použít k zápisu definici rozhraní OpenAPI 2.0 v aplikaci function app. Tento soubor pak můžete hostovat pomocí aplikace function app.

[OpenAPI metadata](http://swagger.io/) umožňuje funkci, která je hostitelem rozhraní REST API pro širokou škálu dalšího softwaru. Tento software obsahuje nabídkami Microsoftu jako PowerApps a [funkce API Apps služby Azure App Service](../app-service/app-service-web-overview.md), nástroje pro vývojáře třetích stran, jako je [Postman](https://www.getpostman.com/docs/importing_swagger), a [řada dalších balíčků](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Doporučujeme začít se [úvodní kurz](./functions-api-definition-getting-started.md) a vrácení k tomuto dokumentu chcete další informace týkající se konkrétních funkcí.

## <a name="enable"></a>Povolit podporu definice OpenAPI
Můžete nakonfigurovat všechna nastavení OpenAPI na **definice rozhraní API** stránky v aplikaci function app **funkce platformy**.

> [!NOTE]
> Funkce definice rozhraní API funkce není podporována pro beta verze modulu runtime aktuálně.

Chcete-li povolit generování definování rychlý start a hostovanou definici OpenAPI, nastavte **zdroj definice rozhraní API** k **– funkce (Preview)**. **Externí adresa URL** umožňuje funkce pomocí definice OpenAPI, který má hostovanou jinde.

## <a name="generate-definition"></a>Generovat kostru Swagger z metadat funkce
Šablonu můžete začít psát první definici OpenAPI. Definice funkce šablony vytvoří stručnou definici OpenAPI pomocí všechna metadata v souboru function.json pro jednotlivé funkce triggeru HTTP. Je potřeba vyplnit Další informace o rozhraní API z [specifikace OpenAPI](http://swagger.io/specification/), jako je například šablony žádostí a odpovědí.

Podrobné pokyny najdete v tématu [úvodní kurz](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Dostupné šablony

|Název| Popis |
|:-----|:-----|
|Vygenerovanou definici|Definice OpenAPI s maximální množství informací, které lze odvodit z metadat existující funkce.|

### <a name="quickstart-details"></a>Součástí metadat v vygenerovanou definici

Následující tabulka představuje nastavení portálu Azure portal a odpovídajících dat v function.json, jak je namapován na vygenerované kostru Swagger.

|Swagger.json|Uživatelské rozhraní portálu|Function.json|
|:----|:-----|:-----|
|[Hostitel](http://swagger.io/specification/#fixed-fields-15)|**Funkční aplikace nastavení** > **nastavení služby App Service** > **přehled** > **adresy URL**|*Není k dispozici.*
|[Cesty](http://swagger.io/specification/#paths-object-29)|**Integrace** > **vybrané metody HTTP**|Vazby: trasy
|[Cesta položky](http://swagger.io/specification/#path-item-object-32)|**Integrace** > **šablonu trasy**|Vazby: metody
|[Zabezpečení](http://swagger.io/specification/#security-scheme-object-112)|**klíče**|*Není k dispozici.*|
|ID operace *|**Trasa + povolené akce**|Trasa + povolených příkazů|

\*ID operace se vyžaduje jenom pro integraci s PowerApps a Flow.
> [!NOTE]
> Rozšíření x-ms-summary obsahuje zobrazovaný název v Logic Apps, PowerApps a Flow.
>
> Další informace najdete v tématu [přizpůsobení definice Swaggeru pro PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Nastavení definice rozhraní API pomocí CI/CD

 Je nutné povolit hostování na portálu, dříve než povolíte správy zdrojového kódu k úpravě definice vašeho rozhraní API ze správy zdrojových kódů definice rozhraní API. Postupujte podle těchto pokynů:

1. Přejděte do **definice rozhraní API (preview)** ve vašich nastavení aplikace function app.
  1. Nastavte **zdroj definice rozhraní API** k **funkce**.
  1. Klikněte na tlačítko **šablonu definic rozhraní API vygenerovat** a potom **Uložit** k vytvoření definice šablony pro úpravu později.
  1. Poznamenejte si adresu URL definice rozhraní API a klíč.
1. [Nastavení průběžné integrace a nasazování (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Upravit swagger.json ve správě zdrojového kódu v \site\wwwroot\.azurefunctions\swagger\swagger.json.

Nyní, jsou změny swagger.json ve vašem úložišti hostované aplikace function App na rozhraní API pro definici adresy URL a klíč, který jste si poznamenali v kroku 1.c.

## <a name="next-steps"></a>Další postup
* [Úvodní kurz](functions-api-definition-getting-started.md). Vyzkoušejte náš návod zobrazíte definice OpenAPI v akci.
* [Úložiště GitHub funkce – Azure](https://github.com/Azure/Azure-Functions/). Podívejte se funkce úložiště sdělte nám svůj názor na verzi preview podpory definice rozhraní API. Vytvořit problém na Githubu pro všechno, co chcete zobrazit aktualizovaný.
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md). Další informace o kódování funkcí a definování triggerů a vazeb.
