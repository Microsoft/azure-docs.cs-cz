---
title: 'Azure AD Connect: předávací ověřování – jak funguje | Microsoft Docs'
description: Tento článek popisuje, jak Azure Active Directory předávací ověřování funguje.
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836355"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory předávací ověřování: technický hluboký podrobně
Tento článek představuje přehled způsobu, jakým funguje předávací ověřování Azure Active Directory (Azure AD). Podrobné technické a bezpečnostní informace najdete v článku o [podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md) .

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak Azure Active Directory předávací ověřování funguje?

>[!NOTE]
>Aby se pro fungování předávacího ověřování fungovaly, musí se uživatelé zřídit do Azure AD z místní služby Active Directory pomocí Azure AD Connect. Předávací ověřování se nevztahuje jenom na uživatele, kteří jsou jenom pro Cloud.

Když se uživatel pokusí přihlásit k aplikaci zabezpečené službou Azure AD a v tenantovi je povolený předávací ověřování, dojde k následujícím krokům:

1. Uživatel se pokusí o přístup k aplikaci, například k aplikaci [Outlook Web App](https://outlook.office365.com/owa/).
2. Pokud uživatel ještě není přihlášený, uživatel se přesměruje na **přihlašovací stránku uživatele** Azure AD.
3. Uživatel zadá své uživatelské jméno na přihlašovací stránku služby Azure AD a pak vybere tlačítko **Další** .
4. Uživatel zadá své heslo na přihlašovací stránku Azure AD a pak vybere tlačítko **Přihlásit** se.
5. Azure AD při přijetí žádosti o přihlášení umístí uživatelské jméno a heslo (šifrované pomocí veřejného klíče ověřovacích agentů) do fronty.
6. Místní agent ověřování Získá uživatelské jméno a šifrované heslo z fronty. Všimněte si, že agent se často dotazuje na požadavky z fronty, ale načítá žádosti přes předem zavedené trvalé připojení.
7. Agent dešifruje heslo pomocí jeho privátního klíče.
8. Agent ověří uživatelské jméno a heslo ke službě Active Directory pomocí standardních rozhraní API systému Windows, což je podobný mechanismus, který používá Active Directory Federation Services (AD FS) (AD FS). Uživatelské jméno může být buď místní výchozí uživatelské jméno, obvykle `userPrincipalName` nebo jiný atribut nakonfigurovaný v Azure AD Connect (známý jako `Alternate ID` ).
9. Místní řadič domény (DC) služby Active Directory vyhodnocuje požadavek a vrátí příslušnou odpověď (úspěch, selhání, heslo vypršel nebo uživatel uzamkl) k agentovi.
10. Agent ověřování zase vrátí tuto odpověď zpět do služby Azure AD.
11. Azure AD vyhodnocuje odpověď a odpoví na uživatele podle potřeby. Například služba Azure AD buď podepisuje uživatele hned, nebo požadavky na Multi-Factor Authentication Azure AD.
12. Pokud je přihlášení uživatele úspěšné, uživatel může získat přístup k aplikaci.

Následující diagram znázorňuje všechny komponenty a postup, který je součástí:

![Předávací ověřování](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které nejsou.
- [Rychlé zprovoznění](how-to-connect-pta-quick-start.md): zprovoznění předávacího ověřování Azure AD.
- [Migrace z AD FS na předávací ověřování](https://aka.ms/adfstoPTADP) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): umožňuje nakonfigurovat funkci inteligentního uzamknutí pro vašeho tenanta, aby se chránily uživatelské účty.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Přečtěte si odpovědi na nejčastější dotazy.
- [Řešení potíží](tshoot-connect-pass-through-authentication.md): Naučte se řešit běžné problémy s funkcí předávacího ověřování.
- [Hloubkové podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Přečtěte si další informace o této doplňkové funkci.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte Fórum Azure Active Directory k započetí nových požadavků na funkce.

