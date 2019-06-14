---
title: Správa aplikací pomocí služby Azure Active Directory | Microsoft Docs
description: Tento článek popisuje výhody integrace Azure Active Directory s vaší místní a cloudové aplikace a aplikace SaaS.
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
ms.openlocfilehash: 9246d7bd48579def171986606e88c09593029aa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108148"
---
# <a name="application-management-with-azure-active-directory"></a>Správa aplikací pomocí služby Azure Active Directory

Azure Active Directory (Azure AD) zjednodušuje způsob, jak spravovat vaše aplikace tím, že poskytuje jedinou identitu systému pro vaše cloudové a místní aplikace. Můžete přidat svůj software jako služba (SaaS) aplikací, u místních aplikací a obchodní (LOB) aplikace do služby Azure AD. Pak uživatele přihlásit jednou snadno a bezpečně přístup k těmto aplikacím, spolu s Office 365 a další obchodní aplikace od Microsoftu. Snížit náklady na správu pomocí automatizace zřizování uživatelů. Můžete také použít zásady podmíněného přístupu a ověřování službou Multi-Factor Authentication a zajistit tak zabezpečenou webovou aplikaci přístup.

![Aplikace federované přes službu Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Každý den se přidávají, vyvíjejí a vyřazují nové aplikace. Mnoho aplikací a přístupové body je důležitější než kdy dřív cloudové řešení ke správě přístupu uživatelů ke všem aplikacím.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jaké typy aplikací můžete integrovat s Azure AD?
Existují čtyři hlavní typy aplikací, které můžete přidat do vaší **podnikové aplikace** a spravovat pomocí služby Azure AD:

-   **Aplikace Azure AD Fotogalerie** – Azure AD má galerie, která obsahuje tisíce aplikací, které byly předem integrovaných pro jednotné přihlašování s Azure AD. Galerie pravděpodobně obsahuje i některé aplikace, které využívá vaše organizace. [Další informace o plánování integraci vašich aplikací](plan-an-application-integration.md), nebo získat integrace podrobné kroky pro jednotlivé aplikace v [kurzy k aplikacím SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/). 

-   **Místní aplikace pomocí Proxy aplikace** – s Azure AD Application Proxy, můžete integrovat svoje místní webové aplikace s Azure AD pro podporu jednotného přihlašování. Pak mají přístup koncoví uživatelé webových aplikací v místním stejným způsobem, jakým přistupují k Office 365 a dalším aplikacím SaaS. [Zjistěte, proč použít Proxy aplikace a jak to funguje](what-is-application-proxy.md).

-   **Vlastní aplikace** – při vytváření vlastní-obchodních aplikací, můžete je integrovat s Azure AD pro podporu jednotného přihlašování. Když si zaregistrujete aplikaci s využitím Azure AD, budete mít kontrolu nad zásady ověřování pro aplikaci. Další informace najdete v tématu [doprovodné materiály pro vývojáře](developer-guidance-for-integrating-applications.md).

-   **Aplikace mimo galerii** – používání vlastních aplikací. Podpora jednotného přihlašování pro jiné aplikace jejich přidáním do služby Azure AD. Můžete integrovat všechny webový odkaz, který chcete nebo jakékoliv aplikace, která vykreslí pole uživatelského jména a hesla, podporuje přes protokol SAML nebo OpenID Connect nebo SCIM podporuje. Další informace najdete v tématu [nakonfigurovat jednotné přihlašování pro aplikace mimo galerii](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Řízení rizik pomocí zásad podmíněného přístupu
Párování s Azure AD jednotné přihlašování (SSO) [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) poskytuje vysokou úroveň zabezpečení pro přístup k aplikacím. Funkce zabezpečení zahrnují ochranu cloudových identit, řízení přístupu na základě rizik, nativní služby Multi-Factor authentication a zásady podmíněného přístupu. Tyto funkce umožňují podrobné zásady řízení přístupu na základě aplikací nebo skupin, které potřebují vyšší úroveň zabezpečení.

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování
Povolení jednotného přihlašování mezi aplikacemi a Office 365 poskytuje stávajícím uživatelům nadstandardní prostředí pro přihlašování, protože snižuje počet výzev k přihlášení nebo je úplně eliminuje. Prostředí je tak pro uživatele ucelenější, neruší ho zobrazováním mnoha výzev k přihlášení a uživatel nemusí spravovat mnoho hesel. Obchodní skupiny mohou spravovat a schvalovat přístup prostřednictvím samoobslužného a dynamického členství. Když správu přístupu k aplikacím povolíte správným lidem, zvýšíte zabezpečení systému identit.

Jednotné přihlašování zvyšuje zabezpečení. *Bez jednotného přihlašování* musí správci vytvářet a aktualizovat uživatelské účty pro každou jednotlivou aplikaci, což zabere nějaký čas. Uživatelé navíc musí používat pro přístup ke svým aplikacím různé přihlašovací údaje. V důsledku toho uživatelé tíhnou k tomu, aby si zapisovali hesla nebo používali jiná řešení pro správu hesel, což s sebou nese riziko ohrožení zabezpečení dat. [Další informace o jednotné přihlašování](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů
Pomocí Azure AD můžete monitorovat přihlašování k aplikacím prostřednictvím sestav, které využívají nástroje SIEM (Security Incident and Event Monitoring). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů
Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

## <a name="next-steps"></a>Další postup

- [Co je Proxy aplikací?](what-is-application-proxy.md)
- [Rychlé zprovoznění: Přidání Galerie aplikace do svého tenanta Azure AD](add-application-portal.md)
