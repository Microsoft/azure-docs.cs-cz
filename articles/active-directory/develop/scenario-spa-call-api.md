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
ms.openlocfilehash: 3b71f66dfcbd33cdecc1a6fea46871ba0cda687d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442884"
---
# <a name="single-page-application-call-a-web-api"></a>Jednostránkové aplikace: volání webového rozhraní API

Doporučujeme, abyste `acquireTokenSilent` před voláním webového rozhraní API zavolali metodu pro získání nebo obnovení přístupového tokenu. Po vytvoření tokenu můžete zavolat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pomocí získaného přístupového tokenu jako nosiče v požadavku HTTP můžete volat libovolné webové rozhraní API, například Microsoft Graph API. Příklad:

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

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](scenario-spa-production.md)prostředí.