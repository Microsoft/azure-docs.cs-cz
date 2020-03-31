---
title: Interaktivní chování výzvy k požadavku (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se přizpůsobit chování výzev v interaktivních hovorech pomocí knihovny Microsoft Authentication Library for JavaScript (MSAL.js).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695972"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Prompt chování v interaktivních požadavcích MSAL.js

Když uživatel vytvořil aktivní relaci Azure AD s více uživatelskými účty, přihlašovací stránka Azure AD ve výchozím nastavení vyzve uživatele k výběru účtu před pokračováním k přihlášení. Uživatelé neuvidí prostředí výběru účtu, pokud existuje pouze jedna ověřená relace s Azure AD.

Knihovna MSAL.js (počínaje v0.2.4) neodesílá parametr výzvy`loginRedirect`během `loginPopup` `acquireTokenRedirect` interaktivních požadavků ( , , a ), a `acquireTokenPopup`tím nevynucuje žádné chování výzvy. Pro požadavky tichých `acquireTokenSilent` tokenů pomocí metody msal.js `none`předá parametr výzvy nastavený na .

Na základě scénáře aplikace můžete řídit chování výzvy pro interaktivní požadavky nastavením parametru výzvy v parametrech požadavku předaných metodám. Například pokud chcete vyvolat prostředí výběru účtu:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Následující hodnoty výzvy lze předat při ověřování pomocí Azure AD:

**přihlášení:** Tato hodnota vynutí, aby uživatel zadali pověření v požadavku na ověření.

**select_account:** Tato hodnota poskytne uživateli možnost i možnost výběru účtu se seznamem všech účtů v relaci.

**souhlas:** Tato hodnota vyvolá dialog o souhlasu OAuth, který umožňuje uživatelům udělit oprávnění k aplikaci.

**žádné:** Tato hodnota zajistí, že uživatel nezobrazí žádnou interaktivní výzvu. Doporučuje se nepředávat tuto hodnotu interaktivním metodám v souboru MSAL.js, protože může mít neočekávané chování. Místo toho `acquireTokenSilent` použijte metodu k dosažení tichých volání.

## <a name="next-steps"></a>Další kroky

Přečtěte si `prompt` více o parametru v [implicitním grantovém protokolu OAuth 2.0,](v2-oauth2-implicit-grant-flow.md) který používá knihovna MSAL.js.
