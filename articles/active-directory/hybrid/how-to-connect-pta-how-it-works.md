---
title: 'Azure AD Connect: Předávací ověřování – jak funguje | Dokumenty společnosti Microsoft'
description: Tento článek popisuje, jak funguje předávací ověřování služby Azure Active Directory.
services: active-directory
keywords: Předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347770"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Předávací ověřování služby Azure Active Directory: Podrobný technický prohloubení
Tento článek je přehled o tom, jak funguje předávací ověřování služby Azure Active directory (Azure AD). Podrobné technické a bezpečnostní informace naleznete v článku [zabezpečení hloubkového prohledání.](how-to-connect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak funguje předávací ověřování služby Azure Active Directory?

>[!NOTE]
>Jako předpoklad pro předávací ověřování k práci, uživatelé musí být zřízena do Služby Azure AD z místního služby Active Directory pomocí Azure AD Connect. Předávací ověřování se nevztahuje na uživatele pouze pro cloud.

Když se uživatel pokusí přihlásit k aplikaci zabezpečené službou Azure AD a pokud je v tenantovi povoleno předávací ověřování, dojde k následujícím krokům:

1. Uživatel se pokusí získat přístup k aplikaci, například [Outlook Web App](https://outlook.office365.com/owa/).
2. Pokud uživatel ještě není přihlášen, je uživatel přesměrován na stránku **přihlášení uživatele** služby Azure AD.
3. Uživatel zadá své uživatelské jméno na přihlašovací stránku Azure AD a pak vybere tlačítko **Další.**
4. Uživatel zadá své heslo na přihlašovací stránku Azure AD a pak vybere tlačítko **Přihlásit** se.
5. Azure AD, na přijetí žádosti o přihlášení, umístí uživatelské jméno a heslo (šifrované pomocí veřejného klíče agenty ověřování) ve frontě.
6. Místní agent ověřování načte uživatelské jméno a šifrované heslo z fronty. Všimněte si, že agent není často dotazování na požadavky z fronty, ale načte požadavky přes předem nařízené trvalé připojení.
7. Agent dešifruje heslo pomocí jeho soukromého klíče.
8. Agent ověřuje uživatelské jméno a heslo proti službě Active Directory pomocí standardních rozhraní API systému Windows, což je podobný mechanismus, který používá služba AD FS (Active Directory Federation Services). Uživatelské jméno může být místní výchozí uživatelské jméno, obvykle `userPrincipalName`nebo jiný atribut nakonfigurovaný ve službě Azure AD Connect (označované jako `Alternate ID`).
9. Místní řadič domény služby Active Directory (DC) vyhodnotí požadavek a vrátí agentovi příslušnou odpověď (úspěch, neúspěch, platnost hesla nebo uzamčení uživatelem).
10. Agent ověřování, zase vrátí tuto odpověď zpět do Služby Azure AD.
11. Azure AD vyhodnotí odpověď a odpoví uživateli podle potřeby. Například Azure AD buď podepíše uživatele okamžitě nebo požadavky na Azure vícefaktorové ověřování.
12. Pokud je přihlášení uživatele úspěšné, může k aplikaci přistupovat.

Následující diagram znázorňuje všechny součásti a kroky:

![Předávací ověřování](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které ne.
- [Rychlý start:](how-to-connect-pta-quick-start.md)Zprovoznění při předávacím ověřování Azure AD.
- [Migrace ze služby AD FS do předávacího ověřování](https://aka.ms/adfstoPTADP) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení:](../authentication/howto-password-smart-lockout.md)Nakonfigurujte funkci inteligentního uzamčení ve vašem tenantovi k ochraně uživatelských účtů.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Najděte odpovědi na často kladené otázky.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md)služby : Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory můžete zasít nové požadavky na funkce.

