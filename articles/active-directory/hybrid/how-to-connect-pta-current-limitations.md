---
title: 'Azure AD Connect: Předávací ověřování – aktuální omezení | Dokumenty společnosti Microsoft'
description: Tento článek popisuje aktuální omezení předávacího ověřování služby Azure Active Directory (Azure AD).
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc67d46b08bf5765c59806b45edd82f38720cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347730"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Předávací ověřování služby Azure Active Directory: Aktuální omezení

>[!IMPORTANT]
>Předávací ověřování Azure Active Directory (Azure AD) je bezplatná funkce a k jeho použití nepotřebujete žádné placené edice Azure AD. Předávací ověřování je dostupné jenom ve světové instanci Azure AD a ne v [cloudu Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) nebo [v cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Podporované scénáře

Jsou podporovány následující scénáře:

- Přihlášení uživatelů k aplikacím založeným na webovém prohlížeči.
- Přihlášení uživatelů ke klientům aplikace Outlook pomocí starších protokolů, jako jsou Exchange ActiveSync, EAS, SMTP, POP a IMAP.
- Přihlášení uživatelů ke starším klientským aplikacím Office a aplikacím Office, které podporují [moderní ověřování](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): verze Office 2013 a 2016.
- Uživatel přihlášení ke starším protokolům aplikací, jako je například PowerShell verze 1.0 a další.
- Azure AD se připojí pro zařízení s Windows 10.
- Hesla aplikací pro vícefaktorové ověřování.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře _nejsou_ podporovány:

- Detekce uživatelů s [uniklými přihlašovacími údaji](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Služba Azure AD Domain Services potřebuje synchronizaci hash hesel, která má být povolena v tenantovi. Proto klienti, kteří používají předávací ověřování _nefungují_ pro scénáře, které potřebují služby Domény Azure AD.
- Předávací ověřování není integrované se službou [Azure AD Connect Health](whatis-hybrid-identity-health.md).

> [!IMPORTANT]
> Jako řešení _pouze_ pro nepodporované scénáře (s výjimkou integrace Azure AD Connect Health) povolte synchronizaci hash hesel na stránce [Volitelné funkce](how-to-connect-install-custom.md#optional-features) v průvodci Azure AD Connect.
> 
> [!NOTE]
> Povolení synchronizace hash hesel vám dává možnost ověřování převzetí služeb při selhání, pokud je narušena vaše místní infrastruktura. Toto převzetí služeb při selhání z předávacího ověřování na synchronizaci hash hesla není automatické. Budete muset přepnout metodu přihlášení ručně pomocí Azure AD Connect. Pokud server se systémem Azure AD Connect přejde dolů, budete potřebovat pomoc od podpory Microsoftu k vypnutí předávacího ověřování.

## <a name="next-steps"></a>Další kroky
- [Rychlý start:](how-to-connect-pta-quick-start.md)Zprovoznění pomocí předávacího ověřování Azure AD.
- [Migrace ze služby AD FS do předávacího ověřování](https://aka.ms/ADFSTOPTADPDownload) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Přečtěte si, jak nakonfigurovat funkci Inteligentní uzamčení ve vašem tenantovi za účelem ochrany uživatelských účtů.
- [Technické podrobné informace](how-to-connect-pta-how-it-works.md): Zjistěte, jak funguje funkce předávacího ověřování.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Najděte odpovědi na nejčastější dotazy týkající se funkce Předávací ověřování.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): Získejte podrobné technické informace o funkci předávacího ověřování.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md)služby : Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory můžete zasít nové požadavky na funkce.
