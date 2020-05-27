---
title: Samoobslužné resetování hesla pro Windows – Azure Active Directory
description: Jak povolit samoobslužné resetování hesla pomocí zapomenutého hesla na přihlašovací obrazovce Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d1ffec6a849cb97a6151717c3e30dc362b1403
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826600"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Postupy: povolení resetování hesla z přihlašovací obrazovky Windows

V počítačích se systémem Windows 7, 8, 8,1 a 10 můžete uživatelům povolit resetování hesla na přihlašovací obrazovce systému Windows. Uživatelé už nemusí najít zařízení s webovým prohlížečem pro přístup k [portálu SSPR](https://aka.ms/sspr).

![Příklady přihlašovacích obrazovek Windows 7 a 10 se zobrazeným odkazem na SSPR](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Obecná omezení

- Resetování hesla není v současné době podporováno ze vzdálené plochy nebo z rozšířených relací technologie Hyper-V.
- Je známo, že někteří poskytovatelé přihlašovacích údajů třetích stran způsobují problémy s touto funkcí.
- Vypnutí řízení uživatelských účtů prostřednictvím změny [klíče registru EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) je známo, že způsobují problémy.
- Tato funkce nefunguje pro sítě s nasazeným ověřováním sítě 802.1 x a možnost provést těsně před přihlášením uživatele. Pro povolení této funkce doporučujeme, aby se sítě s nasazeným ověřováním pomocí sítě 802.1 x používaly ověřování počítače.
- Počítače připojené k hybridní službě Azure AD musí mít na řadiči domény linku připojení k síti, aby bylo možné použít nové heslo a aktualizovat přihlašovací údaje uložené v mezipaměti. To znamená, že zařízení musí být buď v interní síti organizace, nebo na síti VPN se síťovým přístupem k místnímu řadiči domény. 
- Pokud použijete image, před provedením kroku CopyProfile zajistěte, aby byla mezipaměť webu pro předdefinovaný správce vymazána. Další informace o tomto kroku najdete v článku o [výkonu nekvalitního výkonu při používání vlastního výchozího uživatelského profilu](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- U následujících nastavení je známo, že se bude rušit možnost používat a resetovat hesla na zařízeních s Windows 10.
    - Pokud zásady ve verzích Windows 10 před v1809 vyžadují CTRL + ALT + DEL, **resetování hesla** nebude fungovat.
    - Pokud jsou oznámení na zamykací obrazovce vypnutá, **resetování hesla** nebude fungovat.
    - HideFastUserSwitching je nastavené na povoleno nebo 1.
    - DontDisplayLastUserName je nastavené na povoleno nebo 1.
    - NoLockScreen je nastavené na povoleno nebo 1.
    - EnableLostMode je nastavené na zařízení.
    - Explorer. exe se nahradí vlastním prostředím.
- Tato funkce může způsobit, že tato funkce nefunguje v kombinaci následujících tří nastavení.
    - Interaktivní přihlašování: Nevyžadovat CTRL + ALT + DEL = zakázáno
    - DisableLockScreenAppNotifications = 1 nebo povoleno
    - SKU Windows není Home nebo Professional Edition.

## <a name="windows-10-password-reset"></a>Resetování hesla Windows 10

### <a name="windows-10-prerequisites"></a>Požadavky Windows 10

- Správce musí povolit samoobslužné resetování hesla služby Azure AD z Azure Portal.
- **Před použitím této funkce se uživatelé musí zaregistrovat pro SSPR.**
- Požadavky na síťový proxy server
   - Zařízení s Windows 10 
       - Port 443 až `passwordreset.microsoftonline.com` a`ajax.aspnetcdn.com`
       - Zařízení s Windows 10 podporují jenom konfiguraci proxy serveru na úrovni počítače.
- Spusťte aspoň Windows 10, verze z dubna 2018 Update (v1803) a zařízení musí být buď:
    - Připojené k Azure AD
    - k hybridní službě Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Povolení pro Windows 10 pomocí Intune

Nasazení změny konfigurace, která umožní resetování hesla z přihlašovací obrazovky, pomocí Intune je nejflexibilnější metodou. Intune vám umožňuje nasadit změnu konfigurace pro vámi zvolenou skupinu počítačů. Tato metoda vyžaduje registraci zařízení v Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Vytvoření zásad konfigurace zařízení v Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Intune**.
1. Vytvoření nového profilu konfigurace zařízení kliknutím na profily **Konfigurace zařízení**  >  **Profiles**  >  **vytvořit profil**
   - Zadejte výstižný název profilu.
   - Volitelně zadejte výstižný popis profilu.
   - Jako platformu vyberte **Windows 10 a novější**.
   - Jako typ profilu vyberte **Vlastní**.
1. Konfigurovat **Nastavení**
   - **Přidejte** následující Nastavení identifikátoru OMA-URI, které povolí odkaz na resetování hesla.
      - Zadejte výstižný název vysvětlující, co toto nastavení dělá.
      - Volitelně zadejte výstižný popis nastavení.
      - Identifikátor **OMA-URI** nastavte na `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      - **Datový typ** nastavte na **Integer**.
      - Jako **Hodnota** nastavte **1**.
      - Klikněte na tlačítko **OK**.
   - Klikněte na tlačítko **OK**.
1. Klikněte na **vytvořit** .
1. Tato zásada se dá přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace najdete v článku [přiřazení profilů uživatelů a zařízení v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Povolení pro Windows 10 pomocí registru

1. Přihlaste se k počítači s Windows pomocí přihlašovacích údajů správce.
1. Spusťte program **regedit** jako správce.
1. Nastavte následující klíč registru:
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Řešení potíží s resetováním hesla Windows 10

Protokol auditu služby Azure AD bude obsahovat informace o IP adrese a typu klienta, kde došlo k resetování hesla.

![Příklad resetování hesla systému Windows 7 v protokolu auditu Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Když uživatelé resetují heslo na přihlašovací obrazovce zařízení s Windows 10, vytvoří se dočasný účet s nízkou úrovní oprávnění s názvem `defaultuser1` . Tento účet se používá k zabezpečení procesu resetování hesla. Samotný účet má náhodně generované heslo, nezobrazuje se pro přihlášení k zařízení a automaticky se odebere po resetování hesla uživatelem. `defaultuser`Může existovat více profilů, ale lze je bezpečně ignorovat.

## <a name="windows-7-8-and-81-password-reset"></a>Resetování hesla Windows 7, 8 a 8,1

### <a name="windows-7-8-and-81-prerequisites"></a>Požadavky pro Windows 7, 8 a 8,1

- Správce musí povolit samoobslužné resetování hesla služby Azure AD z Azure Portal.
- **Před použitím této funkce se uživatelé musí zaregistrovat pro SSPR.**
- Požadavky na síťový proxy server
   - Zařízení se systémem Windows 7, 8 a 8,1
       - Port 443 až`passwordreset.microsoftonline.com`
- Opraven operační systém Windows 7 nebo Windows 8.1.
- Protokol TLS 1,2 povolený pomocí pokynů uvedených v [nastavení registru TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Pokud je na vašem počítači povolený víc než jeden poskytovatel přihlašovacích údajů třetích stran, zobrazí se uživatelům na přihlašovací obrazovce víc než jeden profil uživatele.

> [!WARNING]
> Je nutné povolit TLS 1,2, není pouze nastaveno na automatické vyjednávání.

### <a name="install"></a>Instalace

1. Stáhněte si odpovídající instalační program pro verzi systému Windows, kterou chcete povolit.
   - Software je k dispozici na webu Microsoft Download Center na adrese[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Přihlaste se k počítači, do kterého chcete nainstalovat, a spusťte instalační program.
1. Po instalaci se důrazně doporučuje restartování počítače.
1. Po restartování počítače na přihlašovací obrazovce vyberte uživatele a klikněte na tlačítko zapomenuté heslo. Pro zahájení pracovního postupu pro resetování hesla.
1. Dokončete pracovní postup podle pokynů na obrazovce a resetujte heslo.

![Příklad Windows 7 klikl na "Zapomenuté heslo?" Tok SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Bezobslužná instalace

- Pro tichou instalaci použijte příkaz "msiexec/i SsprWindowsLogon. PROD. msi/Qn"
- Pro tichou odinstalaci použijte příkaz "msiexec/x SsprWindowsLogon. PROD. msi/Qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Řešení potíží s resetováním hesla pro Windows 7, 8 a 8,1

Události se budou protokolovat v počítači i ve službě Azure AD. Události Azure AD budou zahrnovat informace o IP adrese a ClientType, kde došlo k resetování hesla.

![Příklad resetování hesla systému Windows 7 v protokolu auditu Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Pokud se vyžaduje další protokolování, můžete změnit klíč registru v počítači tak, aby se povolilo podrobné protokolování. Povolí podrobné protokolování jenom pro účely řešení potíží.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Chcete-li povolit podrobné protokolování, vytvořte `REG_DWORD: "EnableLogging"` a nastavte jej na hodnotu 1.
- Chcete-li zakázat podrobné protokolování, změňte `REG_DWORD: "EnableLogging"` hodnotu na 0.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Teď, když jste nakonfigurovali resetování hesla pro zařízení s Windows, které změny pro uživatele? Jak se dozví, že své heslo můžou resetovat na přihlašovací obrazovce?

![Příklady přihlašovacích obrazovek Windows 7 a 10 se zobrazeným odkazem na SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Když se uživatel pokusí přihlásit, zobrazí se jim odkaz pro **resetování** hesla nebo **zapomenuté heslo** , který otevře možnosti samoobslužného resetování hesla na přihlašovací obrazovce. Tato funkce umožňuje uživatelům resetovat své heslo, aniž by museli použít jiné zařízení pro přístup k webovému prohlížeči.

Vaši uživatelé najdou pokyny k použití této funkce v tématu popisujícím [resetování hesla k pracovnímu nebo školnímu účtu](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="next-steps"></a>Další kroky

[Plánování metod ověřování pro povolení](concept-authentication-methods.md)

[Konfigurace Windows 10](https://docs.microsoft.com/windows/configuration/)
