---
title: Samoobslužné resetování hesla pro zařízení s Windows – Azure Active Directory
description: Přečtěte si, jak povolit samoobslužné resetování hesla Azure Active Directory na přihlašovací obrazovce pro Windows.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491381"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Povolení samoobslužného resetování hesla Azure Active Directory na přihlašovací obrazovce Windows

Samoobslužné resetování hesla (SSPR) poskytuje uživatelům ve službě Azure Active Directory (Azure AD) možnost změnit nebo resetovat svoje heslo bez zásahu správce nebo helpdesku. Uživatelé obvykle otevírají webový prohlížeč na jiném zařízení pro přístup k [portálu SSPR](https://aka.ms/sspr). Pro zlepšení prostředí v počítačích se systémem Windows 7, 8, 8,1 a 10 můžete uživatelům povolit resetování hesla na přihlašovací obrazovce Windows.

![Příklady přihlašovacích obrazovek Windows 7 a 10 se zobrazeným odkazem na SSPR](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> V tomto kurzu se dozvíte správce, jak povolit SSPR pro zařízení s Windows v podniku.
>
> Pokud váš IT tým nepovolil možnost používat SSPR ze zařízení s Windows nebo máte problémy během přihlašování, obraťte se na helpdesk, kde najdete další pomoc.

## <a name="general-limitations"></a>Obecná omezení

Následující omezení platí pro použití SSPR z přihlašovací obrazovky Windows:

- Resetování hesla není v současné době podporováno ze vzdálené plochy nebo z rozšířených relací technologie Hyper-V.
- Je známo, že někteří poskytovatelé přihlašovacích údajů třetích stran způsobují problémy s touto funkcí.
- Vypnutí řízení uživatelských účtů prostřednictvím změny [klíče registru EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) je známo, že způsobují problémy.
- Tato funkce nefunguje pro sítě s nasazeným ověřováním sítě 802.1 x a možnost provést těsně před přihlášením uživatele. Pro povolení této funkce pro sítě s nasazeným ověřováním pomocí sítě 802.1 x doporučujeme použít ověřování počítače.
- Počítače připojené k hybridní službě Azure AD musí mít na řadiči domény linku připojení k síti, aby bylo možné použít nové heslo a aktualizovat přihlašovací údaje uložené v mezipaměti. To znamená, že zařízení musí být buď v interní síti organizace, nebo na síti VPN se síťovým přístupem k místnímu řadiči domény.
- Pokud použijete image, před provedením kroku CopyProfile zajistěte, aby byla mezipaměť webu pro předdefinovaný správce vymazána. Další informace o tomto kroku najdete v článku o [výkonu nekvalitního výkonu při používání vlastního výchozího uživatelského profilu](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- U následujících nastavení je známo, že se bude rušit možnost používat a resetovat hesla na zařízeních s Windows 10:
    - Pokud zásady ve Windows 10 vyžadují CTRL + ALT + DEL, **resetování hesla** nebude fungovat.
    - Pokud jsou oznámení na zamykací obrazovce vypnutá, **resetování hesla** nebude fungovat.
    - *HideFastUserSwitching* je nastavené na povoleno nebo 1.
    - *DontDisplayLastUserName* je nastavené na povoleno nebo 1.
    - *NoLockScreen* je nastavené na povoleno nebo 1.
    - *EnableLostMode* je nastavené na zařízení.
    - Explorer.exe se nahradí vlastním prostředím.
- Tato funkce může způsobit, že tato funkce nefunguje v kombinaci následujících tří nastavení.
    - Interaktivní přihlašování: Nevyžadovat CTRL + ALT + DEL = zakázáno
    - *DisableLockScreenAppNotifications* = 1 nebo povoleno
    - SKU Windows není Home nebo Professional Edition.

> [!NOTE]
> Tato omezení platí také pro resetování PIN kódu ve Windows Hello pro firmy na zamykací obrazovce zařízení.
>

## <a name="windows-10-password-reset"></a>Resetování hesla Windows 10

Pokud chcete na přihlašovací obrazovce nakonfigurovat zařízení s Windows 10 pro SSPR, přečtěte si následující požadavky a kroky konfigurace.

### <a name="windows-10-prerequisites"></a>Požadavky Windows 10

- Správce [musí povolit samoobslužné resetování hesla služby Azure AD z Azure Portal](tutorial-enable-sspr.md).
- Před použitím této funkce se uživatelé musí zaregistrovat pro SSPR. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Není jedinečné, aby bylo možné používat SSPR z přihlašovací obrazovky Windows, všichni uživatelé musí před resetováním hesla zadat kontaktní údaje pro ověření.
- Požadavky na síťový proxy server:
    - Port 443 až `passwordreset.microsoftonline.com` a `ajax.aspnetcdn.com`
    - Zařízení s Windows 10 podporují jenom konfiguraci proxy serveru na úrovni počítače.
- Spusťte aspoň Windows 10, verze z dubna 2018 Update (v1803) a zařízení musí být buď:
    - Připojené k Azure AD
    - k hybridní službě Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Povolení pro Windows 10 pomocí Intune

Nasazení změny konfigurace, aby se povolilo SSPR z přihlašovací obrazovky pomocí Intune, je nejpružnější metoda. Intune vám umožňuje nasadit změnu konfigurace pro vámi zvolenou skupinu počítačů. Tato metoda vyžaduje registraci zařízení v Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Vytvoření zásad konfigurace zařízení v Intune

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **Intune**.
1. Vytvořte nový profil konfigurace zařízení tak, že přejdete na **konfigurační**  >  **profily** zařízení a pak vyberete **+ vytvořit profil** .
   - Pro **platformu** vyberte *Windows 10 a novější* .
   - Jako **typ profilu** vyberte *vlastní* .
1. Vyberte **vytvořit** a zadejte smysluplný název profilu, jako je například *přihlašovací obrazovka Windows 10 SSPR* .

    Volitelně můžete zadat smysluplný popis profilu a pak vybrat **Další**.
1. V části *nastavení konfigurace* vyberte **Přidat** a zadejte následující nastavení OMA-URI, které povolí odkaz pro resetování hesla:
      - Zadejte smysluplný název, který vysvětluje, co toto nastavení dělá, například *odkaz Přidat SSPR*.
      - Volitelně můžete zadat smysluplný popis nastavení.
      - Identifikátor **OMA-URI** nastavte na `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      - **Datový typ** nastavte na **Integer**.
      - Jako **Hodnota** nastavte **1**.

    Vyberte **Přidat** a **Další**.
1. Zásady je možné přiřadit konkrétním uživatelům, zařízením nebo skupinám. Přiřaďte profil požadovaný pro vaše prostředí, ideálně do testovací skupiny zařízení a pak vyberte **Další**.

    Další informace najdete v tématu [přiřazení profilů uživatelů a zařízení v Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Nakonfigurujte pravidla použitelnosti podle požadavků vašeho prostředí, jako je třeba *přiřadit profil, pokud je operační systém Windows 10 Enterprise*, a pak vyberte **Další**.
1. Zkontrolujte svůj profil a pak vyberte **vytvořit**.

### <a name="enable-for-windows-10-using-the-registry"></a>Povolení pro Windows 10 pomocí registru

Pokud chcete povolit SSPR na přihlašovací obrazovce pomocí klíče registru, proveďte následující kroky:

1. Přihlaste se k počítači s Windows pomocí přihlašovacích údajů správce.
1. Stisknutím **Windows**  +  **R** otevřete dialog *Spustit* a pak spusťte **Regedit** jako správce.
1. Nastavíte následující klíč registru:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Řešení potíží s resetováním hesla Windows 10

Pokud máte problémy s používáním SSPR z přihlašovací obrazovky Windows, protokol auditu služby Azure AD obsahuje informace o IP adrese a *ClientType* , kde došlo k resetování hesla, jak je znázorněno v následujícím příkladu výstupu:

![Příklad resetování hesla systému Windows 7 v protokolu auditu Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Když uživatelé resetují heslo na přihlašovací obrazovce zařízení s Windows 10, vytvoří se dočasný účet s nízkou úrovní oprávnění s názvem `defaultuser1` . Tento účet se používá k zabezpečení procesu resetování hesla.

Samotný účet má náhodně generované heslo, nezobrazuje se pro přihlášení k zařízení a automaticky se odebere po resetování hesla uživatelem. `defaultuser`Může existovat více profilů, ale lze je bezpečně ignorovat.

## <a name="windows-7-8-and-81-password-reset"></a>Resetování hesla Windows 7, 8 a 8,1

Pokud chcete na přihlašovací obrazovce nakonfigurovat zařízení se systémem Windows 7, 8 nebo 8,1 pro SSPR, přečtěte si následující požadavky a kroky konfigurace.

### <a name="windows-7-8-and-81-prerequisites"></a>Požadavky pro Windows 7, 8 a 8,1

- Správce [musí povolit samoobslužné resetování hesla služby Azure AD z Azure Portal](tutorial-enable-sspr.md).
- Před použitím této funkce se uživatelé musí zaregistrovat pro SSPR. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Není jedinečné, aby bylo možné používat SSPR z přihlašovací obrazovky Windows, všichni uživatelé musí před resetováním hesla zadat kontaktní údaje pro ověření.
- Požadavky na síťový proxy server:
    - Port 443 až `passwordreset.microsoftonline.com`
- Opraven operační systém Windows 7 nebo Windows 8.1.
- Protokol TLS 1,2 povolený pomocí pokynů uvedených v [nastavení registru TLS (Transport Layer Security)](/windows-server/security/tls/tls-registry-settings#tls-12).
- Pokud je na vašem počítači povolený víc než jeden poskytovatel přihlašovacích údajů třetích stran, na přihlašovací obrazovce se uživatelům zobrazí víc než jeden profil uživatele.

> [!WARNING]
> Je nutné povolit TLS 1,2, není pouze nastaveno na automatické vyjednávání.

### <a name="install"></a>Instalace

V systému Windows 7, 8 a 8,1 musí být v počítači nainstalována malá součást, aby bylo možné SSPR povolit na přihlašovací obrazovce. Chcete-li nainstalovat tuto komponentu SSPR, proveďte následující kroky:

1. Stáhněte si odpovídající instalační program pro verzi systému Windows, kterou chcete povolit.

    Instalační program softwaru je k dispozici na webu Microsoft Download Center na adrese [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Přihlaste se k počítači, do kterého chcete nainstalovat, a spusťte instalační program.
1. Po instalaci se důrazně doporučuje restartování počítače.
1. Po restartování klikněte na obrazovce přihlášení na uživatele a vyberte zapomenuté heslo. Pro zahájení pracovního postupu pro resetování hesla.
1. Dokončete pracovní postup podle pokynů na obrazovce a resetujte heslo.

![Příklad Windows 7 klikl na "Zapomenuté heslo?" Tok SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Bezobslužná instalace

Komponentu SSPR lze nainstalovat nebo odinstalovat bez výzvy pomocí následujících příkazů:

- Pro tichou instalaci použijte příkaz "msiexec/i SsprWindowsLogon.PROD.msi/Qn"
- Pro tichou odinstalaci použijte příkaz "msiexec/x SsprWindowsLogon.PROD.msi/Qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Řešení potíží s resetováním hesla pro Windows 7, 8 a 8,1

Pokud máte problémy s používáním SSPR z přihlašovací obrazovky Windows, události se protokolují v počítači i ve službě Azure AD. Události Azure AD obsahují informace o IP adrese a ClientType, kde došlo k resetování hesla, jak je znázorněno v následujícím příkladu výstupu:

![Příklad resetování hesla systému Windows 7 v protokolu auditu Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Pokud se vyžaduje další protokolování, můžete změnit klíč registru v počítači tak, aby se povolilo podrobné protokolování. Povolit podrobné protokolování pro účely řešení potíží pouze pomocí následující hodnoty klíče registru:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Chcete-li povolit podrobné protokolování, vytvořte `REG_DWORD: "EnableLogging"` a nastavte jej na hodnotu 1.
- Chcete-li zakázat podrobné protokolování, změňte `REG_DWORD: "EnableLogging"` hodnotu na 0.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Které změny pro uživatele jsou u SSPR nakonfigurovaných pro vaše zařízení s Windows? Jak se dozví, že své heslo můžou resetovat na přihlašovací obrazovce? Následující ukázkové snímky obrazovky zobrazují další možnosti, jak může uživatel resetovat heslo pomocí SSPR:

![Příklady přihlašovacích obrazovek Windows 7 a 10 se zobrazeným odkazem na SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Když se uživatel pokusí přihlásit, uvidí **heslo pro resetování** hesla nebo **zapomenuté heslo** , které otevře prostředí samoobslužného resetování hesla na přihlašovací obrazovce. Tato funkce umožňuje uživatelům resetovat své heslo, aniž by museli použít jiné zařízení pro přístup k webovému prohlížeči.

Další informace o tom, jak používat tuto funkci, najdete v rámečku [resetování svého pracovního nebo školního hesla](../user-help/active-directory-passwords-update-your-own-password.md) .

## <a name="next-steps"></a>Další kroky

Chcete-li zjednodušit registraci uživatelů, můžete [předem naplnit kontaktní údaje pro ověřování uživatelů pro SSPR](howto-sspr-authenticationdata.md).
