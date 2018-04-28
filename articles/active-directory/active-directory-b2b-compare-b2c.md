---
title: Porovnání spolupráce B2B a v Azure Active Directory B2C | Microsoft Docs
description: Jaký je rozdíl mezi spolupráce Azure Active Directory B2B a Azure AD B2C?
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 32d7d135e67a05b59279e647c1fb2aa8c9686cef
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porovnání spolupráce B2B a B2C v Azure Active Directory

Spolupráce B2B Azure Active Directory (Azure AD) a Azure AD B2C umožňují pracovat s externími uživateli ve službě Azure AD. Ale jak porovnávají?


Možnosti spolupráce B2B |     Azure AD B2C samostatné nabídky
-------- | --------
Určený pro: organizace, které mají být schopen provést ověřování uživatelů od organizace partnera, bez ohledu na to zprostředkovatele identity. | Určený pro: pozvání zákazníkům mobilní zařízení a webové aplikace, zda jednotlivce, institucionální nebo organizace zákazníků do služby Azure AD.
Podporované identit: zaměstnanci s pracovní nebo školní účty, partneři s pracovní nebo školní účty nebo všechny e-mailovou adresu. Brzy k podpoře přímé federace.  | Podporované identit: spotřebitelské uživatele s účty místních aplikací (všechny e-mailové adresy nebo uživatelské jméno) ani žádný podporované sociálních identitu pomocí přímého federation.
Adresář, kterému uživatelé partnera jsou v: uživatelům z partnerských z externí organizace jsou spravované ve stejném adresáři jako zaměstnanci, ale speciálně poznámky. Je možné spravovat stejným způsobem jako zaměstnanci, mohou být přidány do stejné skupiny a tak dále  | Adresář, kterému entitami uživatelů zákazníků jsou v: V adresáři aplikace. Spravované samostatně z organizace zaměstnanců a partnera adresář (pokud existuje.
Jednotné přihlašování (SSO) k aplikacím všechny připojené AD Azure je podporována. Je třeba zadat přístup k Office 365 nebo místními aplikacemi a do jiných aplikací SaaS, například služby Salesforce nebo Workday.  |  Jednotné přihlašování pro zákazníka, které jsou ve vlastnictví aplikací v rámci klienty Azure AD B2C je podporována. Jednotné přihlašování k Office 365 nebo do jiných aplikací společnosti Microsoft a Microsoft SaaS není podporována.
Životní cyklus partnera: spravuje hostitele nebo pozvání organizace.  | Životní cyklus zákazníka: samoobslužný postup nebo spravovaných aplikací.
Zásady zabezpečení a dodržování předpisů: spravuje hostitele nebo pozvání organizace.  | Zásady zabezpečení a dodržování předpisů: spravovaných aplikací.
Branding: Brand hostitele nebo pozvání organizace se používá.  |    Branding: Spravované aplikace. Obvykle bývá produktu s organizace směrem do pozadí značky.
Další informace: [příspěvku na blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Další informace: [stránky produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Další postup

Projděte si naše další články ohledně spolupráce B2B ve službě Azure AD:

* [Co je spolupráce B2B ve službě Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Vlastnosti uživatele spolupráce B2B](active-directory-b2b-user-properties.md)
* [Přidání uživatele spolupráce B2B k roli](active-directory-b2b-add-guest-to-role.md)
* [Delegovat pozvánek spolupráce B2B](active-directory-b2b-delegate-invitations.md)
* [Dynamické skupiny a spolupráci B2B](active-directory-b2b-dynamic-groups.md)
* [Konfigurace aplikací SaaS pro spolupráci B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokeny uživatele spolupráce B2B](active-directory-b2b-user-token.md)
* [Deklarace uživatele spolupráce B2B mapování](active-directory-b2b-claims-mapping.md)
* [Externí sdílení Office 365](active-directory-b2b-o365-external-user.md)
* [Aktuální omezení spolupráce B2B](active-directory-b2b-current-limitations.md)
* [Získání podpory pro spolupráci B2B](active-directory-b2b-support.md)
