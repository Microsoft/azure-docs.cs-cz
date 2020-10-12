---
title: Verze a plány spotřeby pro Azure Multi-Factor Authentication
description: Přečtěte si o klientovi Azure Multi-Factor Authentication a různých metodách a verzích, které jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 829a82cb94e5c2a7e7cbc9190047c368299612cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052745"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funkce a licence pro Azure Multi-Factor Authentication

Aby bylo možné chránit uživatelské účty ve vaší organizaci, měli byste použít službu Multi-Factor Authentication. Tato funkce je zvláště důležitá pro účty, které mají privilegovaný přístup k prostředkům. Základní funkce služby Multi-Factor Authentication jsou k dispozici pro správce Microsoft 365 a Azure Active Directory (Azure AD) bez dalších nákladů. Pokud chcete upgradovat funkce pro vaše správce nebo rozšíření Multi-Factor Authentication na ostatní uživatele, můžete si Azure Multi-Factor Authentication koupit několika způsoby.

> [!IMPORTANT]
> Tento článek podrobně popisuje různé způsoby, kterými se dá Azure Multi-Factor Authentication licencovat a používat. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami za Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostupné verze Azure Multi-Factor Authentication

V závislosti na potřebách vaší organizace se dá Azure Multi-Factor Authentication použít a licencovat několika různými způsoby. Možná už máte nárok na použití Azure Multi-Factor Authentication v závislosti na licenci pro Azure AD, EMS nebo Microsoft 365, kterou máte v současnosti. Následující tabulka obsahuje podrobnosti o různých způsobech, jak získat Azure Multi-Factor Authentication a některé funkce a případy použití pro každý z nich.

| Pokud jste uživatelem | Možnosti a případy použití |
| --- | --- |
| Microsoft 365 Business Premium a EMS nebo Microsoft 365 E3 a E5 | Služba EMS E3, Microsoft 365 E3 a Microsoft 365 Business Premium zahrnuje Azure AD Premium P1. EMS E5 nebo Microsoft 365 E5 zahrnuje Azure AD Premium P2. Pomocí stejných funkcí podmíněného přístupu uvedených v následujících částech můžete uživatelům poskytnout službu Multi-Factor Authentication. |
| Azure AD Premium P1 | [Podmíněný přístup Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) můžete použít k zobrazení výzvy uživatelům k ověřování službou Multi-Factor Authentication během určitých scénářů nebo událostí, aby vyhovovaly vašim obchodním požadavkům. |
| Azure AD Premium P2 | Poskytuje nejsilnější umístění zabezpečení a vylepšené uživatelské prostředí. Přidá [podmíněný přístup založený na rizikech](../conditional-access/howto-conditional-access-policy-risk.md) k funkcím Azure AD Premium P1, které se přizpůsobí uživatelským vzorům a minimalizuje výzvy k ověření službou Multi-Factor Authentication. |
| Všechny plány Microsoft 365 | Azure Multi-Factor Authentication můžete [Povolit pro jednotlivé uživatele](howto-mfa-userstates.md), nebo povolit nebo zakázat pro všechny uživatele s použitím [výchozích hodnot zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md). Správa Azure Multi-Factor Authentication je prostřednictvím portálu Microsoft 365. Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v tématu [zabezpečení Microsoft 365ch prostředků pomocí služby Multi-Factor Authentication](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Azure AD Free | Můžete použít [výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) a povolit službu Multi-Factor Authentication pro všechny uživatele. Nemáte podrobnější kontrolu nad povolenými uživateli nebo scénáři, ale poskytuje další krok zabezpečení.<br /> I když se výchozí nastavení zabezpečení nepoužívá k povolení služby Multi-Factor Authentication pro všechny uživatele, můžou být uživatelé přiřazení role *globálního správce služby Azure AD* nakonfigurováni tak, aby používali službu Multi-Factor Authentication. Tato funkce úrovně Free zajišťuje, aby byly důležité účty správců chráněné službou Multi-Factor Authentication. |

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Azure Multi-Factor Authentication. Naplánujte si potřeby pro zabezpečení ověřování uživatelů a pak určete, který přístup splňuje tyto požadavky. Přestože Azure AD Free například poskytuje výchozí nastavení zabezpečení, které poskytuje Azure Multi-Factor Authentication, lze pro výzvu k ověření použít pouze aplikaci Mobile Authenticator, nikoli telefonní hovor nebo SMS. Tento přístup může být omezením, pokud nemůžete zajistit, aby byla aplikace pro mobilní ověřování nainstalovaná na osobním zařízení uživatele.

| Příznak | Azure AD Free – výchozí hodnoty zabezpečení | Azure AD Free – globální Správci služby Azure AD | Aplikace Microsoft 365 | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrana účtů správců tenantů Azure AD pomocí MFA | ● | ● (Jenom účty*globálního správce Azure AD* ) | ● | ● |
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

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Nákup a povolení Azure Multi-Factor Authentication

Pokud chcete použít Azure Multi-Factor Authentication, zaregistrujte se nebo Zakupte si opravňující úroveň služby Azure AD. Služba Azure AD přichází ve čtyřech edicích: Free, Microsoft 365 Apps, Premium P1 a Premium P2.

Edice Free je součástí předplatného Azure. V [následující části](#azure-ad-free-tier) najdete informace o tom, jak používat výchozí nastavení zabezpečení nebo jak chránit účty pomocí role *globálního správce služby Azure AD* .

Edice Azure AD Premium jsou k dispozici prostřednictvím zástupce společnosti Microsoft, [programu Open Volume License](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)a [programu Cloud Solution Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Předplatitelé Azure a Microsoft 365 můžou taky koupit Azure Active Directory Premium P1 a P2 online. [Přihlaste](https://portal.office.com/Commerce/Catalog.aspx) se k nákupu.

Po zakoupení požadované úrovně Azure AD [Naplánujte a nasaďte Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure AD Free úroveň

Všichni uživatelé v tenantovi Azure AD Free můžou používat ověřování Azure Multi-Factor Authentication prostřednictvím výchozích hodnot zabezpečení. Aplikace pro mobilní ověřování je jedinou metodou, kterou je možné použít pro Azure Multi-Factor Authentication při použití výchozích hodnot zabezpečení Azure AD Free.

* [Další informace o výchozím nastavení zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Povolit výchozí nastavení zabezpečení pro uživatele v Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Pokud nechcete povolit službu Azure Multi-Factor Authentication pro všechny uživatele, můžete místo toho použít ochranu uživatelských účtů pomocí role *globálního správce služby Azure AD* . Tento přístup poskytuje další výzvy k ověření pro kritické účty správců. Azure Multi-Factor Authentication povolíte jedním z následujících způsobů v závislosti na typu účtu, který používáte:

* Pokud používáte účet Microsoft, [Zaregistrujte se do služby Multi-Factor Authentication](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Pokud nepoužíváte účet Microsoft, [zapněte službu Multi-Factor Authentication pro uživatele nebo skupinu v Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Další kroky

* Další informace o cenách najdete v tématu [ceny služby Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Co je podmíněný přístup](../conditional-access/overview.md)

