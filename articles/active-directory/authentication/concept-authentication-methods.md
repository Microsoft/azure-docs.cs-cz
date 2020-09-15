---
title: Metody a funkce ověřování – Azure Active Directory
description: Přečtěte si o různých metodách ověřování a funkcích, které jsou k dispozici v Azure Active Directory, které vám pomůžou zlepšit a zabezpečit události přihlášení.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: dcbfd05df84e32423df425f3bdd231a26e4f3bca
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527036"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jaké metody ověřování jsou k dispozici v Azure Active Directory?

V rámci přihlašovacího prostředí pro účty v Azure Active Directory (Azure AD) existují různé způsoby, jak se může uživatel sami ověřit. Uživatelské jméno a heslo představují nejběžnější způsob, jak by uživatel historicky poskytoval přihlašovací údaje. Díky modernímu ověřování a funkcím zabezpečení ve službě Azure AD by mělo být základní heslo doplněno nebo nahrazeno bezpečnějšími metodami ověřování.

![Tabulka sílu a upřednostňovaných metod ověřování ve službě Azure AD](media/concept-authentication-methods/authentication-methods.png)

Metody ověřování bez hesla, jako jsou Windows Hello, FIDO2 Security Keys a aplikace Microsoft Authenticator poskytují nejbezpečnější přihlašovací události.

Azure Multi-Factor Authentication zvyšuje zabezpečení při použití hesla, když se uživatel přihlásí. Uživateli se může zobrazit výzva k zadání dalších forem ověřování, jako je třeba reakce na nabízené oznámení, zadání kódu ze softwaru nebo hardwarového tokenu nebo reakce na volání SMS nebo telefon.

