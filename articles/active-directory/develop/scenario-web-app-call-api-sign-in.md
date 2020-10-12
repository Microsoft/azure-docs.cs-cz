---
title: Odebrání účtů z mezipaměti tokenů při odhlašování – Microsoft Identity Platform | Azure
description: Zjistěte, jak odebrat účet z mezipaměti tokenů při odhlášení.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5246100815fde569c55027a555464c44a240d4b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119993"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webová aplikace, která volá webová rozhraní API: Odebrání účtů z mezipaměti tokenů při globálním odhlašování

Zjistili jste, jak přidat přihlášení do webové aplikace ve [webové aplikaci, která se přihlásí uživatelům: přihlášení a](scenario-web-app-sign-user-sign-in.md)odhlášení.

Odhlášení se liší u webové aplikace, která volá webová rozhraní API. Když se uživatel odhlásí z vaší aplikace nebo z jakékoli aplikace, musíte tokeny přidružené k tomuto uživateli odebrat z mezipaměti tokenů.

## <a name="intercept-the-callback-after-single-sign-out"></a>Zachytit zpětné volání po jednotném odhlašování

Pokud chcete vymazat položku mezipaměti tokenů přidruženou k účtu, který se odhlásil, může vaše aplikace zachytit `logout` událost po události. Webové aplikace ukládají přístupové tokeny pro každého uživatele v mezipaměti tokenů. Zachytáváním po `logout` zpětném volání může webová aplikace odebrat uživatele z mezipaměti.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. identity. Web se stará o implementaci odhlašování za vás. Podrobnosti najdete v tématu [Microsoft. identity. Web Source Code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ukázka ASP.NET neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="java"></a>[Java](#tab/java)

Ukázka Java neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="python"></a>[Python](#tab/python)

Ukázka Pythonu neodebírá účty z mezipaměti při globálním odhlašování.

---

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---