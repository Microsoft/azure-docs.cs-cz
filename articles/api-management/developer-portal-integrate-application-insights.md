---
title: Integrace Application Insights na portál pro vývojáře
titleSuffix: Azure API Management
description: Naučte se integrovat Application Insights do spravovaného nebo samoobslužného portálu pro vývojáře.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741631"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integrace Application Insights na portál pro vývojáře

Oblíbená funkce Azure Monitor je Application Insights. Je to rozšiřitelná služba správy výkonu aplikací (APM) pro vývojáře a DevOps odborníky. Pomocí této služby můžete monitorovat portál pro vývojáře a detekovat anomálie výkonu. Application Insights obsahuje výkonné analytické nástroje, které vám pomůžou zjistit, co uživatelé skutečně dělají při návštěvě portálu pro vývojáře.

## <a name="add-application-insights-to-your-portal"></a>Přidání Application Insights na portál

Pomocí těchto kroků připojte Application Insights k portálu pro vývojáře spravovaného nebo samoobslužného hostování.

> [!IMPORTANT]
> Kroky 1 a 2 se pro spravované portály nevyžadují. Pokud máte spravovaný portál, přejděte ke kroku 4.

1. Nastavte [místní prostředí](developer-portal-self-host.md#step-1-set-up-local-environment) pro nejnovější vydání portálu pro vývojáře.

1. Nainstalujte balíček **npm** pro přidání [Paperbits pro Azure](https://github.com/paperbits/paperbits-azure):

    ```console
    npm install @paperbits/azure --save
    ```

1. V `startup.publish.ts` souboru ve `src` složce importujte a zaregistrujte modul Application Insights:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Načíst konfiguraci portálu:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Zvětšete konfiguraci lokality z předchozího kroku pomocí Application Insights konfigurace:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)
- [Automatizace nasazení portálu](automate-portal-deployments.md)
- [Samoobslužný hostování portálu pro vývojáře](developer-portal-self-host.md)
