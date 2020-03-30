---
title: Co jsou skupinové licencování – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o licencování na základě skupiny Azure Active Directory, včetně toho, jak funguje a doporučené postupy.
services: active-directory
keywords: Licencování Azure AD
author: msaburnley
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
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561557"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Co je skupinové licencování ve službě Azure Active Directory?

Placené cloudové služby Microsoftu, jako jsou Office 365, Enterprise Mobility + Security, Dynamics 365 a další podobné produkty, vyžadují licence. Tyto licence se přiřazují jednotlivým uživatelům, kteří potřebují přístup k těmto službám. Ke správě licencí používají správci některý z portálů pro správu (Office nebo Azure) a rutiny PowerShellu. Azure Active Directory (Azure AD) je základní infrastruktura, která podporuje správu identit pro všechny cloudové služby Microsoftu. Azure AD uchovává informace o stavu přiřazení licencí uživatelům.

Až doposud bylo možné licence přiřazovat jenom na úrovni jednotlivých uživatelů, což mohlo správu ve velkém měřítku ztěžovat. Pokud chtěl správce například při změnách v organizaci (třeba při nástupu nebo odchodu uživatelů v organizaci nebo oddělení) přidat nebo odebrat uživatelské licence, musel často psát složitý powershellový skript. Tento skript prováděl jednotlivá volání cloudové služby.

Aby se tyto problémy odstranily, umožňuje teď Azure AD licencování na základě skupin. Jednu nebo více licencí produktů je možné přiřadit skupině. Azure AD zajišťuje, že se tyto licence přiřadí všem členům dané skupiny. Všem novým členům, kteří se ke skupině připojí, se přiřadí příslušné licence. Když členové skupinu opustí, tyto licence se odeberou. Tato správa licencí eliminuje potřebu automatizace správy licencí prostřednictvím prostředí PowerShell, aby odrážela změny ve struktuře organizace a oddělení pro jednotlivé uživatele.

## <a name="licensing-requirements"></a>Licenční požadavky
Chcete-li používat skupinové licence, musíte mít jednu z následujících licencí:

- Placené nebo zkušební předplatné pro Azure AD Premium P1 a vyšší

- Placená nebo zkušební edice Office 365 Enterprise E3 nebo Office 365 A3 nebo Office 365 GCC G3 nebo Office 365 E3 pro GCCH nebo Office 365 E3 pro DOD a vyšší

### <a name="required-number-of-licenses"></a>Požadovaný počet licencí
Pro všechny skupiny přiřazené k licenci musíte mít také licenci pro každého jedinečného člena. I když nemusíte každému členovi skupiny přiřazovat licenci, musíte mít alespoň dostatek licencí, aby bylo možné zahrnout všechny členy. Máte-li například v tenantovi 1 000 jedinečných členů, kteří jsou součástí licencovaných skupin, musíte mít ke splnění licenční smlouvy alespoň 1 000 licencí.

## <a name="features"></a>Funkce

Toto jsou hlavní znaky licencování na základě skupin:

- Licence lze přiřazovat jakékoli skupině zabezpečení v Azure AD. Skupiny zabezpečení lze synchronizovat z místního prostředí pomocí služby Azure AD Connect. Skupiny zabezpečení můžete vytvářet také přímo v Azure AD (označují se také jako výhradně cloudové skupiny) nebo automaticky prostřednictvím funkce dynamické skupiny Azure AD.

- Pokud je licence produktu přiřazená skupině, může správce jeden nebo více plánů služby v daném produktu zakázat. Toto přiřazení se obvykle provádí, když organizace ještě není připravena začít používat službu zahrnutou v produktu. Správce může například jednomu oddělení přiřadit Office 365, ale dočasně zakázat službu Yammer.

- Všechny cloudové služby Microsoftu, které vyžadují licencování na úrovni uživatele, jsou podporované. Tato podpora zahrnuje všechny produkty Office 365, enterprise mobility + zabezpečení a dynamics 365.

- Skupinové licencování je momentálně dostupné jenom přes [portál Azure](https://portal.azure.com). Pokud používáte především jiné portály pro správu pro správu uživatelů a skupin, jako je například [Centrum pro správu Microsoftu 365](https://admin.microsoft.com), můžete v tom pokračovat. Ke správě licencí na úrovni skupin byste však měli používat Azure Portal.

- Azure AD automaticky spravuje úpravy licencí, které jsou důsledkem změn členství ve skupinách. Úpravy licencí jsou obvykle účinné během několika minut od změny členství.

- Uživatel může být členem více skupin, které mají určené licenční zásady. Uživatel může mít také některé licence, které byly přiřazeny přímo, mimo jakékoli skupiny. Výsledný stav uživatele je kombinací všech přiřazených licencí na produkty a služby. Pokud je uživateli přiřazena stejná licence z více zdrojů, licence bude spotřebována pouze jednou.

- V některých případech nelze licence uživateli přiřadit. Může se například stát, že v tenantovi není dostatek dostupných licencí nebo že ve stejnou dobu byly přiřazeny konfliktní služby. Správci mají přístup k informacím o uživatelích, u kterých služba Azure AD nemohla provést úplné zpracování licencí skupiny. Na základě těchto informací pak můžou provádět nápravné akce.

## <a name="your-feedback-is-welcome"></a>Vítáme vaše připomínky a názory.

Pokud máte zpětnou vazbu nebo žádosti o funkce, podělte se s námi pomocí [fóra pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Další kroky

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Přiřazení licencí ke skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Jak migrovat uživatele mezi licencemi produktů pomocí skupinových licencí ve službě Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
