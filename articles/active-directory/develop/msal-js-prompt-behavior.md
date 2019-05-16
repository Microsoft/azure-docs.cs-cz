---
title: Výzvu chování v interaktivních požadavků (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o přizpůsobení chování výzvy v interaktivním volání pomocí knihovna Microsoft Authentication Library pro JavaScript (MSAL.js).
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
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544275"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Chování výzvy v MSAL.js interaktivních požadavků

Když se uživatel stal aktivní Azure AD relaci s několika uživatelskými účty, Azure AD přihlašovací stránku ve výchozím nastavení vyzve uživatele k výběru účtu než přistoupíte k přihlášení. Uživatelé neuvidí výběr účtu, pokud existuje pouze jeden ověřeného relace s Azure AD.

Knihovna MSAL.js (počínaje v0.2.4) neodesílá parametrem výzvy k zadání během interaktivní žádosti (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` a `acquireTokenPopup`) a tím nevynucuje žádné chování výzvy. Pro tichou žádosti o tokeny pomocí `acquireTokenSilent` metody MSAL.js předá výzvy parametrem nastaveným na `none`.

Založený na scénáři vaše aplikace, můžete řídit chování výzvy pro interaktivní žádosti nastavením parametrem výzvy k zadání v žádosti o parametry předané metodám. Například, pokud chcete vyvolat prostředí pro výběr účtu:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Následující příkazový řádek hodnoty můžete předávat při ověřování ve službě Azure AD:

**Přihlášení:** Tato hodnota bude nutí uživatele k zadání přihlašovacích údajů na žádost o ověření.

**select_account:** Tato hodnota bude uživateli poskytnout prostředí pro výběr účtu výpis všechny účty v relaci.

**vyjádření souhlasu:** Tato hodnota se vyvolá dialogu souhlasu OAuth, který umožňuje uživatelům udělit oprávnění k aplikaci.

**Žádné:** Tato hodnota se zajistí, že uživatel neuvidí žádné interaktivní výzvu. Je doporučeno nechcete předat tuto hodnotu v MSAL.js interaktivních metod, jak může mít neočekávané chování. Místo toho použijte `acquireTokenSilent` metodou k dosažení tiché volání.

## <a name="next-steps"></a>Další postup

Další informace najdete `prompt` parametr [implicitní grant OAuth 2.0](v2-oauth2-implicit-grant-flow.md) protokolu, které knihovna MSAL.js používá.
