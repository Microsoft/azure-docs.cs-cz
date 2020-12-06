---
title: Verze Multi-Factor Authentication a plány spotřeby pro Azure AD
description: Přečtěte si o klientovi Multi-Factor Authentication Azure AD a různých metodách a verzích, které jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743951"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Funkce a licence pro Azure AD Multi-Factor Authentication

Aby bylo možné chránit uživatelské účty ve vaší organizaci, měli byste použít službu Multi-Factor Authentication. Tato funkce je zvláště důležitá pro účty, které mají privilegovaný přístup k prostředkům. Základní funkce služby Multi-Factor Authentication jsou k dispozici pro správce Microsoft 365 a Azure Active Directory (Azure AD) bez dalších nákladů. Pokud chcete upgradovat funkce pro vaše správce nebo rozšíření služby Multi-Factor Authentication na zbytek uživatelů, můžete si koupit Azure AD Multi-Factor Authentication několika způsoby.

> [!IMPORTANT]
> Tento článek podrobně popisuje různé způsoby, jak může být služba Azure AD Multi-Factor Authentication licencovaná a používaná. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami pro Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Dostupné verze služby Azure AD Multi-Factor Authentication

V závislosti na potřebách vaší organizace se dá Azure AD Multi-Factor Authentication použít a licencovat několika různými způsoby. Možná už máte nárok na použití Azure AD Multi-Factor Authentication v závislosti na licenci pro Azure AD, EMS nebo Microsoft 365, kterou máte v současnosti. Následující tabulka obsahuje podrobné informace o různých způsobech, jak získat Multi-Factor Authentication služby Azure AD, a některé z funkcí a případy použití pro každý z nich.

