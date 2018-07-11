---
title: 'Služby Azure AD Connect: Předávací ověřování - aktuální omezení | Dokumentace Microsoftu'
description: Tento článek popisuje aktuální omezení předávací ověřování služby Azure Active Directory (Azure AD)
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
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 34b83c54e31ed73af3f776a6add8f218dda35cf7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918916"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure předávací ověřování služby Active Directory: Aktuální omezení

>[!IMPORTANT]
>Předávací ověřování Azure Active Directory (Azure AD) je bezplatná funkce a není nutné žádné placené edice Azure AD, aby ho použít. Předávací ověřování je k dispozici v celosvětové instance služby Azure AD a ne v pouze [cloudu Microsoft Azure Germany](http://www.microsoft.de/cloud-deutschland) nebo [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Podporované scénáře

Se plně podporují následující scénáře:

- Přihlášení uživatelů pro všechny webové aplikace založené na prohlížeči.
- Přihlášení uživatele do aplikací Office, které podporují [moderní ověřování](https://aka.ms/modernauthga): Office 2016 a Office 2013 _s_ moderní ověřování.
- Přihlášení uživatelů do Outlookové klienty pomocí starší verze protokoly, například Exchange ActiveSync, SMTP, POP a IMAP.
- Přihlášení uživatele ke Skypu pro firmy, které podporují moderní ověřování, včetně online a hybridní topologie. Další informace o podporovaných topologiích [tady](https://technet.microsoft.com/library/mt803262.aspx).
- Doména služby Azure AD připojí pro zařízení s Windows 10.
- Hesla aplikací pro ověřování službou Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře jsou _není_ podporovány:

- Přihlášení uživatele do starší klientské aplikace Office, s výjimkou aplikace Outlook (naleznete v tématu **Podporované scénáře** výše): Office 2010 a Office 2013 _bez_ moderní ověřování. Organizace se doporučuje přepnout na moderní ověřování, pokud je to možné. Moderní ověřování umožňuje podporu předávací ověřování. Také pomáhá vám zabezpečit vaše uživatelské účty pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) funkce, jako je Azure Multi-Factor Authentication.
- Přístup k sdílení kalendáře a volném informací v Exchangi hybridní prostředí s Office 2010 pouze.
- Uživatelská přihlášení ke Skypu pro firmy klientské aplikace _bez_ moderní ověřování.
- Přihlašování uživatelů k prostředí PowerShell verze 1.0. Doporučujeme používat PowerShell verze 2.0.
- Zjišťování uživatelů s [úniku přihlašovacích údajů](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services musí být na tenantovi povolena synchronizaci hodnot Hash hesel. Proto tenanty, které používají předávací ověřování _pouze_ nefungují pro scénáře, které je třeba Azure AD Domain Services.
- Předávací ověřování není integrován s [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Programu registrace zařízení Apple (Apple DEP) pomocí Pomocníka pro nastavení iOS nepodporuje moderní ověřování. Selže, registrace zařízení Apple s programem DEP do Intune pro spravované domény pomocí předávacího ověřování. Zvažte použití [aplikaci portál společnosti](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) jako alternativu.

>[!IMPORTANT]
>Jako alternativní řešení pro nepodporované scénáře _pouze_, povolte synchronizaci hodnot Hash hesel na [volitelné funkce](active-directory-aadconnect-get-started-custom.md#optional-features) stránky v průvodci službou Azure AD Connect. Při přihlášení uživatelů do aplikací uvedených v "nepodporované scénáře", jsou tyto konkrétní žádostí o přihlášení _není_ zpracování pomocí agentů předávací ověřování a proto je nebudou zaznamenána v [ Předávací ověřování protokoly](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Povolení synchronizace hodnot hash hesel nabízí možnost převzetí služeb při selhání ověřování je-li přerušit vaši místní infrastrukturu. Toto převzetí služeb při selhání z předávací ověřování na synchronizaci hodnot hash hesel služby Active Directory nebude automatická. Budete potřebovat přepnout metodu přihlašování ručně pomocí služby Azure AD Connect. Pokud server se službou Azure AD Connect ocitne mimo provoz, bude vyžadovat pomoc od společnosti Microsoft Support vypnout předávací ověřování.

## <a name="next-steps"></a>Další postup
- [Rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md): uvedení do provozu pomocí předávacího ověřování Azure AD.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení ve svém tenantovi k ochraně uživatelské účty.
- [Podrobné technické informace](active-directory-aadconnect-pass-through-authentication-how-it-works.md): pochopit, jak funguje funkce předávací ověřování.
- [Nejčastější dotazy k](active-directory-aadconnect-pass-through-authentication-faq.md): Najděte odpovědi na nejčastější dotazy týkající se funkcí předávací ověřování.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Podrobné informace o zabezpečení](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): získáte podrobné technické informace o funkci předávací ověřování.
- [Azure AD bezproblémového jednotného přihlašování k](active-directory-aadconnect-sso.md): Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fórum Azure Active Directory do souboru žádostí o nové funkce.
