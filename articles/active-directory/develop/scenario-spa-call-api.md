---
title: Sestavení jednostránkové aplikace s voláním webového rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se vytvořit jednostránkovou aplikaci, která volá webové rozhraní API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80882127"
---
# <a name="single-page-application-call-a-web-api"></a>Jednostránkové aplikace: volání webového rozhraní API

Doporučujeme, abyste `acquireTokenSilent` před voláním webového rozhraní API zavolali metodu pro získání nebo obnovení přístupového tokenu. Po vytvoření tokenu můžete zavolat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pomocí získaného přístupového tokenu jako nosiče v požadavku HTTP můžete volat libovolné webové rozhraní API, například Microsoft Graph API. Například:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Úhlová obálka MSAL využívá výhod zachytávací protokolu HTTP k tichému získání přístupových tokenů a k jejich připojení k požadavkům protokolu HTTP na rozhraní API. Další informace najdete v tématu [získání tokenu pro volání rozhraní API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-spa-production.md)
