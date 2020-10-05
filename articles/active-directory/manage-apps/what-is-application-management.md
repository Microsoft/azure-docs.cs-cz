---
title: Co je Správa aplikací v Azure Active Directory
description: Přehled použití Azure Active Directory (AD) jako systému správy identit a přístupu (IAM) pro vaše cloudové a místní aplikace.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: abe222ff783032c5d6d19a42bfb3d8aeffc284a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90604117"
---
# <a name="what-is-application-management"></a>Co je správa aplikací?

Azure AD je systém pro správu identit a přístupu (IAM, Identity and Access Management). Poskytuje centrální místo pro ukládání informací o digitálních identitách. Softwarové aplikace můžete nakonfigurovat tak, aby jako místo, kam se ukládají informace o uživatelích, používaly Azure AD. 

Služba Azure AD musí být nakonfigurovaná tak, aby se mohla integrovat s aplikací. Jinými slovy, musí znát, co aplikace používají jako systém identit. Proces uchovávání těchto aplikací na základě služby Azure AD a způsobu jejich zpracování se označuje jako Správa aplikací.

Aplikace spravujete v okně **podnikové aplikace** , které najdete v části Správa na portálu Azure Active Directory.

![Možnost podnikové aplikace v části spravovat na portálu Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Co je systém pro správu identit a přístupu (IAM)?
Aplikace je software, který se používá k nějakému účelu. Většina aplikací vyžaduje, aby se uživatelé přihlásili, aby aplikace mohla poskytovat přizpůsobené prostředí pro konkrétního uživatele. Jinými slovy aplikace potřebuje znát identitu uživatele pomocí aplikace. Protože ví, jakou funkci má uživatel nabízet nebo odebírat,

Pokud každá aplikace udržuje přehled o uživatelích samostatně, pak by výsledkem bylo silo různých uživatelských jmen a přihlášení pro každou aplikaci. Jedna aplikace by neznala žádné informace o uživatelích v jiných aplikacích.

Centralizovaný systém identit tento problém vyřeší tím, že poskytuje jediné místo pro ukládání informací o uživateli, které mohou být použity všemi aplikacemi. Tyto systémy jsou známé jako systémy správy identit a přístupu (IAM). Azure Active Directory je systém IAM pro Cloud Microsoftu.

>[!TIP]
>Systém IAM poskytuje jediné místo, kde si můžete sledovat identity uživatelů. Azure AD je systém IAM pro Cloud Microsoftu.


## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Každý den se přidávají, vyvíjejí a vyřazují nové aplikace. Díky tomu, že mnoho aplikací a přístupových bodů je důležitější než dřív, použití cloudového řešení ke správě přístupu uživatelů ke všem aplikacím.

>[!TIP]
>Galerie aplikací Azure AD obsahuje mnoho oblíbených aplikací, které už jsou předem nakonfigurované tak, aby fungovaly se službou Azure AD jako zprostředkovatel identity.

## <a name="how-does-azure-ad-work-with-applications"></a>Jak Azure AD pracuje s aplikacemi?

Azure AD zjednodušuje způsob správy aplikací tím, že poskytuje jeden systém identit pro cloudové a místní aplikace. Do Azure AD můžete přidat své aplikace SaaS (software jako služba), místní aplikace a obchodní aplikace (LOB). Pak se uživatelé přihlásí jednou k bezpečnému a bezproblémovému přístupu k těmto aplikacím společně s Microsoft 365 a dalšími podnikovými aplikacemi od Microsoftu. [Automatizaci zřizování uživatelů](../app-provisioning/user-provisioning.md)můžete snížit náklady na správu. K zajištění zabezpečeného přístupu k aplikacím můžete použít taky službu Multi-Factor Authentication a zásady podmíněného přístupu.

![Diagram, který zobrazuje aplikace federované přes Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jaké typy aplikací je možné integrovat se službou Azure AD?

Existují čtyři hlavní typy aplikací, které můžete přidat do **podnikových aplikací** a spravovat je pomocí Azure AD:

- **Aplikace Galerie Azure AD** – Azure AD obsahuje galerii obsahující tisíce aplikací, které jsou předem integrované pro jednotné přihlašování pomocí Azure AD. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. [Přečtěte si, jak naplánovat integraci aplikací](plan-an-application-integration.md), nebo Získejte podrobné kroky integrace pro jednotlivé aplikace v [kurzech k aplikacím SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Místní aplikace s proxy aplikací** – s Azure proxy aplikací služby AD můžete integrovat místní webové aplikace se službou Azure AD, aby podporovaly jednotné přihlašování. Pak koncoví uživatelé budou mít přístup k místním webovým aplikacím stejným způsobem jako přístup k Microsoft 365 a dalším aplikacím SaaS, najdete je v tématu [poskytnutí vzdáleného přístupu k místním aplikacím prostřednictvím proxy aplikací služby Azure AD](application-proxy.md).

- **Vlastní aplikace vyvíjené** – při vytváření vlastních obchodních aplikací je můžete integrovat se službou Azure AD a podporovat jednotné přihlašování. Díky registraci aplikace ve službě Azure AD máte kontrolu nad zásadami ověřování pro aplikaci. Další informace najdete v tématu [doprovodné materiály pro vývojáře](developer-guidance-for-integrating-applications.md).

- **Aplikace mimo galerii** – Přineste si vlastní aplikace! Podpora jednotného přihlašování pro jiné aplikace jejich přidáním do služby Azure AD. Existuje několik způsobů, jak integrovat aplikaci, některé z nich jsou uvedeny níže. Další informace najdete v tématu [Konfigurace jednotného přihlašování SAML](configure-saml-single-sign-on.md).

>[!TIP]
>Službu Azure AD můžete integrovat s aplikací i v případě, že ještě není předem nakonfigurovaná a v galerii aplikací. **Službu Azure AD můžete integrovat s některým** z následujících způsobů:
> - Libovolný webový odkaz nebo aplikace, které vykreslí **pole uživatelské jméno a heslo**.
> - Všechny aplikace, které podporují **protokoly SAML nebo OpenID Connect**.
> - Všechny aplikace, které podporují **systém pro správu identit mezi doménami (SCIM)** .

## <a name="manage-risk-with-conditional-access-policies"></a>Řízení rizik pomocí zásad podmíněného přístupu

Přihlašování služby Azure AD jednotného přihlašování (SSO) s [podmíněným přístupem](../conditional-access/concept-conditional-access-cloud-apps.md) poskytuje vysoké úrovně zabezpečení pro přístup k aplikacím. Mezi možnosti zabezpečení patří ochrana identity v cloudovém měřítku, řízení přístupu na základě rizika, nativní vícefaktorové ověřování a zásady podmíněného přístupu. Tyto funkce umožňují podrobné zásady řízení přístupu na základě aplikací nebo skupin, které potřebují vyšší úroveň zabezpečení.

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování

Povolení jednotného přihlašování (SSO) napříč aplikacemi a Microsoft 365 poskytuje uživatelům nadřazené přihlašování pomocí omezení nebo eliminace výzev k přihlášení. Prostředí je tak pro uživatele ucelenější, neruší ho zobrazováním mnoha výzev k přihlášení a uživatel nemusí spravovat mnoho hesel. Obchodní skupiny mohou spravovat a schvalovat přístup prostřednictvím samoobslužného a dynamického členství. Když správu přístupu k aplikacím povolíte správným lidem, zvýšíte zabezpečení systému identit.

Jednotné přihlašování zvyšuje zabezpečení. *Bez jednotného přihlašování* musí správci vytvářet a aktualizovat uživatelské účty pro každou jednotlivou aplikaci, což zabere nějaký čas. Uživatelé navíc musí používat pro přístup ke svým aplikacím různé přihlašovací údaje. V důsledku toho uživatelé tíhnou k tomu, aby si zapisovali hesla nebo používali jiná řešení pro správu hesel, což s sebou nese riziko ohrožení zabezpečení dat. [Přečtěte si další informace o jednotném přihlašování](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů

Pomocí Azure AD můžete monitorovat přihlašování k aplikacím prostřednictvím sestav, které využívají nástroje SIEM (Security Incident and Event Monitoring). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů

Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

Pro aplikace zaměřené na lidské zdroje nebo jiné aplikace s velkou sadou uživatelů můžete využít zřizování aplikací k automatizaci procesu zřizování a rušení zřizování uživatelů, viz téma [co je zřizování aplikací?](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](view-applications-portal.md)
- [Začínáme s integrací aplikací](plan-an-application-integration.md)
- [Informace o automatizaci zřizování](../app-provisioning/user-provisioning.md)
