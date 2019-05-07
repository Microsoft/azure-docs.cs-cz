---
title: Jednostránkové aplikace (volání webového rozhraní API) - platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (volání webového rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074842"
---
# <a name="single-page-application---call-a-web-api"></a>Jednostránková aplikace – volání a webového rozhraní API

Doporučujeme vám, že zavoláte `acquireTokenSilent` metodou získat nebo obnovit přístupový token před voláním metody webového rozhraní API. Jakmile máte token, můžete volat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

### <a name="javascript"></a>JavaScript

Použijte pořízené přístupový token jako nosiče v jednom požadavku HTTP k volání jakékoli webové rozhraní API, jako je například Microsoft Graph API. Příklad:

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

### <a name="angular"></a>Úhlová

Jak je uvedeno v [získávání tokenů části](scenario-spa-acquire-token.md), MSAL Angular obálky využívá zachycování HTTP automaticky tiše získat přístupové tokeny a připojit je na požadavky HTTP do rozhraní API.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-spa-production.md)
