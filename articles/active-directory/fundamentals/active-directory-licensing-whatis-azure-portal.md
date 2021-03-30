---
title: Co je licencování na základě skupin – Azure Active Directory | Microsoft Docs
description: Přečtěte si o Azure Active Directory licencování na základě skupin, včetně toho, jak to funguje, a osvědčených postupů.
services: active-directory
keywords: Licencování Azure AD
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0490334c759da6ef7ba7ff2535f5f561cdb7a9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369807"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Co je licencování na základě skupin v Azure Active Directory?

Placené cloudové služby Microsoftu, jako jsou Microsoft 365, Enterprise Mobility + Security, Dynamics 365 a další podobné produkty, vyžadují licence. Tyto licence se přiřazují jednotlivým uživatelům, kteří potřebují přístup k těmto službám. Ke správě licencí používají správci některý z portálů pro správu (Office nebo Azure) a rutiny PowerShellu. Azure Active Directory (Azure AD) je základní infrastruktura, která podporuje správu identit pro všechny cloudové služby Microsoftu. Azure AD uchovává informace o stavu přiřazení licencí uživatelům.

Až doposud bylo možné licence přiřazovat jenom na úrovni jednotlivých uživatelů, což mohlo správu ve velkém měřítku ztěžovat. Pokud chtěl správce například při změnách v organizaci (třeba při nástupu nebo odchodu uživatelů v organizaci nebo oddělení) přidat nebo odebrat uživatelské licence, musel často psát složitý powershellový skript. Tento skript prováděl jednotlivá volání cloudové služby.

Aby se tyto problémy odstranily, umožňuje teď Azure AD licencování na základě skupin. Jednu nebo více licencí produktů je možné přiřadit skupině. Azure AD zajišťuje, že se tyto licence přiřadí všem členům dané skupiny. Všem novým členům, kteří se ke skupině připojí, se přiřadí příslušné licence. Když členové skupinu opustí, tyto licence se odeberou. Správa licencování eliminuje nutnost automatizace správy licencí prostřednictvím PowerShellu, aby odrážela změny v organizaci a struktuře oddělení podle jednotlivých uživatelů.

## <a name="licensing-requirements"></a>Licenční požadavky
K používání licencování na základě skupin musíte mít jednu z následujících licencí:

- Placené nebo zkušební předplatné pro Azure AD Premium P1 a vyšší

- Placená nebo zkušební edice Office 365 Enterprise E3 nebo Office 365 a3 nebo Office 365 RSZ G3 nebo Office 365 E3 pro GCCH nebo Office 365 E3 pro DOD a vyšší

### <a name="required-number-of-licenses"></a>Požadovaný počet licencí
U všech skupin, které mají přiřazenou licenci, musíte mít také licenci pro každého jedinečného člena. I když nemusíte přiřazovat licence ke skupině, musíte mít aspoň tolik licencí, aby zahrnovali všechny členy. Pokud například máte 1 000 jedinečných členů, kteří jsou součástí licencovaných skupin ve vašem tenantovi, musíte mít minimálně 1 000 licencí, aby splnily licenční smlouvu.

## <a name="features"></a>Funkce

Toto jsou hlavní znaky licencování na základě skupin:

- Licence lze přiřazovat jakékoli skupině zabezpečení v Azure AD. Skupiny zabezpečení je možné synchronizovat z místního prostředí pomocí Azure AD Connect. Skupiny zabezpečení můžete vytvářet také přímo v Azure AD (označují se také jako výhradně cloudové skupiny) nebo automaticky prostřednictvím funkce dynamické skupiny Azure AD.

- Pokud je licence produktu přiřazená skupině, může správce jeden nebo více plánů služby v daném produktu zakázat. Toto přiřazení se obvykle provádí, když organizace ještě není připravená začít používat službu, která je součástí produktu. Správce může například přiřadit Microsoft 365 oddělení, ale dočasně zakázat službu Yammer.

- Všechny cloudové služby Microsoftu, které vyžadují licencování na úrovni uživatele, jsou podporované. Tato podpora zahrnuje všechny Microsoft 365 produkty, Enterprise Mobility + Security a Dynamics 365.

- Licencování na základě skupin je aktuálně k dispozici pouze prostřednictvím [Azure Portal](https://portal.azure.com). Pokud primárně používáte jiné portály pro správu uživatelů a skupin, jako je například [Centrum pro správu Microsoft 365](https://admin.microsoft.com), můžete pokračovat. Ke správě licencí na úrovni skupin byste však měli používat Azure Portal.

- Azure AD automaticky spravuje úpravy licencí, které jsou důsledkem změn členství ve skupinách. Úpravy licencí jsou obvykle účinné během několika minut od změny členství.

- Uživatel může být členem více skupin, které mají určené licenční zásady. Uživatel může mít také některé licence, které byly přiřazeny přímo, mimo jakékoli skupiny. Výsledný stav uživatele je kombinací všech přiřazených licencí na produkty a služby. Pokud má uživatel přiřazenou stejnou licenci z více zdrojů, licence se spotřebuje jenom jednou.

- V některých případech nelze licence uživateli přiřadit. Může se například stát, že v tenantovi není dostatek dostupných licencí nebo že ve stejnou dobu byly přiřazeny konfliktní služby. Správci mají přístup k informacím o uživatelích, u kterých služba Azure AD nemohla provést úplné zpracování licencí skupiny. Na základě těchto informací pak můžou provádět nápravné akce.

## <a name="your-feedback-is-welcome"></a>Vítáme vaše připomínky a názory.

Pokud máte vaše připomínky nebo žádosti o funkce, sdílejte je s námi pomocí [fóra správce Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Další kroky

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Přiřazení licencí ke skupině v Azure Active Directory](../enterprise-users/licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../enterprise-users/licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](../enterprise-users/licensing-groups-migrate-users.md)
* [Postup migrace uživatelů mezi licencemi k produktu pomocí licencování na základě skupin v Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../enterprise-users/licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupin v Azure Active Directory](../enterprise-users/licensing-ps-examples.md)
