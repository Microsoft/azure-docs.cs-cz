---
title: Jaké jsou základní informace o Azure správu identit a přístupu? – Azure Active Directory | Dokumentace Microsoftu
description: Další informace o možnostech pokročilé ochrany a další nástroje, které jsou k dispozici s edicí Azure Active Directory Premium.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734668"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Jaké jsou základní informace o Azure správu identit a přístupu?
Azure AD Premium je cloudové identity a přístupu řešení pro správu, s funkcemi rozšířené ochrany. Tyto rozšířené možnosti poskytují zabezpečená identita pro všechny vaše aplikace ochrana identity (rozšířené s [Microsoft graphu security intelligence](https://www.microsoft.com/security/intelligence)), a [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Azure AD pomáhá chránit identity vašich uživatelů v reálném čase, což pomáhá vytvořit na základě rizik a adaptivní zásady přístupu kolem údajů o vaší organizaci.

Podívejte se na toto krátké video s rychlým přehledem správy a ochrany identit Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Azure AD také poskytuje sadu nástrojů, které pomáhají zabezpečit, automatizovat a spravovat prostředí, včetně resetování hesel, Správa uživatelů a skupin a požadavky aplikace. Azure AD také můžete spravovat zařízení uživatelů a přístup a kontrolu nad Software jako služba (SaaS) aplikací.

Další informace o nákladech edice Azure Active Directory Premium a související nástroje najdete v tématu [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Propojení místní služby Active Directory s Azure AD a Office 365
Rozšířit vaše místní implementace služby Active Directory do cloudu díky integraci s Azure AD prostřednictvím místních adresářů [správy hybridních identit](https://aka.ms/aadframework). [Azure AD Connect](../connect/active-directory-aadconnect.md) poskytuje Tato integrace poskytuje uživatelům jedné identity a jednotného přihlašování (SSO) přístup k místním prostředkům a cloudové služby, jako je Office 365.

Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako jsou nástroje DirSync a Azure AD Sync, a usnadnit tak vyhověl vaším potřebám identity synchronizace mezi místními a Azure AD. Synchronizace Azure AD Connect je povolená díky:

- **Synchronizace.** Za vytváření uživatelů, skupin a dalších objektů. Je také zajistit, že informace o identitě pro místní uživatele odpovídá Novinky ve službě Azure AD. Zapnete zpětný zápis hesla také pomáhá udržovat synchronizované místních adresářů, když se uživatelé aktualizace hesla ve službě Azure AD.

- ověřování. Volba metody správné ověřování je důležité při nastavování služby Azure AD hybridní řešení identit. Můžete použít cloudové ověřování (synchronizace hodnot Hash hesel / předávací ověřování) nebo federovaného ověřování (služba AD FS) pro vaši organizaci. Další informace o dostupných parametrech najdete v tématu [zvolte správné ověřování pro vaše řešení hybridní identity Azure Active Directory](https://aka.ms/auth-options).

- **Monitorování stavu.** Azure AD Connect Health poskytuje monitorování a centrální umístění na webu Azure portal, chcete-li zobrazit tuto aktivitu. Další informace najdete v tématu [Sledování infrastruktury místních identit a synchronizačních služeb v cloudu](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zvýšení produktivity a snížení nákladů na helpdesk s využitím samoobslužného a jednotného přihlašování
Uživatelé ušetřit čas při mají jednotné uživatelské jméno a heslo a současně konzistentní prostředí na všech zařízeních. Uživatelé také ušetřit čas prováděním úloh samoobslužných služeb, jako je třeba[resetovat zapomenuté heslo](../user-help/active-directory-passwords-update-your-own-password.md) nebo žádosti o přístup k aplikaci bez čekání technickou podporu o pomoc.

Posílení jednotné přihlašování a jednotné prostředí, Azure AD [rozšiřuje vaši místní Active Directory](../connect/active-directory-aadconnect.md) do cloudu, aby vaši uživatelé používali svůj primární účet organizace pro zařízení připojená k doméně firemním prostředkům, a všechny webové a aplikacím SaaS, které potřebují k pomocí provádí úlohy. 

Kromě toho přístup k aplikaci lze automaticky zřídit (nebo zrušení zřízení) na základě členství ve skupinách a zaměstnance stav uživatele, což pomáhá řídit přístup do Galerie aplikací nebo vlastních místních aplikací, které jste vyvinuté a publikované prostřednictvím [Proxy aplikací Azure AD](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Správa a řízení přístupu k prostředkům vaší organizace
Řešení správy identit a přístupu Microsoft umožňují chránit přístup k aplikacím a prostředkům napříč datového centra vaší organizace a do cloudu. Tento přístup management pomáhá zajistit další úrovně ověřování [ověřování službou Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) a [zásady podmíněného přístupu](../conditional-access/overview.md). Monitorování podezřelé aktivity prostřednictvím rozšířená tvorba sestav zabezpečení, auditování a výstrahy navíc můžou pomoct zmírnit potenciální potíže se zabezpečením.

Pomocí zásad podmíněného přístupu v Azure AD Premium vám umožňuje vytvořit pravidla přístupu na základě zásad pro libovolnou AD připojené aplikaci Azure, jako jsou například aplikace SaaS, vlastní aplikace běžící v cloudu nebo lokálně nebo webové aplikace). Azure AD vyhodnocuje pravidla v reálném čase, jejich vynucením pokaždé, když se uživatel pokusí o přístup k aplikaci. Zásady ochrany identit Azure vám umožní automaticky Napravte (podle blokování přístupu, vynucování služby Multi-Factor Authentication nebo resetování hesel uživatelů) Pokud je zjištěno podezřelé aktivity.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), součástí edice Azure Active Directory Premium 2, vám umožní zjistit, omezte a monitorujte účty pro správu a jejich přístup k prostředkům v Azure Active Directory a další Služeb Microsoft online services. PIM umožňuje spravovat přístup správce na vyžádání pro přesné časový interval, co potřebujete, což znamená, že můžete povolit správcům žádost o ověření službou Multi-Factor Authentication, dočasné zvýšení svých oprávnění pro předem určenou dobu před jejich účty vrátit do stavu v režimu normálního uživatele.

## <a name="next-steps"></a>Další postup
Další informace o architektuře služby Azure AD najdete v tématu [co je architektura služby Azure AD?](active-directory-architecture.md).
