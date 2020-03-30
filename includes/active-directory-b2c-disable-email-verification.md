---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126737"
---
## <a name="disable-email-verification"></a>Zakázání ověření e-mailu

Ve výchozím nastavení Azure Active Directory B2C (Azure AD B2C) ověřuje e-mailovou adresu zákazníka pro místní účty (účty pro uživatele, kteří se zaregistrují pomocí e-mailové adresy nebo uživatelského jména). Azure AD B2C zajišťuje platné e-mailové adresy tím, že vyžaduje, aby zákazníci ověřit během procesu registrace. Také zabraňuje škodlivým aktérům používat automatizované procesy ke generování podvodných účtů ve vašich aplikacích.

Někteří vývojáři aplikací dávají přednost přeskočení ověření e-mailu během procesu registrace a místo toho mají zákazníci ověřit svou e-mailovou adresu později. Pro podporu tohoto, Azure AD B2C můžete nakonfigurovat zakázat ověření e-mailu. Tím se vytvoří hladší proces registrace a vývojářům poskytne flexibilitu k odlišení zákazníků, kteří ověřili svou e-mailovou adresu od zákazníků, kteří tak neučinili.

> [!WARNING]
> Zakázání ověření e-mailu v procesu registrace může vést k spamu. Pokud zakážete výchozí ověření e-mailu poskytované Azure AD B2C, doporučujeme implementovat náhradní ověřovací systém.
