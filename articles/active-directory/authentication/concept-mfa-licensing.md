---
title: Plány využití a verze Azure MFA – Azure Active Directory
description: Informace o klientovi Multi-Factor Authentication a různých metodách a verzích jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e34e0433dd362150680046c2f0ca53996063c4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151745"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak získat Azure Multi-Factor Authentication

V případě ochrany vašich účtů by dvoustupňové ověřování mělo být ve vaší organizaci standardní. Tato funkce je zvláště důležitá pro účty, které mají privilegovaný přístup k prostředkům. Z tohoto důvodu Microsoft nabízí základní funkce pro dvoustupňové ověřování pro správce Office 365 a Azure Active Directory (Azure AD) bez dalších poplatků. Pokud chcete upgradovat funkce pro správce nebo rozšíření ověřování se dvěma kroky na zbytek uživatelů, můžete si Azure Multi-Factor Authentication koupit několika způsoby.

> [!IMPORTANT]
> Tento článek je určený jako průvodce, který vám pomůže pochopit různé způsoby nákupu Azure Multi-Factor Authentication. Konkrétní podrobnosti o cenách a fakturaci byste měli vždycky odkázat na [stránku s cenami Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostupné verze Azure Multi-Factor Authentication

V následující tabulce jsou popsány rozdíly mezi verzemi ověřování Multi-Factor Authentication:

| Version | Popis |
| --- | --- |
| Možnost Free | Zákazníci, kteří využívají Bezplatné výhody Azure AD, můžou použít [výchozí nastavení zabezpečení](../conditional-access/concept-conditional-access-security-defaults.md) a povolit službu Multi-Factor Authentication ve svém prostředí. |
| Multi-Factor Authentication for Office 365 | Tato verze je spravovaná na portálu Office 365 nebo Microsoft 365. Správci mohou [zabezpečit prostředky sady Office 365 pomocí dvoustupňového ověřování](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Tato verze je součástí předplatného sady Office 365. |
| Multi-Factor Authentication pro správce Azure AD | Uživatelé, kteří mají přiřazenou roli globálního správce služby Azure AD v klientech Azure AD, můžou povolit dvoustupňové ověřování bez dalších nákladů. |
| Azure Multi-Factor Authentication | Azure Multi-Factor Authentication, který se často označuje jako "plná" verze, nabízí bohatou sadu funkcí. Poskytuje další možnosti konfigurace prostřednictvím [Azure Portal](https://portal.azure.com), pokročilých sestav a podpory pro řadu místních a cloudových aplikací. Azure Multi-Factor Authentication je funkce [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) a [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Noví zákazníci už nemůžou koupit Azure Multi-Factor Authentication jako samostatnou nabídku platnou od 1. září 2018. Multi-Factor Authentication bude i nadále k dispozici jako funkce v rámci licencí Azure AD Premium nebo Microsoft 365 Business.

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Azure Multi-Factor Authentication.

> [!NOTE]
> Tato srovnávací tabulka popisuje funkce, které jsou součástí každé verze Multi-Factor Authentication. Pokud máte úplnou službu Azure Multi-Factor Authentication, některé funkce nemusí být k dispozici v závislosti na tom, jestli používáte [vícefaktorové ověřování v cloudu nebo v místním ověřování](concept-mfa-whichversion.md).
>

| Funkce | Multi-Factor Authentication for Office 365 | Multi-Factor Authentication pro správce Azure AD | Azure Multi-Factor Authentication | Výchozí nastavení zabezpečení | 
| --- |:---:|:---:|:---:|:---:|
| Ochrana účtů správce Azure AD pomocí MFA |● |● (Jenom účty globálního správce Azure AD) |● |● |
| Mobilní aplikace jako druhý faktor |● |● |● |● |
| Telefonní hovor jako druhý faktor |● |● |● |   |
| SMS jako druhý faktor |● |● |● |   |
| Hesla aplikací pro klienty, kteří nepodporují MFA |● |● |● |   |
| Řízení správce přes metody ověřování |● |● |● |   |
| Ochrana účtů bez správy pomocí vícefaktorového ověřování |● | |● |● |
| Režim kódu PIN | | |● |   |
| Výstraha podvodů | | |● |   |
| Sestavy MFA | | |● |   |
| Jednorázové potlačení | | |● |   |
| Vlastní přivítání pro telefonní hovory | | |● |   |
| Vlastní ID volajícího pro telefonní hovory | | |● |   |
| Důvěryhodné IP adresy | | |● |   |
| Zapamatovat MFA pro důvěryhodná zařízení |● |● |● |   |
| MFA pro místní aplikace | | |● |   |

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru k dispozici pro MFA a SSPR uživatele v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Telefonní hovor bude dál k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv jenom na bezplatné nebo zkušební klienty Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Jak zapnout Azure Multi-Factor Authentication pro správce Azure AD

Uživatelé přiřazení role globálního správce v klientech Azure AD můžou povolit dvoustupňové ověřování pro své účty globálního správce Azure AD bez dalších nákladů. Pokud používáte účet Microsoft, můžete službu Multi-Factor Authentication zaregistrovat pomocí pokynů uvedených v článku o podpoře účet Microsoft v článku [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Pokud nepoužíváte účet Microsoft, zapněte službu Multi-Factor Authentication pro globální správce pomocí pokynů uvedených v článku jak pro [uživatele nebo skupinu vyžadovat dvoustupňové ověřování](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Jak koupit Azure Multi-Factor Authentication

Zakupte licence, které zahrnují Azure Multi-Factor Authentication, jako je Azure Active Directory Premium, nebo sadu licencí, která zahrnuje Azure AD Premium, nebo podmíněný přístup a přiřaďte je uživatelům v Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licencování na základě spotřeby

Licencování na základě spotřeby už od 1. září 2018 není k dispozici pro nové zákazníky.

Od 1. září 2018 již možná nebudou vytvořeny Noví zprostředkovatelé ověřování. Stávající zprostředkovatelé ověřování se můžou dál používat a aktualizovat. Multi-Factor Authentication bude nadále dostupná funkce v Azure AD Premium licencích.

Při použití poskytovatele služby Azure Multi-Factor Authentication jsou k dispozici dva modely využití, které se účtují prostřednictvím předplatného Azure:

1. On **povolený uživatel** – pro podniky, které chtějí povolit dvoustupňové ověřování pro pevný počet zaměstnanců, kteří pravidelně potřebují ověřování. Účtování podle uživatelů vychází z počtu uživatelů, kteří mají v tenantovi Azure AD a na serveru Azure MFA povolený vícefaktorové ověřování. Pokud jsou uživatelé povoleni pro vícefaktorové ověřování v Azure AD i v Azure MFA serveru a je povolená synchronizace domény (Azure AD Connect), Počítáme větší skupinu uživatelů. Pokud není synchronizovaná doména povolená, počet všech uživatelů, kteří mají v Azure AD a Azure MFA serveru povolený MFA, vypočítáváme. Fakturuje se poměrná a nahlásí se na každý den v systému obchodování.

   > [!NOTE]
   > Příklad fakturace 1: máte 5 000 uživatelů povolených pro MFA ještě dnes. Systém MFA rozděluje toto číslo o 31 a oznamuje 161,29 uživatelům za tento den. Zítra můžete povolit 15 dalších uživatelů, takže systém MFA oznámí 161,77 uživatelům za tento den. Na konci fakturačního cyklu se celkový počet uživatelů účtovaných v rámci předplatného Azure přidá až do přibližně 5 000.
   >
   > Fakturací příklad 2: máte kombinaci uživatelů s licencemi a uživateli bez, abyste měli k dispozici zprostředkovatele Azure MFA pro každého uživatele, který by tento rozdíl provedl. Ve vašem tenantovi jsou 4 500 Enterprise Mobility + Security licencí, ale 5 000 uživatelé mají povolený MFA. Vaše předplatné Azure se účtuje za 500 uživatelů, poměrné a hlášené denně jako 16,13 uživatelé.
   >

1. **Podle ověřování** – pro podniky, které chtějí povolit dvoustupňové ověřování pro velkou skupinu uživatelů, kteří zřídka potřebují ověřování. Vyúčtování vychází z počtu žádostí o dvoustupňové ověření, bez ohledu na to, jestli tato ověření proběhla úspěšně nebo jsou zamítnutá. Tato fakturace se zobrazí v příkazu Azure Usage v sadách po 10 ověřováních a hlásí se každý den.

   > [!NOTE]
   > Příklad fakturace 3: dnes byla přijata služba Azure MFA s 3 105 2 požadavky na ověření kroku. Vaše předplatné Azure se účtuje za 310,5 sad ověřování.
   >

Je důležité si uvědomit, že můžete mít licence, ale pořád se vám bude účtovat konfigurace na základě spotřeby. Pokud nastavíte zprostředkovatele Azure MFA pro ověřování, bude se vám účtovat každá žádost o dvoustupňové ověření, a to i ty, které udělali uživatelé s licencemi. Pokud nastavíte zprostředkovatele Azure MFA pro jednotlivé uživatele na doménu, která není propojená s vaším klientem služby Azure AD, bude se vám účtovat za povoleného uživatele, a to i v případě, že uživatelé mají licence na Azure AD.

## <a name="next-steps"></a>Další kroky

- Další informace o cenách najdete v tématu [ceny Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
