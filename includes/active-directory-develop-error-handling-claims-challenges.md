---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760658"
---
## <a name="conditional-access-and-claims-challenges"></a>Podmíněný přístup a výzvy k deklaracím

Při tichém získávání tokenů může vaše aplikace obdržet chyby, když se v rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásada MFA [, jako je](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) například zásada MFA.

Vzor pro zpracování této chyby je interaktivní získání tokenu pomocí MSAL. Tím se uživateli zobrazí výzva k uspokojení požadovaných zásad podmíněného přístupu.

V některých případech při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. Pokud má například zásada podmíněného přístupu spravované zařízení (Intune), bude mít tato chyba něco jako [AADSTS53000: vaše zařízení musí být spravované pro přístup k tomuto prostředku](../articles/active-directory/develop/reference-aadsts-error-codes.md) nebo podobným způsobem. V takovém případě můžete deklarace identity předat voláním metody získat token, aby se uživateli zobrazila výzva, aby splnila příslušné zásady.
