---
title: Azure Active Directory passwordless přihlášení (preview)
description: Passwordless přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 nebo aplikace Microsoft Authenticator (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712082"
---
# <a name="what-is-passwordless"></a>Co je passwordless?

Vícefaktorové ověřování (MFA) je skvělý způsob, jak zabezpečit vaši organizaci, ale uživatelé získají frustrovaní s další vrstvu nad museli pamatovat si hesla. Metody ověřování passwordless jsou pohodlnější, protože heslo je odebrán a nahrazen parametrem něco, co máte navíc něco, co jste nebo něco, co znáte.

|   | Něco, co máte | Něco se nebo vědět |
| --- | --- | --- |
| Bez hesla | Klíč telefonního nebo zabezpečení | Biometriku nebo PIN kód |

Uvědomujeme, že každá organizace má jiné potřeby, pokud jde o ověřování. Společnost Microsoft aktuálně nabízí Windows Hello, náš přední passwordless prostředí u počítačů s Windows. Přidali jsme nové přihlašovací údaje passwordless řady: Aplikace Microsoft Authenticator a FIDO2 zabezpečení klíčů.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Povolte nadřízeného zaměstnance telefon se metoda passwordless ověřování. Už využíváte aplikaci Microsoft Authenticator jako možnost pohodlný ověřování službou Multi-Factor Authentication kromě hesla. Ale nyní je k dispozici jako passwordless možnost.

Změní se žádné iOS nebo telefon s Androidem na silné a passwordless pověření tím, že uživatelům umožní přihlásit na jakoukoli platformu a prohlížeč získávání upozornění na svůj telefon, odpovídající číslo zobrazené na obrazovce a v telefonu a následným použitím jejich biometrické) touch nebo identifikace tváře a) nebo PIN kód k potvrzení.

## <a name="fido2-security-keys"></a>FIDO2 zabezpečení klíčů

Metoda unphishable založené na standardech passwordless ověřování, která můžou mít libovolné provedení jsou FIDO2 zabezpečení klíče. Rychlé identitu Online (FIDO) je otevřený standard passwordless ověřování. Umožňuje uživatelům a organizacím využívat standardní pro přihlášení ke svým prostředkům bez uživatelského jména a hesla použijte externí bezpečnostní klíč nebo klíč platformy Integrovaná zařízení.

Zaměstnanci ve veřejné verzi preview, můžete použít externí zabezpečení klíčů pro přihlášení ke svým počítačům Azure Active Directory Windows 10 připojená k (s verzí 1809 nebo vyšší) a získat jednotné přihlašování do cloudových prostředků. Můžete také přihlásí k podporovaných prohlížečů.

I když existují mnoho klíče, které jsou FIDO2 certifikovaný FIDO Alliance, Microsoft vyžaduje od některé volitelné rozšíření specifikaci FIDO2 CTAP k implementaci dodavatelem pro zajištění maximálního zabezpečení a dosažení co nejlepších výsledků.

Klíč zabezpečení **musí** implementovat následující funkce a rozšíření z protokolu FIDO2 CTAP být kompatibilní se službou Microsoft:

| # | Funkce / důvěřovat rozšíření | Proč je to se vyžaduje? |
| --- | --- | --- |
| 1 | Rezidentní klíč | Tato funkce umožňuje bezpečnostní klíč k být portable, přičemž svoje přihlašovací údaje jsou uloženy na klíč zabezpečení. |
| 2 | PIN kód klienta | Tato funkce umožňuje ochraně vašich přihlašovacích údajů pomocí druhého faktoru a platí pro zabezpečení klíčů, které nemají uživatelské rozhraní. |
| 3 | hmac-secret | Toto rozšíření se zajistí, že se že můžete se přihlásit k zařízení při je offline nebo v režim v letadle. |
| 4 | Více účtů na poskytovatele prostředků | Tato funkce zajišťuje, že používáte stejný klíč zabezpečení napříč několika službami, jako jsou Microsoft Account a Azure Active Directory. |

Následující poskytovatelé nabízejí různé typy zařízení, které jsou známé jako kompatibilní s prostředím paswordless FIDO2 zabezpečení klíče. Společnost Microsoft doporučuje zákazníkům kontaktovat dodavatele, stejně jako FIDO Alliance a vyhodnocení zabezpečení vlastnosti těchto klíčů.

| Poskytovatel | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Pokud jste dodavatel a chcete, aby vaše zařízení v tomto seznamu, obraťte se na [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

FIDO2 zabezpečení klíče jsou skvělou možnost pro podniky, které jsou velmi citlivé z hlediska zabezpečení nebo mít scénáře nebo zaměstnancům, kteří nejsou ochoten nebo schopen jejich telefon používat jako druhý faktor.

## <a name="what-scenarios-work-with-the-preview"></a>Jaké scénáře fungovat s verzí preview?

1. Správci mohou povolit metody passwordless ověřování pro svého tenanta
1. Správci mohou cílit na všechny uživatele nebo vyberte uživatele nebo skupiny v rámci svého tenanta pro každou metodu
1. Koncovým uživatelům můžete registrovat a spravovat tyto metody passwordless ověřování na portálu účtu
1. Koncoví uživatelé můžou přihlásit se pomocí těchto metod passwordless ověřování
   1. Aplikace Microsoft Authenticator: Bude práce v každý scénář, ve kterém se používá ověřování Azure AD, včetně ve všech prohlížečích, během Windows 10 si nastavení pole (OOBE) a s integrovat mobilní aplikace pro všechny operační systémy.
   1. Zabezpečení klíčů: Bude fungovat na zamykací obrazovce Windows 10 verze 1809 nebo vyšší a web v podporovaných prohlížečích, jako je Microsoft Edge.

## <a name="next-steps"></a>Další postup

[Povolit passwordless možnosti ve vaší organizaci](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Externí odkazy

[FIDO Alliance](https://fidoalliance.org/)

[Specifikace FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
