---
title: Přihlášení bez hesla pomocí aplikace Microsoft Authenticator – Azure Active Directory
description: Povolení bezhesla přihlášení k Azure AD pomocí Microsoft Authenticator app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c684d6f1fbd8128ae020b6fd29da928b286aa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126685"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (preview)

Aplikaci Microsoft Authenticator lze použít k přihlášení k libovolnému účtu Azure AD bez použití hesla. Podobně jako technologie [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator ověřování založené na klíči k povolení pověření uživatele, které je svázané se zařízením a používá biometrické nebo PIN kódy. Tuto metodu ověřování lze použít na libovolné platformě zařízení, včetně mobilních zařízení, a s libovolnou aplikací nebo webem, který se integruje s ověřovacími knihovnami Microsoftu. 

![Příklad přihlášení prohlížeče s žádostí o schválení přihlášení uživatelem](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Místo zobrazení výzvy k zadání hesla po zadání uživatelského jména se osobě, která povolila přihlášení k telefonu z aplikace Microsoft Authenticator, zobrazí zpráva s oznámením, že mají klepnout na číslo v aplikaci. V aplikaci musí uživatel odpovídat číslu, zvolit Schválit, pak zadat svůj PIN nebo biometrický kód a pak se ověření dokončí.

> [!NOTE]
> Tato funkce byla v aplikaci Microsoft Authenticator od března 2017, takže je možné, že pokud je zásada povolena pro adresář, uživatelé se mohou okamžitě setkat s tímto tokem a zobrazit chybovou zprávu, pokud nebyly povoleny zásadami. Uvědomte si a připravte uživatele na tuto změnu.

## <a name="prerequisites"></a>Požadavky

- Azure Multi-Factor Authentication, s nabízená oznámení povolena jako metoda ověření 
- Nejnovější verze Microsoft Authenticator nainstalovaná na zařízeních se systémem iOS 8.0 nebo vyšším nebo Androidem 6.0 nebo vyšším.

> [!NOTE]
> Pokud jste povolili předchozí náhled bezhesla aplikace Microsoft Authenticator bez hesla pomocí prostředí Azure AD PowerShell, byla povolena pro celý adresář. Pokud povolíte pomocí této nové metody, bude supercede zásady Prostředí PowerShell. Doporučujeme povolit pro všechny uživatele ve vašem tenantovi prostřednictvím nových metod ověřování, jinak uživatelé, kteří nejsou v nové zásadě, se již nebudou moci bez hesla přihlásit. 

## <a name="enable-passwordless-authentication-methods"></a>Povolení metod ověřování bez hesla

### <a name="enable-the-combined-registration-experience"></a>Povolení kombinovaného registračního prostředí

Registrační funkce pro metody ověřování bez hesla spoléhají na kombinovaný náhled registrace. Postupujte podle kroků v článku [Povolit kombinovanou registraci informací o zabezpečení (náhled)](howto-registration-mfa-sspr-combined.md), chcete-li povolit kombinovaný náhled registrace.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Povolení metod ověřování pomocí telefonického přihlášení bez hesla

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Vyhledejte a vyberte *Azure Active Directory*. Vyberte**zásadu metody** > ověřování **zabezpečení** > **(preview)**
1. V části **Přihlášení k telefonu bez hesla**zvolte následující možnosti.
   1. **Povolit** – ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybraní uživatelé
1. **Uložit** pro nastavení nové zásady

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrace a správa uživatelů aplikace Microsoft Authenticator

1. Přejít na[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Přihlásit se, pokud ještě ne
1. Přidejte ověřovací aplikaci kliknutím na **Add Method**, výběrem **ověřovací aplikace**a kliknutím na **Přidat**
1. Podle pokynů nainstalujte a nakonfigurujte aplikaci Microsoft Authenticator na vašem zařízení.
1. Kliknutím na **Hotovo** dokončete tok nastavení aplikace Authenticator MFA. 
1. V **aplikaci Microsoft Authenticator**zvolte **Povolit přihlášení k telefonu** z rozevírací nabídky účtu.
1. Podle pokynů v aplikaci dokončete registraci pro přihlášení k telefonu bez hesla. 

Organizace mohou uživatele nasměrovat na článek [Přihlášení pomocí telefonu, nikoli heslo](../user-help/microsoft-authenticator-app-phone-signin-faq.md) pro další pomoc s nastavením v aplikaci Microsoft Authenticator a povolením přihlášení k telefonu. Chcete-li použít tato nastavení, budete muset odhlásit a přihlásit zpět do tenanta. 

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

Pro verzi Public Preview neexistuje žádný způsob, jak vynutit uživatelům vytvořit nebo použít toto nové pověření. Uživatel se setká s přihlášením bez hesla pouze poté, co správce povolil svého klienta **a** uživatel aktualizoval svou aplikaci Microsoft Authenticator, aby povolil přihlášení telefonem.

Po zadání uživatelského jména na webu a výběru možnosti **Další**se uživatelům zobrazí číslo a v aplikaci Microsoft Authenticator se zobrazí výzva k výběru příslušného čísla k ověření namísto použití hesla. 

![Příklad přihlášení prohlížeče pomocí aplikace Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Známé problémy

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Uživatel není povolen zásadou, ale stále má metodu přihlášení k telefonu bez hesla v aplikaci Microsoft Authenticator

Je možné, že uživatel v určitém okamžiku vytvořil přihlašovací údaje telefonu bez hesla v aktuální aplikaci Microsoft Authenticator nebo na starším zařízení. Jakmile správce povolí zásady metody ověřování pro přihlášení telefonem bez hesla, každý uživatel s registrovaným přihlašovacím údajem začne docházet k nové přihlašovací výzvě bez ohledu na to, zda bylo povoleno používat zásadu nebo ne. Pokud uživateli nebylo povoleno používat pověření podle zásad, zobrazí se po dokončení toku ověřování chyba. 

Správce může povolit uživateli používat bezhesla telefonní přihlášení, nebo uživatel musí odebrat metodu. Pokud uživatel již registrované zařízení nemá, může [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) přejít na něj a odebrat ho. Pokud stále používají ověřovací dokument pro vícefaktorové ověřování, mohou zvolit **zakázat přihlašování k telefonu** z aplikace Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integrace ad FS

Pokud uživatel povolil pověření microsoft authenticator bez hesla, ověřování pro tohoto uživatele bude vždy výchozí odeslání oznámení ke schválení. Tato logika zabraňuje uživatelům v hybridním tenantovi přesměrováni na službu ADFS pro ověření přihlášení, aniž by uživatel udělal další krok a kliknul na tlačítko "Místo toho použít heslo". Tento proces také obejde všechny místní zásady podmíněného přístupu a předávací toky ověřování. 

Pokud má uživatel nezodpovězené ověření telefonického přihlášení bez hesla čekající na vyřízení a pokusí se znovu přihlásit, může být uživatel místo toho převeden do systému ADFS a zadat heslo.  

### <a name="azure-mfa-server"></a>Server Azure MFA

Koncoví uživatelé, kteří jsou povoleni pro vícefaktorové ověřování prostřednictvím místního serveru Azure MFA v organizaci, můžou stále vytvářet a používat přihlašovací přihlašovací údaje k telefonu bez hesla. Pokud se uživatel pokusí upgradovat více instalací (5+) microsoft authenticator s pověřením, tato změna může mít za následek chybu.  

### <a name="device-registration"></a>Registrace zařízení

Jedním z předpokladů pro vytvoření tohoto nového silného pověření je, že zařízení, kde je nainstalovaná aplikace Microsoft Authenticator, musí být také registrováno v rámci klienta Azure AD pro jednotlivého uživatele. Vzhledem k aktuálním omezením registrace zařízení může být zařízení registrováno pouze v jednom klientovi. Toto omezení znamená, že pro přihlášení po telefonu lze povolit pouze jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator.

### <a name="intune-mobile-application-management"></a>Správa mobilních aplikací Intune 

Koncoví uživatelé, na které se vztahují zásady, které vyžadují správu mobilních aplikací (MAM), nemohou zaregistrovat přihlašovací údaje bez hesla v aplikaci Microsoft Authenticator. 

> [!NOTE]
> Registrace zařízení není stejná jako správa zařízení nebo "MDM." Pouze přidruží ID zařízení a ID uživatele společně v adresáři Azure AD.  

## <a name="next-steps"></a>Další kroky

[Co je přístup bez hesla?](concept-authentication-passwordless.md)

[Informace o registraci zařízení](../devices/overview.md#getting-devices-in-azure-ad)

[Informace o vícefaktorovém ověřování Azure](../authentication/howto-mfa-getstarted.md)
