---
title: Azure Active Directory přihlašování se nehesly (Preview)
description: Přečtěte si o možnostech pro přihlášení k neheslům Azure Active Directory pomocí klíčů zabezpečení FIDO2 nebo Microsoft Authenticator aplikace.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b4ac8f87e8e19d3487859849ba37272c501751d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744376"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Možnosti ověřování neheslem pro Azure Active Directory

Funkce, jako je Multi-Factor Authentication (MFA), jsou skvělým způsobem, jak zabezpečit vaši organizaci, ale uživatelé často získají frustrovaní s dodatečnou vrstvou zabezpečení, která si musí pamatovat hesla. Metody ověřování bez hesla jsou pohodlnější, protože heslo se odebírá a nahrazuje něco, co máte, a něco vás nebo něco znáte.

| Ověřování  | Něco, co máte | Něco, co se vám nebo znáte |
| --- | --- | --- |
| Bez hesla | Bezpečnostní klíč zařízení, telefonu nebo Windows 10 | Biometrika nebo PIN |

Každá organizace má při ověřování jiné požadavky. Microsoft nabízí následující tři možnosti ověřování bez hesla, které se integrují s Azure Active Directory (Azure AD):

- Windows Hello pro firmy
- Aplikace Microsoft Authenticator
- FIDO2 klíče zabezpečení

