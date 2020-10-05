---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87311533"
---
> [!IMPORTANT]
> MSAL.js 2,0 aktuálně nepodporuje Azure AD B2C pro použití s tokem autorizačního kódu PKCE. V tuto chvíli Azure AD B2C doporučuje použít implicitní tok, jak je popsáno v [kurzu: registrace aplikace][implicit-flow]. Pokud chcete postupovat podle tohoto problému, přečtěte si [MSAL.js wiki][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
