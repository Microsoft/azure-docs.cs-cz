---
title: Jednostránková aplikace (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (Přesun do produkčního prostředí)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075007"
---
# <a name="single-page-application---move-to-production"></a>Jednostránková aplikace – Přesun do produkčního prostředí

Teď, když víte, jak k získání tokenu pro volání webových rozhraní API, zjistěte, jak přejít do produkčního prostředí.

## <a name="improve-your-app"></a>Zlepšení vaší aplikace

Postupujte podle kroků, které jsou potřebné k tomu aplikace v produkčním prostředí připraveno.

- [Povolení protokolování](msal-logging.md) ve vaší aplikaci.

## <a name="test-your-integration"></a>Otestovat integraci vašich

- Otestovat integraci vašich podle [kontrolní seznam k Microsoft identity platform integrace](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Další postup

Tady je několik dalších ukázky a kurzy:

- Podrobné informace z tohoto rychlého startu ukázkový, který vysvětluje kód pro přihlášení uživatelů a získání přístupového tokenu pro volání rozhraní Graph API MS využitím MSAL.js

    > [!div class="nextstepaction"]
    > [Kurz jazyka JavaScript SPA](./tutorial-v2-javascript-spa.md)

- Ukázka představením toho, jak získat tokeny pro vlastní back-endové webové rozhraní API pomocí MSAL.js

     > [!div class="nextstepaction"]
     > [Jednostránková aplikace s back-endu ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Ukázky ukazují, jak používat MSAL.js k přihlášení uživatelů v aplikaci registrovanou pomocí Azure AD B2C

    > [!div class="nextstepaction"]
    > [Jednostránková aplikace pomocí Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
