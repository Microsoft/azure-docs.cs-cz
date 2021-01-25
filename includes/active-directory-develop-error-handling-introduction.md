---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760642"
---
Tento článek obsahuje přehled různých typů chyb a doporučení pro zpracování běžných chyb při přihlašování.

## <a name="msal-error-handling-basics"></a>Základy zpracování chyb MSAL

Výjimky v knihovně Microsoft Authentication Library (MSAL) jsou určené vývojářům aplikací k odstraňování potíží, nikoli k zobrazení koncovým uživatelům. Zprávy výjimek nejsou lokalizovány.

Při zpracování výjimek a chyb můžete použít samotný typ výjimky a kód chyby k rozlišení mezi výjimkami.  Seznam kódů chyb najdete v tématu [ověřování Azure AD a kódy chyb autorizace](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Během přihlašování se může zobrazit chyba týkající se souhlasu, podmíněného přístupu (MFA, Správa zařízení, omezení na základě umístění), vystavení a uplatnění tokenu a vlastností uživatele.

V následující části najdete další podrobnosti o zpracování chyb v aplikaci.