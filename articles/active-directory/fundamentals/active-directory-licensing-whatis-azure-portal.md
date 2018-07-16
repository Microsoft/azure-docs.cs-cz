---
title: Co je licencování na základě skupin v Azure AD? | Microsoft Docs
description: Popis licencování na základě skupin v Azure Active Directory, principy fungování a osvědčené postupy
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.workload: identity
ms.date: 06/13/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: c6800e5a00d2fb837538141571c105a1d21e4374
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860337"
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Základní informace o licencování na základě skupin v Azure Active Directory

Placené cloudové služby Microsoftu, jako jsou Office 365, Enterprise Mobility + Security, Dynamics 365 a další podobné produkty, vyžadují licence. Tyto licence se přiřazují jednotlivým uživatelům, kteří potřebují přístup k těmto službám. Ke správě licencí používají správci některý z portálů pro správu (Office nebo Azure) a rutiny PowerShellu. Azure Active Directory (Azure AD) je základní infrastruktura, která podporuje správu identit pro všechny cloudové služby Microsoftu. Azure AD uchovává informace o stavu přiřazení licencí uživatelům.

Až doposud bylo možné licence přiřazovat jenom na úrovni jednotlivých uživatelů, což mohlo správu ve velkém měřítku ztěžovat. Pokud chtěl správce například při změnách v organizaci (třeba při nástupu nebo odchodu uživatelů v organizaci nebo oddělení) přidat nebo odebrat uživatelské licence, musel často psát složitý powershellový skript. Tento skript prováděl jednotlivá volání cloudové služby.

Aby se tyto problémy odstranily, umožňuje teď Azure AD licencování na základě skupin. Jednu nebo více licencí produktů je možné přiřadit skupině. Azure AD zajišťuje, že se tyto licence přiřadí všem členům dané skupiny. Všem novým členům, kteří se ke skupině připojí, se přiřadí příslušné licence. Když členové skupinu opustí, tyto licence se odeberou. Tím se eliminuje potřeba, aby automatizace správy licencí přes PowerShell odrážela změny jednotlivých uživatelů ve struktuře organizace a oddělení.

>[!NOTE]
>Tato funkce je v tuto chvíli ve verzi Public Preview. Buďte připravení na to, že jakékoli změny se můžou zrušit nebo odebrat. Tato funkce je během období Public Preview dostupná ve všech placených licenčních plánech Azure Active Directory (Azure AD). Až ale bude tato funkce obecně dostupná, můžou některé aspekty této funkce vyžadovat jednu nebo více licencí Azure Active Directory Premium.

## <a name="features"></a>Funkce

Toto jsou hlavní znaky licencování na základě skupin:

- Licence lze přiřazovat jakékoli skupině zabezpečení v Azure AD. Skupiny zabezpečení lze synchronizovat místně pomocí služby Azure AD Connect. Skupiny zabezpečení můžete vytvářet také přímo v Azure AD (označují se také jako výhradně cloudové skupiny) nebo automaticky prostřednictvím funkce dynamické skupiny Azure AD.

- Pokud je licence produktu přiřazená skupině, může správce jeden nebo více plánů služby v daném produktu zakázat. Obvykle se tak postupuje v případě, že organizace ještě není k používání služby zahrnuté v daném produktu připravená. Správce může například jednomu oddělení přiřadit Office 365, ale dočasně zakázat službu Yammer.

- Všechny cloudové služby Microsoftu, které vyžadují licencování na úrovni uživatele, jsou podporované. Zahrnuje to všechny produkty Office 365, Enterprise Mobility + Security a Dynamics 365.

- Licencování na základě skupin je momentálně dostupné jenom prostřednictvím webu [Azure Portal](https://portal.azure.com). Pokud používáte ke správě uživatelů a skupin primárně jiné portály pro správu, například portál Office 365, můžete v tom dále pokračovat. Ke správě licencí na úrovni skupin byste však měli používat Azure Portal.

- Azure AD automaticky spravuje úpravy licencí, které jsou důsledkem změn členství ve skupinách. Úpravy licencí jsou obvykle účinné během několika minut od změny členství.

- Uživatel může být členem více skupin, které mají určené licenční zásady. Uživatel může mít také některé licence, které byly přiřazeny přímo, mimo jakékoli skupiny. Výsledný stav uživatele je kombinací všech přiřazených licencí na produkty a služby.

- V některých případech nelze licence uživateli přiřadit. Může se například stát, že v tenantovi není dostatek dostupných licencí nebo že ve stejnou dobu byly přiřazeny konfliktní služby. Správci mají přístup k informacím o uživatelích, u kterých služba Azure AD nemohla provést úplné zpracování licencí skupiny. Na základě těchto informací pak můžou provádět nápravné akce.

- Během období Public Preview se k používání správy licencí na základě skupin v tenantovi vyžaduje placené nebo zkušební předplatné pro edice Azure AD Basic nebo Premium.

## <a name="your-feedback-is-welcome"></a>Vítáme vaše připomínky a názory.

Pokud máte požadavky k připomínkám nebo funkcím, napište nám je prosím na [fórum pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Další kroky

Další informace o jiných scénářích pro správu licencí pomocí licencování na základě skupin najdete v tématech:

* [Přiřazení licencí ke skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
