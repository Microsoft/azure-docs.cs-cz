---
title: Azure Active Directory přihlašování se nehesly (Preview)
description: Přečtěte si o možnostech pro přihlášení k neheslům Azure Active Directory pomocí klíčů zabezpečení FIDO2 nebo Microsoft Authenticator aplikace.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505769"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Možnosti ověřování neheslem pro Azure Active Directory

Multi-Factor Authentication (MFA) je skvělý způsob, jak zabezpečit vaši organizaci, ale uživatelé často získají frustrovaní s dodatečnou vrstvou zabezpečení, a to nad tím, že si hesla zapamatují. Metody ověřování bez hesla jsou pohodlnější, protože heslo se odebírá a nahrazuje něco, co máte, a něco vás nebo něco znáte.

|   | Něco, co máte | Něco, co se vám nebo znáte |
| --- | --- | --- |
| Bez hesla | Bezpečnostní klíč zařízení, telefonu nebo Windows 10 | Biometrika nebo PIN |

Každá organizace má při ověřování jiné požadavky. Microsoft nabízí následující tři možnosti ověřování s nehesly:

- Windows Hello pro firmy
- Aplikace Microsoft Authenticator
- FIDO2 klíče zabezpečení

![Ověřování: zabezpečení a pohodlí](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Windows Hello pro firmy je ideální pro informační pracovníky, kteří mají vlastní určený počítač s Windows. Biometrika a kód PIN jsou přímo vázané na počítač uživatele, který brání přístupu od jiných osob než vlastník. S integrací infrastruktury veřejných klíčů (PKI) a integrovanou podporou jednotného přihlašování (SSO) nabízí Windows Hello pro firmy pohodlný způsob, jak bezproblémově přistupovat k podnikovým prostředkům místně a v cloudu.

[Průvodce plánováním](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello pro firmy se dá využít k rozhodování o typu nasazení Windows Hello pro firmy a možnostech, které budete muset vzít v úvahu.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Umožňuje, aby se telefon od zaměstnance stal metodou ověřování pomocí hesla. Kromě hesla už možná používáte aplikaci Microsoft Authenticator jako pohodlný možnost vícefaktorového ověřování. Ověřovací aplikaci můžete použít také jako možnost s neplatnými hesly.

![Přihlášení k Microsoft Edge pomocí aplikace Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikace ověřovatele přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla. Uživatelé se můžou přihlašovat k libovolné platformě nebo prohlížeči tím, že získají oznámení na telefonu, odpovídají číslu zobrazenému na obrazovce na telefonu a pak se pomocí jejich biometriky (dotyky nebo obličeje) nebo kódu PIN ověří.

## <a name="fido2-security-keys"></a>FIDO2 klíče zabezpečení

Klíče zabezpečení FIDO2 jsou nenáročné metody ověřování bez hesla založené na standardech, které mohou být v libovolném formuláři. Rychlá identita online (FIDO) je otevřený standard pro ověřování neheslem. FIDO umožňuje uživatelům a organizacím využít standard pro přihlášení ke svým prostředkům bez uživatelského jména a hesla pomocí externího bezpečnostního klíče nebo klíče platformy integrovaného do zařízení.

Ve verzi Public Preview můžou zaměstnanci použít bezpečnostní klíče pro přihlášení ke svým zařízením s Windows 10 připojeným k Azure AD nebo k hybridnímu připojení Azure AD a získat jednotné přihlášení ke svým cloudovým a místním prostředkům. Uživatelé se také můžou přihlašovat k podporovaným prohlížečům. Klíče zabezpečení FIDO2 představují skvělou možnost pro podniky, které jsou velmi citlivé na zabezpečení, nebo které mají scénáře nebo zaměstnanci, kteří nejsou ochotni nebo nedokázali používat svůj telefon jako druhý faktor.

![Přihlaste se k Microsoft Edge pomocí bezpečnostního klíče.](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

I když je k dispozici mnoho klíčů, které jsou FIDO2 certifikovány nástrojem FIDO Alliance, společnost Microsoft vyžaduje některá volitelná rozšíření FIDO2 specifikace pro klienta na protokol CTAP (klient-to-Authenticator), které má dodavatel implementovat, aby bylo zajištěno maximální zabezpečení a nejlepší Využij.

Bezpečnostní klíč **musí** implementovat následující funkce a rozšíření od protokolu FIDO2 CTAP, aby byl kompatibilní s Microsoftem:

| # | Vztah důvěryhodnosti funkcí nebo rozšíření | Proč je tato funkce nebo rozšíření nutná? |
| --- | --- | --- |
| 1 | Rezidentní klíč | Tato funkce umožňuje přenos bezpečnostního klíče, kde se ukládají přihlašovací údaje na klíč zabezpečení. |
| 2 | Kód PIN klienta | Tato funkce umožňuje chránit vaše přihlašovací údaje pomocí druhého faktoru a vztahuje se na klíče zabezpečení, které nemají uživatelské rozhraní. |
| 3 | hmac-secret | Toto rozšíření zajišťuje, že se můžete přihlásit k zařízení, když je v režimu online nebo v letadle. |
| 4 | Více účtů na RP | Tato funkce zajišťuje, že můžete použít stejný bezpečnostní klíč v rámci více služeb, jako je například účet Microsoft a Azure Active Directory. |

Následující poskytovatelé nabízejí bezpečnostní klíče FIDO2 různých faktorů, u kterých je známo, že jsou kompatibilní s prostředím bez hesla. Pro vyhodnocení vlastností zabezpečení těchto klíčů doporučujeme, abyste se obrátili na dodavatele i na FIDO Alliance.

| Zprostředkovatel | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Pokud si koupíte a plánujete používat klíče zabezpečení založené na technologii NFC, budete pro klíč zabezpečení potřebovat podporovanou čtečku NFC. Čtečka NFC není požadavkem nebo omezením Azure. Seznam podporovaných čtecích zařízení NFC vám poskytne dodavatel s klíčem zabezpečení na NFC.

Pokud jste dodavatel a chcete v tomto seznamu podporovaných zařízení získat své zařízení, kontaktujte [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Jaké scénáře fungují s verzí Preview?

- Správci můžou u svého tenanta povolit metody ověřování nejenom heslem.
- Správci můžou cílit na všechny uživatele nebo vybrat uživatele/skupiny v rámci svého tenanta.
- Koncoví uživatelé můžou tyto metody ověřování s heslem zaregistrovat a spravovat na portálu účtů.
- Koncoví uživatelé se můžou přihlásit pomocí těchto metod ověřování bez hesla.
   - Microsoft Authenticator aplikace: funguje v situacích, kdy se používá ověřování Azure AD, včetně všech prohlížečů, během instalace systému Windows 10 (OOBE) a s integrovanými mobilními aplikacemi v jakémkoli operačním systému.
   - Klíče zabezpečení: Pracujte na zamykací obrazovce pro Windows 10 a na webu v podporovaných prohlížečích, jako je Microsoft Edge.

## <a name="next-steps"></a>Další kroky

[Povolení možností passwordlesss klíče zabezpečení FIDO2 ve vaší organizaci](howto-authentication-passwordless-security-key.md)

[Povolení možností pro bezheslem na telefonu ve vaší organizaci](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externí odkazy

[FIDO Alliance](https://fidoalliance.org/)

[Specifikace CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
