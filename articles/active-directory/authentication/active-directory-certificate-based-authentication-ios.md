---
title: Ověřování založené na certifikátech v iOS – Azure Active Directory
description: Přečtěte si o podporovaných scénářích a požadavcích na konfiguraci ověřování na základě certifikátů v řešeních se zařízeními s iOS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848795"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory ověřování pomocí certifikátů v iOS

zařízení s iOS můžou k ověřování použít ověřování pomocí certifikátu (certifikátů), aby se při připojování k Azure Active Directory používal klientský certifikát na svém zařízení:

* Mobilní aplikace Office, jako je Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Konfigurace této funkce eliminuje nutnost zadat kombinaci uživatelského jména a hesla k určitým e-mailovým a systém Microsoft Office aplikacím na vašem mobilním zařízení.

Toto téma vám poskytne požadavky a podporované scénáře konfigurace certifikátů na zařízení iOS (Android) pro uživatele klientů v aplikacích Office 365 Enterprise, Business, školství, USA Enterprise, Čína a Německo.

Tato funkce je dostupná ve verzi Preview v Office 365 USA a v federálních plánech.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací Microsoftu

| Aplikace | Podpora |
| --- | --- |
| Aplikace Azure Information Protection |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Portál společnosti Intune |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Microsoft Teams |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneNote |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneDrive |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Outlook |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Power BI |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Skype pro firmy |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Word/Excel/PowerPoint |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Yammer |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |

## <a name="requirements"></a>Požadavky

Verze operačního systému zařízení musí být iOS 9 a vyšší.

Je nutné nakonfigurovat Federační server.

Pro aplikace Office v iOS se vyžaduje Microsoft Authenticator.

Pro Azure Active Directory odvolání klientského certifikátu musí mít token ADFS následující deklarace identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (sériové číslo certifikátu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (řetězec pro vystavitele klientského certifikátu)

Azure Active Directory přidá tyto deklarace do aktualizačního tokenu, pokud jsou k dispozici v tokenu služby AD FS (nebo jakémkoli jiném tokenu SAML). Pokud je nutné ověřit token aktualizace, slouží tyto informace ke kontrole odvolání.

Osvědčeným postupem je aktualizovat chybové stránky ADFS vaší organizace s použitím následujících informací:

* Požadavek na instalaci Microsoft Authenticator v iOS
* Pokyny, jak získat uživatelský certifikát

Další informace najdete v tématu [přizpůsobení AD FS přihlašovací stránky](https://technet.microsoft.com/library/dn280950.aspx).

Některé aplikace Office (s povoleným moderním ověřováním) odesílají do služby Azure AD ve své žádosti "*prompt = Login*". Ve výchozím nastavení Azure AD překládá "*prompt = Login*" v požadavku na ADFS jako '*wauth = usernamepassworduri*' (požádá ADFS, aby provedou ověřování U/P) a '*wfresh = 0*' (požádá službu AD FS, aby ignorovala stav jednotného přihlašování a provede nové ověřování). Pokud chcete pro tyto aplikace povolit ověřování na základě certifikátů, musíte změnit výchozí chování služby Azure AD. Stačí nastavit '*PromptLoginBehavior*' v nastavení federované domény na '*disabled*'.
K provedení této úlohy můžete použít rutinu [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podpora klientů Exchange ActiveSync

V systému iOS 9 nebo novějším se podporuje nativní e-mailový klient iOS. Pro všechny ostatní aplikace Exchange ActiveSync, abyste zjistili, jestli je tato funkce podporovaná, kontaktujte vývojáře aplikace.

## <a name="next-steps"></a>Další kroky

Pokud chcete ve svém prostředí nakonfigurovat ověřování na základě certifikátů, přečtěte si téma Začínáme [s ověřováním pomocí certifikátů v Androidu](../authentication/active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
