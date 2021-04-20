---
title: Integrace Google Tag Manager na portál pro vývojáře
titleSuffix: Azure API Management
description: Naučte se, jak připojit Google Tag Manager do spravovaného nebo samoobslužného portálu pro vývojáře ve službě Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741632"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integrace Google Tag Manager do API Management portálu pro vývojáře

[Google tag Manager](https://developers.google.com/tag-manager) je systém správy značek vytvořený společností Google. Můžete ji použít ke správě značek JavaScriptu a HTML používaných pro sledování a analýzu na webech. Můžete například použít Google Tag Manager pro integraci Google Analytics, Heat mapy nebo chatovacích robotů o, jako je LiveChat.

Postupujte podle kroků v tomto článku a připojte Google Tag Manager do spravovaného nebo samoobslužného portálu pro vývojáře ve službě Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Přidání Google Tag Manager na portál

Pomocí těchto kroků připojte Google Tag Manager k portálu pro vývojáře spravovaného nebo samoobslužného hostování.

> [!IMPORTANT]
> Kroky 1 a 2 se pro spravované portály nevyžadují. Pokud máte spravovaný portál, přejděte ke kroku 4.

1. Nastavte [místní prostředí](developer-portal-self-host.md#step-1-set-up-local-environment) pro nejnovější vydání portálu pro vývojáře.

1. Nainstalujte balíček **npm** pro přidání [Paperbits pro Google tag Manager](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. V `startup.publish.ts` souboru ve `src` složce importujte a zaregistrujte modul GTM:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Zvětšete konfiguraci lokality z předchozího kroku pomocí Google Tag Manager konfigurace:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

- [Automatizace nasazení portálu](automate-portal-deployments.md)
- [Samoobslužný hostování portálu pro vývojáře](developer-portal-self-host.md)