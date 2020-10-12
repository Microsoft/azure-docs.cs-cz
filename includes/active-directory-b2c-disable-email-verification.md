---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79126737"
---
## <a name="disable-email-verification"></a>Zakázání ověření e-mailu

Ve výchozím nastavení Azure Active Directory B2C (Azure AD B2C) ověřuje e-mailovou adresu vašeho zákazníka u místních účtů (účty pro uživatele, kteří se registrují pomocí e-mailové adresy nebo uživatelského jména). Azure AD B2C zajišťuje platné e-mailové adresy tím, že je vyžaduje, aby je uživatelé během procesu registrace ověřili. Zabraňuje také škodlivým aktérům v použití automatizovaných procesů ke generování podvodných účtů ve vašich aplikacích.

Někteří vývojáři aplikací chtějí během procesu registrace přeskočit ověřování e-mailu a místo toho můžou e-mailové adresy ověřit později. V případě potřeby je Azure AD B2C možné nakonfigurovat pro zákaz ověřování e-mailů. Tím dojde k vytvoření plynulého procesu registrace a vývojářům umožníte odlišit zákazníky, kteří si ověřili jejich e-mailovou adresu od zákazníků, kteří ne.

> [!WARNING]
> Vypnutí ověřování e-mailu v procesu registrace může vést k spamu. Pokud zakážete výchozí ověřování e-mailů poskytnutých Azure AD B2C, doporučujeme implementovat systém náhradního ověřování.
