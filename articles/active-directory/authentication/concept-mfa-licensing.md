---
title: Verze a plány spotřeby Azure pro vícefaktorové ověřování
description: Přečtěte si o vícefaktorovém ověřovacím klientovi Azure a o různých dostupných metodách a verzích.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647998"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funkce a licence pro azure vícefaktorové ověřování

K ochraně uživatelských účtů ve vaší organizaci by mělo být použito vícefaktorové ověřování. Tato funkce je obzvláště důležitá pro účty, které mají privilegovaný přístup k prostředkům. Základní funkce vícefaktorového ověřování jsou dostupné správcům Office 365 a Azure Active Directory (Azure AD) bez dalších nákladů. Pokud chcete upgradovat funkce pro správce nebo rozšířit vícefaktorové ověřování na ostatní uživatele, můžete si azure vícefaktorové ověřování zakoupit několika způsoby.

> [!IMPORTANT]
> Tento článek podrobně popisuje různé způsoby, jak azure vícefaktorové ověřování můžete licencovat a používat. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami azure multi-factor authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostupné verze Azure Multi-Factor Authentication

Azure Multi-Factor Authentication lze použít a licencovat několika různými způsoby v závislosti na potřebách vaší organizace. Už teď máte právo používat Azure Multi-Factor Authentication v závislosti na Azure AD, Office 365, EMS nebo Microsoft 365 licenci, kterou v současné době máte. V následující tabulce jsou uvedeny různé způsoby získání azure multifaktorového ověřování a některé funkce a případy použití pro každou z nich.

