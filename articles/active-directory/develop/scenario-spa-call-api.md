---
title: Jednostránkové aplikace (volání webového rozhraní API) - platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (volání webového rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545600"
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
