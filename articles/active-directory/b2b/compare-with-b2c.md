---
title: Porovnání spolupráce B2B a B2C Azure Active Directory | Microsoft Docs
description: Jaký je rozdíl mezi spoluprací B2B a B2C v Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "68380749"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porovnání spolupráce B2B a B2C v Azure Active Directory

Jak spolupráce B2B v Azure Active Directory (Azure AD), tak Azure AD B2C umožňují v Azure AD pracovat s externími uživateli. Ale jaké je jejich porovnání?

**Azure AD B2B** je pro firmy, které chtějí bezpečně sdílet soubory a prostředky s externími uživateli, aby s nimi mohli spolupracovat. Správce Azure nastaví B2B na webu Azure Portal a Azure AD zajistí federaci mezi vaší firmou a externím partnerem. Uživatelé se ke sdíleným prostředkům přihlašují jednoduše na základě pozvánky a jejího uplatnění. Použijí pracovní nebo školní účet, případně jakýkoli e-mailový účet.
 
**Azure AD B2C** je hlavně pro firmy a vývojáře, kteří vytvářejí aplikace určené pro zákazníky. V případě Azure AD B2C můžou vývojáři pro svou aplikaci použít Azure AD jako plně funkční systém identit a zákazníci zase můžou k přihlašování použít stávající identitu (třeba Facebook nebo Gmail).

V následující tabulce najdete podrobné porovnání.


Možnosti spolupráce B2B |     Azure AD B2C – samostatná nabídka
-------- | --------
Komu je určená: Organizacím, které chtějí ověřovat uživatele z partnerských organizací bez ohledu na zprostředkovatele identity. | Komu je určená: Pozvaným zákazníkům, kteří používají mobilní a webové aplikace. Mohou to být jednotlivci, instituce nebo zákazníci z organizací, kteří používají k ověřování Azure AD.
Podporované identity: U zaměstnanců a partnerů jsou to pracovní nebo školní účty nebo jakákoli e-mailová adresa. Brzy bude podporované přímé federování.  | Podporované identity: Koncoví uživatelé s účty místních aplikací (jakákoli e-mailová adresa nebo uživatelské jméno) nebo jiná podporovaná sociální identita s přímým federováním.
Externí uživatelé jsou spravováni ve stejném adresáři jako zaměstnanci, ale speciálně s poznámkami. Můžou být spravované stejným způsobem jako zaměstnanci, můžou se přidávat do stejných skupin a tak dále.  | Externí uživatelé jsou spravováni v adresáři aplikace. Spravují se odděleně od zaměstnance organizace a adresáře partnerů (pokud existuje).
Ve všech aplikacích připojených k Azure AD je podporované jednotné přihlašování. Můžete třeba poskytnout přístup k aplikacím Office 365 nebo k místním a dalším aplikacím SaaS, jako je Salesforce nebo Workday.  |  V rámci tenantů Azure AD B2C je podporované jednotné přihlašování k aplikacím patřícím zákazníkům. Jednotné přihlašování k Office 365 nebo jiným aplikacím Microsoft SaaS se nepodporuje.
Životní cyklus partnera: Spravuje ho hostitelská organizace nebo organizace, která odesílá pozvánky.  | Životní cyklus zákazníka: Zajišťovaný samostatně nebo spravovaný aplikací.
Zásady zabezpečení a dodržování předpisů: spravované organizací hostitele nebo pozváním (například pomocí [zásad podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Zásady zabezpečení a dodržování předpisů: Spravuje je aplikace.
Značka: Používá se značka hostitele nebo organizace, která odesílá pozvánky.  |    Značka: Spravuje ji aplikace. Většinou se používá značka produktu. Značka organizace ustupuje do pozadí.
Další informace: [blogový příspěvek](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](what-is-b2b.md)  | Další informace: [stránka produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

