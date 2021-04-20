---
title: Podpora rozhraní API ve statických Web Appsch Azure s využitím Azure Functions
description: Informace o funkcích API, které Azure static Web Apps podporuje
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737473"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Podpora rozhraní API ve službě Azure static Web Apps Preview s využitím Azure Functions

Azure static Web Apps poskytuje koncové body rozhraní API bez serveru přes [Azure Functions](../azure-functions/functions-overview.md). Využitím Azure Functions se dynamicky škálují rozhraní API na základě poptávky a zahrnují tyto funkce:

- **Integrované zabezpečení** s přímým přístupem k [ověřování uživatelů a datům autorizace na základě rolí](user-information.md) .
- **Bezproblémové směrování** , díky kterému bude trasa _rozhraní API_ dostupná webové aplikaci bezpečně, aniž by vyžadovala vlastní pravidla CORS.
- **Aktivační události http** a vstupní/výstupní vazby.

## <a name="configuration"></a>Konfigurace

Koncové body rozhraní API jsou k dispozici webové aplikaci prostřednictvím trasy _rozhraní API_ . I když je tato trasa pevná, máte kontrolu nad složkou a projektem, kde najdete přidruženou aplikaci Azure Functions. Toto umístění můžete změnit [úpravou souboru YAML pracovního postupu](github-actions-workflow.md#build-and-deploy) umístěného ve složce _. GitHub/pracovní postupy_ vašeho úložiště.

## <a name="constraints"></a>Omezení

Azure static Web Apps poskytuje rozhraní API prostřednictvím Azure Functions. Schopnosti Azure Functions jsou zaměřené na konkrétní sadu funkcí, které vám umožní vytvořit rozhraní API pro webovou aplikaci a povolit zabezpečenou připojení webové aplikace k rozhraní API. Tyto funkce se dodávají s určitými omezeními, včetně:

- Předpona trasy rozhraní API musí být _rozhraní API_.
- Rozhraní API musí být buď Node.js 12, .NET Core 3,1 nebo Python 3,8 Azure Functions aplikace.
- Pravidla směrování pro funkce rozhraní API podporují pouze [přesměrování](routes.md#redirects) a [zabezpečení tras s rolemi](routes.md#securing-routes-with-roles).
- Aktivační události jsou omezené na [http](../azure-functions/functions-bindings-http-webhook.md).
  - Vstupní a výstupní [vazby](../azure-functions/functions-triggers-bindings.md#supported-bindings) jsou podporovány.
- Protokoly jsou k dispozici pouze v případě, že do aplikace Functions přidáte [Application Insights](../azure-functions/functions-monitoring.md) .
- Některá nastavení aplikace spravuje služba. Nemůžete nakonfigurovat nastavení aplikace, která začínají následujícími předponami: `APPSETTING_` , `AZUREBLOBSTORAGE_` , `AZUREFILESSTORAGE_` , `AZURE_FUNCTION_` , `CONTAINER_` , `DIAGNOSTICS_` , `DOCKER_` , `FUNCTIONS_` , `IDENTITY_` , `MACHINEKEY_` , `MAINSITE_` , `MSDEPLOY_` , `SCMSITE_` , `SCM_` , `WEBSITES_` , `WEBSITE_` , `WEBSOCKET_` , `AzureWeb` .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
