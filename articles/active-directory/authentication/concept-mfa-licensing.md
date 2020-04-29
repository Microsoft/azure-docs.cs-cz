---
title: Verze a plány spotřeby pro Azure Multi-Factor Authentication
description: Přečtěte si o klientovi Azure Multi-Factor Authentication a různých metodách a verzích, které jsou k dispozici.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77647998"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funkce a licence pro Azure Multi-Factor Authentication

Aby bylo možné chránit uživatelské účty ve vaší organizaci, měli byste použít službu Multi-Factor Authentication. Tato funkce je zvláště důležitá pro účty, které mají privilegovaný přístup k prostředkům. Základní funkce služby Multi-Factor Authentication jsou k dispozici správcům Office 365 a Azure Active Directory (Azure AD) bez dalších poplatků. Pokud chcete upgradovat funkce pro vaše správce nebo rozšíření Multi-Factor Authentication na ostatní uživatele, můžete si Azure Multi-Factor Authentication koupit několika způsoby.

> [!IMPORTANT]
> Tento článek podrobně popisuje různé způsoby, kterými se dá Azure Multi-Factor Authentication licencovat a používat. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami za Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostupné verze Azure Multi-Factor Authentication

V závislosti na potřebách vaší organizace se dá Azure Multi-Factor Authentication použít a licencovat několika různými způsoby. Možná už máte nárok na použití Azure Multi-Factor Authentication v závislosti na licenci Azure AD, Office 365, EMS nebo Microsoft 365, kterou máte v současnosti. Následující tabulka obsahuje podrobnosti o různých způsobech, jak získat Azure Multi-Factor Authentication a některé funkce a případy použití pro každý z nich.

