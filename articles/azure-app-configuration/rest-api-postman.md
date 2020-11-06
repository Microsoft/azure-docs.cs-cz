---
title: Azure Active Directory REST API-test pomocí metody post
description: Použití post k otestování konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9690678fc7b794c694e588a7993cb131d8264a72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424073"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Testování REST API konfigurace aplikace Azure pomocí post

K otestování REST API pomocí nástroje [post](https://www.getpostman.com/)musíte do svých požadavků zahrnout hlavičky HTTP vyžadované pro [ověřování](./rest-api-authentication-hmac.md) . Tady je postup, jak nakonfigurovat metodu post pro testování REST API a automatické generování ověřovacích hlaviček:

1. Vytvořit novou [žádost](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Přidání `signRequest` funkce z [ukázky ověřování JavaScriptu](./rest-api-authentication-hmac.md#javascript) do [skriptu před vyžádáním](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) pro požadavek
1. Na konec skriptu před vyžádáním přidejte následující kód. Aktualizujte přístupový klíč tak, jak je znázorněno v komentáři TODO.

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Odeslat žádost
