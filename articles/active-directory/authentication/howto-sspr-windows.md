---
title: Samoobslužné resetování hesla pro Windows – Azure Active Directory
description: Jak povolit samoobslužné resetování hesla pomocí zapomenutého hesla na přihlašovací obrazovce systému Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f0e5242d87bc68efd92a52619e8d48cff9ac87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370069"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Postup: Povolení resetování hesla z přihlašovací obrazovky systému Windows

U počítačů se systémem Windows 7, 8, 8.1 a 10 můžete uživatelům povolit resetování hesla na přihlašovací obrazovce systému Windows. Uživatelé již nemusí najít zařízení s webovým prohlížečem pro přístup k [portálu SSPR](https://aka.ms/sspr).

![Příklad Windows 7 a 10 přihlašovací obrazovky s odkazem SSPR zobrazeny](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Obecná omezení

- Resetování hesla není aktuálně podporováno ze vzdálené plochy nebo z rozšířených relací technologie Hyper-V.
- Je známo, že někteří poskytovatelé pověření třetích stran způsobují problémy s touto funkcí.
- Zakázání uac prostřednictvím změny [klíče registru EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) je známo, že způsobují problémy.
- Tato funkce nefunguje pro sítě s nasazeným ověřováním sítě 802.1x a možností "Provést bezprostředně před přihlášením uživatele". Pro sítě s nasazeným ověřováním v síti 802.1x se doporučuje použít k povolení této funkce ověřování počítače.
- Hybridní počítače s připojením služby Azure AD musí mít zorné pole připojení k síti k řadiči domény, aby bylo nutné používat nové heslo a aktualizovat přihlašovací údaje uložené v mezipaměti.
- Pokud používáte bitovou kopii, před spuštěním sysprep ujistěte se, že webová mezipaměť je vymazána pro vestavěnou správce před provedením kroku CopyProfile. Další informace o tomto kroku naleznete v článku podpory [Výkon nízký při použití vlastního výchozího profilu uživatele](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Následující nastavení je známo, že v rozporu s možností používání a resetování hesel na zařízeních s Windows 10
    - Pokud je zásada Ctrl+Alt+Del vyžadována zásadami ve verzích Windows 10 před v1809, **resetování hesla** nebude fungovat.
    - Pokud jsou oznámení zamykací obrazovky vypnutá, **resetování hesla** nebude fungovat.
    - HideFastUserSwitching je nastavenna na povoleno nebo 1
    - DontDisplayLastUserName je nastavena na povoleno nebo 1
    - NoLockScreen je nastavena na povoleno nebo 1
    - EnableLostMode je nastavena na zařízení
    - Program Explorer.exe je nahrazen vlastním prostředím
- Kombinace následujících specifických tří nastavení může způsobit, že tato funkce nebude fungovat.
    - Interaktivní přihlášení: Nevyžadují kombinaci kláves CTRL+ALT+DEL = Zakázáno
    - Zakázat LockScreenAppNotifications = 1 nebo Povoleno
    - IsContentDeliveryPolicyEnforced = 1 nebo True

## <a name="windows-10-password-reset"></a>Resetování hesla windows 10

### <a name="windows-10-prerequisites"></a>Předpoklady pro Windows 10

- Správce musí povolit samoobslužné resetování hesla Azure AD z webu Azure Portal.
- **Uživatelé se musí před použitím této funkce zaregistrovat pro sspr.**
- Požadavky na síťový proxy server
   - Zařízení s Windows 10 
       - Port 443 `passwordreset.microsoftonline.com` až a`ajax.aspnetcdn.com`
       - Zařízení s Windows 10 podporují jenom konfiguraci proxy serveru na úrovni počítače
- Spusťte alespoň Windows 10, verze z dubna 2018 Update (v1803) a zařízení musí být buď:
    - Připojené k Azure AD
    - k hybridní službě Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Povolení pro Windows 10 pomocí Intune

Nasazení změny konfigurace, která umožní resetování hesla z přihlašovací obrazovky, pomocí Intune je nejflexibilnější metodou. Intune vám umožňuje nasadit změnu konfigurace pro vámi zvolenou skupinu počítačů. Tato metoda vyžaduje registraci zařízení v Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Vytvoření zásad konfigurace zařízení v Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Intune**.
1. Vytvoření nového konfiguračního profilu zařízení na stránce**Profil** >  **konfigurace** > zařízení**Vytvořit profil**
   - Zadejte výstižný název profilu.
   - Volitelně zadejte výstižný popis profilu.
   - Jako platformu vyberte **Windows 10 a novější**.
   - Jako typ profilu vyberte **Vlastní**.
1. Nakonfigurujte **Nastavení**.
   - **Přidejte** následující Nastavení identifikátoru OMA-URI, které povolí odkaz na resetování hesla.
      - Zadejte výstižný název vysvětlující, co toto nastavení dělá.
      - Volitelně zadejte výstižný popis nastavení.
      - Identifikátor **OMA-URI** nastavte na `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      - **Datový typ** nastavte na **Integer**.
      - Jako **Hodnota** nastavte **1**.
      - Klikněte na tlačítko **OK**.
   - Klikněte na tlačítko **OK**.
1. Klikněte na **Vytvořit.**
1. Tuto zásadu lze přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace naleznete v článku [Přiřazení profilů uživatelů a zařízení v Aplikaci Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Povolení pro Windows 10 pomocí registru

1. Přihlášení k počítači s Windows pomocí přihlašovacích údajů pro správu
1. Spusťte program **regedit** jako správce.
1. Nastavte následující klíč registru:
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Poradce při potížích s resetováním hesla windows 10

Protokol auditu služby Azure AD bude obsahovat informace o IP adrese a typu klienta, kde došlo k resetování hesla.

![Příklad obnovení hesla systému Windows 7 v protokolu auditování služby Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Když uživatelé resetují své heslo z přihlašovací obrazovky zařízení s `defaultuser1` Windows 10, vytvoří se dočasný účet s nízkými oprávněními. Tento účet slouží k zabezpečení procesu resetování hesla. Samotný účet má náhodně generované heslo, nezobrazuje se pro přihlášení zařízení a bude automaticky odebrán poté, co uživatel resetuje své heslo. Může `defaultuser` existovat více profilů, ale lze je bezpečně ignorovat.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 a 8.1 resetování hesla

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 a 8.1 předpoklady

- Správce musí povolit samoobslužné resetování hesla Azure AD z webu Azure Portal.
- **Uživatelé se musí před použitím této funkce zaregistrovat pro sspr.**
- Požadavky na síťový proxy server
   - Zařízení se systémem Windows 7, 8 a 8.1
       - Port 443 až`passwordreset.microsoftonline.com`
- Opravensystém Windows 7 nebo Windows 8.1.
- Protokol TLS 1.2 byl povolen pomocí pokynů v [nastavení registru TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- Pokud je ve vašem počítači povoleno více než jednoho zprostředkovatele pověření třetí strany, uživatelé uvidí na přihlašovací obrazovce více než jeden uživatelský profil.

> [!WARNING]
> TLS 1.2 musí být povolena, a to nejen nastavit automatické vyjednávání

### <a name="install"></a>Instalace

1. Stáhněte si příslušnou instalační službu pro verzi systému Windows, kterou chcete povolit.
   - Software je k dispozici v centru pro stahování Microsoft na adrese[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Přihlaste se k počítači, do kterého chcete nainstalovat, a spusťte instalační program.
1. Po instalaci je velmi doporučeno restartování.
1. Po restartu, na přihlašovací obrazovce zvolte uživatele a klikněte na tlačítko "Zapomněli heslo?" zahájíte pracovní postup pro resetování hesla.
1. Dokončete pracovní postup podle kroků na obrazovce a obnovte heslo.

![Příklad Windows 7 kliknul na "Zapomněl i heslo?" Tok sspr](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Bezobslužná instalace

- Pro tichou instalaci použijte příkaz "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Pro tichý odinstalaci použijte příkaz "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Poradce při potížích s obnovením hesla pro Windows 7, 8 a 8.1

Události se budou protokolovány v počítači i ve službě Azure AD. Události Azure AD budou obsahovat informace o IP adrese a typu ClientType, kde došlo k resetování hesla.

![Příklad obnovení hesla systému Windows 7 v protokolu auditování služby Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Pokud je vyžadováno další protokolování, klíč registru v počítači lze změnit, aby bylo možné podrobné protokolování. Povolte podrobné protokolování pouze pro účely řešení potíží.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Chcete-li povolit podrobné `REG_DWORD: “EnableLogging”`protokolování, vytvořte a a nastavte jej na 1.
- Chcete-li zakázat podrobné `REG_DWORD: “EnableLogging”` protokolování, změňte na 0.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Nyní, když jste nakonfigurovali resetování hesla pro vaše zařízení se systémem Windows, jaké změny pro uživatele? Jak se dozví, že své heslo můžou resetovat na přihlašovací obrazovce?

![Příklad Windows 7 a 10 přihlašovací obrazovky s odkazem SSPR zobrazeny](./media/howto-sspr-windows/windows-reset-password.png)

Když se uživatelé pokusí přihlásit, zobrazí se jim odkaz **Resetovat heslo** nebo **Zapomenuté heslo,** který na přihlašovací obrazovce otevře samoobslužné možnosti resetování hesla. Tato funkce umožňuje uživatelům resetovat své heslo, aniž by museli použít jiné zařízení pro přístup k webovému prohlížeči.

Vaši uživatelé najdou pokyny k použití této funkce v tématu popisujícím [resetování hesla k pracovnímu nebo školnímu účtu](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="next-steps"></a>Další kroky

[Plánování metod ověřování, které umožní](concept-authentication-methods.md)

[Konfigurace Windows 10](https://docs.microsoft.com/windows/configuration/)
