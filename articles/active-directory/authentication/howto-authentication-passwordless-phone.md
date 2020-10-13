---
title: Přihlášení bez hesla pomocí aplikace Microsoft Authenticator Azure Active Directory
description: Povolení přihlášení neheslem do Azure AD pomocí aplikace Microsoft Authenticator (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964821"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview)

Aplikaci Microsoft Authenticator můžete použít k přihlášení k jakémukoli účtu Azure AD bez použití hesla. Podobně jako technologie [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou vázané na zařízení, a používá biometriku nebo PIN kód. Tato metoda ověřování se dá použít na libovolné platformě zařízení, včetně mobilních aplikací, a na libovolné aplikaci nebo web, která se integruje s knihovnami ověřování Microsoftu.

![Příklad přihlášení prohlížeče, které žádá uživatele o schválení přihlášení](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Namísto zobrazení výzvy k zadání hesla po zadání uživatelského jména se uživateli, který povolil telefonní přihlášení z aplikace Microsoft Authenticator, zobrazí zpráva s výzvou, aby v aplikaci klepne na číslo. K dokončení procesu přihlašování v aplikaci musí uživatel souhlasit s číslem, zvolit **schválit**a pak zadat PIN nebo biometrické údaje.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat přihlášení bez hesla pro telefonickou práci s aplikací Microsoft Authenticator, musí být splněné následující předpoklady:

- Azure Multi-Factor Authentication s nabízenými oznámeními, která jsou povolená jako metoda ověřování.
- Na zařízeních se systémem iOS 8,0 nebo vyšším je nainstalovaná nejnovější verze Microsoft Authenticator nebo Android 6,0 nebo novější.

> [!NOTE]
> Pokud jste povolili Microsoft Authenticator přihlášení bez hesla aplikace bez použití služby Azure AD PowerShell, je povolený pro celý adresář. Pokud povolíte použití této nové metody, bude nahrazena zásadou PowerShellu. Doporučujeme povolit pro všechny uživatele ve vašem tenantovi prostřednictvím nové nabídky *metody ověřování* , jinak se uživatelé, kteří nejsou v nové zásadě, už nebudou moct přihlásit bez hesla.

## <a name="enable-passwordless-authentication-methods"></a>Povolit metody ověřování nejenom pro hesla

Pokud chcete ve službě Azure AD používat ověřování bez hesla, nejdřív povolte kombinované možnosti registrace a pak uživatelům povolte metodu less pro heslo.

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla se spoléhají na funkci kombinované registrace. Pokud chcete, aby uživatelé mohli provést kombinovanou registraci sami, postupujte podle pokynů pro [Povolení registrace kombinovaných informací o zabezpečení](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Povolit metody ověřování přihlašování bezheslem pro telefonování

Azure AD umožňuje vybrat, které metody ověřování se dají použít během procesu přihlašování. Potom si zaregistrujte metody, které chce použít.

Pokud chcete metodu ověřování povolit pro přihlášení telefon bez hesla, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu *globálního správce* .
1. Vyhledejte a vyberte *Azure Active Directory*a pak přejděte k zásadě ověřování metody **zabezpečení**  >  **Authentication methods**  >  **(Preview)** .
1. V části **přihlášení bez hesla pro telefonování**vyberte následující možnosti:
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. Chcete-li použít novou zásadu, vyberte možnost **Uložit**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrace a Správa uživatelů aplikace Microsoft Authenticator

Když je k dispozici metoda ověřování bez hesla pro použití ve službě Azure AD, uživatelé se teď musí zaregistrovat k metodě ověřování bez hesla pomocí následujících kroků:

1. Přejděte na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Přihlaste se a pak přidejte ověřovací aplikaci výběrem **Přidat metodu > ověřovací aplikace**a pak **Přidat**.
1. Podle pokynů nainstalujte a nakonfigurujte aplikaci Microsoft Authenticator na svém zařízení.
1. Vyberte **Hotovo** , aby se konfigurace ověřovatele dokončila.
1. V aplikaci **Microsoft Authenticator** App vyberte v rozevírací nabídce u registrovaného účtu možnost **Povolit přihlášení k telefonu** .
1. Podle pokynů v aplikaci dokončete registraci účtu pro přihlášení přes telefon s neheslem.

Organizace můžou své uživatele nasměrovat na své [telefonní číslo, ne heslo](../user-help/user-help-auth-app-sign-in.md) pro další pomoc při konfiguraci Microsoft Authenticator aplikace a povolení přihlášení k telefonu.

> [!NOTE]
> Uživatelé, kterým zásada nepovoluje používat přihlášení telefonem, už je nepůjde povolit v aplikaci Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

Uživatel může začít používat přihlášení se svým heslem, jakmile správce povolí svého tenanta a uživatel aktualizoval svou Microsoft Authenticator aplikaci, aby umožnil přihlášení k telefonu.

Pokud chcete začít používat přihlášení telefonem, po zadání uživatelského jména na přihlašovací stránce a výběru možnosti **Další**si uživatelé můžou muset vybrat **jiné způsoby přihlášení**a pak **schválit žádost v aplikaci Microsoft Authenticator**. Uživatelé se pak zobrazí s číslem a v aplikaci Microsoft Authenticator se zobrazí výzva, aby místo používání hesla vybrali příslušné číslo, které se má ověřit. Jakmile uživatelé použijí přihlášení pomocí telefonu bez hesla, zobrazí se jim výzva k jeho použití, dokud nepoužijí jinou metodu.

![Příklad přihlášení prohlížeče pomocí aplikace Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Známé problémy

V aktuálním prostředí Preview existují následující známé problémy.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nezobrazuje se možnost pro přihlášení bez hesla pro telefonování

Pokud má uživatel nezodpovězené přihlášení k telefonu bez hesla a pokusí se o přihlášení znovu, může se uživateli zobrazit jenom možnost zadat heslo. Otevřete Microsoft Authenticator a odpovězte na případné výzvy k zadání oznámení, abyste mohli dál používat přihlašování pomocí telefonu bez hesla.

### <a name="federated-accounts"></a>Federované účty

Pokud uživatel povolil jakékoli přihlašovací údaje bez hesla, přihlášení služby Azure AD přestane používat login_hint k urychlení uživatele do federovaného přihlašovacího umístění. Tato logika zabraňuje tomu, aby se uživatelé v hybridním tenantovi nasměrovali na AD FS pro ověřování přihlašování bez toho, aby uživatel musel další krok kliknout na použít heslo.

### <a name="azure-mfa-server"></a>Azure MFA Server

Koncoví uživatelé, kteří mají povolený MFA prostřednictvím místního Azure MFA serveru organizace, můžou pořád vytvářet a používat přihlašovací údaje pro přihlášení bez hesla pro přihlášení k telefonu. Pokud se uživatel pokusí upgradovat více instalací (5 +) Microsoft Authenticator s přihlašovacími údaji, může tato změna způsobit chybu.  

### <a name="device-registration"></a>Registrace zařízení

Jedním z požadavků pro vytvoření těchto nových silných přihlašovacích údajů je, že zařízení, ve kterém je nainstalovaná aplikace Microsoft Authenticator, musí být také zaregistrovaná v tenantovi služby Azure AD pro jednotlivé uživatele. Kvůli aktuálním omezením registrace zařízení se může zařízení zaregistrovat jenom v jednom tenantovi. Tento limit znamená, že pro přihlašování telefonem se dá povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator.

> [!NOTE]
> Registrace zařízení není stejná jako Správa zařízení nebo MDM. Přidružuje jenom ID zařízení a ID uživatele společně v adresáři Azure AD.  

## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure AD a metodách bez hesla najdete v následujících článcích:

* [Informace o tom, jak funguje ověřování heslem](concept-authentication-passwordless.md)
* [Další informace o registraci zařízení](../devices/overview.md#getting-devices-in-azure-ad)
* [Přečtěte si o Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
