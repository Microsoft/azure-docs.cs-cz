---
title: Porovnání spolupráce B2B a B2C v Azure Active Directory | Dokumentace Microsoftu
description: Jaký je rozdíl mezi spolupráce Azure Active Directory s B2B a Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b59dba541394c105370cfd3af0768a8477ddb4e6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348255"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Porovnání spolupráce B2B a B2C v Azure Active Directory

Spolupráce B2B ve službě Azure Active Directory (Azure AD) a Azure AD B2C umožňují pracovat s externími uživateli ve službě Azure AD. Ale jak jejich porovnání?


Možnosti spolupráce B2B |     Samostatné nabídky Azure AD B2C
-------- | --------
Určený pro: organizace, které mají být schopen provést ověřování uživatelů z partnerské organizace, bez ohledu na zprostředkovatele identity. | Určený pro: pozvání zákazníkům mobilní a webové aplikace, ať už jednotlivce, zákazníci institucionální nebo organizace do služby Azure AD.
Identity nepodporuje: zaměstnancům pracovní nebo školní účty, partnerům, kteří mají pracovní nebo školní účty nebo všechny e-mailovou adresu. Brzy pro podporu federace s přímým přístupem.  | Identity nepodporuje: spotřebitelské uživatele s účty místní aplikaci (všechny e-mailové adresy nebo uživatelského jména) nebo některou podporované identity v sociálních sítích s federací s přímým přístupem.
Který adresář jsou uživatelé partnera v: uživatelé partnerských od externí organizace jsou spravované ve stejném adresáři jako zaměstnanci, ale speciálně s poznámkami. Je možné spravovat stejným způsobem jako zaměstnanci, lze přidat do stejné skupiny a tak dále  | Který adresář entity zákazník uživatelů jsou v: V adresáři aplikace. Spravované samostatně z organizace zaměstnanců a partnerů adresáře (pokud existuje.
Jednotné přihlašování (SSO) do všech připojení AD aplikací Azure se podporuje. Můžete například poskytnout přístup k Office 365 nebo místních aplikací a dalšími aplikacemi SaaS, jako je například Salesforce nebo Workday.  |  Podporuje jednotné přihlašování pro aplikace v rámci Azure AD B2C tenanti vlastněné zákazníkem. Jednotné přihlašování k Office 365 nebo jiným aplikacím Microsoftu a Microsoft SaaS se nepodporuje.
Životní cyklus partnera: spravuje hostitele/pozvání organizace.  | Životní cyklus zákazníků: samoobslužné nebo spravovaných aplikací.
Zásady zabezpečení a dodržování předpisů: spravuje hostitele/pozvání organizace (třeba index Mei [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Zásady zabezpečení a dodržování předpisů: spravovaných aplikací.
Branding: Značku hostitele/pozvání organizace se používá.  |    Branding: Spravovaných aplikací. Obvykle je spíše produktů pod značkou jiných s vyblednutí organizace na pozadí.
Další informace: [Blogový příspěvek](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](what-is-b2b.md)  | Další informace: [stránky produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

