---
title: Chování výzvy interaktivního požadavku (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přizpůsobit chování výzvy v interaktivních voláních pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477545"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Chování výzvy v MSAL.js interaktivní požadavky

Když uživatel navázal aktivní relaci Azure AD s několika uživatelskými účty, zobrazí se ve výchozím nastavení přihlašovací stránka Azure AD, aby uživatel před přihlášením vybral účet. Pokud bude k dispozici jenom jedna ověřená relace se službou Azure AD, uživatelé nebudou vidět prostředí pro výběr účtu.

Knihovna MSAL.js (začínající v v 0.2.4) neposílá parametr Prompt během interaktivních požadavků ( `loginRedirect` , a `loginPopup` `acquireTokenRedirect` `acquireTokenPopup` ), a proto nezpůsobuje žádné chování výzvy. Pro požadavky na tiché tokeny pomocí `acquireTokenSilent` metody MSAL.js předá parametr Prompt nastaven na `none` .

V závislosti na scénáři vaší aplikace můžete řídit chování výzvy pro interaktivní požadavky tím, že nastavíte parametr Prompt v parametrech požadavku předaných metodám. Například pokud chcete vyvolat prostředí pro výběr účtu:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Při ověřování ve službě Azure AD můžete předat následující hodnoty dotazu:

**přihlašovací jméno:** Tato hodnota vynutí, aby uživatel zadal přihlašovací údaje k žádosti o ověření.

**select_account:** Tato hodnota uživateli poskytne seznam všech účtů v relaci, který bude mít možnost výběru účtu.

**souhlas:** Tato hodnota vyvolá dialog pro vyjádření souhlasu OAuth, který umožňuje uživatelům udělit oprávnění k aplikaci.

**žádné:** Tato hodnota zajistí, že uživatel nebude zobrazovat žádné interaktivní výzvy. Doporučuje se předat tuto hodnotu do interaktivních metod v MSAL.js, protože může mít neočekávané chování. Místo toho použijte `acquireTokenSilent` metodu k dosažení tichého volání.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o `prompt` parametru v protokolu [OAuth 2,0 pro implicitní udělení](v2-oauth2-implicit-grant-flow.md) , který MSAL.js Library používá.
