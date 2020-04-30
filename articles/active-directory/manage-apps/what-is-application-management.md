---
title: Správa aplikací pomocí služby Azure Active Directory | Microsoft Docs
description: Tento článek popisuje výhody Integrace Azure Active Directory s vašimi místními, cloudem a SaaS aplikacemi.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77063318"
---
# <a name="application-management-with-azure-active-directory"></a>Správa aplikací pomocí Azure Active Directory

Azure Active Directory (Azure AD) zjednodušuje způsob správy aplikací tím, že poskytuje jeden systém identit pro cloudové a místní aplikace. Do Azure AD můžete přidat své aplikace SaaS (software jako služba), místní aplikace a obchodní aplikace (LOB). Pak se uživatelé přihlásí jednou k bezpečnému a bezproblémovému přístupu k těmto aplikacím společně se sadou Office 365 a dalšími podnikovými aplikacemi od Microsoftu. [Automatizaci zřizování uživatelů](../app-provisioning/user-provisioning.md)můžete snížit náklady na správu. K zajištění zabezpečeného přístupu k aplikacím můžete použít taky službu Multi-Factor Authentication a zásady podmíněného přístupu.

![Diagram, který zobrazuje aplikace federované přes Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Každý den se přidávají, vyvíjejí a vyřazují nové aplikace. Díky tomu, že mnoho aplikací a přístupových bodů je důležitější než dřív, použití cloudového řešení ke správě přístupu uživatelů ke všem aplikacím.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jaké typy aplikací je možné integrovat se službou Azure AD?

Existují čtyři hlavní typy aplikací, které můžete přidat do **podnikových aplikací** a spravovat je pomocí Azure AD:

- **Aplikace Galerie Azure AD** – Azure AD obsahuje galerii obsahující tisíce aplikací, které jsou předem integrované pro jednotné přihlašování pomocí Azure AD. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. [Přečtěte si, jak naplánovat integraci aplikací](plan-an-application-integration.md), nebo Získejte podrobné kroky integrace pro jednotlivé aplikace v [kurzech k aplikacím SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Místní aplikace s proxy aplikací** – s Azure proxy aplikací služby AD můžete integrovat místní webové aplikace se službou Azure AD, aby podporovaly jednotné přihlašování. Pak koncoví uživatelé budou mít přístup k místním webovým aplikacím stejným způsobem jako přistupující k Office 365 a dalším aplikacím SaaS. [Zjistěte, proč používat proxy aplikace a jak funguje](what-is-application-proxy.md).

- **Vlastní aplikace vyvíjené** – při vytváření vlastních obchodních aplikací je můžete integrovat se službou Azure AD a podporovat jednotné přihlašování. Díky registraci aplikace ve službě Azure AD máte kontrolu nad zásadami ověřování pro aplikaci. Další informace najdete v tématu [doprovodné materiály pro vývojáře](developer-guidance-for-integrating-applications.md).

- **Aplikace mimo galerii** – Přineste si vlastní aplikace! Podpora jednotného přihlašování pro jiné aplikace jejich přidáním do služby Azure AD. Můžete integrovat libovolný webový odkaz, který chcete, nebo libovolnou aplikaci, která vykresluje pole uživatelské jméno a heslo, podporuje protokoly SAML nebo OpenID Connect nebo podporuje SCIM. Další informace najdete v tématu [Konfigurace jednotného přihlašování pro aplikace mimo galerii](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Řízení rizik pomocí zásad podmíněného přístupu

Přihlašování služby Azure AD jednotného přihlašování (SSO) s [podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) poskytuje vysoké úrovně zabezpečení pro přístup k aplikacím. Mezi možnosti zabezpečení patří ochrana identity v cloudovém měřítku, řízení přístupu na základě rizika, nativní vícefaktorové ověřování a zásady podmíněného přístupu. Tyto funkce umožňují podrobné zásady řízení přístupu na základě aplikací nebo skupin, které potřebují vyšší úroveň zabezpečení.

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování

Povolení jednotného přihlašování mezi aplikacemi a Office 365 poskytuje stávajícím uživatelům nadstandardní prostředí pro přihlašování, protože snižuje počet výzev k přihlášení nebo je úplně eliminuje. Prostředí je tak pro uživatele ucelenější, neruší ho zobrazováním mnoha výzev k přihlášení a uživatel nemusí spravovat mnoho hesel. Obchodní skupiny mohou spravovat a schvalovat přístup prostřednictvím samoobslužného a dynamického členství. Když správu přístupu k aplikacím povolíte správným lidem, zvýšíte zabezpečení systému identit.

Jednotné přihlašování zvyšuje zabezpečení. *Bez jednotného přihlašování* musí správci vytvářet a aktualizovat uživatelské účty pro každou jednotlivou aplikaci, což zabere nějaký čas. Uživatelé navíc musí používat pro přístup ke svým aplikacím různé přihlašovací údaje. V důsledku toho uživatelé tíhnou k tomu, aby si zapisovali hesla nebo používali jiná řešení pro správu hesel, což s sebou nese riziko ohrožení zabezpečení dat. [Přečtěte si další informace o jednotném přihlašování](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů

Pomocí Azure AD můžete monitorovat přihlašování k aplikacím prostřednictvím sestav, které využívají nástroje SIEM (Security Incident and Event Monitoring). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů

Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

## <a name="next-steps"></a>Další kroky

- [Co je proxy aplikací?](what-is-application-proxy.md)
- [Rychlý Start: Přidání aplikace Galerie do tenanta Azure AD](add-application-portal.md)