| Pokud jste uživatelem | Možnosti a případy použití |
| --- | --- |
| EMS nebo Microsoft 365 E3 a E5 | EMS E3 nebo Microsoft 365 E3 (včetně EMS a Office 365), zahrnuje Azure AD Premium P1. EMS E5 nebo Microsoft 365 E5 zahrnuje Azure AD Premium P2. Pomocí stejných funkcí podmíněného přístupu uvedených v následujících částech můžete uživatelům poskytnout službu Multi-Factor Authentication. |
| Azure AD Premium P1 | [Podmíněný přístup Azure AD](../conditional-access/overview.md) můžete použít k zobrazení výzvy uživatelům k ověřování službou Multi-Factor Authentication během určitých scénářů nebo událostí, aby vyhovovaly vašim obchodním požadavkům. |
| Azure AD Premium P2 | Poskytuje nejsilnější umístění zabezpečení a vylepšené uživatelské prostředí. Přidá [podmíněný přístup založený na rizikech](../conditional-access/howto-conditional-access-policy-risk.md) k funkcím Azure AD Premium P1, které se přizpůsobí uživatelským vzorům a minimalizuje výzvy k ověření službou Multi-Factor Authentication. |
| Office 365 Business Premium, E3 nebo E5 | Pro všechny přihlašovací události je Azure Multi-Factor Authentication buď povolený, nebo zakázaný pro všechny uživatele. Pro podmnožinu uživatelů není možné povolit ověřování pomocí služby Multi-Factor Authentication nebo jenom v některých případech. Správa je prostřednictvím portálu Office 365. Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v tématu [zabezpečení prostředků Office 365 pomocí služby Multi-Factor Authentication](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD Free | [Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) můžete použít k povolení služby Multi-Factor Authentication pro všechny uživatele při každém provedení žádosti o ověření. Nemáte podrobnější kontrolu nad povolenými uživateli nebo scénáři, ale poskytuje další krok zabezpečení.<br /> I když se výchozí nastavení zabezpečení nepoužívá k povolení služby Multi-Factor Authentication pro všechny uživatele, můžou být uživatelé přiřazení role *globálního správce služby Azure AD* nakonfigurováni tak, aby používali službu Multi-Factor Authentication. Tato funkce úrovně Free zajišťuje, aby byly důležité účty správců chráněné službou Multi-Factor Authentication. |

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Azure Multi-Factor Authentication. Naplánujte si potřeby pro zabezpečení ověřování uživatelů a pak určete, který přístup splňuje tyto požadavky. Přestože Azure AD Free například poskytuje výchozí nastavení zabezpečení, které poskytuje Azure Multi-Factor Authentication, lze pro výzvu k ověření použít pouze aplikaci Mobile Authenticator, nikoli telefonní hovor nebo SMS. Tento přístup může být omezením, pokud nemůžete zajistit, aby byla aplikace pro mobilní ověřování nainstalovaná na osobním zařízení uživatele.

| Funkce | Azure AD Free – výchozí hodnoty zabezpečení | Azure AD Free – globální Správci služby Azure AD | Office 365 Business Premium, E3 nebo E5 | Azure AD Premium P1 nebo P2 |
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

> [!IMPORTANT]
> Od března 2019 nejsou možnosti telefonního hovoru k dispozici pro Azure Multi-Factor Authentication a uživatelé samoobslužného resetování hesla Azure v klientech Azure AD Free/zkušební verze. Tato změna nemá vliv na zprávy SMS. Telefonní hovory budou dál dostupné uživatelům v Azure AD Premium tenantů P1 nebo P2 nebo používají nebo Office 365 Business Premium, E3 nebo E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Nákup a povolení Azure Multi-Factor Authentication

Pokud chcete použít Azure Multi-Factor Authentication, zaregistrujte se nebo Zakupte si opravňující úroveň služby Azure AD. Azure AD nabízí čtyři edice – zdarma, edice Office 365 Apps (pro Office 365 Business Premium E3 nebo E5 Customers), Premium P1 a Premium P2.

Edice Free je součástí předplatného Azure. V [následující části](#azure-ad-free-tier) najdete informace o tom, jak používat výchozí nastavení zabezpečení nebo jak chránit účty pomocí role *globálního správce služby Azure AD* .

Edice Azure AD Premium jsou k dispozici prostřednictvím zástupce společnosti Microsoft, [programu Open Volume License](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)a [programu Cloud Solution Providers](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Předplatitelé Azure a Office 365 si můžou koupit taky Azure Active Directory Premium P1 a P2 online. [Přihlaste](https://portal.office.com/Commerce/Catalog.aspx) se k nákupu.

> [!IMPORTANT]
> Licencování na základě spotřeby už od 1. září 2018 není k dispozici pro nové zákazníky. Stávající zákazníci, kteří používají model založený na spotřebě, můžou dál používat buď účtování podle povoleného uživatele nebo ověřování.

Po zakoupení požadované úrovně Azure AD [Naplánujte a nasaďte Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure AD Free úroveň

Všichni uživatelé v tenantovi Azure AD Free můžou používat ověřování Azure Multi-Factor Authentication prostřednictvím výchozích hodnot zabezpečení. Tato výchozí nastavení zabezpečení umožňují ověřování Azure Multi-Factor Authentication pro všechny uživatele, pokaždé, když se přihlásí. Aplikace pro mobilní ověřování je jedinou metodou, kterou je možné použít pro Azure Multi-Factor Authentication při použití výchozích hodnot zabezpečení Azure AD Free.

* [Další informace o výchozím nastavení zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Povolit výchozí nastavení zabezpečení pro uživatele v Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Pokud nechcete povolit službu Azure Multi-Factor Authentication pro všechny uživatele a každou událost přihlášení, můžete místo toho vybrat možnost ochrany uživatelských účtů pomocí role *globálního správce služby Azure AD* . Tento přístup poskytuje další výzvy k ověření pro kritické účty správců. Azure Multi-Factor Authentication povolíte jedním z následujících způsobů v závislosti na typu účtu, který používáte:

* Pokud používáte účet Microsoft, [Zaregistrujte se do služby Multi-Factor Authentication](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Pokud nepoužíváte účet Microsoft, [zapněte službu Multi-Factor Authentication pro uživatele nebo skupinu v Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Další kroky

Další informace o cenách najdete v tématu [ceny služby Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
