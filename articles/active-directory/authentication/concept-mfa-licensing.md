---
title: Azure MFA verze a plány consumption | Dokumentace Microsoftu
description: Informace o klientovi služby Multi-Factor Authentication a různé metody a verze, které jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 15a528bfdc1f23c932d1206dbca4b05af8bd58ba
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186697"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak získat Azure Multi-Factor Authentication

Pokud jde o chránit vaše účty, by měla být dvoustupňové ověřování standardní napříč vaší organizací. Tato funkce je obzvláště důležité pro účty, které mají privilegovaný přístup k prostředkům. Z tohoto důvodu Microsoft nabízí funkce základní dvoustupňové ověřování pro Office 365 a správci služby Azure Active Directory (Azure AD) bez dalších poplatků. Pokud chcete upgradovat funkce pro správce nebo rozšířit dvoustupňové ověřování ostatních uživatelů, můžete zakoupit Azure Multi-Factor Authentication několika způsoby.

> [!IMPORTANT]
> Tento článek slouží jako vodítko, které vám pomohou pochopit různé způsoby, jak koupit Azure Multi-Factor Authentication. Pro konkrétní podrobnosti o cenách a fakturaci, byste měli vždy použít [stránce s cenami služby Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostupné verze služby Azure Multi-Factor Authentication

Následující tabulka popisuje rozdíly mezi tři verze služby Multi-Factor authentication:

| Verze | Popis |
| --- | --- |
| Služba Multi-Factor Authentication (vícefaktorové ověřování) pro Office 365 |Tato verze funguje výhradně s aplikacemi Office 365 a spravuje se z portálu Office 365. Správci můžou [svázat prostředky služeb Office 365 s dvoustupňovým ověřováním](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Tato verze je součástí předplatného Office 365. |
| Ověřování službou Multi-Factor Authentication pro správce Azure AD | Uživatelé s rolí globální správce Azure AD v tenantů Azure AD můžete zapnout dvoustupňové ověřování bez dalších poplatků.|
| Azure Multi-Factor Authentication | Často označuje jako "úplné" verze, ověřování Azure Multi-Factor Authentication nabízí nejkomplexnější sadu funkcí. Poskytuje dodatečné konfigurační možnosti přes [webu Azure portal](https://portal.azure.com)rozšířená tvorba sestav a podpora pro celou řadu v místním i cloudovým aplikacím. Azure Multi-Factor Authentication je funkce [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features)a je možné nasadit v cloudu nebo místně. |

> [!NOTE]
> Noví zákazníci už koupit Azure Multi-Factor Authentication jako samostatná nabízí efektivní dne 1. května 2018. Ověřování službou Multi-Factor Authentication bude nadále k dispozici jako funkci v licence Azure AD Premium.

## <a name="feature-comparison-of-versions"></a>Funkce porovnání verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích ověřování Azure Multi-Factor Authentication.

> [!NOTE]
> Tento článek popisuje tuto tabulku s porovnáním funkcí, které jsou součástí jednotlivých verzí služby Multi-Factor Authentication. Pokud máte plnou verzi služby Azure Multi-Factor Authentication, některé funkce nemusí být k dispozici v závislosti na tom, jestli používáte [MFA v cloudu nebo MFA v místním](concept-mfa-whichversion.md).
>

| Funkce | Služba Multi-Factor Authentication (vícefaktorové ověřování) pro Office 365 | Ověřování službou Multi-Factor Authentication pro správce Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Chránit účty správců Azure AD pomocí vícefaktorového ověřování |● |● (pouze účty globální správce Azure AD) |● |
| Mobilní aplikace jako druhý faktor |● |● |● |
| Telefonní hovor jako druhý faktor |● |● |● |
| Služby SMS jako druhý faktor |● |● |● |
| Hesla aplikací pro klienty, kteří nepodporují MFA |● |● |● |
| Kontrola správce nad metodami ověřování |● |● |● |
| Chránit účty bez oprávnění správce se používá služba MFA |● (pouze pro aplikace Office 365) | |● |
| Režim kódu PIN | | |● |
| Výstraha podvodů | | |● |
| Sestavy MFA | | |● |
| Jednorázové potlačení | | |● |
| Vlastní přivítání pro telefonní hovory | | |● |
| ID vlastního volajícího pro telefonní hovory | | |● |
| Důvěryhodné IP adresy | | |● |
| Zapamatovat MFA pro důvěryhodná zařízení |● |● |● |
| Vícefaktorové ověřování pro místní aplikace | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Jak zapnout ověřování Azure Multi-Factor Authentication pro správce Azure AD

Uživatelé s rolí globální správce v tenantů Azure AD můžete zapnout dvoustupňové ověřování pro účty Azure AD globálního správce bez dalších poplatků. Pokud používáte Microsoft Account, si můžete zaregistrovat ověřování služby Multi-Factor Authentication pomocí pokynů v článku podpory účtu Microsoft, [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Pokud nepoužíváte Account Microsoft, zapnout ověřování službou Multi-Factor Authentication pro globální správce, pomocí pokynů v článku [vyžadování dvoustupňového ověřování pro uživatele nebo skupinu](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Jak zakoupit Azure Multi-Factor Authentication

Pokud chcete všechny funkce, které nabízí Azure Multi-Factor Authentication, máte několik možností:

### <a name="option-1---licenses-that-include-mfa"></a>Možnost 1 - licence, které zahrnují MFA

Nákup licence, které zahrnují ověřování Azure Multi-Factor Authentication, jako je Azure Active Directory Premium, nebo sadu licencí, které zahrnuje Azure AD Premium a přiřadit je uživatelům v Azure Active Directory.

### <a name="option-2---mfa-consumption-based-model"></a>Možnost 2 – model založený na spotřebě MFA

Tato možnost je už dostupná pro nové zákazníky platit od 1. září 2018.

Může vytvořit už nebude platit 1. září 2018 nové zprostředkovatelé vícefaktorového ověřování. Existující zprostředkovatelé vícefaktorového ověřování nadále používat a aktualizovat. Ověřování službou Multi-Factor Authentication bude i nadále k dispozici funkce v licence Azure AD Premium.

Při použití poskytovatele Azure Multi-Factor Authentication, existují dva modely využití k dispozici prostřednictvím předplatného Azure, které se účtují:

1. **Per Enabled User** – pro podniky, které chcete povolit dvoustupňové ověřování pro pevně daný počet zaměstnanců, kteří potřebují pravidelně ověřování. Fakturace po uživatelích vychází z počtu uživatelů s povoleným vícefaktorovým Ověřováním ve vašem tenantovi Azure AD a Azure MFA serveru. Když uživatelé budou povolené pro vícefaktorové ověřování v Azure AD a Azure MFA serverem a je povolená synchronizace domény (Azure AD Connect), pak se řadí větší sadu uživatelů. Pokud domény synchronizace není povolená, pak se řadí součet všech uživatelů s povoleným vícefaktorovým Ověřováním ve službě Azure AD a Azure MFA serveru. Fakturace se účtuje pomocí poměrného přepočítání a který ohlásil systému obchodování každý den.

   > [!NOTE]
   > Fakturace – Příklad 1: Máte 5 000 uživatelů s povoleným vícefaktorovým Ověřováním ještě dnes. Vícefaktorové ověřování systému vydělí toto číslo 31 a 161.29 uživatelé sestavy pro daný den. Zítra povolíte 15 více uživatelů, takže systém MFA ohlásí 161.77 uživatelů pro daný den. Na konci fakturačního cyklu přidá celkový počet uživatelů, na které se fakturuje oproti předplatnému Azure až po 5 000.
   >
   > Fakturace příklad 2: Máte různé uživatelů s licencemi a uživatele bez, takže budete mít zprostředkovatele Azure MFA na uživatele a společně tvoří rozdíl. Existují 4500 Enterprise Mobility + Security koupím ve svém tenantovi, ale 5 000 uživatelů s povoleným vícefaktorovým Ověřováním. Vaše předplatné Azure se účtuje pro 500 uživatelů, nebo jeho poměrnou část a hlášené každý den jako 16.13 uživatelů.
   >

1. **Za ověření** – pro podniky, které chcete povolit dvoustupňové ověřování pro velkou skupinu uživatelů, kteří často potřebují ověřování. Fakturace vychází z počtu žádosti o dvoustupňové ověření, bez ohledu na to, zda tyto ověření proběhnou úspěšně, nebo byl odepřen. Toto účtování se objeví ve výpisu využití Azure v balíčcích 10 ověření a je pokud hlášeno každý den.

   > [!NOTE]
   > Příklad fakturace 3: V současné době služba Azure MFA přijala 3,105 žádosti o dvoustupňové ověření. Vaše předplatné Azure účtuje 310.5 sady ověřování.
   >

Je důležité si uvědomit, že může mít licence, ale stále bude fakturována částka pro konfiguraci založenou na skutečné spotřebě. Pokud nastavíte zprostředkovatele Azure MFA na ověřování, fakturovaného pro každý požadavek dvoustupňové ověření, včetně těch, které provádí uživatelé, kteří mají licence. Pokud nastavíte zprostředkovatele Azure MFA na uživatele v doméně, který není přidružený k vašemu tenantovi Azure AD vám účtuje za povoleného uživatele i v případě, že uživatelé mají licence na Azure AD.

## <a name="next-steps"></a>Další postup

- Podrobnosti o cenách najdete v části [ceny služby Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Zvolte, jestli se má nasadit Azure MFA [v cloudu nebo místně](concept-mfa-whichversion.md)
