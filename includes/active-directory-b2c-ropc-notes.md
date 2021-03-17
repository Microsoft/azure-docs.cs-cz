---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78186983"
---
## <a name="ropc-flow-notes"></a>Poznámky ke ROPC toku
V Azure Active Directory B2C (Azure AD B2C) jsou podporovány následující možnosti:

- **Nativní klient**: během ověřování dojde k interakci uživatele, když se kód spouští na zařízení na straně uživatele. Zařízení může být mobilní aplikace, která běží v nativním operačním systému, jako je Android a iOS.
- **Veřejný klientský tok**: v volání rozhraní API se odešlou jenom přihlašovací údaje uživatele shromážděné aplikací. Přihlašovací údaje aplikace se neodesílají.
- **Přidat nové deklarace identity**: obsah TOKENu ID se dá změnit tak, aby se přidaly nové deklarace identity.

Následující toky nejsou podporovány:

- **Server-Server**: systém ochrany identit potřebuje spolehlivou IP adresu získanou od volajícího (nativního klienta) v rámci interakce. V případě volání rozhraní API na straně serveru se používá jenom IP adresa serveru. Pokud dojde k překročení dynamické prahové hodnoty neúspěšných ověření, může systém Identity Protection identifikovat opakovanou IP adresu jako útočníka.
- **Důvěrný tok klienta**: ID klienta aplikace je ověřeno, ale tajný klíč aplikace není ověřen.

Při použití toku ROPC Vezměte v úvahu následující skutečnosti:

- ROPC nefunguje, pokud dojde k přerušení ověřovacího toku, který potřebuje zásah uživatele. Například pokud vypršela platnost hesla nebo je nutné ho změnit, je vyžadováno ověřování Multi-Factor Authentication nebo další informace, které je potřeba shromáždit během přihlašování (například souhlas uživatele).
- ROPC podporuje pouze místní účty. Uživatelé se nemůžou přihlašovat pomocí federovaných zprostředkovatelů identity, jako je Microsoft, Google +, Twitter, AD-FS nebo Facebook.
- Správa relací, včetně zůstatku přihlášeného (políčko zůstat přihlášeni), se nedá použít.
