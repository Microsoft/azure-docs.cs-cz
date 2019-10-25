---
title: Chování výzvy v interaktivních žádostech (Microsoft Authentication Library pro JavaScript)
titleSuffix: Microsoft identity platform
description: Přečtěte si o přizpůsobení chování výzvy v interaktivních voláních pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803028"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Chování výzvy v interaktivních žádostech MSAL. js

Když uživatel navázal aktivní relaci Azure AD s několika uživatelskými účty, zobrazí se ve výchozím nastavení přihlašovací stránka Azure AD, aby uživatel před přihlášením vybral účet. Pokud bude k dispozici jenom jedna ověřená relace se službou Azure AD, uživatelé nebudou vidět prostředí pro výběr účtu.

Knihovna MSAL. js (začínající v v 0.2.4) neodesílá parametr výzvy během interaktivních požadavků (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` a `acquireTokenPopup`), a proto nevynutila žádné chování výzvy. Pro požadavky na tiché tokeny pomocí metody `acquireTokenSilent` předává MSAL. js parametr Prompt nastavený na `none`.

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

**žádné:** Tato hodnota zajistí, že uživatel nebude zobrazovat žádné interaktivní výzvy. Doporučuje se předat tuto hodnotu do interaktivních metod v MSAL. js, protože to může mít neočekávané chování. Místo toho použijte metodu `acquireTokenSilent` k zajištění tichého volání.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o parametru `prompt` v protokolu [OAuth 2,0 implicitního udělení](v2-oauth2-implicit-grant-flow.md) , který používá knihovna MSAL. js.
