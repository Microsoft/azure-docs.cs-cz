---
title: Jednostránkové aplikace (volání webového rozhraní API) – Microsoft Identity Platform
description: Naučte se vytvářet jednostránkové aplikace (volání webového rozhraní API).
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
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766153"
---
# <a name="single-page-application-call-a-web-api"></a>Jednostránkové aplikace: volání webového rozhraní API

Doporučujeme, abyste před voláním webového rozhraní API zavolali metodu `acquireTokenSilent` pro získání nebo obnovení přístupového tokenu. Po vytvoření tokenu můžete zavolat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Úhlová

Úhlová obálka MSAL využívá výhod zachytávací protokolu HTTP k tichému získání přístupových tokenů a k jejich připojení k požadavkům protokolu HTTP na rozhraní API. Další informace najdete v tématu [získání tokenu pro volání rozhraní API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-spa-production.md)