| Pokud jste uživatelem | Možnosti a případy použití |
| --- | --- |
| Microsoft 365 Business Premium a EMS nebo Microsoft 365 E3 a E5 | Služba EMS E3, Microsoft 365 E3 a Microsoft 365 Business Premium zahrnuje Azure AD Premium P1. EMS E5 nebo Microsoft 365 E5 zahrnuje Azure AD Premium P2. Pomocí stejných funkcí podmíněného přístupu uvedených v následujících částech můžete uživatelům poskytnout službu Multi-Factor Authentication. |
| Azure AD Premium P1 | [Podmíněný přístup Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) můžete použít k zobrazení výzvy uživatelům k ověřování službou Multi-Factor Authentication během určitých scénářů nebo událostí, aby vyhovovaly vašim obchodním požadavkům. |
| Azure AD Premium P2 | Poskytuje nejsilnější umístění zabezpečení a vylepšené uživatelské prostředí. Přidá [podmíněný přístup založený na rizikech](../conditional-access/howto-conditional-access-policy-risk.md) k funkcím Azure AD Premium P1, které se přizpůsobí uživatelským vzorům a minimalizuje výzvy k ověření službou Multi-Factor Authentication. |
| Všechny plány Microsoft 365 | Službu Azure AD Multi-Factor Authentication můžete [Povolit pro jednotlivé uživatele](howto-mfa-userstates.md)nebo povolit nebo zakázat pro všechny uživatele s použitím [výchozích hodnot zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md). Správa Multi-Factor Authentication služby Azure AD je prostřednictvím portálu Microsoft 365. Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v tématu [zabezpečení Microsoft 365ch prostředků pomocí služby Multi-Factor Authentication](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Free | Můžete použít [výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) a povolit službu Multi-Factor Authentication pro všechny uživatele. Nemáte podrobnější kontrolu nad povolenými uživateli nebo scénáři, ale poskytuje další krok zabezpečení.<br /> I když se výchozí nastavení zabezpečení nepoužívá k povolení služby Multi-Factor Authentication pro všechny uživatele, můžou být uživatelé přiřazení role *globálního správce služby Azure AD* nakonfigurováni tak, aby používali službu Multi-Factor Authentication. Tato funkce úrovně Free zajišťuje, aby byly důležité účty správců chráněné službou Multi-Factor Authentication. |

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Multi-Factor Authentication služby Azure AD. Naplánujte si potřeby pro zabezpečení ověřování uživatelů a pak určete, který přístup splňuje tyto požadavky. I když Azure AD Free například poskytuje výchozí nastavení zabezpečení, které poskytuje Multi-Factor Authentication Azure AD, dá se pro výzvu k ověření použít jenom aplikace Mobile Authenticator, ne telefonní hovor nebo SMS. Tento přístup může být omezením, pokud nemůžete zajistit, aby byla aplikace pro mobilní ověřování nainstalovaná na osobním zařízení uživatele.

| Funkce | Azure AD Free – výchozí hodnoty zabezpečení | Azure AD Free – globální Správci služby Azure AD | Aplikace Microsoft 365 | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrana účtů správců tenantů Azure AD pomocí MFA | ● | ● (Jenom účty *globálního správce Azure AD* ) | ● | ● |
| Mobilní aplikace jako druhý faktor | ● | ● | ● | ● |
| Telefonní hovor jako druhý faktor | | ● | ● | ● |
| SMS jako druhý faktor | | ● | ● | ● |
| Řízení správce přes metody ověřování | | ● | ● | ● |
| Výstraha podvodů | | | | ● |
| Sestavy MFA | | | | ● |
| Vlastní přivítání pro telefonní hovory | | | | ● |
| Vlastní ID volajícího pro telefonní hovory | | | | ● |
| Důvěryhodné IP adresy | | | | ● |
| Zapamatovat MFA pro důvěryhodná zařízení | | ● | ● | ● |
| MFA pro místní aplikace | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Nákup a povolení Multi-Factor Authentication služby Azure AD

Pokud chcete použít Multi-Factor Authentication služby Azure AD, zaregistrujte se nebo Zakupte si opravňující úroveň služby Azure AD. Služba Azure AD přichází ve čtyřech edicích: Free, Microsoft 365 Apps, Premium P1 a Premium P2.

Edice Free je součástí předplatného Azure. V [následující části](#azure-ad-free-tier) najdete informace o tom, jak používat výchozí nastavení zabezpečení nebo jak chránit účty pomocí role *globálního správce služby Azure AD* .

Edice Azure AD Premium jsou k dispozici prostřednictvím zástupce společnosti Microsoft, [programu Open Volume License](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)a [programu Cloud Solution Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Předplatitelé Azure a Microsoft 365 můžou taky koupit Azure Active Directory Premium P1 a P2 online. [Přihlaste](https://portal.office.com/Commerce/Catalog.aspx) se k nákupu.

Po zakoupení požadované úrovně Azure AD si [Naplánujte a nasaďte Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure AD Free úroveň

Všichni uživatelé v tenantovi Azure AD Free můžou použít Azure AD Multi-Factor Authentication prostřednictvím výchozích hodnot zabezpečení. Aplikace pro mobilní ověřování je jedinou metodou, kterou je možné použít pro Multi-Factor Authentication Azure AD při použití výchozích hodnot zabezpečení Azure AD Free.

* [Další informace o výchozím nastavení zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Povolit výchozí nastavení zabezpečení pro uživatele v Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Pokud nechcete povolit Multi-Factor Authentication služby Azure AD pro všechny uživatele, můžete místo toho vybrat možnost ochrany uživatelských účtů pomocí role *globálního správce služby Azure AD* . Tento přístup poskytuje další výzvy k ověření pro kritické účty správců. V závislosti na typu účtu, který používáte, povolíte Multi-Factor Authentication Azure AD jedním z následujících způsobů:

* Pokud používáte účet Microsoft, [Zaregistrujte se do služby Multi-Factor Authentication](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Pokud nepoužíváte účet Microsoft, [zapněte službu Multi-Factor Authentication pro uživatele nebo skupinu v Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Další kroky

* Další informace o cenách najdete v tématu [ceny služby Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Co je podmíněný přístup](../conditional-access/overview.md)

