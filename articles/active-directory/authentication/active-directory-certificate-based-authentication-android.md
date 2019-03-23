---
title: Ověřování pomocí certifikátu v systému Android – Azure Active Directory
description: Další informace o podporovaných scénářích a požadavky pro konfiguraci ověřování na základě certifikátů v řešeních pro zařízení s Androidem
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
ms.openlocfilehash: b55b439f61c76d6d0524c1f01ba5fef745187d04
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368285"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Ověřování Azure Active Directory na základě certifikátů v Androidu

Zařízení s androidem můžete použít k ověření do Azure Active Directory pomocí klientského certifikátu na svých zařízeních při připojování k ověřování prostřednictvím certifikátu (certifikátů):

* Mobilní aplikace Office, jako je například Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Tuto funkci konfiguruje eliminuje nutnost zadat kombinace uživatelského jména a hesla do určité e-mailu a aplikace Microsoft Office na svém mobilním zařízení.

Toto téma obsahuje požadavky a podporované scénáře konfigurace certifikátů pro zařízení s iOS(Android) pro uživatele tenantů v Office 365 Enterprise, obchodní, vzdělávání, pro státní správu USA, Čína, a Německo plány.

Tato funkce je dostupná ve verzi preview v plánech Office 365 Government obrany USA a federální.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací Microsoft

| Aplikace | Podpora |
| --- | --- |
| Azure Information Protection aplikace |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Portál společnosti Intune |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Microsoft Teams |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| OneNote |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| OneDrive |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Outlook |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Power BI |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Skype pro firmy |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Word / Excel / PowerPoint |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |
| Yammer |![Značka zaškrtnutí označuje, podpora pro tuto aplikaci][1] |

### <a name="implementation-requirements"></a>Požadavky na implementaci

Musí být verze operačního systému zařízení s Androidem 5.0 (Lollipop) a vyšší.

Federační server musí být nakonfigurován.

Pro Azure Active Directory k odvolání klientského certifikátu musí mít token služby AD FS následující deklarace:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Sériové číslo certifikátu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Řetězce pro vystavitele certifikátu klienta)

Azure Active Directory přidá tyto deklarace do tokenu obnovení v případě, že jsou k dispozici v tokenu služby AD FS (nebo jiný token SAML). Pokud token obnovení je potřeba ověřit, tyto informace slouží ke kontrole odvolání.

Jako osvědčený postup měli byste aktualizovat chybové stránky služby AD FS vaší organizace s následujícími informacemi:

* Požadavky pro instalaci aplikace Microsoft Authenticator na Androidu.
* Pokyny, jak získat certifikát uživatele.

Další informace najdete v tématu [přizpůsobení stránek služby AD FS přihlásit](https://technet.microsoft.com/library/dn280950.aspx).

Některé aplikace Office (s povolené moderní ověřování) odeslat "*řádku = přihlášení*" do služby Azure AD v žádosti. Ve výchozím nastavení, Azure AD přeloží "*řádku = přihlášení*"v žádosti o služby AD FS jako"*wauth = usernamepassworduri*" (zeptá služby AD FS provést ověření U/P) a "*wfresh = 0*" (zeptá služby AD FS do Ignorovat stav jednotného přihlašování a proveďte nové ověření). Pokud chcete povolit ověřování pomocí certifikátu pro tyto aplikace, budete muset změnit výchozí chování služby Azure AD. Nastavte "*PromptLoginBehavior*'v nastavení federovanou doménu na"*zakázané*".
Můžete použít [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) rutina k provedení této úlohy:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Podpora klientů protokolu Exchange ActiveSync

Některé aplikace Exchange ActiveSync v systému Android 5.0 (Lollipop) nebo novější podporují. Pokud chcete zjistit, pokud tuto funkci podporovat e-mailové aplikace, obraťte se na vaše aplikace pro vývojáře.

## <a name="next-steps"></a>Další postup

Pokud chcete nakonfigurovat ověřování prostřednictvím certifikátu ve vašem prostředí, přečtěte si téma [Začínáme s ověřováním na základě certifikátů v Androidu](active-directory-certificate-based-authentication-get-started.md) pokyny.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
