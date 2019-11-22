---
title: Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview) – Azure Active Directory
description: Povolení přihlášení neheslem do Azure AD pomocí aplikace Microsoft Authenticator (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca668036a8b4a4af261ee85b8a884c22258beb4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269504"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview)

Aplikaci Microsoft Authenticator můžete použít k přihlášení k jakémukoli účtu Azure AD bez použití hesla. Podobně jako technologie [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou vázané na zařízení, a používá biometriku nebo PIN kód. Tato metoda ověřování se dá použít na libovolné platformě zařízení, včetně mobilních aplikací, a na libovolné aplikaci nebo web, která se integruje s knihovnami ověřování Microsoftu. 

![Příklad přihlášení prohlížeče, které žádá uživatele o schválení přihlášení](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Namísto zobrazení výzvy k zadání hesla po zadání uživatelského jména se uživateli, který povolil přihlášení telefonem z Microsoft Authenticator aplikace, zobrazí zpráva s oznámením, že se má v aplikaci klepnout na číslo. V aplikaci musí uživatel souhlasit s číslem, zvolit schválit, potom zadat PIN nebo biometriku a ověřování se dokončí.

> [!NOTE]
> Tato funkce byla v aplikaci Microsoft Authenticator od března 2017, takže pokud je zásada pro adresář povolená, můžou se uživatelé setkat okamžitě a zobrazit chybovou zprávu, pokud je zásada nepovolila. Mějte na paměti informace a připravte uživatele na tuto změnu.

## <a name="prerequisites"></a>Požadavky

- Azure Multi-Factor Authentication s nabízenými oznámeními povolenými jako metoda ověřování 
- Na zařízeních se systémem iOS 8,0 nebo vyšším je nainstalovaná nejnovější verze Microsoft Authenticator nebo Android 6,0 nebo novější.

> [!NOTE]
> Pokud jste ve verzi Preview služby Azure AD PowerShell povolili předchozí přihlášení bez hesla aplikace Microsoft Authenticator, je povolená pro celý adresář. Pokud povolíte použití této nové metody, bude nahrazena zásadou PowerShellu. Doporučujeme povolit pro všechny uživatele ve vašem tenantovi prostřednictvím nových metod ověřování, jinak se uživatelé, kteří nejsou v nové zásadě, už nebudou moci přihlásit passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Povolit metody ověřování nejenom pro hesla

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla jsou závislé na kombinované registraci ve verzi Preview. Postupujte podle kroků v článku [Povolení registrace kombinovaných zabezpečení (Preview)](howto-registration-mfa-sspr-combined.md), aby se aktivovala kombinovaná registrační verze.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Povolit metody ověřování přihlašování bezheslem pro telefonování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte *Azure Active Directory*. Vyberte **metody ověřování** **zabezpečení** >  > **zásady metody ověřování (Preview)** .
1. V části **přihlášení bez hesla pro telefonování**vyberte následující možnosti.
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. **Uložit** pro nastavení nové zásady

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrace a Správa uživatelů aplikace Microsoft Authenticator

1. Přejít na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Přihlásit se, pokud ještě není
1. Přidejte aplikaci ověřovatele tak, že kliknete na **Přidat metodu**, zvolíte **ověřovací aplikaci**a kliknete na **Přidat** .
1. Podle pokynů nainstalujte a nakonfigurujte na svém zařízení Microsoft Authenticatorovou aplikaci.
1. Kliknutím na **Hotovo** dokončíte tok nastavení aplikace MFA pro ověřování. 
1. V **Microsoft Authenticator**v rozevírací nabídce účet vyberte **Povolit přihlášení k telefonu** .
1. Podle pokynů v aplikaci dokončete registraci pro přihlášení k telefonu s neheslem. 

Organizace mohou nasměrovat své uživatele na článek [Přihlásit se svým telefonem, nejedná](../user-help/microsoft-authenticator-app-phone-signin-faq.md) se o heslo pro další pomoc při nastavování v aplikaci Microsoft Authenticator a povolení přihlašování telefonem.

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

Ve verzi Public Preview neexistuje způsob, jak vyhovět uživatelům při vytváření nebo používání těchto nových přihlašovacích údajů. Uživatel může přihlašovat se jenom heslem jenom tehdy, když správce povolí svého tenanta **, a** uživatel aktualizoval svoji Microsoft Authenticator aplikaci, aby umožnil přihlášení k telefonu.

Po zadání uživatelského jména na webu a výběru možnosti **Další**se uživatelům zobrazí číslo a zobrazí se výzva v aplikaci Microsoft Authenticator, aby místo používání hesla vybrala příslušné číslo, které se má ověřit. 

![Příklad přihlášení prohlížeče pomocí aplikace Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Známé problémy

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Uživatel není povolen zásadami, ale pořád má v Microsoft Authenticator metodu přihlašování přes telefon bez hesla.

Je možné, že uživatel v určitém okamžiku vytvořil přihlašovací údaje pro přihlášení bez hesla v aktuální Microsoft Authenticator aplikaci nebo na dřívějším zařízení. Když správce povolí zásadu metody ověřování pro telefonické přihlášení k telefonu bez hesla, bude začít používat nové přihlašovací výzvy, a to bez ohledu na to, jestli je povolil použití této zásady. Pokud uživatel nepovolil použití přihlašovacích údajů podle zásad, zobrazí se po dokončení toku ověřování chyba. 

Správce může povolit, aby uživatel mohl používat přihlášení bez hesla pro telefonování, nebo musí tuto metodu odebrat uživatel. Pokud uživatel už registrované zařízení nemá, může přejít na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) a odebrat ho. Pokud i nadále používají ověřovací data pro MFA, můžou zvolit **Zakázat přihlášení telefonem** z Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integrace AD FS

Pokud uživatel povolil Microsoft Authenticator přihlašovací údaje bez hesla, ověřování pro tohoto uživatele vždy bude ve výchozím nastavení odesláno oznámení ke schválení. Tato logika brání uživatelům v hybridním tenantovi směrovat se na službu AD FS, aby se ověřování přihlásilo bez toho, aby uživatel musel další krok kliknout na použít heslo. Tento proces taky obchází všechny místní zásady podmíněného přístupu a předávací ověřovací toky. 

Pokud má uživatel nezodpovězené přihlášení k telefonu bez hesla a pokusí se o přihlášení znovu, může se stát, že se uživatel bude muset místo toho pokusit zadat heslo.  

### <a name="azure-mfa-server"></a>Azure MFA Server

Koncoví uživatelé, kteří mají povolený MFA prostřednictvím místního Azure MFA serveru organizace, můžou pořád vytvářet a používat přihlašovací údaje pro přihlášení bez hesla. Pokud se uživatel pokusí upgradovat více instalací (5 +) Microsoft Authenticator s přihlašovacími údaji, může tato změna způsobit chybu.  

### <a name="device-registration"></a>Registrace zařízení

Jedním z požadavků pro vytvoření těchto nových silných přihlašovacích údajů je, že zařízení, ve kterém je nainstalovaná aplikace Microsoft Authenticator, musí být také zaregistrovaná v tenantovi služby Azure AD pro jednotlivé uživatele. Kvůli aktuálním omezením registrace zařízení se může zařízení zaregistrovat jenom v jednom tenantovi. Tento limit znamená, že pro přihlašování telefonem se dá povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator.

> [!NOTE]
> Registrace zařízení není stejná jako Správa zařízení nebo MDM. Přidružuje jenom ID zařízení a ID uživatele společně v adresáři Azure AD.  

## <a name="next-steps"></a>Další kroky

[Co není heslo?](concept-authentication-passwordless.md)

[Další informace o registraci zařízení](../devices/overview.md#getting-devices-in-azure-ad)

[Přečtěte si o Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
