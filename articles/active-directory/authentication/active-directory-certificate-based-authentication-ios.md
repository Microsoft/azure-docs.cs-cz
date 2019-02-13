---
title: Ověřování Azure Active Directory na základě certifikátů v Iosu
description: Další informace o podporované scénáře a požadavky pro konfiguraci ověřování na základě certifikátů v řešení se zařízeními s Iosem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 717eef0857d6db53462ef1b5eb4786962550d44b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188065"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Ověřování Azure Active Directory na základě certifikátů v Iosu

zařízení se systémem iOS můžete použít k ověření do Azure Active Directory pomocí klientského certifikátu na svých zařízeních při připojování k ověřování prostřednictvím certifikátu (certifikátů):

* Mobilní aplikace Office, jako je například Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Tuto funkci konfiguruje eliminuje nutnost zadat kombinace uživatelského jména a hesla do určité e-mailu a aplikace Microsoft Office na svém mobilním zařízení.

Toto téma obsahuje požadavky a podporované scénáře konfigurace certifikátů pro zařízení s iOS(Android) pro uživatele tenantů v Office 365 Enterprise, obchodní, vzdělávání, pro státní správu USA, Čína, a Německo plány.

Tato funkce je dostupná ve verzi preview v plánech Office 365 Government obrany USA a federální.

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
| Word / Excel / PowerPoint |![Zaškrtnout][1] |
| Yammer |![Zaškrtnout][1] |

## <a name="requirements"></a>Požadavky

Verze operačního systému zařízení musí být iOS 9 a vyšší

Federační server musí být nakonfigurován.

Microsoft Authenticator je vyžadována pro aplikace Office pro iOS.

Pro Azure Active Directory k odvolání klientského certifikátu musí mít token služby AD FS následující deklarace:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Sériové číslo certifikátu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Řetězce pro vystavitele certifikátu klienta)

Azure Active Directory přidá tyto deklarace do tokenu obnovení v případě, že jsou k dispozici v tokenu služby AD FS (nebo jiný token SAML). Pokud token obnovení je potřeba ověřit, tyto informace slouží ke kontrole odvolání.

Jako osvědčený postup měli byste aktualizovat chybové stránky služby AD FS vaší organizace s následujícími informacemi:

* Požadavky pro instalaci aplikace Microsoft Authenticator v Iosu
* Pokyny, jak získat certifikát uživatele.

Další informace najdete v tématu [přizpůsobení stránek služby AD FS přihlásit](https://technet.microsoft.com/library/dn280950.aspx).

Některé aplikace Office (s povolené moderní ověřování) odeslat "*řádku = přihlášení*" do služby Azure AD v žádosti. Ve výchozím nastavení, Azure AD přeloží "*řádku = přihlášení*"v žádosti o služby AD FS jako"*wauth = usernamepassworduri*" (zeptá služby AD FS provést ověření U/P) a "*wfresh = 0*" (zeptá služby AD FS do Ignorovat stav jednotného přihlašování a proveďte nové ověření). Pokud chcete povolit ověřování pomocí certifikátu pro tyto aplikace, budete muset změnit výchozí chování služby Azure AD. Stačí nastavit "*PromptLoginBehavior*'v nastavení federovanou doménu na"*zakázané*".
Můžete použít [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) rutina k provedení této úlohy:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podpora klientů protokolu Exchange ActiveSync

V systému iOS 9 nebo novější se podporuje e-mailového klienta nativní aplikace pro iOS. Pro všechny ostatní aplikace Exchange ActiveSync Chcete-li zjistit, jestli je tato funkce podporuje, obraťte se na vaše aplikace pro vývojáře.

## <a name="next-steps"></a>Další postup

Pokud chcete nakonfigurovat ověřování prostřednictvím certifikátu ve vašem prostředí, přečtěte si téma [Začínáme s ověřováním na základě certifikátů v Androidu](../authentication/active-directory-certificate-based-authentication-get-started.md) pokyny.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
