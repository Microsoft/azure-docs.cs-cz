---
title: Přihlášení se Azure Active Directory nejenom heslem (Preview)
description: Přihlaste se k Azure AD pomocí klíčů zabezpečení FIDO2 nebo aplikace Microsoft Authenticator (Preview), přihlaste se svým heslem.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac59dbed0ba2766f3367e19c5fddeed52e250a5b
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880159"
---
# <a name="passwordless-authentication-options"></a>Možnosti ověřování s heslem

Multi-Factor Authentication (MFA) je skvělý způsob, jak zabezpečit vaši organizaci, ale uživatelé získají frustrovaní s dodatečnou vrstvou a zapamatují si jejich hesla. Metody ověřování bez hesla jsou pohodlnější, protože heslo se odebírá a nahrazuje něco, co jste vy nebo něco věděli.

|   | Něco, co máte | Něco, co se vám nebo znáte |
| --- | --- | --- |
| Bez hesla | Bezpečnostní klíč zařízení, telefonu nebo Windows 10 | Biometrika nebo PIN |

Každá organizace má při ověřování jiné požadavky. Microsoft nabízí tři možnosti ověřování s nehesly:

- Windows Hello pro firmy 
- Aplikace Microsoft Authenticator 
- FIDO2 klíče zabezpečení

![Ověřování: zabezpečení a pohodlí](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy 

Windows Hello pro firmy je ideální pro informační pracovníky, kteří mají vlastní určený počítač s Windows. Biometrika a kód PIN jsou přímo vázané na počítač uživatele, který brání přístupu od někoho jiného než vlastník. S integrací infrastruktury veřejných klíčů a integrovanou podporou jednotného přihlašování (SSO) nabízí Windows Hello pro firmy jednoduchou a pohodlnou metodu pro bezproblémové přístup k podnikovým prostředkům místně a v cloudu.

[Průvodce plánováním](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello pro firmy se dá využít k rozhodování o typu nasazení Windows Hello pro firmy a možnostech, které budete muset vzít v úvahu.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Umožňuje, aby se telefon od zaměstnance stal metodou ověřování pomocí hesla. Kromě hesla už možná používáte aplikaci Microsoft Authenticator jako pohodlný možnost vícefaktorového ověřování. Ale teď je dostupná jako možnost bez hesla.

![Přihlášení k Microsoft Edge pomocí aplikace Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla tím, že se uživatelům umožní přihlásit se k libovolné platformě nebo prohlížeči, a to tak, že se jim na telefonu zobrazí oznámení, které odpovídá číslu zobrazenému na obrazovce a pak pomocí jejich biometriky ( a potvrďte ji nebo připnout.

## <a name="fido2-security-keys"></a>FIDO2 klíče zabezpečení

Klíče zabezpečení FIDO2 jsou nenáročné metody ověřování bez hesla založené na standardech, které mohou být v libovolném formuláři. Rychlá identita online (FIDO) je otevřený standard pro ověřování neheslem. Umožňuje uživatelům a organizacím využít standard pro přihlášení ke svým prostředkům bez uživatelského jména nebo hesla pomocí externího bezpečnostního klíče nebo klíče platformy integrovaného do zařízení.

Ve verzi Public Preview můžou zaměstnanci použít externí bezpečnostní klíče pro přihlášení ke svým Azure Active Directory připojeným počítačům s Windows 10 (se spuštěnou verzí 1809 nebo vyšší) a k získání jednotného přihlašování ke svým cloudovým prostředkům. Můžou se také přihlásit k podporovaným prohlížečům.

![Přihlaste se k Microsoft Edge pomocí bezpečnostního klíče.](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

I když je k dispozici mnoho klíčů, které jsou FIDO2 certifikovány nástrojem FIDO Alliance, společnost Microsoft vyžaduje některá volitelná rozšíření FIDO2 specifikace pro klienta na protokol CTAP (klient-to-Authenticator), které má dodavatel implementovat, aby bylo zajištěno maximální zabezpečení a nejlepší Využij.

Bezpečnostní klíč **musí** implementovat následující funkce a rozšíření od protokolu FIDO2 CTAP, aby byl kompatibilní s Microsoftem:

| # | Vztah důvěryhodnosti funkcí nebo rozšíření | Proč je tato funkce nebo rozšíření nutná? |
| --- | --- | --- |
| 1\. místo | Rezidentní klíč | Tato funkce umožňuje přenos bezpečnostního klíče, kde se ukládají přihlašovací údaje na klíč zabezpečení. |
| 2 | Kód PIN klienta | Tato funkce umožňuje chránit vaše přihlašovací údaje pomocí druhého faktoru a vztahuje se na klíče zabezpečení, které nemají uživatelské rozhraní. |
| 3 | HMAC – tajný klíč | Toto rozšíření zajišťuje, že se můžete přihlásit k zařízení, když je v režimu online nebo v letadle. |
| 4 | Více účtů na RP | Tato funkce zajišťuje, že můžete použít stejný bezpečnostní klíč v rámci více služeb, jako je například účet Microsoft a Azure Active Directory. |

Následující poskytovatelé nabízejí bezpečnostní klíče FIDO2 různých faktorů, u kterých je známo, že jsou kompatibilní s prostředím bez hesla. Společnost Microsoft doporučuje zákazníkům vyhodnotit vlastnosti zabezpečení těchto klíčů kontaktováním dodavatele i FIDO Alliance.

| Poskytovatel | Kontaktovat |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

Pokud jste dodavatel a chcete v tomto seznamu získat své zařízení, obraťte se na [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

Klíče zabezpečení FIDO2 představují skvělou možnost pro podniky, které jsou velmi citlivé na zabezpečení, nebo které mají scénáře nebo zaměstnanci, kteří nejsou ochotni nebo nedokázali používat svůj telefon jako druhý faktor.

## <a name="what-scenarios-work-with-the-preview"></a>Jaké scénáře fungují s verzí Preview?

- Správci můžou u svého tenanta povolit metody ověřování nejenom heslem.
- Správci můžou cílit na všechny uživatele nebo vybrat uživatele/skupiny v rámci svého tenanta.
- Koncoví uživatelé můžou tyto metody ověřování s heslem zaregistrovat a spravovat na portálu účtů.
- Koncoví uživatelé se můžou přihlásit pomocí těchto metod ověřování bez hesla.
   - Microsoft Authenticator aplikace: budou fungovat ve scénářích, kdy se používá ověřování Azure AD, včetně všech prohlížečů, během instalace Windows 10 z počítače (OOBE) a s integrovanými mobilními aplikacemi v jakémkoli operačním systému.
   - Klíče zabezpečení: budou fungovat na zamykací obrazovce pro Windows 10 verze 1809 nebo vyšší a na webu v podporovaných prohlížečích, jako je Microsoft Edge.

## <a name="next-steps"></a>Další kroky

[Povolení možností passwordlesss klíče zabezpečení FIDO2 ve vaší organizaci](howto-authentication-passwordless-security-key.md)

[Povolení možností pro bezheslem na telefonu ve vaší organizaci](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externí odkazy

[FIDO Alliance](https://fidoalliance.org/)

[Specifikace CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
