---
title: 'Azure AD Connect: Předávací ověřování – aktuální omezení | Microsoft Docs'
description: Tento článek popisuje aktuální omezení předávací ověřování Azure Active Directory (Azure AD)
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 680e9967010771b8e3651c6f4eed81237f8fb4c3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure předávací ověřování služby Active Directory: Aktuální omezení

>[!IMPORTANT]
>Předávací ověřování Azure Active Directory (Azure AD) je bezplatná funkce a nepotřebujete žádné placené edice Azure AD pro použití. Předávací ověřování je k dispozici v celém světě instanci Azure AD a ne v pouze [cloudu Microsoft Azure v Německu](http://www.microsoft.de/cloud-deutschland) nebo [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Podporované scénáře

Plně podporuje následující scénáře:

- Přihlášení uživatele na všechny webové aplikace založené na prohlížeči.
- Uživatelská přihlášení do aplikace Office, které podporují [moderní ověřování](https://aka.ms/modernauthga): Office 2016 a Office 2013 _s_ moderní ověřování.
- Přihlášení uživatele pro klienty Outlook, kteří používají starší verze protokoly, například Exchange ActiveSync, SMTP, POP a IMAP.
- Přihlášení uživatele ke Skypu pro firmy, které podporují moderní ověřování, včetně online a hybridní topologie. Další informace o podporovaných topologiích [zde](https://technet.microsoft.com/library/mt803262.aspx).
- Azure AD domain spojí pro zařízení s Windows 10.
- Hesla aplikací pro službu Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře jsou _není_ podporovány:

- Přihlášení uživatele pro starší klientské aplikace Office, s výjimkou aplikace Outlook (viz **Podporované scénáře** výše): Office 2010 a Office 2013 _bez_ moderní ověřování. Organizace doporučujeme přepnout na moderní ověřování, pokud je to možné. Moderní ověřování umožňuje podporu předávací ověřování. Také pomáhá vám zabezpečit vaše uživatelské účty pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) funkce, jako je Azure Multi-Factor Authentication.
- Přístup ke sdílení kalendáře a volném čase v systému Exchange hybridní prostředí v Office 2010 pouze.
- Uživatelská přihlášení ke Skypu pro firmy klientské aplikace _bez_ moderní ověřování.
- Přihlášení uživatele k prostředí PowerShell, verze 1.0. Doporučujeme použít PowerShell verze 2.0.
- Zjišťování uživatelů s [úniku přihlašovacích údajů](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services vyžaduje synchronizaci hodnoty Hash hesla, aby byl povolen u klienta. Proto klientů, které používají předávací ověřování _pouze_ nefungují pro scénáře, které je třeba Azure AD Domain Services.
- Předávací ověřování není integrovaná s [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Apple Device Enrollment Program (Apple DEP) pomocí Pomocníka s nastavením iOS nepodporuje moderní ověřování. To nebude možné registrovat zařízení Apple DEP do Intune pro spravované domény pomocí předávacího ověřování. Zvažte použití [aplikaci portál společnosti](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) jako alternativu.

>[!IMPORTANT]
>Jako alternativní řešení pro nepodporované scénáře _pouze_, povolte synchronizaci hodnoty Hash hesla na [volitelné funkce](active-directory-aadconnect-get-started-custom.md#optional-features) stránku průvodce Azure AD Connect. Při přihlášení uživatele do aplikace uvedené v "nepodporované scénáře" části, jsou tyto konkrétní požadavky na přihlášení _není_ zpracovávaných předávací ověřování agentů a nebude proto zaznamenaná v [ Předávací ověřování protokoly](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Povolení synchronizaci hodnoty hash hesla nabízí možnost převzetí služeb při selhání ověřování Pokud dojde k narušení na místní infrastrukturu. Toto převzetí služeb při selhání z předávací ověřování synchronizaci hodnoty hash hesla služby Active Directory není automatické. Budete potřebovat přepnout metoda přihlašování ručně pomocí služby Azure AD Connect. Pokud server se službou Azure AD Connect přestane fungovat, budete vyžadovat pomoc od společnosti Microsoft Support vypnout předávací ověřování.

## <a name="next-steps"></a>Další postup
- [Rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md): zprovoznění s Azure AD předávací ověřování.
- [Inteligentní uzamčení](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení na vašeho klienta k ochraně uživatelské účty.
- [Podrobné technické informace](active-directory-aadconnect-pass-through-authentication-how-it-works.md): pochopit, jak funguje funkci předávací ověřování.
- [Nejčastější dotazy](active-directory-aadconnect-pass-through-authentication-faq.md): Vyhledejte odpovědi na nejčastější dotazy o funkci předávací ověřování.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Přečtěte si o řešení běžných problémů s funkcí předávací ověřování.
- [Podrobné informace zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): podrobný technický informace o funkci předávací ověřování.
- [Azure AD bezproblémové SSO](active-directory-aadconnect-sso.md): Další informace o této funkci vzájemně doplňují.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fóru Azure Active Directory do souboru žádosti o nové funkce.
