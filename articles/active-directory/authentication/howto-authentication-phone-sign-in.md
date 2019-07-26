---
title: Přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview) – Azure Active Directory
description: Přihlaste se k Azure AD pomocí aplikace Microsoft Authenticator bez použití hesla (Public Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357137"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Public Preview)

Aplikaci Microsoft Authenticator můžete použít k přihlášení k jakémukoli účtu Azure AD bez použití hesla. Podobně jako technologie [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou vázané na zařízení, a používá biometriku nebo PIN kód.

![Příklad přihlášení prohlížeče, které žádá uživatele o schválení přihlášení](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Namísto zobrazení výzvy k zadání hesla po zadání uživatelského jména se uživateli, který povolil přihlášení telefonem v aplikaci Microsoft Authenticator, zobrazí zpráva s oznámením, že v aplikaci klepne na číslo. V aplikaci musí uživatel souhlasit s číslem, zvolit schválit, potom zadat PIN nebo biometriku a ověřování se dokončí.

## <a name="enable-my-users"></a>Povolit moji uživatelům

### <a name="tenant-prerequisites"></a>Předpoklady klienta

* Azure Active Directory
* Koncoví uživatelé s povoleným ověřováním Azure Multi-Factor Authentication
* Uživatelé můžou registrovat svoje zařízení.

### <a name="steps-to-enable"></a>Postup povolení

Postupujte podle kroků v článku [Povolení přihlášení bez hesla pro Azure AD](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods)a povolte v adresáři metody ověřování bez hesla.

> [!NOTE]
> Pokud jste tuto funkci pro vašeho tenanta dříve povolili pomocí skriptu PowerShellu, nastavení nové zásady pro uživatele a skupiny přepíše stávající zásady v rámci tenanta. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Jak mám koncoví uživatelé povolit přihlašování telefonem?

Ve verzi Public Preview neexistuje způsob, jak vyhovět uživatelům při vytváření nebo používání těchto nových přihlašovacích údajů. Jakmile správce povolí svého tenanta a uživatel aktualizoval svou Microsoft Authenticator aplikaci, aby umožnil přihlášení k telefonnímu číslu, bude mít koncový uživatel pouze přihlášení k heslům.

> [!NOTE]
> Tato funkce byla v aplikaci od března 2017, takže existuje možnost, že když je zásada pro tenanta povolená, může se tento tok zobrazit okamžitě. Mějte na paměti informace a připravte uživatele na tuto změnu.
>

1. Registrace v Azure Multi-Factor Authentication
1. Na zařízeních se systémem iOS 8,0 nebo vyšším je nainstalovaná nejnovější verze Microsoft Authenticator nebo Android 6,0 nebo novější.
1. Pracovní nebo školní účet s nabízenými oznámeními přidanými do aplikace Dokumentaci pro koncové uživatele najdete na adrese [https://aka.ms/authappstart](https://aka.ms/authappstart).

Jakmile má uživatel účet MFA s nabízenými oznámeními nastavenými v Microsoft Authenticator aplikaci, může postupovat podle kroků v článku [přihlášení pomocí telefonu, nikoli hesla](../user-help/microsoft-authenticator-app-phone-signin-faq.md) k dokončení registrace telefonického přihlášení.

## <a name="known-issues"></a>Známé problémy

### <a name="ad-fs-integration"></a>Integrace AD FS

Pokud uživatel povolil Microsoft Authenticator přihlašovací údaje bez hesla, ověřování pro tohoto uživatele vždy bude ve výchozím nastavení odesláno oznámení ke schválení. Tato logika brání uživatelům v hybridním tenantovi směrovat se na službu AD FS, aby se ověřování přihlásilo bez toho, aby uživatel musel další krok kliknout na použít heslo. Tento proces taky obchází všechny místní zásady podmíněného přístupu a předávací ověřovací toky. Výjimkou z tohoto procesu je, že pokud je zadaný login_hint, uživatel se do AD FS přepošle a obejít možnost používat přihlašovací údaje bez hesla.

### <a name="azure-mfa-server"></a>Azure MFA Server

Koncoví uživatelé, kteří mají povolený MFA prostřednictvím místního Azure MFA serveru organizace, můžou pořád vytvářet a používat přihlašovací údaje pro přihlášení bez hesla pro přihlášení k telefonu. Pokud se uživatel pokusí upgradovat více instalací (5 +) Microsoft Authenticator s přihlašovacími údaji, může tato změna způsobit chybu.  

### <a name="device-registration"></a>Registrace zařízení

Jedním z požadavků pro vytvoření tohoto nového, silného pověření je, že zařízení, ve kterém se nachází, je zaregistrované v tenantovi služby Azure AD pro jednotlivé uživatele. V důsledku omezení registrace zařízení se může zařízení zaregistrovat jenom v jednom tenantovi. Tento limit znamená, že pro přihlašování telefonem se dá povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator.

## <a name="next-steps"></a>Další postup

[Co není heslo?](concept-authentication-passwordless.md)

[Další informace o registraci zařízení](../devices/overview.md#getting-devices-in-azure-ad)

[Další informace o službě Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
