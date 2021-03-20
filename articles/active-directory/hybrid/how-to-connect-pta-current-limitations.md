---
title: 'Azure AD Connect: předávací ověřování – aktuální omezení | Microsoft Docs'
description: Tento článek popisuje aktuální omezení předávacího ověřování služby Azure Active Directory (Azure AD).
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280192"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory předávací ověřování: aktuální omezení

>[!IMPORTANT]
>Předávací ověřování pro Azure Active Directory (Azure AD) je bezplatná funkce, takže nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat. Předávací ověřování je dostupné jenom v rámci světové instance Azure AD, a ne v [cloudu Microsoft Azure (Německo)](https://www.microsoft.de/cloud-deutschland) nebo [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Podporované scénáře

Podporovány jsou následující scénáře:

- Přihlášení uživatelů do aplikací založených na webovém prohlížeči.
- Přihlášení uživatelů do klientů Outlooku pomocí starších protokolů, jako jsou Exchange ActiveSync, EAS, SMTP, POP a IMAP.
- Přihlášení uživatelů ke starším klientským aplikacím Office a aplikacím Office, které podporují [moderní ověřování](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): verze Office 2013 a 2016.
- Přihlášení uživatelů ke starším aplikacím protokolu, jako je například PowerShell verze 1,0 a další.
- Spojení Azure AD pro zařízení s Windows 10.
- Hesla aplikací pro Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře _nejsou podporovány:_

- Rozpoznávání uživatelů s [nevrácenými přihlašovacími údaji](../identity-protection/overview-identity-protection.md).
- Azure AD Domain Services vyžaduje, aby byla v tenantovi povolená synchronizace hodnot hash hesel. Proto klienti, kteří používají předávací ověřování, nefungují _jenom_ u scénářů, které potřebují Azure AD Domain Services.
- Předávací ověřování není integrováno s [Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Alternativním řešením pro nepodporované scénáře _(_ s výjimkou Azure AD Connect Health integrace) je povolení synchronizace hodnot hash hesel na stránce [volitelné funkce](how-to-connect-install-custom.md#optional-features) v průvodci Azure AD Connect.
> 
> [!NOTE]
> Povolení synchronizace hodnot hash hesel vám umožní v případě narušení vaší místní infrastruktury možnost ověřování při selhání. Toto převzetí služeb při selhání z předávacího ověřování na synchronizaci hodnoty hash hesla není automatické. Metodu přihlašování budete muset ručně přepínat pomocí Azure AD Connect. Pokud server se spuštěným Azure AD Connect přestane fungovat, budete od podpora Microsoftu potřebovat k vypnutí předávacího ověřování podporu.

## <a name="next-steps"></a>Další kroky
- [Rychlý Start](how-to-connect-pta-quick-start.md): zprovoznění předávacího ověřování Azure AD v provozu.
- [Migrace z AD FS na předávací ověřování](https://aka.ms/ADFSTOPTADPDownload) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Naučte se konfigurovat funkci inteligentního uzamknutí pro vašeho tenanta pro ochranu uživatelských účtů.
- [Technický hluboký podrobně](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Přečtěte si odpovědi na nejčastější dotazy týkající se funkce předávacího ověřování.
- [Řešení potíží](tshoot-connect-pass-through-authentication.md): Naučte se řešit běžné problémy s funkcí předávacího ověřování.
- [Hloubkové podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Přečtěte si další informace o této doplňkové funkci.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte Fórum Azure Active Directory k započetí nových požadavků na funkce.
