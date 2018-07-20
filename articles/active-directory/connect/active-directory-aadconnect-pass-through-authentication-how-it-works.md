---
title: 'Služby Azure AD Connect: Předávací ověřování – jak to funguje | Dokumentace Microsoftu'
description: Tento článek popisuje, jak funguje Azure Active Directory předávací ověřování
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 371f3ea3b764eecbb621fc06ec483de4778e7db2
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159487"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure předávací ověřování služby Active Directory: Podrobné technické informace
Tento článek je přehled toho, jak funguje předávací ověřování služby Azure Active directory (Azure AD). Podrobné technické a informace o zabezpečení najdete v tématu [podrobné informace o zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) článku.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak funguje předávacího ověřování Azure Active Directory?

[!NOTE]
Jako předpoklad pro předávací ověřování pro práci uživatelé musí zřídit v Azure AD z místní služby Active Directory pomocí služby Azure AD Connect. Předávací ověřování se nevztahují na uživatelů pouze cloudu.

Když se uživatel pokusí k přihlášení k aplikaci zabezpečené službou Azure AD a předávací ověřování je povoleno na tenantovi, dojde k následujícím krokům:

1. Uživatel se pokusí o přístup k aplikaci, například [Outlook Web App](https://outlook.office365.com/owa/).
2. Pokud již není přihlášený uživatel, bude uživatel přesměrován do služby Azure AD **přihlášení uživatele** stránky.
3. Uživatel zadá své uživatelské jméno do služby Azure AD přihlašovací stránku a potom vybere **Další** tlačítko.
4. Uživatel zadá své heslo na přihlašovací stránce služby Azure AD a potom vybere **přihlášení** tlačítko.
5. Azure AD, při přijetí požadavku pro přihlášení, umístí uživatelského jména a hesla (šifrované pomocí veřejného klíče agentů ověřování) ve frontě.
6. Místní ověřovací Agent načte uživatelské jméno a šifrované heslo z fronty. Všimněte si, že Agent nemá často dotazovat na žádosti z fronty, ale načte požadavky prostřednictvím předem zavedené trvalé připojení.
7. Agent dešifruje heslo pomocí jeho privátní klíč.
8. Agent ověří uživatelské jméno a použije hesla služby Active Directory pomocí standardních rozhraní Windows API, které je podobné mechanismus pro jaké Active Directory Federation Services (AD FS). Uživatelské jméno může být buď místní výchozí uživatelské jméno, obvykle `userPrincipalName`, nebo jiný atribut nakonfigurované ve službě Azure AD Connect (označované jako `Alternate ID`).
9. Místní řadič domény služby Active Directory (DC) vyhodnotí žádost a vrací odpovídající odpověď (úspěch, chyba, vypršení platnosti hesla a uživatel uzamčen) k agentovi.
10. Ověřovací Agent pak vrátí tuto odpověď zpět do služby Azure AD.
11. Azure AD vyhodnocuje odpovědi a odpovídá uživateli podle potřeby. Například Azure AD buď okamžitě přihlásí uživatel nebo žádosti o Azure Multi-Factor Authentication.
12. Pokud přihlášení uživatele úspěšné, může uživatel přístup k aplikaci.

Následující diagram znázorňuje všechny součásti a kroky:

![Předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Další postup
- [Aktuální omezení](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [Rychlý Start](active-directory-aadconnect-pass-through-authentication-quick-start.md): uvedení do provozu na předávacího ověřování Azure AD.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Nakonfigurujte schopnosti inteligentním uzamčením ve svém tenantovi k ochraně uživatelské účty.
- [Nejčastější dotazy](active-directory-aadconnect-pass-through-authentication-faq.md): Najděte odpovědi na nejčastější dotazy.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Podrobné informace o zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): získáte podrobné technické informace o funkci předávací ověřování.
- [Azure AD bezproblémového jednotného přihlašování k](active-directory-aadconnect-sso.md): Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fórum Azure Active Directory do souboru žádostí o nové funkce.