| Pokud jste uživatelem | Možnosti a případy použití |
| --- | --- |
| EMS nebo Microsoft 365 E3 a E5 | EMS E3 nebo Microsoft 365 E3 (který zahrnuje EMS a Office 365), zahrnuje Azure AD Premium P1. EMS E5 nebo Microsoft 365 E5 zahrnuje Azure AD Premium P2. Stejné funkce podmíněného přístupu uvedené v následujících částech můžete použít k poskytování vícefaktorového ověřování uživatelům. |
| Azure AD Premium P1 | [Podmíněný přístup Azure AD](../conditional-access/overview.md) můžete použít k zobrazení výzvy uživatelům k vícefaktorovému ověřování během určitých scénářů nebo událostí tak, aby vyhovovaly vašim obchodním požadavkům. |
| Azure AD Premium P2 | Poskytuje nejsilnější pozici zabezpečení a lepší uživatelské prostředí. Přidává [podmíněný přístup založený na rizicích](../conditional-access/howto-conditional-access-policy-risk.md) na funkce Azure AD Premium P1, které se přizpůsobí vzorcům uživatele a minimalizují výzvy k vícefaktorovému ověřování. |
| Office 365 Business Premium, E3 nebo E5 | Azure Multi-Factor Authentication je povolená nebo zakázaná pro všechny uživatele, pro všechny události přihlášení. Neexistuje možnost povolit pouze vícefaktorové ověřování pro podmnožinu uživatelů nebo pouze v určitých scénářích. Správa probíhá prostřednictvím portálu Office 365. Pro lepší uživatelské prostředí upgradujte na Azure AD Premium P1 nebo P2 a použijte podmíněný přístup. Další informace najdete v [tématu zabezpečení prostředků Office 365 s vícefaktorovým ověřováním](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Azure AD Free | [Výchozí hodnoty zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) můžete použít k povolení vícefaktorového ověřování pro všechny uživatele při každém požadavku na ověření. Nemáte podrobné řízení o povolené uživatele nebo scénáře, ale poskytuje další krok zabezpečení.<br /> I v případě, že výchozí zabezpečení se nepoužívají k povolení vícefaktorového ověřování pro všechny, uživatelé přiřazené role *globálního správce Služby Azure AD* lze nakonfigurovat tak, aby používali vícefaktorové ověřování. Tato funkce volné vrstvy zajišťuje, že účty kritického správce jsou chráněny vícefaktorovým ověřováním. |

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verzí

Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Azure Multi-Factor Authentication. Naplánujte si potřeby zabezpečení ověřování uživatelů a určete, který přístup tyto požadavky splňuje. Například i když Azure AD Free poskytuje výchozí nastavení zabezpečení, které poskytují Azure Multi-Factor Authentication, lze použít pouze mobilní ověřovací aplikace pro výzvu ověřování, nikoli telefonní hovor nebo SMS. Tento přístup může být omezení, pokud nemůžete zajistit, že mobilní ověřovací aplikace je nainstalována na osobním zařízení uživatele.

| Funkce | Azure AD Free – výchozí nastavení zabezpečení | Azure AD Free – globální správci Azure AD | Office 365 Business Premium, E3 nebo E5 | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrana účtů správce klienta Azure AD pomocí mfa | ● | ● (pouze účty*globálního správce Azure AD)* | ● | ● |
| Mobilní aplikace jako druhý faktor | ● | ● | ● | ● |
| Telefonní hovor jako druhý faktor | | ● | ● | ● |
| SMS jako druhý faktor | | ● | ● | ● |
| Kontrola správce nad metodami ověřování | | ● | ● | ● |
| Výstraha podvodů | | | | ● |
| Sestavy MFA | | | | ● |
| Vlastní přivítání pro telefonní hovory | | | | ● |
| Vlastní ID volajícího pro telefonní hovory | | | | ● |
| Důvěryhodné IP adresy | | | | ● |
| Zapamatovat MFA pro důvěryhodná zařízení | | ● | ● | ● |
| Vícefaktorové finanční vyhotovení pro místní aplikace | | | | ● |

> [!IMPORTANT]
> Od března 2019 už nejsou možnosti telefonního hovoru dostupné pro uživatele Azure Multi-Factor Authentication a Azure Self-Service Password Reset v tenantech Azure AD Free / trial. Sms zprávy nejsou touto změnou ovlivněny. Telefonní hovory jsou i nadále dostupné uživatelům v tenantech Azure AD Premium P1 nebo P2 nebo v aplikacích Office 365 Business Premium, E3 nebo E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Nákup a povolení azure vícefaktorového ověřování

Pokud chcete používat Azure Multi-Factor Authentication, zaregistrujte nebo zakupte způsobilou úroveň Azure AD. Azure AD se dodává ve čtyřech edicích – Free, Office 365 apps edition (pro zákazníky Office 365 Business Premium E3 nebo E5), Premium P1 a Premium P2.

Edice Free je součástí předplatného Azure. Informace o tom, jak používat výchozí hodnoty zabezpečení nebo jak chránit účty pomocí role *globálního správce Azure AD,* najdete [v následující části.](#azure-ad-free-tier)

Edice Azure AD Premium jsou dostupné prostřednictvím vašeho zástupce společnosti Microsoft, [programu Open Volume License Program](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)a programu [Zprostředkovatelé cloudových řešení](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Předplatitelé Azure a Office 365 můžou taky nakupovat Azure Active Directory Premium P1 a P2 online. [Přihlaste](https://portal.office.com/Commerce/Catalog.aspx) se k nákupu.

> [!IMPORTANT]
> Září 2018 již nejsou pro nové zákazníky k dispozici licencování na základě spotřeby. Stávající zákazníci, kteří používají model založený na spotřebě, mohou nadále používat buď pro uživatele s povolenou povolenou službou, nebo na fakturaci ověřování.

Po zakoupení požadované vrstvy Azure AD [naplánujte a nasaďte azure multifaktorové ověřování](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Azure AD free vrstva

Všichni uživatelé v tenantovi Azure AD Free můžou používat azure multifaktorové ověřování pomocí výchozích nastavení zabezpečení. Tyto výchozí nastavení zabezpečení povolit Azure Vícefaktorové ověřování pro všechny uživatele, pokaždé, když se přihlásí. Mobilní ověřovací aplikace je jediná metoda, kterou se dá použít pro Azure Multi-Factor Authentication při použití výchozích nastavení zabezpečení Azure AD Free.

* [Další informace o výchozích nastaveních zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Povolení výchozích nastavení zabezpečení pro uživatele ve službě Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Pokud nechcete povolit Azure Multi-Factor Authentication pro všechny uživatele a každou událost přihlášení, můžete místo toho zvolit jenom chránit uživatelské účty s rolí *globálního správce Azure AD.* Tento přístup poskytuje další výzvy k ověření pro účty kritického správce. Azure multifaktorové ověřování povolíte jedním z následujících způsobů v závislosti na typu účtu, který používáte:

* Pokud používáte účet Microsoft, [zaregistrujte se pro vícefaktorové ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Pokud nepoužíváte účet Microsoft, [zapněte vícefaktorové ověřování pro uživatele nebo skupinu ve službě Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Další kroky

Další informace o nákladech najdete v tématu [Ceny azure multifaktorového ověřování](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
