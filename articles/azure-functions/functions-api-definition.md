---
title: Metadata OpenAPI v Azure Functions | Microsoft Docs
description: Přehled podpory OpenAPI v Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Podpora metadat OpenAPI 2.0 v Azure Functions (preview)
OpenAPI 2.0 (dříve Swagger) podpora metadat v Azure Functions je funkce preview, která můžete použít k zápisu definici OpenAPI 2.0 v aplikaci funkce. Tento soubor pak můžete hostovat pomocí funkce aplikace.

[OpenAPI metadata](http://swagger.io/) umožňuje funkci, která je hostitelem rozhraní REST API pro širokou škálu na ostatní software. Tento software obsahuje Microsoft nabídky jako PowerApps a [funkce API Apps služby Azure App Service](../app-service/app-service-web-overview.md), jako jsou nástroje pro vývojáře třetích stran [Postman](https://www.getpostman.com/docs/importing_swagger), a [mnoho další balíčky](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Doporučujeme začít s [kurz Začínáme](./functions-api-definition-getting-started.md) a vrátilo se do tohoto dokumentu další informace týkající se konkrétních funkcí.

## <a name="enable"></a>Povolit podporu definice OpenAPI
Můžete nakonfigurovat všechna nastavení OpenAPI na **definice rozhraní API** stránky v aplikaci funkce **funkce**.

Chcete-li povolit generování definici hostované OpenAPI a definice rychlý start, nastavte **zdroj definice rozhraní API** k **– funkce (Preview)**. **Externí adresu URL** umožňuje funkce sloužící definici OpenAPI, která má hostovaný jinde.

## <a name="generate-definition"></a>Generovat kostru Swagger z vaší funkce metadat
Šablonu můžete zahájit zápis svou první OpenAPI definici. Funkce šablony definice vytvoří definici zhuštěných OpenAPI pomocí všechna metadata v souboru function.json pro jednotlivé funkce aktivace protokolu HTTP. Budete muset vyplňte další informace o rozhraní API z [OpenAPI specifikace](http://swagger.io/specification/), jako je například šablony žádostí a odpovědí.

Podrobné pokyny najdete v tématu [kurz Začínáme](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Dostupné šablony

|Název| Popis |
|:-----|:-----|
|Vygenerovaný definice|Definici OpenAPI s maximální velikostí informace, které lze odvodit z existující metadata funkce.|

### <a name="quickstart-details"></a>Zahrnuté metadata v definici generovaného

Následující tabulka představuje nastavení portálu Azure a odpovídajících dat v function.json, jak je mapován na generovaného kostru Swagger.

|Swagger.json|Portál uživatelského rozhraní|Function.json|
|:----|:-----|:-----|
|[Hostitel](http://swagger.io/specification/#fixed-fields-15)|**Funkce nastavení aplikace** > **nastavení služby App Service** > **přehled** > **adresy URL**|*Není k dispozici*
|[Cesty](http://swagger.io/specification/#paths-object-29)|**Integrovat** > **metody vybrané HTTP**|Vazby: trasy
|[Položky cesty](http://swagger.io/specification/#path-item-object-32)|**Integrovat** > **šablonu trasy**|Vazby: metody
|[Zabezpečení](http://swagger.io/specification/#security-scheme-object-112)|**Klíče**|*Není k dispozici*|
|operationID *|**Trasy + povolené příkazy**|Trasy + povolených příkazů|

\*ID operace se vyžaduje jenom pro integraci s PowerApps a toku.
> [!NOTE]
> Rozšíření x-ms souhrn poskytuje zobrazovaný název Logic Apps, PowerApps a toku.
>
> Další informace najdete v tématu [přizpůsobit vaší definici Swaggeru pro PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Použití CI/CD nastavení definice rozhraní API.

 Je nutné povolit definice rozhraní API hostování portálu před povolením zdrojového kódu k úpravě vaše definice rozhraní API od správy zdrojového kódu. Postupujte podle těchto pokynů:

1. Přejděte do **definice rozhraní API (preview)** v aplikaci nastavení funkce.
  1. Nastavit **zdroj definice rozhraní API** k **funkce**.
  1. Klikněte na tlačítko **šablony definice rozhraní API generovat** a potom **Uložit** k vytvoření definice šablony úpravy později.
  1. Poznámka: adresa URL definice rozhraní API a klíč.
1. [Nastavit nepřetržité integrace/průběžné nasazování (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Upravit swagger.json ve správě zdrojového kódu v \site\wwwroot\.azurefunctions\swagger\swagger.json.

Nyní, jsou změny swagger.json ve svém úložišti hostované funkce aplikace na rozhraní API adresa URL definice a klíč, který jste si poznamenali v kroku 1.c.

## <a name="next-steps"></a>Další postup
* [Kurz Začínáme](functions-api-definition-getting-started.md). Zkuste naše návod zobrazíte definici OpenAPI v akci.
* [Azure úložiště GitHub funkce](https://github.com/Azure/Azure-Functions/). Podívejte se na funkce úložiště pro vaše názory na preview podporu definice rozhraní API. Ujistěte se, problém Githubu nic, co chcete zobrazit aktualizovaný.
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md). Další informace o kódování funkcí a definování triggerů a vazeb.
