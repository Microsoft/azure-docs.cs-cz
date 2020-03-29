---
title: Ověřování založené na certifikátech Android – Azure Active Directory
description: Informace o podporovaných scénářích a požadavcích na konfiguraci ověřování na základě certifikátů v řešeních se zařízeními Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9f38c160a72b6c414cdde7d29e4056a4068cdc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848812"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Ověřování na základě certifikátu Azure Active Directory v systému Android

Zařízení s Androidem můžou při připojování k zařízením s certifikátem (CBA) k ověřování ve službě Azure Active Directory pomocí klientského certifikátu na svém zařízení použít ověřování na základě certifikátu (CBA)

* Mobilní aplikace Office, jako je Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Konfigurace této funkce eliminuje potřebu zadávat kombinaci uživatelského jména a hesla do určitých aplikací pošty a sady Microsoft Office v mobilním zařízení.

Toto téma obsahuje požadavky a podporované scénáře pro konfiguraci CBA na zařízení se systémem iOS (Android) pro uživatele klientů v plánech Office 365 Enterprise, Business, Education, US Government, China a Germany.

Tato funkce je dostupná ve verzi Preview v plánech Office 365 Us Government Defense a Federal.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací společnosti Microsoft

| Aplikace | Podpora |
| --- | --- |
| Aplikace Azure Information Protection |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Intune Portál společnosti |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Microsoft Teams |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| OneNote |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| OneDrive |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Outlook |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Power BI |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Skype pro firmy |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Word / Excel / PowerPoint |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |
| Yammer |![Zaškrtněte značku označující podporu pro tuto aplikaci.][1] |

### <a name="implementation-requirements"></a>Prováděcí požadavky

Verze operačního systému zařízení musí být Android 5.0 (Lollipop) a vyšší.

Federační server musí být nakonfigurován.

Aby služba Azure Active Directory mohla odvolat klientský certifikát, musí mít token ADFS následující deklarace identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Sériové číslo klientského certifikátu)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Řetězec pro vystavitele klientského certifikátu)

Azure Active Directory přidá tyto deklarace identity do obnovovacího tokenu, pokud jsou k dispozici v tokenu ADFS (nebo jiném tokenu SAML). Pokud je třeba ověřit obnovovací token, tyto informace se používají ke kontrole odvolání.

Jako osvědčený postup byste měli aktualizovat chybové stránky služby ADFS vaší organizace následujícími informacemi:

* Požadavek na instalaci Microsoft Authenticator v systému Android.
* Pokyny k získání uživatelského certifikátu.

Další informace naleznete [v tématu Přizpůsobení přihlašovacích stránek ve sestavě AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Některé aplikace Office (s povoleným moderním ověřováním) odesílají do Azure AD ve své žádosti*výzvu=přihlášení.* Ve výchozím nastavení Azure AD překládá '*prompt=login*' v požadavku na ADFS jako '*wauth=usernamepassworduri*' (požádá ADFS k tomu U / P Auth) a '*wfresh = 0*' (požádá ADFS ignorovat stav přihlašovaného k as a provést nové ověřování). Pokud chcete povolit ověřování na základě certifikátu pro tyto aplikace, je třeba upravit výchozí chování Azure AD. Nastavte v nastavení federované domény příkaz*PromptLoginBehavior*na '*Zakázáno*'.
K provedení tohoto úkolu můžete použít rutinu [MSOLDomainFederationSettings:](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podpora klientů Exchange ActiveSync

Některé aplikace Exchange ActiveSync v systému Android 5.0 (Lollipop) nebo novější jsou podporovány. Chcete-li zjistit, zda vaše e-mailová aplikace tuto funkci podporuje, obraťte se na vývojáře aplikace.

## <a name="next-steps"></a>Další kroky

Pokud chcete ve svém prostředí nakonfigurovat ověřování na základě certifikátů, přečtěte si pokyny v tématu [Začínáme s ověřováním na základě certifikátu v systému Android.](active-directory-certificate-based-authentication-get-started.md)

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
