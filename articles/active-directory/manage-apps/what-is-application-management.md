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
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: ad572188ceb15a948e4242d0521b8304db45e65b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732337"
---
# <a name="what-is-application-management"></a>Co je správa aplikací?

Azure AD je systém pro správu identit a přístupu (IAM, Identity and Access Management). Poskytuje centrální místo pro ukládání informací o digitálních identitách. Softwarové aplikace můžete nakonfigurovat tak, aby jako místo, kam se ukládají informace o uživatelích, používaly Azure AD. 

Služba Azure AD musí být nakonfigurovaná tak, aby se mohla integrovat s aplikací. Jinými slovy oddělení IT potřebuje zjistit, jaké aplikace tyto identity používají. Díky tomu, že Azure AD ví o těchto aplikacích a jak je zpracovat, se označuje jako Správa aplikací.

Aplikace spravujete na stránce **podnikové aplikace** , která se nachází v části spravovat na portálu Azure Active Directory.

![Možnost podnikové aplikace v části spravovat na portálu Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Co je systém pro správu identit a přístupu (IAM)?
Aplikace je software, který se používá k nějakému účelu. Většina aplikací vyžaduje, aby se uživatelé přihlásili.

Centralizovaný systém identit poskytuje jediné místo pro ukládání informací o uživateli, které mohou být použity všemi aplikacemi. Tyto systémy jsou známé jako systémy správy identit a přístupu (IAM). Azure Active Directory je systém IAM pro Cloud Microsoftu.

>[!TIP]
>Systém IAM poskytuje jediné místo, kde si můžete sledovat identity uživatelů. Azure AD je systém IAM pro Cloud Microsoftu.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Nové aplikace se přidávají, vyvíjí a západ slunce. Díky mnoha aplikacím a přístupovým bodům je důležité používat řešení identity, které s nimi funguje.

>[!TIP]
>Galerie aplikací Azure AD obsahuje mnoho oblíbených aplikací, které už jsou předem nakonfigurované tak, aby fungovaly se službou Azure AD jako zprostředkovatel identity.

## <a name="how-does-azure-ad-work-with-apps"></a>Jak Azure AD funguje s aplikacemi?

Služba Azure AD je uprostřed střední a poskytuje správu identit pro cloudové a místní aplikace. 

![Diagram, který zobrazuje aplikace federované přes Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>[Automatizace zřizování uživatelů](../app-provisioning/user-provisioning.md) vám umožní snížit náklady na správu, aby se uživatelé do služby Azure AD automaticky přidali, když je přidáte do svého systému pro personální oddělení. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jaké typy aplikací je možné integrovat se službou Azure AD?

Službu Azure AD můžete používat jako svůj systém identit, a to jenom pro libovolnou aplikaci. Řada aplikací je již předem nakonfigurovaná a je možné ji nastavit s minimálním úsilím. Tyto předem nakonfigurované aplikace se publikují v [galerii aplikace Azure AD](/azure/active-directory/saas-apps/). 

Většinu aplikací můžete nakonfigurovat ručně pro jednotné přihlašování, pokud už nejsou v galerii. Azure AD poskytuje několik možností jednotného přihlašování. Někteří nejoblíbenější jsou jednotné přihlašování založené na rozhraní SAML a jednotné přihlašování založené na OIDC. Další informace o integraci aplikací pro povolení jednotného přihlašování najdete v tématu [Možnosti jednotného přihlašování](sso-options.md). 

Používá vaše organizace místní aplikace? Můžete je integrovat pomocí proxy aplikací. Další informace najdete v tématu [poskytnutí vzdáleného přístupu k místním aplikacím prostřednictvím proxy aplikací služby Azure AD](application-proxy.md).

>[!TIP]
>Když vytváříte vlastní obchodní aplikace, můžete je integrovat s Azure AD, aby podporovaly jednotné přihlašování. Další informace o vývoji aplikací pro Azure AD najdete v tématu [Microsoft Identity Platform](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Řízení rizik pomocí zásad podmíněného přístupu

Přihlašování služby Azure AD jednotného přihlašování (SSO) s [podmíněným přístupem](../conditional-access/concept-conditional-access-cloud-apps.md) poskytuje vysoké úrovně zabezpečení pro přístup k aplikacím. Zásady podmíněného přístupu poskytují podrobné řízení aplikacím na základě nastavených podmínek. 

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování

Jednotné přihlašování (SSO) poskytuje jednotné uživatelské prostředí mezi Microsoft 365 a všemi ostatními aplikacemi, které používáte. Řekněme, že nepřetržitě zadáte uživatelské jméno a heslo.

Další informace o jednotném přihlašování najdete v tématu [co je jednotné přihlašování](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů

Monitorujte aplikace prostřednictvím sestav, které používají nástroje pro SIEM (incidenty zabezpečení a sledování událostí). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů

Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

Pro aplikace zaměřené na lidské zdroje nebo jiné aplikace s velkou sadou uživatelů můžete pomocí zřizování aplikací usnadnit život. Zřizování aplikací automatizuje proces přidávání a odebírání uživatelů. Další informace najdete v tématu [co je zřizování aplikací?](../app-provisioning/user-provisioning.md) .

## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](view-applications-portal.md)
- [Začínáme s integrací aplikací](plan-an-application-integration.md)
- [Informace o automatizaci zřizování](../app-provisioning/user-provisioning.md)