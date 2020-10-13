---
title: Ověřování založené na certifikátech v iOS – Azure Active Directory
description: Přečtěte si o podporovaných scénářích a požadavcích ke konfiguraci ověřování na základě certifikátů pro Azure Active Directory v řešeních se zařízeními s iOS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa6c28eaa75485e0b45bb4404e685b6ee223b46
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965586"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory ověřování pomocí certifikátů v iOS

Pro zvýšení zabezpečení můžou zařízení s iOS používat ověřování na základě certifikátu (certifikátů) k ověřování Azure Active Directory (Azure AD) pomocí klientského certifikátu na svém zařízení při připojování k následujícím aplikacím nebo službám:

* Mobilní aplikace Office, jako je Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Použití certifikátů eliminuje nutnost zadat kombinaci uživatelského jména a hesla k určitým e-mailovým a systém Microsoft Officem aplikacím na vašem mobilním zařízení.

Tento článek podrobně popisuje požadavky a podporované scénáře konfigurace certifikátů na zařízení s iOS. CERTIFIKÁTŮ pro iOS je k dispozici napříč veřejnými cloudy Azure, cloudem Microsoftu pro státní správu, Microsoft Cloud Německem a Microsoft Azure čínskou organizací 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Podpora mobilních aplikací Microsoftu

| Aplikace | Podpora |
| --- | --- |
| Aplikace Azure Information Protection |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Intune Portál společnosti |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Microsoft Teams |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Office (mobilní zařízení) |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneNote |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| OneDrive |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Outlook |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Power BI |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Skype pro firmy |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Word/Excel/PowerPoint |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |
| Yammer |![Zaškrtněte, Pokud označíte podporu pro tuto aplikaci.][1] |

## <a name="requirements"></a>Požadavky

Pokud chcete používat certifikátů se systémem iOS, platí následující požadavky a doporučení:

* Verze operačního systému zařízení musí být iOS 9 nebo vyšší.
* Pro aplikace Office v iOS se vyžaduje Microsoft Authenticator.
* V řetězci klíčů macOS musí být vytvořená preference identity, která zahrnuje adresu URL ověřování serveru ADFS. Další informace najdete v tématu [Vytvoření preference identity v přístupu k řetězci klíčů na Macu](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Platí následující požadavky a předpoklady pro Active Directory Federation Services (AD FS) (ADFS):

* Pro ověřování pomocí certifikátu musí být povolený server ADFS a používat federované ověřování.
* Certifikát musí používat rozšířené použití klíče (EKU) a obsahovat hlavní název uživatele (UPN *) v alternativním názvu subjektu (hlavní název systému NT)*.

## <a name="configure-adfs"></a>Konfigurace služby ADFS

Aby služba Azure AD mohla odvolat klientský certifikát, musí mít token ADFS následující deklarace identity. Azure AD přidá tyto deklarace do aktualizačního tokenu, pokud jsou k dispozici v tokenu služby AD FS (nebo jakémkoli jiném tokenu SAML). Pokud je nutné ověřit token aktualizace, slouží tyto informace ke kontrole odvolání:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -přidat sériové číslo vašeho klientského certifikátu
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` – Přidejte řetězec pro vystavitele klientského certifikátu.

Osvědčeným postupem je, že byste měli aktualizovat také chybové stránky ADFS vaší organizace s následujícími informacemi:

* Požadavek na instalaci Microsoft Authenticator v systému iOS.
* Pokyny, jak získat uživatelský certifikát

Další informace najdete v tématu [přizpůsobení AD FS přihlašovací stránky](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Použití moderního ověřování s aplikacemi Office

Některé aplikace Office s povoleným moderním ověřováním odesílají `prompt=login` do služby Azure AD v jejich žádosti. Ve výchozím nastavení Azure AD překládá `prompt=login` v žádosti na službu AD FS `wauth=usernamepassworduri` (požádá službu AD FS, aby provedou ověřování U/P), a `wfresh=0` (POžádá službu ADFS, aby ignorovala stav jednotného přihlašování a provede nové ověřování). Pokud chcete pro tyto aplikace povolit ověřování na základě certifikátů, upravte výchozí chování služby Azure AD.

Chcete-li aktualizovat výchozí chování, nastavte '*PromptLoginBehavior*' v nastavení federované domény na *disabled*. K provedení této úlohy můžete použít rutinu [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) , jak je znázorněno v následujícím příkladu:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Podpora pro klienty Exchange ActiveSync

V systému iOS 9 nebo novějším se podporuje nativní e-mailový klient iOS. Pokud chcete zjistit, jestli je tato funkce podporovaná pro všechny ostatní aplikace Exchange ActiveSync, obraťte se na vývojáře aplikace.

## <a name="next-steps"></a>Další kroky

Pokyny ke konfiguraci ověřování na základě certifikátů v prostředí najdete v tématu Začínáme [s ověřováním na základě certifikátů](active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png