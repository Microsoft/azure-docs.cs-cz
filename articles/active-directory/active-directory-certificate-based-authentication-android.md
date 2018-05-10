---
title: Azure Active Directory ověřování prostřednictvím certifikátu v systému Android
description: Další informace o podporované scénáře a požadavky pro konfiguraci ověřování na základě certifikátů v řešení se zařízeními s Androidem
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: ef58fd6c4701f367c6b8664c9cf9ee76e15fbd70
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory ověřování prostřednictvím certifikátu v systému Android

Zařízení se systémem Android můžete použít ověřování pomocí certifikátů (CBA) k ověření do Azure Active Directory pomocí klientského certifikátu na svém zařízení při připojení k:

* Mobilní aplikace Office, jako je například Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Konfigurace tato funkce eliminuje potřebu zadejte kombinace uživatelského jména a hesla do určité e-mailu a aplikace Microsoft Office na vašem mobilním zařízení.

Toto téma poskytuje požadavky a podporované scénáře konfigurace CBA pro zařízení s iOS(Android) pro uživatele klientů v Office 365 Enterprise, Business, Education, US Government, Čína, a plány Německu.

Tato funkce je dostupná ve verzi preview v Office 365 US Government obrany a federální plány.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací Microsoft

| Aplikace | Podpora |
| --- | --- |
| Azure Information Protection aplikace |![Zaškrtnout][1] |
| Portál společnosti Intune |![Zaškrtnout][1] |
| Microsoft Teams |![Zaškrtnout][1] |
| OneNote |![Zaškrtnout][1] |
| OneDrive |![Zaškrtnout][1] |
| Outlook |![Zaškrtnout][1] |
| Power BI |![Zaškrtnout][1] |
| Skype pro firmy |![Zaškrtnout][1] |
| Aplikace Word / Excel / PowerPoint |![Zaškrtnout][1] |
| Yammer |![Zaškrtnout][1] |

### <a name="implementation-requirements"></a>Požadavky na implementaci

Verze operačního systému zařízení musí být Android 5.0 (typu Lupa) a vyšší.

Federační server musí být nakonfigurované.

Pro Azure Active Directory k odvolání certifikátu klienta musí mít tokenu služby AD FS následující deklarace identity:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Sériové číslo certifikátu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (String pro vystavitele certifikátu klienta)

Azure Active Directory přidá tyto deklarace do tokenu obnovení, pokud jsou k dispozici v tokenu služby AD FS (nebo jiné tokenu SAML). Pokud token obnovení, musí se ověřit, tyto informace slouží ke kontrole odvolání.

Jako osvědčený postup by měl aktualizovat chybové stránky služby AD FS vaší organizace s následujícími informacemi:

* Požadavky pro instalaci Microsoft Authenticator pro Android.
* Pokyny, jak získat certifikát uživatele.

Další informace najdete v tématu [přizpůsobení stránek přihlášení AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Odeslat některé aplikace Office (s povolené moderní ověřování),*řádku = přihlášení*se do služby Azure AD v jejich požadavku. Ve výchozím nastavení, Azure AD překládá '*řádku = přihlášení*"v žádosti do AD FS jako'*wauth = usernamepassworduri*' (požádá ADFS udělat ověřování U/P) a"*wfresh = 0*' (požádá služby AD FS na Ignorovat stavu jednotné přihlašování a provést novou ověřování). Pokud chcete povolit ověřování pomocí certifikátů pro tyto aplikace, budete muset upravit výchozí chování Azure AD. Nastavte '*PromptLoginBehavior*"v nastavení federované domény k'*zakázané*'.
Můžete použít [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) rutina k provedení této úlohy:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podporu klientů protokolu Exchange ActiveSync

Jsou podporovány některých aplikací Exchange ActiveSync v systému Android 5.0 (typu Lupa) nebo novější. Pokud chcete zjistit, pokud tuto funkci podporovat e-mailové aplikace, obraťte se na vývojáře vaší aplikace.

## <a name="next-steps"></a>Další postup

Pokud chcete nakonfigurovat ověřování pomocí certifikátů ve vašem prostředí, přečtěte si téma [Začínáme s ověřováním na základě certifikátu v systému Android](active-directory-certificate-based-authentication-get-started.md) pokyny.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
