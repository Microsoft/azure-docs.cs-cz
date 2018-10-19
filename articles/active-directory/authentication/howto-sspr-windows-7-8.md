---
title: Azure AD samoobslužné resetování hesla Windows 7 a 8.1
description: Povolení samoobslužného resetování hesla pomocí zapomněli jste heslo na Windows 7 nebo 8.1 přihlašovací obrazovka
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 1465ba23f6fed85f0d4164f8e3fdc8c38be43ad3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427722"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Postupy: povolení heslo resetovat z Windows 7, 8 a 8.1

Jako správce máte povolené samoobslužné resetování hesla (SSPR), ale uživatelé zachovat volání technickou podporu k resetování hesla, protože do okna prohlížeče, nelze získat přístup k [portál samoobslužné resetování HESLA](https://aka.ms/sspr). U počítačů s Windows 10, můžete povolit na "Resetovat heslo" odkaz na přihlašovací obrazovce pomocí tohoto kurzu [hesla Azure AD z přihlašovací obrazovky resetování](tutorial-sspr-windows.md), následující pokyny vám pomůže s Windows 7, 8 a 8.1 uživatelům umožnit resetování hesla pomocí samoobslužné resetování HESLA na přihlašovací obrazovce Windows.

Na rozdíl od počítačů s Windows 10, Windows 7, 8 a 8.1 počítače tak nebudou mít připojené k doméně Azure AD nebo požadavek připojeného k doméně služby Active Directory pro heslo resetovat.

![Například Windows 7 přihlašovací obrazovka s "Zapomněli jste heslo?" Zobrazí odkaz](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Požadavky

* Samoobslužné resetování hesla Azure AD musí být povolené.
* Verzi Windows 7 nebo Windows 8.1 operačního systému.
* Součástí TLS 1.2 povoleno podle pokynů k [zabezpečení TLS (Transport Layer), nastavení registru](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> Musí být povolený protokol TLS 1.2, ne jenom nastavený na auto negotiate.

## <a name="install"></a>Instalace

1. Stáhněte si odpovídající instalační program pro verzi Windows, které chcete povolit.

   1. Software je k dispozici na webu Microsoft download center na [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Přihlaste se k počítači, ve kterém chcete nainstalovat a spusťte instalační program.
1. Po dokončení instalace je důrazně doporučujeme restartovat počítač.
1. Po restartování počítače na přihlašovací obrazovce zvolte uživatele a klikněte na tlačítko "Zapomněli jste heslo?" k zahájení heslo pracovního postupu pro obnovení.
1. Dokončení pracovního postupu pokynů na obrazovce pro resetování hesla.

![Například Windows 7 kliknutí na "Zapomněli jste heslo?" resetování hesla pomocí samoobslužné služby flow](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Bezobslužná instalace

* Pro tichou instalaci použijte příkaz "msiexec /i SsprWindowsLogon.PROD.msi /qn"
* Pro tichou odinstalaci použijte příkaz "msiexec /x SsprWindowsLogon.PROD.msi /qn"

## <a name="caveats"></a>Upozornění

Je třeba zaregistrovat pro samoobslužné resetování HESLA, než bude moct použít odkaz "Zapomněli jste heslo".

![Další bezpečnostní údaje, je potřeba resetovat heslo](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Pomocí aplikace Microsoft Authenticator pro oznámení a kódy k resetování hesla nefunguje, pokud je tato počáteční verze. Uživatelé musí mít alternativní metody zaregistrované, které splňují požadavky vašich zásad.

## <a name="troubleshooting"></a>Řešení potíží

Na počítači a ve službě Azure AD, budou protokolovány události.

Azure AD události bude obsahovat informace o IP adresu a typu klienta, kde došlo k resetování hesla.

![Příklad Windows 7 přihlašovací obrazovky heslo resetovat do protokolu auditu Azure AD](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Pokud se vyžaduje dodatečné protokolování, klíče registru na počítači můžete změnit na zapnout podrobné protokolování. Zapnout podrobné protokolování pro řešení potíží s pouze pro účely.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Pokud chcete zapnout podrobné protokolování, vytvořte REG_DWORD: "EnableLogging" a nastavte ho na hodnotu 1.
* Vypnout podrobné protokolování, změňte REG_DWORD "EnableLogging" na hodnotu 0.

Pokud vaše počítače Windows 7, 8 a 8.1 jsou za proxy server nebo bránu firewall, má povolený provoz HTTPS (443) do passwordreset.microsoftonline.com.

## <a name="next-steps"></a>Další postup

[Povolit uživatelům resetování hesla na přihlašovací obrazovce Windows 10](tutorial-sspr-windows.md)