![Ověřování: zabezpečení a pohodlí](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Windows Hello pro firmy je ideální pro informační pracovníky, kteří mají vlastní určený počítač s Windows. Pověření biometriky a PIN kódu jsou přímo vázaná na počítač uživatele, který brání přístupu od jiných osob než vlastník. S integrací infrastruktury veřejných klíčů (PKI) a integrovanou podporou jednotného přihlašování (SSO) nabízí Windows Hello pro firmy pohodlný způsob, jak bezproblémově přistupovat k podnikovým prostředkům místně a v cloudu.

![Příklad přihlašování uživatelů pomocí Windows Hello pro firmy](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Následující kroky ukazují, jak proces přihlášení funguje se službou Azure AD:

![Diagram, který popisuje kroky týkající se přihlašování uživatelů pomocí Windows Hello pro firmy](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Uživatel se do systému Windows přihlásí pomocí gesta biometriky nebo PIN kódu. Gesto odemkne privátní klíč Windows Hello pro firmy a pošle se do zprostředkovatele podpory zabezpečení Cloud Authentication, který se označuje jako *poskytovatel cloudového přístupového bodu*.
1. Zprostředkovatel cloudového přístupového bodu vyžaduje hodnotu NONCE (náhodné libovolné číslo, které lze použít pouze jednou) z Azure AD.
1. Služba Azure AD vrátí hodnotu NONCE platnou po dobu 5 minut.
1. Poskytovatel cloudového AP podepíše hodnotu NONCE pomocí privátního klíče uživatele a vrátí hodnotu podepsaná hodnota v rámci služby Azure AD.
1. Azure AD ověří podepsanou hodnota nonce pomocí zabezpečeného registrovaného veřejného klíče uživatele s podpisem hodnoty nonce. Po ověření signatury ověří služba Azure AD vrácenou znaménku nonce. Když se hodnota nonce ověří, Azure AD vytvoří primární obnovovací token (PRT) s klíčem relace, který se zašifruje na transportní klíč zařízení a vrátí ho do poskytovatele cloudového AP.
1. Zprostředkovatel cloudového AP obdrží šifrované PRT s klíčem relace. Pomocí privátního transportního klíče zařízení dešifruje zprostředkovatel cloudového AP klíč relace a chrání klíč relace pomocí čipu TPM (Trusted Platform Module) daného zařízení.
1. Zprostředkovatel cloudového AP vrátí úspěšnou odpověď ověření systému Windows. Uživatel pak může získat přístup k Windows i k cloudovým a místním aplikacím, aniž by bylo nutné znovu ověřovat (SSO).

[Průvodce plánováním](/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello pro firmy se dá využít k rozhodování o typu nasazení Windows Hello pro firmy a možnostech, které budete muset vzít v úvahu.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Můžete taky dovolit, aby se telefon od zaměstnance stal metodou ověřování pomocí hesla. Kromě hesla už možná používáte aplikaci Microsoft Authenticator jako pohodlný možnost vícefaktorového ověřování. Ověřovací aplikaci můžete použít také jako možnost s neplatnými hesly.

![Přihlášení k Microsoft Edge pomocí aplikace Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikace ověřovatele přepíná telefon s iOS nebo Androidem do silných přihlašovacích údajů bez hesla. Uživatelé se můžou přihlašovat k libovolné platformě nebo prohlížeči tím, že získají oznámení na telefonu, odpovídají číslu zobrazenému na obrazovce na telefonu a pak se pomocí jejich biometriky (dotyky nebo obličeje) nebo kódu PIN ověří. Podrobnosti o instalaci najdete [v tématu Stažení a instalace aplikace Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) .

Přihlášení bez hesla pomocí aplikace Microsoft Authenticator do služby Azure AD je momentálně ve verzi Preview. Použití aplikace Microsoft Authenticator pro sekundární ověřování pro Multi-Factor Authentication Azure AD, Samoobslužné resetování hesla (SSPR) nebo softwarové tokeny OATH jsou GA. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Ověřování pomocí hesla s použitím ověřovací aplikace se řídí stejným základním vzorem jako Windows Hello pro firmy. Je to trochu složitější, protože je potřeba identifikovat uživatele, aby služba Azure AD mohla najít Microsoft Authenticator verzi aplikace, která se používá:

![Diagram, který popisuje kroky týkající se přihlašování uživatelů pomocí aplikace Microsoft Authenticator](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Uživatel zadá své uživatelské jméno.
1. Azure AD zjistí, že uživatel má silné přihlašovací údaje a spustí tok silných přihlašovacích údajů.
1. Do aplikace se pošle oznámení přes Apple Push Notification Service (APNS) na zařízeních s iOS nebo přes Firebase Cloud Messaging (FCM) na zařízeních s Androidem.
1. Uživatel obdrží nabízené oznámení a otevře aplikaci.
1. Aplikace volá Azure AD a obdrží výzvu a hodnotu NONCE pro kontrolu přítomnosti.
1. Uživatel dokončí výzvu zadáním jejich biometriky nebo PIN kódu k odemknutí privátního klíče.
1. Hodnota nonce je podepsaná privátním klíčem a pošle zpátky do služby Azure AD.
1. Azure AD provede ověření veřejného a privátního klíče a vrátí token.

Pokud chcete začít přihlašovat bez hesla, proveďte následující kroky:

> [!div class="nextstepaction"]
> [Povolení podepisování pomocí hesla s použitím ověřovací aplikace](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 klíče zabezpečení

FIDO (Fast IDentity online) Alliance pomáhá zvýšit úroveň otevřených standardů ověřování a snížit používání hesel jako formy ověřování. FIDO2 je nejnovější standard, který zahrnuje standard webového ověřování (WebAuthn).

Klíče zabezpečení FIDO2 jsou nenáročné metody ověřování bez hesla založené na standardech, které mohou být v libovolném formuláři. Rychlá identita online (FIDO) je otevřený standard pro ověřování neheslem. FIDO umožňuje uživatelům a organizacím využít standard pro přihlášení ke svým prostředkům bez uživatelského jména a hesla pomocí externího bezpečnostního klíče nebo klíče platformy integrovaného do zařízení.

Uživatelé můžou zaregistrovat a pak vybrat FIDO2 klíč zabezpečení v přihlašovacím rozhraní jako hlavní způsob ověřování. Tyto klíče zabezpečení FIDO2 jsou obvykle zařízeními USB, ale mohou také používat Bluetooth nebo NFC. V případě hardwarového zařízení, které zpracovává ověřování, se zabezpečení účtu zvyšuje, protože není k dispozici žádné heslo, které by mohlo být zveřejněné nebo odhadované.

Bezpečnostní klíče FIDO2 se dají použít k přihlášení ke svým zařízením s Windows 10 připojeným k Azure AD nebo k hybridnímu připojení k Azure AD a k získání jednotného přihlašování ke svým cloudovým a místním prostředkům. Uživatelé se také můžou přihlašovat k podporovaným prohlížečům. Klíče zabezpečení FIDO2 představují skvělou možnost pro podniky, které jsou velmi citlivé na zabezpečení, nebo které mají scénáře nebo zaměstnanci, kteří nejsou ochotni nebo nedokázali používat svůj telefon jako druhý faktor.

Přihlášení pomocí klíčů zabezpečení FIDO2 do služby Azure AD je momentálně ve verzi Preview. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

![Přihlaste se k Microsoft Edge pomocí bezpečnostního klíče.](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Následující postup se používá, když se uživatel přihlásí pomocí bezpečnostního klíče FIDO2:

![Diagram, který popisuje kroky týkající se přihlašování uživatelů pomocí bezpečnostního klíče FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Uživatel připojí FIDO2 bezpečnostní klíč k počítači.
2. Systém Windows detekuje bezpečnostní klíč FIDO2.
3. Systém Windows odešle požadavek na ověření.
4. Azure AD pošle zpátky hodnotu nonce.
5. Uživatel dokončí svůj gesto, aby odemkl privátní klíč uložený v zabezpečeném enklávy bezpečnostního klíče FIDO2.
6. Klíč zabezpečení FIDO2 podepíše hodnoty nonce pomocí privátního klíče.
7. Požadavek tokenu primární aktualizace tokenu (PRT) s podepsanou hodnotu NONCE se pošle do služby Azure AD.
8. Azure AD ověří podepsaná hodnota nonce pomocí veřejného klíče FIDO2.
9. Azure AD vrátí PRT a povolí přístup k místním prostředkům.

I když existuje mnoho klíčů, které jsou FIDO2 certifikovány nástrojem FIDO Alliance, společnost Microsoft vyžaduje některá volitelná rozšíření FIDO2 specifikace pro klienta do protokolu CTAP (Client-to-Authenticator Protocol), aby bylo zajištěno maximální zabezpečení a nejlepší prostředí.

Bezpečnostní klíč **musí** implementovat následující funkce a rozšíření od protokolu FIDO2 CTAP, aby byl kompatibilní s Microsoftem:

| # | Vztah důvěryhodnosti funkcí nebo rozšíření | Proč je tato funkce nebo rozšíření nutná? |
| --- | --- | --- |
| 1 | Rezidentní klíč | Tato funkce umožňuje přenos bezpečnostního klíče, kde se ukládají přihlašovací údaje na klíč zabezpečení. |
| 2 | Kód PIN klienta | Tato funkce umožňuje chránit vaše přihlašovací údaje pomocí druhého faktoru a vztahuje se na klíče zabezpečení, které nemají uživatelské rozhraní. |
| 3 | HMAC – tajný klíč | Toto rozšíření zajišťuje, že se můžete přihlásit k zařízení, když je v režimu online nebo v letadle. |
| 4 | Více účtů na RP | Tato funkce zajišťuje, že můžete použít stejný bezpečnostní klíč v rámci více služeb, jako je například účet Microsoft a Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>FIDO2 zprostředkovatelé zabezpečení klíčů

Následující poskytovatelé nabízejí bezpečnostní klíče FIDO2 různých faktorů, u kterých je známo, že jsou kompatibilní s prostředím bez hesla. Pro vyhodnocení vlastností zabezpečení těchto klíčů doporučujeme, abyste se obrátili na dodavatele i na FIDO Alliance.

| Poskytovatel | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Řešení TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Identita Gemalto (skupina Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |

> [!NOTE]
> Pokud si koupíte a plánujete používat klíče zabezpečení založené na technologii NFC, budete pro klíč zabezpečení potřebovat podporovanou čtečku NFC. Čtečka NFC není požadavkem nebo omezením Azure. Seznam podporovaných čtecích zařízení NFC vám poskytne dodavatel s klíčem zabezpečení na NFC.

Pokud jste dodavatel a chcete v tomto seznamu podporovaných zařízení získat svoje zařízení, kontaktujte [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

Pokud chcete začít s klíči zabezpečení FIDO2, proveďte následující kroky:

> [!div class="nextstepaction"]
> [Povolení podepisování hesla pomocí klíčů zabezpečení FIDO2](howto-authentication-passwordless-security-key.md)

## <a name="what-scenarios-work-with-the-preview"></a>Jaké scénáře fungují s verzí Preview?

Funkce přihlašování s heslem v Azure AD jsou momentálně ve verzi Preview. Platí následující důležité informace:

- Správci můžou u svého tenanta povolit metody ověřování nejenom heslem.
- Správci můžou cílit na všechny uživatele nebo vybrat uživatele/skupiny v rámci svého tenanta.
- Koncoví uživatelé můžou tyto metody ověřování s heslem zaregistrovat a spravovat na portálu účtů.
- Koncoví uživatelé se můžou přihlásit pomocí těchto metod ověřování bez hesla.
   - Microsoft Authenticator aplikace: funguje v situacích, kdy se používá ověřování Azure AD, včetně všech prohlížečů, během instalace systému Windows 10 (OOBE) a s integrovanými mobilními aplikacemi v jakémkoli operačním systému.
   - Klíče zabezpečení: Pracujte na zamykací obrazovce pro Windows 10 a na webu v podporovaných prohlížečích, jako je Microsoft Edge (starší verze i nová hrana).

## <a name="choose-a-passwordless-method"></a>Zvolit metodu neplatných hesel

Volba mezi těmito třemi možnostmi bez hesla závisí na požadavcích na zabezpečení, platformu a aplikace vaší společnosti.

Tady je několik faktorů, které je potřeba vzít v úvahu při volbě technologie nepracující s Microsoftem.

||**Windows Hello pro firmy**|**Přihlášení bez hesla pomocí aplikace Microsoft Authenticator**|**FIDO2 klíče zabezpečení**|
|:-|:-|:-|:-|
|**Před požadavky**| Windows 10 verze 1809 nebo novější<br>Azure Active Directory| Aplikace Microsoft Authenticator<br>Telefon (zařízení s iOS a Androidem se systémem Android 6,0 nebo vyšším)|Windows 10 verze 1903 nebo novější<br>Azure Active Directory|
|**Režim**|Platforma|Software|Hardware|
|**Systémy a zařízení**|POČÍTAČ s integrovaným čipem TPM (Trusted Platform Module)<br>Připnutí a biometrika rozpoznávání |Připnutí a biometrika rozpoznávání na telefonu|FIDO2 zabezpečení zařízení, která jsou kompatibilní s Microsoftem|
|**Uživatelské prostředí**|Přihlaste se pomocí PIN nebo biometrického rozpoznávání (obličeje, Iris nebo otisk prstu) pomocí zařízení s Windows.<br>Ověřování Windows Hello je vázané na zařízení; pro přístup k firemním prostředkům potřebuje uživatel zařízení i přihlašovací komponentu, jako je PIN nebo biometrické faktor.|Přihlaste se pomocí mobilního telefonu pomocí skenování otisků prstů, obličeje nebo Iris nebo PIN kódu.<br>Uživatelé se přihlásí k pracovnímu nebo osobnímu účtu na svém počítači nebo mobilním telefonu.|Přihlášení pomocí zařízení zabezpečení FIDO2 (biometrika, PIN a NFC)<br>Uživatel může získat přístup k zařízení na základě organizačních ovládacích prvků a ověřování na základě kódu PIN, biometrika pomocí zařízení, jako jsou klíče zabezpečení USB a čipové karty, klíče nebo wearables s podporou NFC.|
|**Povolené scénáře**| Heslo – bez možnosti použití zařízení s Windows<br>Platí pro vyhrazený pracovní počítač s možností jednotného přihlašování k zařízením a aplikacím.|Řešení bez hesla odkudkoli pomocí mobilního telefonu.<br>Platí pro přístup k pracovním nebo osobním aplikacím na webu z libovolného zařízení.|Prostředí bez hesla pro pracovní procesy s využitím biometrika, kódu PIN a NFC.<br>Platí pro sdílené počítače a v případě, že mobilní telefon není možnost životaschopnosti (například pro pracovníky helpdesku, veřejný terminál nebo tým v nemocnicích).|

Pomocí následující tabulky vyberte, kterou metodu budou podporovat vaše požadavky a uživatele.

|Nežádoucí|Scénář|Prostředí|Technologie nepracující s heslem|
|:-|:-|:-|:-|
|**Správce**|Zabezpečený přístup k zařízení pro úlohy správy|Přiřazené zařízení s Windows 10|Bezpečnostní klíč Windows Hello pro firmy a/nebo FIDO2|
|**Správce**|Úlohy správy na zařízeních s jiným systémem než Windows| Mobilní zařízení nebo zařízení s jiným systémem než Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Informační pracovník**|Produktivita práce|Přiřazené zařízení s Windows 10|Bezpečnostní klíč Windows Hello pro firmy a/nebo FIDO2|
|**Informační pracovník**|Produktivita práce| Mobilní zařízení nebo zařízení s jiným systémem než Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Pracovní proces prvotní**|Veřejné terminály v továrně, závodě, maloobchodním prodeji nebo zadávání dat|Sdílená zařízení s Windows 10|FIDO2 klíče zabezpečení|

## <a name="next-steps"></a>Další kroky

Pokud chcete začít bez hesla ve službě Azure AD, proveďte jednu z následujících akcí:

* [Povolit přihlašování s heslem FIDO2 Security Key](howto-authentication-passwordless-security-key.md)
* [Povolit telefonické přihlášení bez hesla pomocí ověřovací aplikace](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externí odkazy

* [FIDO Alliance](https://fidoalliance.org/)
* [Specifikace CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
