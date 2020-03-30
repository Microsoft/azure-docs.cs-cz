---
title: Správa aplikací pomocí služby Azure Active Directory | Microsoft Docs
description: Tento článek popisuje výhody integrace služby Azure Active Directory s místními, cloudovými a SaaS aplikacemi.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca73fac06649f801461e53130a67aa9ec0ad0d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063318"
---
# <a name="application-management-with-azure-active-directory"></a>Správa aplikací pomocí služby Azure Active Directory

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým spravujete aplikace tím, že poskytuje jeden systém identit pro vaše cloudové a místní aplikace. Software můžete přidat jako aplikace služby (SaaS), místní aplikace a obchodní (LOB) aplikace do Azure AD. Pak se uživatelé jednou přihlásí, aby měli k těmto aplikacím bezpečný a bezproblémový přístup, spolu s Office 365 a dalšími obchodními aplikacemi od Microsoftu. Automatizací [zřizování uživatelů](../app-provisioning/user-provisioning.md)můžete snížit náklady na správu . K zajištění zabezpečeného přístupu k aplikacím můžete také použít vícefaktorové ověřování a zásady podmíněného přístupu.

![Diagram, který zobrazuje aplikace federované prostřednictvím Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Každý den se přidávají, vyvíjejí a vyřazují nové aplikace. S tolika aplikacemi a přístupovými body je důležitější než kdy jindy používat cloudové řešení pro správu přístupu uživatelů ke všem aplikacím.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jaké typy aplikací můžu integrovat s Azure AD?

Existují čtyři hlavní typy aplikací, které můžete přidat do svých **podnikových aplikací** a spravovat pomocí Azure AD:

- **Aplikace Azure AD Gallery** – Azure AD má galerii, která obsahuje tisíce aplikací, které byly předem integrované pro jednotné přihlašování pomocí Azure AD. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. [Přečtěte si o plánování integrace aplikací](plan-an-application-integration.md)nebo podrobné kroky integrace pro jednotlivé aplikace v [kurzech aplikací SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Místní aplikace s proxy aplikací** – s proxy aplikací Azure AD můžete integrovat místní webové aplikace s Azure AD pro podporu jednotného přihlášení. Koncoví uživatelé pak mohou přistupovat k místním webovým aplikacím stejným způsobem jako k Office 365 a dalším aplikacím SaaS. [Zjistěte, proč používat proxy aplikace a jak to funguje](what-is-application-proxy.md).

- **Vlastní vyvinuté aplikace** – Při vytváření vlastních obchodních aplikací je můžete integrovat s Azure AD pro podporu jednotného přihlašování. Registrací aplikace ve službě Azure AD máte kontrolu nad zásadami ověřování pro aplikaci. Další informace naleznete v [pokynech pro vývojáře](developer-guidance-for-integrating-applications.md).

- **Non-Galerie aplikace** - Přineste si vlastní aplikace! Podporujte jednotné přihlašování pro jiné aplikace jejich přidáním do Azure AD. Můžete integrovat libovolný webový odkaz, který chcete, nebo jakoukoli aplikaci, která vykresluje uživatelské jméno a heslo pole, podporuje SAML nebo OpenID Connect protokoly, nebo podporuje SCIM. Další informace naleznete v [tématu Konfigurace jednotného přihlašování pro aplikace, které nejsou galeriemi](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Správa rizik pomocí zásad podmíněného přístupu

Propojení Azure AD jednotné přihlašování (SSO) s [podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) poskytuje vysokou úroveň zabezpečení pro přístup k aplikacím. Možnosti zabezpečení zahrnují cloudovou ochranu identity, řízení přístupu na základě rizik, nativní vícefaktorové ověřování a zásady podmíněného přístupu. Tyto funkce umožňují podrobné zásady řízení přístupu na základě aplikací nebo skupin, které potřebují vyšší úroveň zabezpečení.

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování

Povolení jednotného přihlašování mezi aplikacemi a Office 365 poskytuje stávajícím uživatelům nadstandardní prostředí pro přihlašování, protože snižuje počet výzev k přihlášení nebo je úplně eliminuje. Prostředí je tak pro uživatele ucelenější, neruší ho zobrazováním mnoha výzev k přihlášení a uživatel nemusí spravovat mnoho hesel. Obchodní skupiny mohou spravovat a schvalovat přístup prostřednictvím samoobslužného a dynamického členství. Když správu přístupu k aplikacím povolíte správným lidem, zvýšíte zabezpečení systému identit.

Jednotné přihlašování zvyšuje zabezpečení. *Bez jednotného přihlašování* musí správci vytvářet a aktualizovat uživatelské účty pro každou jednotlivou aplikaci, což zabere nějaký čas. Uživatelé navíc musí používat pro přístup ke svým aplikacím různé přihlašovací údaje. V důsledku toho uživatelé tíhnou k tomu, aby si zapisovali hesla nebo používali jiná řešení pro správu hesel, což s sebou nese riziko ohrožení zabezpečení dat. [Přečtěte si další informace o jednotném přihlašování](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů

Pomocí Azure AD můžete monitorovat přihlašování k aplikacím prostřednictvím sestav, které využívají nástroje SIEM (Security Incident and Event Monitoring). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů

Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

## <a name="next-steps"></a>Další kroky

- [Co je proxy aplikací?](what-is-application-proxy.md)
- [Úvodní příručka: Přidání aplikace galerie do klienta Azure AD](add-application-portal.md)
