---
title: Chování výzvy interaktivního požadavku (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přizpůsobit chování výzvy v interaktivních voláních pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695972"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Chování výzvy v interaktivních žádostech MSAL. js

Když uživatel navázal aktivní relaci Azure AD s několika uživatelskými účty, zobrazí se ve výchozím nastavení přihlašovací stránka Azure AD, aby uživatel před přihlášením vybral účet. Pokud bude k dispozici jenom jedna ověřená relace se službou Azure AD, uživatelé nebudou vidět prostředí pro výběr účtu.

Knihovna MSAL. js (začínající v v 0.2.4) neodesílá parametr výzvy během interaktivních požadavků`loginRedirect`(, `loginPopup` `acquireTokenRedirect` a `acquireTokenPopup`), a proto neuplatňuje žádné chování výzvy. Pro požadavky na tiché tokeny pomocí `acquireTokenSilent` metody MSAL. js předá parametr Prompt nastaven `none`na.

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

**žádné:** Tato hodnota zajistí, že uživatel nebude zobrazovat žádné interaktivní výzvy. Doporučuje se předat tuto hodnotu do interaktivních metod v MSAL. js, protože to může mít neočekávané chování. Místo toho použijte `acquireTokenSilent` metodu k dosažení tichého volání.

## <a name="next-steps"></a>Další kroky

Přečtěte si další `prompt` informace o parametru v protokolu [OAuth 2,0 implicitního udělení](v2-oauth2-implicit-grant-flow.md) , který používá knihovna MSAL. js.
