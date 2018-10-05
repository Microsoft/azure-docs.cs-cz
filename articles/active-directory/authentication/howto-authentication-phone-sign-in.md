---
title: Přihlašování bez hesla Azure AD pomocí aplikace Microsoft Authenticator (public preview)
description: Přihlášení k Azure AD pomocí aplikace Microsoft Authenticator bez použití hesla (public preview)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: librown
ms.openlocfilehash: 81c249c8dc8475428f4cb0014e57f09e28a3d9af
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804323"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Přihlašování telefonem bez hesla pomocí aplikace Microsoft Authenticator (public preview)

Aplikace Microsoft Authenticator je možné se přihlásit k libovolnému účtu služby Azure AD bez použití hesla. Podobně jako technologii, kterou [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator využívá ověřování pomocí klíče k zajištění pověření uživatele, který se váže na zařízení a používá biometrika nebo PIN kód.

![Příklad prohlížeči přihlášení s výzvou pro uživatele ke schválení pokus o přihlášení v rámci vlastní aplikace Microsoft Authenticator](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Místo zobrazuje se neočekávaná výzva k zadání hesla po zadání uživatelského jména, osoby, která má povolenou přihlašování telefonem v aplikaci Microsoft Authenticator se zobrazí zpráva s upozorněním na klepněte na příslušný počet do své aplikace. V aplikaci uživatel musí shodovat s číslem, zvolte možnost Schválit a pak zadejte svůj PIN kód nebo biometrické, pak ověřování dokončí.

## <a name="enable-my-users"></a>Povolení uživatelé

Pro verzi public preview správce musíte nejprve přidat zásady pomocí powershellu k povolení použití přihlašovacích údajů v tenantovi. Projděte si část "Známé problémy" před provedením tohoto kroku.

### <a name="tenant-prerequisites"></a>Požadavky na klienta

* Azure Active Directory
* Koncoví uživatelé povoleno pro ověřování Azure Multi-Factor Authentication
* Uživatelé můžou registrovat svoje zařízení

### <a name="steps-to-enable"></a>Postup povolení

1. Nainstalujte [verze public preview služby Azure Active Directory V2 Powershellu modulu](https://www.powershellgallery.com/packages/AzureADPreview/).  
2. V prostředí PowerShell spusťte dva příkazy:
   1. `Connect-AzureAD`
      1. V dialogovém okně pro ověření přihlašují účtem v tenantovi. Účet musí být buď správce zabezpečení nebo globální správce.
   2. `New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn`

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Jak koncovým uživatelům povolit přihlašování telefonem?

Pro verzi public preview neexistuje žádný způsob, jak vynutit uživatelům vytvořit nebo použít těchto nových přihlašovacích údajů. Koncový uživatel se přihlašování bez hesla dojít pouze po správcem jako povolené jejich tenanta a uživatel se aktualizovala svoje aplikace Microsoft Authenticator povolit přihlašování telefonem.

> [!NOTE]
> Tato funkce byla v aplikaci od března 2017, je možnost, že pokud je zásada povolená pro tenanta, můžou se setkat tento tok okamžitě. Mějte na paměti a tuto změnu připravit vaši uživatelé.
>

1. Zaregistrujte se do služby Azure Multi-Factor Authentication
1. Nejnovější verzi aplikace Microsoft Authenticator nainstalované v zařízení se systémem iOS 8.0 nebo novější nebo Android 6.0 nebo vyšší.
1. Pracovní nebo školní účet pomocí nabízených oznámení do aplikace. Dokumentaci pro koncové uživatele najdete v [ https://aka.ms/authappstart ](https://aka.ms/authappstart).

Jakmile uživatel má účet MFA s nabízenými oznámeními v aplikaci Microsoft Authenticator, můžete podle kroků v článku [přihlášení pomocí telefonu, ne heslo](../user-help/microsoft-authenticator-app-phone-signin-faq.md) k dokončení registrace telefonu přihlášení.

## <a name="known-issues"></a>Známé problémy

### <a name="ad-fs-integration"></a>Integrace služby AD FS

Když uživatel povolí přihlašovací údaje bez hesla, které Microsoft Authenticator, ověřování pro tohoto uživatele bude vždy ve výchozím nastavení odesílání oznámení o schválení. Tuto logiku zabraňuje uživatelům v tenantovi hybridní do AD FS pro ověřování přihlášení uživatele, s ohledem na další krok směrovat do příslušných klikněte na tlačítko "Místo toho použít heslo." Tento proces bude také vynechat všechny místní zásady podmíněného přístupu a toky předávací ověřování. Výjimkou z tohoto procesu je login_hint je-li zadán, uživatel bude automaticky předána do služby AD FS a vynechat možnost použití přihlašovacích údajů bez hesla.

### <a name="azure-mfa-server"></a>Azure MFA serveru

Koncoví uživatelé, kteří jsou povoleny pro vícefaktorové ověřování pomocí serveru Azure MFA v místním prostředí vaší organizace můžete stále vytvořit a použít jeden telefon bez hesla přihlašovací pověření. Pokud uživatel pokusí o upgrade více instalací (5 +) s pověřením Microsoft Authenticator, tato změna může vést k chybě.  

### <a name="device-registration"></a>Registrace zařízení

Jednou z požadavky na vytvoření této nové silné přihlašovací údaje, je, že registrace zařízení, ve kterém se nachází v rámci tenanta Azure AD pro jednotlivé uživatele. Z důvodu omezení registrace zařízení můžete zařízení registrovat jenom v jednom tenantovi. Toto omezení znamená, že je možné povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator pro přihlašování telefonem.

## <a name="next-steps"></a>Další postup

[Další informace o registraci zařízení](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[Další informace o službě Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
