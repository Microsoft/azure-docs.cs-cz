---
title: Základy správy identit Azure | Microsoft Docs
description: Cloudové identity v současné době představují nejlepší způsob, jak si zachovat přehled a kontrolu nad tím, jak a kdy uživatelé přistupují k podnikovým aplikacím a datům.
keywords: ''
author: eross-msft
manager: mtillman
ms.reviewer: jsnow
ms.author: lizross
ms.date: 08/07/2018
ms.topic: overview
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 327cecd129befb56c33d7fcf2d59ee5b58a18549
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022824"
---
# <a name="fundamentals-of-azure-identity-management"></a>Základy správy identit Azure

S rostoucím množstvím digitálních prostředků společností, které se nacházejí mimo podnikovou síť, v cloudu a na zařízeních, se stává nezbytností zajistit skvělé cloudové řešení pro správu identit a přístupu. Cloudové identity v současné době představují nejlepší způsob, jak si zachovat přehled a kontrolu nad tím, jak a kdy uživatelé přistupují k podnikovým aplikacím a datům.

Microsoft se věnuje zabezpečení cloudových identit již více než deset let a nyní máte prostřednictvím [Azure Active Directory (AD)](active-directory-whatis.md) k dispozici stejné ochranné systémy i vy. S využitím Azure AD můžou podnikoví správci snadno zajistit zodpovědnost uživatelů a správců díky lepšímu zabezpečení a lepším zásadám správného řízení než kdy dřív.

