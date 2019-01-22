---
title: Porovnání spolupráce B2B a B2C v Azure Active Directory | Microsoft Docs
description: Jaký je rozdíl mezi spoluprací B2B a B2C v Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: overview
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 69c8e293186f955e86962a325fce2f54a2eefdc7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432162"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porovnání spolupráce B2B a B2C v Azure Active Directory

Jak spolupráce B2B v Azure Active Directory (Azure AD), tak Azure AD B2C umožňují v Azure AD pracovat s externími uživateli. Ale jaké je jejich porovnání?

**Azure AD B2B** je pro firmy, které chtějí bezpečně sdílet soubory a prostředky s externími uživateli, aby s nimi mohli spolupracovat. Správce Azure nastaví B2B na webu Azure Portal a Azure AD zajistí federaci mezi vaší firmou a externím partnerem. Uživatelé se ke sdíleným prostředkům přihlašují jednoduše na základě pozvánky a jejího uplatnění. Použijí pracovní nebo školní účet, případně jakýkoli e-mailový účet.
 
**Azure AD B2C** je hlavně pro firmy a vývojáře, kteří vytvářejí aplikace určené pro zákazníky. V případě Azure AD B2C můžou vývojáři pro svou aplikaci použít Azure AD jako plně funkční systém identit a zákazníci zase můžou k přihlašování použít stávající identitu (třeba Facebook nebo Gmail).

V následující tabulce najdete podrobné porovnání.


Možnosti spolupráce B2B |     Azure AD B2C – samostatná nabídka
-------- | --------
Určený pro: Organizace, které mají být schopen provést ověřování uživatelů z partnerské organizace, bez ohledu na zprostředkovatele identity. | Určený pro: Pozvání zákazníkům mobilní a webové aplikace, ať už jednotlivce, zákazníci institucionální nebo organizace do služby Azure AD.
Identity nepodporuje: Zaměstnancům pracovní nebo školní účty, partnerům, kteří mají pracovní nebo školní účty nebo všechny e-mailovou adresu. Brzy bude podporované přímé federování.  | Identity nepodporuje: Spotřebitelské uživatele s účty místní aplikaci (všechny e-mailové adresy nebo uživatelského jména) nebo některou podporované identity v sociálních sítích s federací s přímým přístupem.
Který adresář v jsou uživatelé partnera: Uživatelé partnerských od externí organizace jsou spravované ve stejném adresáři jako zaměstnanci, ale speciálně s poznámkami. Můžete je spravovat stejně jako zaměstnance, můžete je přidávat do stejných skupin atd.  | Který adresář v jsou entity zákazník uživatelů: V adresáři aplikace. Jsou spravované odděleně od zaměstnaneckého a partnerského adresáře organizace (pokud existují).
Ve všech aplikacích připojených k Azure AD je podporované jednotné přihlašování. Můžete třeba poskytnout přístup k aplikacím Office 365 nebo k místním a dalším aplikacím SaaS, jako je Salesforce nebo Workday.  |  V rámci tenantů Azure AD B2C je podporované jednotné přihlašování k aplikacím patřícím zákazníkům. Jednotné přihlašování do aplikací Office 365 nebo do jiných aplikací SaaS od Microsoftu nebo jiných dodavatelů není podporované.
Životní cyklus partnera: Spravovat hostitele nebo pozvání organizace.  | Životní cyklus zákazníků: Samoobslužný postup nebo spravovaných aplikací.
Zásady zabezpečení a dodržování předpisů: Spravovat hostitele nebo pozvání organizace (třeba index Mei [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Zásady zabezpečení a dodržování předpisů: Spravované aplikace.
Značky: Hostitele/pozvání značku organizace se používá.  |    Značky: Spravované aplikace. Většinou se používá značka produktu. Značka organizace ustupuje do pozadí.
Další informace: [Blogový příspěvek](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](what-is-b2b.md)  | Další informace: [Stránka produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

