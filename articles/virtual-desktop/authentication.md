---
title: Ověřování virtuálních počítačů s Windows – Azure
description: Metody ověřování pro virtuální počítač s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 32e113bf02a2cbd21b70805396c212a1f9467382
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448232"
---
# <a name="supported-authentication-methods"></a>Podporované metody ověřování

V tomto článku vám poskytneme stručný přehled toho, jaké druhy ověřování můžete použít ve virtuálním počítači s Windows.

## <a name="session-host-authentication"></a>Ověřování hostitele relace

Virtuální počítač s Windows podporuje ověřování hostitele relací pomocí protokolu NTLM (NT LAN Manager) i protokolu Kerberos. Chcete-li však používat protokol Kerberos, musí klient získat lístky zabezpečení protokolu Kerberos ze služby služba KDC (Key Distribution Center) (KDC) spuštěné v řadiči domény. Aby bylo možné získat lístky, klient musí mít k řadiči domény přímý dohled. Pomocí podnikové sítě můžete získat přímý přehled o pohledu. Můžete také použít připojení VPN k podnikové síti nebo nastavit [proxy server služby KDC](key-distribution-center-proxy.md).

Toto jsou aktuálně podporované metody přihlašování:

- Desktopový klient Windows
    - Uživatelské jméno a heslo
    - Pomocí
    - Windows Hello pro firmy (jenom důvěryhodný certifikát)
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
>Čipové karty a Windows Hello pro firmy můžou k přihlášení používat jenom Kerberos. Přihlášení pomocí protokolu Kerberos vyžaduje dohled nad řadičem domény nebo [proxy serverem služby KDC](key-distribution-center-proxy.md).

## <a name="hybrid-identity"></a>Hybridní identita

Virtuální počítač s Windows podporuje [hybridní identity](../active-directory/hybrid/whatis-hybrid-identity.md) prostřednictvím Azure Active Directory (AD), včetně těch federovaných pomocí Active Directory Federation Services (AD FS) (ADFS). Vzhledem k tomu, že uživatelé musí být zjistitelní prostřednictvím Azure AD, virtuální plocha Windows nepodporuje samostatná nasazení služby Active Directory se službou AD FS.

## <a name="single-sign-on-sso"></a>Jednotné přihlašování (SSO)

Virtuální klient Windows v současnosti nepodporuje Active Directory Federation Services (AD FS) (ADFS) pro jednotné přihlašování (SSO).

Jediným způsobem, jak se vyhnout zobrazování výzvy k zadání přihlašovacích údajů pro hostitele relace, je uložit je v klientovi. Doporučujeme vám to udělat jenom se zabezpečenými zařízeními, abyste ostatním uživatelům zabránili v přístupu k prostředkům.

## <a name="next-steps"></a>Další kroky

Zajímá o dalších způsobech, jak zajistit zabezpečení nasazení? Projděte si [osvědčené postupy zabezpečení](security-guide.md).