Azure AD Premium je cloudové řešení pro správu identit a přístupu s pokročilými možnostmi ochrany, které umožňuje využívat jednu zabezpečenou identitu pro všechny aplikace, ochranu identity (s využitím systému [Microsoft Intelligent Security Graph](https://www.microsoft.com/security/intelligence)) a technologii Privileged Identity Management. Azure AD Premium není pouze dalším nástrojem pro monitorování nebo generování sestav, dokáže totiž chránit identity uživatelů v reálném čase a umožňuje vytvářet adaptivní zásady přístupu na základě rizika pro zajištění ochrany dat vaší organizace.

Podívejte se na toto krátké video s rychlým přehledem správy a ochrany identit Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft poskytuje nejen identitu, která vám umožní přístup kamkoli, ale také sadu nástrojů pro automatizaci, pomoc se zabezpečením a správu IT v rámci organizace. I po nástupu cloud computingu stále existuje poptávka po správě a řízení úloh IT, jako jsou volání na helpdesk kvůli resetování uživatelského hesla, správa skupin uživatelů a žádosti o aplikace. Aby to celé bylo ještě složitější, zaměstnanci si teď do práce nosí svá osobní zařízení a používají snadno dostupné aplikace SaaS, což výrazně komplikuje zachování kontroly nad jejich zařízeními napříč podnikovými datacentry a veřejnými cloudovými platformami.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Propojení místní služby Active Directory s Azure AD a Office 365
Organizace, které vložily značné investice do místní služby Active Directory, můžou tyto investice rozšířit do cloudu tím, že integrují své místní adresáře se službou Azure AD do [správy hybridních identit](https://aka.ms/aadframework). Tím se zvýší produktivita uživatelů, protože budou mít k dispozici společnou identitu pro přístup k prostředkům bez ohledu na jejich umístění. Uživatelé a organizace pak můžou prostřednictvím jednotného přihlašování přistupovat k místním prostředkům i cloudovým službám, jako je Office 365.

K provedení této integrace vám stačí jediný nástroj – [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Azure AD Connect poskytuje možnosti pro zajištění podpory vašich požadavků na synchronizaci identit a nahrazuje starší verze nástrojů pro integraci identit, jako jsou DirSync nebo Azure AD Sync. Správu identit a jejich synchronizaci mezi místním prostředím a Azure AD v případě Azure AD Connect umožňuje:

- Synchronizace – tato komponenta odpovídá za vytváření uživatelů, skupin a dalších objektů. Také zajišťuje, aby se informace o identitě místních uživatelů a skupin shodovaly s cloudem. Také je možné povolit zpětný zápis hesla a tím zajistit synchronizaci místních adresářů v případě, že uživatel aktualizuje své heslo v Azure AD.
- Ověřování – Když je Azure AD vaší novou rovinou řízení, základem přístupu ke cloudu je ověřování. Volba správné metody ověřování představuje klíčové rozhodnutí při nastavování řešení hybridních identit Azure AD. Přečtěte si [tuto příručku](https://aka.ms/auth-options), která vám pomůže s výběrem mezi cloudovým ověřováním (synchronizace hodnot hash hesel nebo předávací ověřování) nebo federovaným ověřováním (AD FS) pro vaši organizaci.
- Monitorování stavu – [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) může poskytovat robustní monitorování a centrální umístění na webu Azure Portal, kde je možné zobrazit tuto aktivitu.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Zvýšení produktivity a snížení nákladů na helpdesk s využitím samoobslužného a jednotného přihlašování

Zaměstnanci jsou produktivnější, když jim stačí pamatovat si jedno uživatelské jméno a heslo a na každém zařízení mají konzistentní prostředí. Také šetří čas, když můžou provádět samoobslužné úlohy, jako je [resetování zapomenutého hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) nebo zažádání o přístup k aplikaci, aniž by museli čekat na pomoc od helpdesku.

Azure AD [rozšiřuje místní službu Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) do cloudu a tím umožňuje uživatelům používat svůj primární účet organizace pro svá zařízení připojená k doméně, prostředky společnosti i všechny webové aplikace a aplikace SaaS, které k práci potřebují. Kromě toho, že si nemusí pamatovat několik kombinací uživatelského jména a hesla, je možné přístup uživatelů k aplikacím zřizovat (nebo rušit jeho zřízení) také automaticky na základě jejich členství ve skupinách organizace a jejich postavení zaměstnance. Přístup můžete řídit u aplikací z galerie i u vlastních místních aplikací, které jste nasadili a publikovali přes [Proxy aplikací služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Správa a řízení přístupu k podnikovým prostředkům
Řešení Microsoftu pro správu identit a přístupu pomáhají IT oddělením chránit přístup k aplikacím a prostředkům napříč podnikovým datacentrem a v cloudu tím, že poskytují další úrovně ověřování, jako jsou [vícefaktorové ověřování](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) a [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Monitorování podezřelých aktivit prostřednictvím pokročilého generování sestav zabezpečení, auditování a upozorňování pomáhá zmírňovat potenciální problémy se zabezpečením.

Zásady podmíněného přístupu v Azure AD Premium vám jako podnikovému správci poskytují možnost vytvářet pravidla přístupu na základě zásad pro jakoukoli aplikaci připojenou k Azure AD (aplikace SaaS, vlastní aplikace v cloudu nebo místní webové aplikace). Azure AD tyto zásady vyhodnocuje v reálném čase a vynucuje je pokaždé, když se uživatel pokusí o přístup k aplikaci. Zásady ochrany identit Azure umožňují automaticky provést akci v případě zjištění podezřelé aktivity. Mezi tyto akce může patřit zablokování přístupu uživatelům s vysokým rizikem, vynucování vícefaktorového ověřování a resetování uživatelských hesel v případě, že mohlo dojít k ohrožení přihlašovacích údajů.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Technologie [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), která je součástí nabídky Azure Active Directory Premium P2, umožňuje zjišťovat, omezovat a monitorovat účty pro správu a jejich přístup k prostředků v Azure Active Directory a dalších online službách Microsoftu. Také pomáhá spravovat přístup pro správu na vyžádání po přesně danou dobu.

Privileged Identity Management může vynucovat oprávnění správce na vyžádání, aby správci mohli prostřednictvím vícefaktorového ověření požádat o dočasné zvýšení svých oprávnění po předkonfigurovanou dobu, než se jejich účet vrátí do normálního uživatelského stavu.

## <a name="benefits-of-azure-identity"></a>Výhody identity Azure

Se správou identit Azure můžete:

-   Vytvořit a spravovat pro každého uživatele v celém podniku jedinou identitu a zajistit synchronizaci uživatelů, skupin a zařízení pomocí [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Poskytovat prostřednictvím jednotného přihlašování přístup k vašim aplikacím, včetně tisíců předem integrovaných aplikací SaaS, nebo poskytovat zabezpečený vzdálený přístup k místním aplikacím SaaS pomocí [Proxy aplikací služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat [vícefaktorové ověřování](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) na základě pravidel.

-   Zvýšit produktivitu uživatelů pomocí [samoobslužného resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) a požadavků na přístup k aplikacím a skupinám pomocí [portálu MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Využívat výhod [vysoké dostupnosti a spolehlivosti](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) celosvětového cloudového řešení pro správu identit a přístupu na podnikové úrovni.

## <a name="next-steps"></a>Další kroky
[Další informace o řešeních identit Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
