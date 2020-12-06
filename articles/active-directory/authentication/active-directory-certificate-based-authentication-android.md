---
title: Ověřování založené na certifikátech Android – Azure Active Directory
description: Přečtěte si o podporovaných scénářích a požadavcích na konfiguraci ověřování na základě certifikátů v řešeních se zařízeními s Androidem.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd9f59dd75620f3a7b5c9142a4b8f73f75c5ee7b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744478"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory ověřování pomocí certifikátů v Androidu

Zařízení s Androidem můžou k ověřování pomocí ověřování na základě certifikátu (certifikátů) používat certifikát klienta při připojování k Azure Active Directory používání klientského certifikátu na svém zařízení:

* Mobilní aplikace Office, jako je Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Konfigurace této funkce eliminuje nutnost zadat kombinaci uživatelského jména a hesla k určitým e-mailovým a systém Microsoft Office aplikacím na vašem mobilním zařízení.

Toto téma vám poskytne požadavky a podporované scénáře konfigurace certifikátů na zařízení s Androidem pro uživatele klientů v aplikacích Office 365 Enterprise, Business, školství, USA pro státní správu, ČLR a Německo.

Tato funkce je dostupná ve verzi Preview v Office 365 USA a v federálních plánech.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací Microsoftu

| Aplikace | Podpora |
| --- | --- |
| Aplikace Azure Information Protection |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Intune Portál společnosti |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Microsoft Teams |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneNote |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneDrive |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Outlook |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Power BI |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Skype pro firmy |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Word/Excel/PowerPoint |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Yammer |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |

### <a name="implementation-requirements"></a>Požadavky implementace

Verze operačního systému zařízení musí být Android 5,0 (Lupa) a vyšší.

Je nutné nakonfigurovat Federační server.

Pro Azure Active Directory odvolání klientského certifikátu musí mít token ADFS následující deklarace identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Sériové číslo klientského certifikátu)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Řetězec pro vystavitele klientského certifikátu)

Azure Active Directory přidá tyto deklarace do aktualizačního tokenu, pokud jsou k dispozici v tokenu služby AD FS (nebo jakémkoli jiném tokenu SAML). Pokud je nutné ověřit token aktualizace, slouží tyto informace ke kontrole odvolání.

Osvědčeným postupem je aktualizovat chybové stránky ADFS vaší organizace s použitím následujících informací:

* Požadavek na instalaci Microsoft Authenticator v Androidu
* Pokyny, jak získat uživatelský certifikát

Další informace najdete v tématu [přizpůsobení AD FS přihlašovací stránky](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Některé aplikace Office (s povoleným moderním ověřováním) odesílají do služby Azure AD ve své žádosti "*prompt = Login*". Ve výchozím nastavení Azure AD překládá "*prompt = Login*" v požadavku na ADFS jako '*wauth = usernamepassworduri*' (požádá ADFS, aby provedou ověřování U/P) a '*wfresh = 0*' (požádá službu AD FS, aby ignorovala stav jednotného přihlašování a provede nové ověřování). Pokud chcete pro tyto aplikace povolit ověřování na základě certifikátů, musíte změnit výchozí chování služby Azure AD. Nastavte v nastavení federované domény '*PromptLoginBehavior*' na hodnotu '*disabled*'.
K provedení této úlohy můžete použít rutinu [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podpora klientů Exchange ActiveSync

Podporují se některé aplikace Exchange ActiveSync na Androidu 5,0 (Lupa) nebo novější. Chcete-li zjistit, zda vaše e-mailová aplikace tuto funkci podporuje, obraťte se na vývojáře aplikace.

## <a name="next-steps"></a>Další kroky

Pokud chcete ve svém prostředí nakonfigurovat ověřování na základě certifikátů, přečtěte si téma Začínáme [s ověřováním pomocí certifikátů v Androidu](active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png