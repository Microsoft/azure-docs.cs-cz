---
title: Vytvoření jednostránkové aplikace volající webové rozhraní API – platforma identit Microsoftu | Azure
description: Zjistěte, jak vytvořit jednostránkovou aplikaci, která volá webové rozhraní API
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
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159994"
---
# <a name="single-page-application-call-a-web-api"></a>Jednostránková aplikace: Volání webového rozhraní API

Doporučujeme volat metodu `acquireTokenSilent` získat nebo obnovit přístupový token před voláním webové rozhraní API. Poté, co máte token, můžete volat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

# <a name="javascript"></a>[Javascript](#tab/javascript)

Pomocí získaného přístupového tokenu jako nositele v požadavku HTTP volat jakékoli webové rozhraní API, jako je například rozhraní Microsoft Graph API. Například:

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

Obálka MSAL Angular využívá zachycovač http automaticky získat přístupové tokeny tiše a připojit je k požadavkům HTTP k souborům API. Další informace naleznete [v tématu Získání tokenu pro volání rozhraní API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-spa-production.md)
