---
title: Co je skupina založená na licencování v Azure Active Directory? | Dokumenty Microsoft
description: Další informace o licencování na základě skupin Azure Active Directory, včetně jeho fungování a osvědčené postupy.
services: active-directory
keywords: Licencování Azure AD
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3f23b28c1b20155e50fddf17db90cd2a53c04855
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209815"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Co je skupina založená na licencování v Azure Active Directory?

Placené cloudové služby Microsoftu, jako jsou Office 365, Enterprise Mobility + Security, Dynamics 365 a další podobné produkty, vyžadují licence. Tyto licence se přiřazují jednotlivým uživatelům, kteří potřebují přístup k těmto službám. Ke správě licencí používají správci některý z portálů pro správu (Office nebo Azure) a rutiny PowerShellu. Azure Active Directory (Azure AD) je základní infrastruktura, která podporuje správu identit pro všechny cloudové služby Microsoftu. Azure AD uchovává informace o stavu přiřazení licencí uživatelům.

Až doposud bylo možné licence přiřazovat jenom na úrovni jednotlivých uživatelů, což mohlo správu ve velkém měřítku ztěžovat. Pokud chtěl správce například při změnách v organizaci (třeba při nástupu nebo odchodu uživatelů v organizaci nebo oddělení) přidat nebo odebrat uživatelské licence, musel často psát složitý powershellový skript. Tento skript prováděl jednotlivá volání cloudové služby.

Aby se tyto problémy odstranily, umožňuje teď Azure AD licencování na základě skupin. Jednu nebo více licencí produktů je možné přiřadit skupině. Azure AD zajišťuje, že se tyto licence přiřadí všem členům dané skupiny. Všem novým členům, kteří se ke skupině připojí, se přiřadí příslušné licence. Když členové skupinu opustí, tyto licence se odeberou. Tím se eliminuje potřeba, aby automatizace správy licencí přes PowerShell odrážela změny jednotlivých uživatelů ve struktuře organizace a oddělení.


## <a name="features"></a>Funkce

Toto jsou hlavní znaky licencování na základě skupin:

- Licence lze přiřazovat jakékoli skupině zabezpečení v Azure AD. Skupiny zabezpečení se synchronizují z místních, pomocí služby Azure AD Connect. Skupiny zabezpečení můžete vytvářet také přímo v Azure AD (označují se také jako výhradně cloudové skupiny) nebo automaticky prostřednictvím funkce dynamické skupiny Azure AD.

- Pokud je licence produktu přiřazená skupině, může správce jeden nebo více plánů služby v daném produktu zakázat. Obvykle se tak postupuje v případě, že organizace ještě není k používání služby zahrnuté v daném produktu připravená. Správce může například jednomu oddělení přiřadit Office 365, ale dočasně zakázat službu Yammer.

- Všechny cloudové služby Microsoftu, které vyžadují licencování na úrovni uživatele, jsou podporované. Zahrnuje to všechny produkty Office 365, Enterprise Mobility + Security a Dynamics 365.

- Licencování na základě skupin je momentálně dostupné jenom prostřednictvím webu [Azure Portal](https://portal.azure.com). Pokud používáte ke správě uživatelů a skupin primárně jiné portály pro správu, například portál Office 365, můžete v tom dále pokračovat. Ke správě licencí na úrovni skupin byste však měli používat Azure Portal.

- Azure AD automaticky spravuje úpravy licencí, které jsou důsledkem změn členství ve skupinách. Úpravy licencí jsou obvykle účinné během několika minut od změny členství.

- Uživatel může být členem více skupin, které mají určené licenční zásady. Uživatel může mít také některé licence, které byly přiřazeny přímo, mimo jakékoli skupiny. Výsledný stav uživatele je kombinací všech přiřazených licencí na produkty a služby. Pokud uživatel je přiřazen stejný licence z více zdrojů, licence, které bude využívat pouze jednou.

- V některých případech nelze licence uživateli přiřadit. Může se například stát, že v tenantovi není dostatek dostupných licencí nebo že ve stejnou dobu byly přiřazeny konfliktní služby. Správci mají přístup k informacím o uživatelích, u kterých služba Azure AD nemohla provést úplné zpracování licencí skupiny. Na základě těchto informací pak můžou provádět nápravné akce.

- Placené nebo zkušební předplatné pro Azure AD Basic nebo placené nebo zkušební verze Office 365 Enterprise E3, Office 365 A3 a vyšší edice muset v tenantovi pomocí správy na základě skupin licencí. Tato funkce vyžaduje licenci pro každý jedinečný uživatel, který je členem skupiny, které jsou přiřazené licence. Není nutné k přiřazení licencí uživatelům pro ně být členy skupin, které jsou přiřazené licence, ale minimální počet licencí musí mít v tenantovi k pokrytí všech těchto uživatelů. Například pokud máte celkem 1 000 jedinečných uživatelů ve všech skupinách licence přiřazené ve vašem tenantovi, je třeba alespoň 1000 licencí splnit stanovené požadavky licence.

## <a name="your-feedback-is-welcome"></a>Vítáme vaše připomínky a názory.

Pokud máte požadavky k připomínkám nebo funkcím, napište nám je prosím na [fórum pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Další postup

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Přiřazení licencí ke skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
