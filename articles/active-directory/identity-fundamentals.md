---
title: Základy správy identit Azure
description: Cloudové identity jsou nyní nejlepší způsob, jak udržovat kontrolu nad a získat přehled o tom, jak a kdy uživatelé přístup k podnikovým aplikacím a datům.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 552c777b9ae8b33bc624436aeecc8ef9e097cf3c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713785"
---
# <a name="fundamentals-of-azure-identity-management"></a>Základy správy identit Azure

Jako více společnosti digitální prostředky za provozu mimo podnikovou síť, v cloudu a na zařízeních, skvělé cloudových identit a přístupu řešení pro správu se stává stále nezbytné. Cloudové identity jsou nyní nejlepší způsob, jak udržovat kontrolu nad a získat přehled o tom, jak a kdy uživatelé přístup k podnikovým aplikacím a datům.

Microsoft má byla zabezpečení cloudových identit pro přes deset a teď se [Azure Active Directory (AD)](active-directory-whatis.md), jsou k dispozici tyto stejné systémy ochrany. S Azure AD můžete správcům snadno zajistit odpovědnosti za uživatele a správce s lepší zabezpečení a zásad správného řízení než kdy dřív.

Azure AD Premium je cloudových identit a přístupu řešení pro správu pomocí funkce rozšířené ochrany umožňující jednu zabezpečené identitu pro všechny aplikace, ochranu identity (rozšířené pomocí [Microsoft graph zabezpečení intelligence](https://www.microsoft.com/en-us/security/intelligence)) a Privileged Identity Management. Jenom další monitorování nebo generování sestav nástroj Azure AD Premium může chránit identit uživatelů v reálném čase a umožňují vytvořit zásady na základě riziko, adaptivní přístupu k ochraně dat vaší organizace.

Podívejte se na následující krátké video pro rychlý přehled o správu identit Azure AD a ochrany:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Společnost Microsoft poskytuje nejen identitou, která přebírá everywhere, ale taky sadu nástrojů pro automatizaci, zabezpečení a správě IT v rámci vaší organizace. I po nástupem cloud computing přetrvává vyžádání spravovat a řídit IT úkoly, jako je volání na helpdesk k resetování uživatelských hesel, správu skupiny uživatelů a žádosti o aplikace. Komplikují další věci, zaměstnanci teď přináší svá osobní zařízení pro práci a pomocí snadno dostupné SaaS aplikace, které umožňuje údržbu ovládat svých aplikací mezi datovými centry podnikové a veřejný cloud platformy významné výzvu.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Připojit místní službu Active Directory s Azure AD a Office 365
Organizace, které provedli velké investice v místní službě Active Directory můžete rozšířit tyto investice do cloudu díky integraci svých místních adresářů se Azure AD do [hybridní Správa identit](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Díky tomu umožňuje uživatelům zvýšit produktivitu tím, že poskytuje společnou identitu pro přístup k prostředkům bez ohledu na umístění. Uživatelé a organizace můžou pak používat jednotné přihlašování (SSO) přístup k prostředkům i místní a cloudové služby, například Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) je jediný nástroj je nutné získat integrace provést. Azure AD Connect poskytuje možnosti pro podporu vašich potřeb synchronizace identit a nahrazuje starší verze nástrojů pro integraci identity například DirSync a Azure AD Sync. S Azure AD Connect správu identit a synchronizace mezi místními a Azure AD je povolená díky:

- Synchronizace – tato komponenta odpovídá za vytváření uživatelů, skupin a dalších objektů. Také zajišťuje, aby se informace o identitě místních uživatelů a skupin shodovaly s cloudem. Zpětný zápis hesla můžete také povolit pro synchronizaci místních adresářů když uživatel aktualizuje hesla ve službě Azure AD.
- Služba AD FS - federace se nachází volitelné funkce poskytované přes Azure AD Connect, který můžete použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury služby AD FS. Federační slouží organizacím řešení komplexních nasazení, například čipová karta nebo vícefaktorového ověřování jiných výrobců.
- Monitorování stavu – [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) může poskytovat robustní monitorování a centrální umístění na portálu Azure k zobrazení této aktivity.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zvýšení produktivity a snížit náklady na technickou podporu s samoobslužné služby a jeden přihlašování

Zaměstnanci jsou zvýšit produktivitu, pokud mají jedné uživatelské jméno a heslo k mějte na paměti a konzistentního prostředí z každé zařízení. Budou také ušetřit čas při mohou provádět samoobslužné služby úkoly, jako je [resetování zapomenuté heslo](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) nebo žádají o přístup k aplikaci bez čekání na pomoc od oddělení technické podpory.

Azure AD [rozšiřuje místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) do cloudu, uživatelům umožníte používat jejich primární účtu organizace pro svá zařízení připojených k doméně, prostředkům společnosti a všechny webové a aplikace SaaS jejich měli používat ke své práci. Kromě není si museli pamatovat více sad uživatelských jmen a hesel, přístup k aplikaci uživatelů lze také automaticky zřízený (nebo zrušte zřízený) na základě jejich členství ve skupinách organizace a jejich stav jako zaměstnanec. A můžete řídit přístup k pro galerii aplikace nebo pro své vlastní místní aplikace, které jste vyvinuté a publikované prostřednictvím [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Správa a řízení přístupu k podnikovým prostředkům
Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, například povolení další úrovně ověřování [služby Multi-Factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) a [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Monitorování podezřelé aktivity přes pokročilé zabezpečení vytváření sestav a auditování, výstrahy, pomáhá zmírnit potenciální potíže se zabezpečením.

Zásady podmíněného přístupu v Azure AD Premium poskytnout vám správce podnikové sítě, možnost vytvářet pravidla na základě zásad přístupu k Azure AD připojení aplikací (aplikace SaaS, vlastní aplikace běžící v cloudu nebo na místní webové aplikace). Azure AD vyhodnotí tyto zásady v reálném čase a vynucuje je vždy, když uživatel pokusí o přístup k aplikaci. Zásady ochrany identit Azure umožňují automaticky provést akci, pokud je zjištěno podezřelou aktivitu. Tyto akce mohou zahrnovat blokování přístupu pro uživatele na vysoce rizikové, vynucování služby Multi-Factor authentication a resetování hesla uživatele, pokud to vypadá ohrožený přihlašovací údaje.


## <a name="azure-active-directory-privileged-identity-management"></a>Správa privilegovaných identit v Azure Active Directory

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), součástí Azure Active Directory Premium P2 nabídky, můžete zjistit, omezte a monitorujte účty pro správu a jejich přístup k prostředkům v Azure Active Directory a dalšího softwaru společnosti Microsoft Chcete-li služeb online services. Pomáhá také můžete spravovat přístup pro správu na vyžádání pro přesnou dobu, po které potřebujete.

Správa privilegovaných identit můžete vynutit oprávnění správce na vyžádání, tak, aby správci požadovat Multi-Factor ověřený, dočasný zvýšení svá oprávnění na předem nakonfigurovaná dobu před jejich účty vrátit do stavu normální.

## <a name="benefits-of-azure-identity"></a>Výhody identit Azure

Správa identit Azure můžete:

-   Vytvářet a spravovat jedinou identitu pro každého uživatele v celé organizaci, udržování uživatelé, skupiny a zařízení synchronizace s [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Poskytnutí přístupu jeden přihlašování pro vaše aplikace, včetně tisícům předem integrovaných aplikací SaaS nebo poskytovat zabezpečený vzdálený přístup k místním aplikacím SaaS pomocí [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Povolit aplikaci přístup k zabezpečení vynucením založeného na pravidlech [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) pro místní i cloudové aplikace.

-   Zvýšení produktivity uživatelů s [samoobslužného resetování hesel](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)a skupiny a aplikace přístup k požadavků pomocí [MyApps portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Využít [vysokou dostupnost a spolehlivost](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) služby po celém světě, podnikové úrovni, cloudových identit a přístupu řešení pro správu.

## <a name="next-steps"></a>Další postup
[Další informace o řešení Azure identity](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)