Pro zjednodušení možnosti zprovoznění uživatelů a registraci pro MFA i SSPR doporučujeme [Povolit registraci kombinovaných zabezpečení informací](howto-registration-mfa-sspr-combined.md). Pro odolnost proti chybám doporučujeme, abyste uživatelům vyžadovali registraci více metod ověřování. Když je jedna metoda pro uživatele během přihlašování nebo SSPR k dispozici, může se rozhodnout ověřit jinou metodu. Další informace najdete v tématu [Vytvoření odolné strategie správy řízení přístupu v Azure AD](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Síla a zabezpečení metody ověřování

Když nasadíte funkce jako Azure Multi-Factor Authentication ve vaší organizaci, Projděte si dostupné metody ověřování. Vyberte metody, které splňují nebo překračují vaše požadavky z hlediska zabezpečení, použitelnosti a dostupnosti. Pokud je to možné, použijte metody ověřování s nejvyšší úrovní zabezpečení.

Následující tabulka popisuje požadavky na zabezpečení pro dostupné metody ověřování. Dostupnost je označením uživatele, který může používat metodu ověřování, a nikoli dostupnost služby ve službě Azure AD:

| Metoda ověřování       | Zabezpečení | Použitelnost | Phisable? | Kanál je k disstěně? | Dostupnost |
|-----------------------------|:--------:|:---------:|:---------:|:-----------------:|:------------:|
| FIDO2 klíč zabezpečení          | Vysoké     | Vysoké      | No        | No                | Vysoké         |
| Aplikace Microsoft Authenticator | Vysoké     | Vysoké      | Yes       | Ne <sup>1</sup>   | Vysoké         |
| Windows Hello pro firmy  | Vysoké     | Vysoké      | No        | No                | Vysoké         |
| Tokeny OATH hardwaru        | Střední   | Střední    | Yes       | No                | Vysoké         |
| Tokeny OATH softwaru        | Střední   | Střední    | Yes       | Ne <sup>2</sup>   | Vysoké         |
| SMS                         | Střední   | Vysoké      | Yes       | Yes               | Střední       |
| Hlas                       | Střední   | Střední    | Yes       | Yes               | Střední       |
| Heslo                    | Nízká      | Vysoká      | Yes       | Yes               | Vysoké         |

<sup>1</sup> v režimu bez hesla, když je aplikace registrovaná na určitém zařízení<br />
<sup>2</sup> za předpokladu, že aplikace vyžaduje, aby se k odemknutí připnout zařízení

Další informace o chybách zabezpečení a vektorech útoků najdete v tématu [kanály – konektory a útoky phishing v reálném čase](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Pro flexibilitu a použitelnost doporučujeme, abyste používali aplikaci Microsoft Authenticator. Tato metoda ověřování poskytuje nejlepší uživatelské prostředí a několik režimů, jako je například bez hesla, nabízená oznámení MFA a kódy OATH.

## <a name="how-each-authentication-method-works"></a>Jak funguje každá metoda ověřování

Některé metody ověřování lze použít jako primární faktor při přihlášení k aplikaci nebo zařízení, jako je například použití klíče zabezpečení FIDO2 nebo hesla. Další metody ověřování jsou dostupné jenom jako sekundární faktor, pokud používáte Azure Multi-Factor Authentication nebo SSPR.

Následující tabulka popisuje, kdy lze použít metodu ověřování během události přihlášení:

| Metoda                         | Primární ověření | Sekundární ověřování  |
|--------------------------------|:----------------------:|:-------------------------:|
| FIDO2 klíče zabezpečení (Preview)  | Yes                    | VÍCEFAKTOROVÉHO                       |
| Aplikace Microsoft Authenticator    | Ano (Preview)          | MFA a SSPR              |
| Windows Hello pro firmy     | Yes                    | VÍCEFAKTOROVÉHO                       |
| Hardwarové tokeny OATH (Preview) | No                     | VÍCEFAKTOROVÉHO                       |
| Tokeny softwaru OATH           | No                     | VÍCEFAKTOROVÉHO                       |
| SMS                            | Ano (Preview)          | MFA a SSPR              |
| Hlasový hovor                     | No                     | MFA a SSPR              |
| Heslo                       | Yes                    |                           |

Všechny tyto metody ověřování se dají nakonfigurovat v Azure Portal a stále používají [Microsoft Graph REST API beta verzi](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Další informace o tom, jak každá metoda ověřování funguje, najdete v následujících samostatných koncepčních článcích:

* [FIDO2 klíče zabezpečení (Preview)](concept-authentication-passwordless.md#fido2-security-keys)
* [Aplikace Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Tokeny softwaru OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Hardwarové tokeny OATH (Preview)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Přihlášení k serveru SMS (Preview)](howto-authentication-sms-signin.md) a [ověření](concept-authentication-phone-options.md#mobile-phone-verification)
* [Ověřování hlasových hovorů](concept-authentication-phone-options.md)
* Heslo

> [!NOTE]
> Ve službě Azure AD je heslo často jednou z primárních metod ověřování. Nemůžete zakázat metodu ověřování hesla. Pokud jako primární faktor ověřování použijete heslo, zvyšte zabezpečení událostí přihlášení pomocí Azure Multi-Factor Authentication.

V některých scénářích lze použít následující dodatečné metody ověřování:

* [Hesla aplikací](howto-mfa-app-passwords.md) – používá se pro staré aplikace, které nepodporují moderní ověřování a dají se nakonfigurovat pro Multi-Factor Authentication Azure pro jednotlivé uživatele.
* [Bezpečnostní otázky](concept-authentication-security-questions.md) – používá se jenom pro SSPR.
* [E-mailová adresa](concept-sspr-howitworks.md#authentication-methods) – používá se jenom pro SSPR

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v [kurzu samoobslužného resetování hesla (SSPR)][tutorial-sspr] a [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Další informace o konceptech SSPR najdete v tématu [Jak funguje Samoobslužné resetování hesla služby Azure AD][concept-sspr].

Další informace o principech vícefaktorového ověřování najdete v tématu [Jak funguje Azure Multi-Factor Authentication][concept-mfa].

Přečtěte si další informace o konfiguraci metod ověřování pomocí [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
