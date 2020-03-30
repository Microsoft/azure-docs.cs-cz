---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186983"
---
## <a name="ropc-flow-notes"></a>Ropc poznámky o toku
Ve službě Azure Active Directory B2C (Azure AD B2C) jsou podporované následující možnosti:

- **Nativní klient**: Interakce uživatele během ověřování se stane, když kód běží na zařízení na straně uživatele. Zařízení může být mobilní aplikace, která běží v nativním operačním systému, jako je android a iOS.
- **Tok veřejného klienta**: Ve volání rozhraní API jsou odesílána pouze pověření uživatele shromážděná aplikací. Pověření aplikace nejsou odeslány.
- **Přidat nové deklarace identity**: Obsah tokenu ID lze změnit a přidat nové deklarace identity.

Následující toky nejsou podporovány:

- **Server-to-server**: Systém ochrany identity potřebuje spolehlivou IP adresu získanou od volajícího (nativního klienta) jako součást interakce. Při volání rozhraní API na straně serveru se používá pouze ip adresa serveru. Pokud je překročena dynamická prahová hodnota neúspěšných ověřování, může systém ochrany identity identifikovat opakovanou adresu IP jako útočníka.
- **Důvěrný tok klienta**: ID klienta aplikace je ověřeno, ale tajný klíč aplikace není ověřen.

Při použití toku ROPC zvažte následující:

- ROPC nefunguje, pokud dojde k přerušení toku ověřování, který vyžaduje interakci uživatele. Pokud například vypršela platnost hesla nebo je třeba je změnit, je vyžadováno vícefaktorové ověřování nebo pokud je třeba během přihlášení shromažďovat další informace (například souhlas uživatele).
- ROPC podporuje pouze místní účty. Uživatelé se nemohou přihlásit pomocí poskytovatelů federovaných identit, jako jsou Microsoft, Google+, Twitter, AD-FS nebo Facebook.
- Session Management, včetně keep me signed-in (KMSI), se nepoužije.
