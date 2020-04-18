---
title: Ověřování na základě certifikátů v systému iOS – Azure Active Directory
description: Informace o podporovaných scénářích a požadavcích na konfiguraci ověřování na základě certifikátů pro Azure Active Directory v řešeních se zařízeními se systémem iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639633"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Ověřování na základě certifikátu Azure Active Directory v systému iOS

Chcete-li zlepšit zabezpečení, mohou zařízení iOS používat ověřování na základě certifikátu (CBA) k ověření ve službě Azure Active Directory (Azure AD) pomocí klientského certifikátu na svém zařízení při připojování k následujícím aplikacím nebo službám:

* Mobilní aplikace Office, jako je Microsoft Outlook a Microsoft Word
* Klienti Exchange ActiveSync (EAS)

Použití certifikátů eliminuje potřebu zadávat kombinaci uživatelského jména a hesla do určitých aplikací pošty a sady Microsoft Office v mobilním zařízení.

Tento článek podrobně popisuje požadavky a podporované scénáře pro konfiguraci CBA na zařízení se systémem iOS. CBA pro iOS je dostupná ve veřejných cloudech Azure, Microsoft Government Cloud, Microsoft Cloud Germany a Microsoft Azure China 21Vianet.

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

## <a name="requirements"></a>Požadavky

Chcete-li používat CBA s iOS, platí následující požadavky a aspekty:

* Verze operačního systému zařízení musí být iOS 9 nebo vyšší.
* Microsoft Authenticator je vyžadován pro aplikace Office v systému iOS.
* V řetězci klíčů macOS, který obsahuje ověřovací adresu URL serveru ADFS, musí být vytvořena předvolba identity. Další informace najdete [v tématu Vytvoření předvolby identity v přístupu ke klíčence na Macu](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Platí následující požadavky a důležité informace služby ADF (Active Directory Federation Services):

* Server ADFS musí být povolen pro ověřování certifikátů a používat federované ověřování.
* Certifikát musí používat rozšířené použití klíče (EKU) a obsahovat hlavní název uživatele v *alternativním názvu subjektu (hlavní název NT).*

## <a name="configure-adfs"></a>Konfigurace služby ADF

Aby služba Azure AD odvolala klientský certifikát, musí mít token ADFS následující deklarace identity. Azure AD přidá tyto deklarace identity do obnovovacího tokenu, pokud jsou k dispozici v tokenu ADFS (nebo jakýkoli jiný token SAML). Pokud je třeba ověřit obnovovací token, tyto informace se používají ke kontrole odvolání:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- přidejte sériové číslo vašeho klientského certifikátu
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- přidejte řetězec pro vystavitele vašeho klientského certifikátu

Osvědčeným postupem je také aktualizovat chybové stránky služby ADFS vaší organizace následujícími informacemi:

* Požadavek na instalaci Microsoft Authenticator v systému iOS.
* Pokyny k získání uživatelského certifikátu.

Další informace naleznete [v tématu Přizpůsobení přihlašovací stránky ad FS](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Použití moderního ověřování s aplikacemi Office

Některé aplikace Office s `prompt=login` povoleným moderním ověřováním se ve své žádosti odesílají do Azure AD. Ve výchozím nastavení Azure `prompt=login` AD překládá v `wauth=usernamepassworduri` požadavku na ADFS jako (požádá ADFS dělat U / P Auth) a `wfresh=0` (požádá ADFS ignorovat stav přihlašovaného pomocí něj a provést nové ověřování). Pokud chcete povolit ověřování na základě certifikátu pro tyto aplikace, upravte výchozí chování Azure AD.

Chcete-li aktualizovat výchozí chování, nastavte v nastavení federované domény hodnotu*PromptLoginBehavior*na *hodnotu Zakázáno*. K provedení této úlohy můžete použít rutinu [MSOLDomainFederationSettings,](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) jak je znázorněno v následujícím příkladu:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Podpora pro klienty Exchange ActiveSync

V iOS 9 nebo novějším je nativní poštovní klient iOS podporován. Chcete-li zjistit, zda je tato funkce podporována pro všechny ostatní aplikace Exchange ActiveSync, obraťte se na vývojáře aplikace.

## <a name="next-steps"></a>Další kroky

Pokyny najdete v tématu [Začínáme s ověřováním na základě certifikátů.](active-directory-certificate-based-authentication-get-started.md)

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
