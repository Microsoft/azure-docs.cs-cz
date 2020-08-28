---
title: Ověřování virtuálních počítačů s Windows – Azure
description: Metody ověřování pro virtuální počítač s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038545"
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

## <a name="single-sign-on-sso"></a>Jednotné přihlašování (SSO)

Virtuální klient Windows v současnosti nepodporuje Active Directory Federation Services (AD FS) (ADFS) pro ověřování nebo jednotné přihlašování.

Jediným způsobem, jak se vyhnout zobrazování výzvy k zadání přihlašovacích údajů pro hostitele relace, je uložit je v klientovi. Doporučujeme vám to udělat jenom se zabezpečenými zařízeními, abyste ostatním uživatelům zabránili v přístupu k prostředkům.

## <a name="next-steps"></a>Další kroky

Zajímá o dalších způsobech, jak zajistit zabezpečení nasazení? Projděte si [osvědčené postupy zabezpečení](security-guide.md).
