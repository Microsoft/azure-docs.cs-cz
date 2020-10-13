---
title: Ověřování virtuálních počítačů s Windows – Azure
description: Metody ověřování pro virtuální počítač s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500296"
---
# <a name="supported-authentication-methods"></a>Podporované metody ověřování

V tomto článku vám poskytneme stručný přehled toho, jaké druhy ověřování můžete použít ve virtuálním počítači s Windows.

## <a name="session-host-authentication"></a>Ověřování hostitele relace

Virtuální počítač s Windows podporuje ověřování hostitele relací pomocí protokolu NTLM (NT LAN Manager) i protokolu Kerberos. Chcete-li však používat protokol Kerberos, musí klient získat lístky zabezpečení protokolu Kerberos ze služby služba KDC (Key Distribution Center) (KDC) spuštěné v řadiči domény. Aby bylo možné získat lístky, klient musí mít k řadiči domény přímý dohled. Pomocí podnikové sítě můžete získat přímý přehled o pohledu. Můžete také použít připojení VPN k podnikové síti.

Toto jsou aktuálně podporované metody přihlašování:

- Desktopový klient Windows
    - Uživatelské jméno a heslo
    - Pomocí
    - Windows Hello
- Klient Windows Storu
    - Uživatelské jméno a heslo
- Webový klient
    - Uživatelské jméno a heslo
- Android
    - Uživatelské jméno a heslo
- iOS
    - Uživatelské jméno a heslo
- macOS
    - Uživatelské jméno a heslo

>[!NOTE]
>Čipové karty a Windows Hello můžou k přihlášení použít jenom Kerberos. Přihlášení pomocí protokolu Kerberos vyžaduje pro řadič domény řadu kontrol.

## <a name="hybrid-identity"></a>Hybridní identita

Virtuální počítač s Windows podporuje [hybridní identity](../active-directory/hybrid/whatis-hybrid-identity.md) prostřednictvím Azure Active Directory (AD), včetně těch federovaných pomocí Active Directory Federation Services (AD FS) (ADFS). Vzhledem k tomu, že uživatelé musí být zjistitelní prostřednictvím Azure AD, virtuální plocha Windows nepodporuje samostatná nasazení služby Active Directory se službou AD FS.

## <a name="single-sign-on-sso"></a>Jednotné přihlašování (SSO)

Virtuální klient Windows v současnosti nepodporuje Active Directory Federation Services (AD FS) (ADFS) pro jednotné přihlašování (SSO).

Jediným způsobem, jak se vyhnout zobrazování výzvy k zadání přihlašovacích údajů pro hostitele relace, je uložit je v klientovi. Doporučujeme vám to udělat jenom se zabezpečenými zařízeními, abyste ostatním uživatelům zabránili v přístupu k prostředkům.

## <a name="next-steps"></a>Další kroky

Zajímá o dalších způsobech, jak zajistit zabezpečení nasazení? Projděte si [osvědčené postupy zabezpečení](security-guide.md).
