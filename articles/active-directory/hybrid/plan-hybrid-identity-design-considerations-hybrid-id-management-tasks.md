---
title: Návrh hybridní identity – úlohy správy Azure | Dokumenty společnosti Microsoft
description: Pomocí řízení podmíněného přístupu služba Azure Active Directory zkontroluje konkrétní podmínky, které vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Jakmile jsou tyto podmínky splněny, uživatel je ověřen a povolen přístup k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109385"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plán pro životní cyklus hybridní identity
Identita je jedním ze základů vaší strategie podnikové mobility a přístupu k aplikacím. Ať už se přihlašujete k mobilnímu zařízení nebo aplikaci SaaS, vaše identita je klíčem k získání přístupu ke všemu. Na nejvyšší úrovni řešení správy identit zahrnuje sjednocení a synchronizaci mezi úložišti identit, což zahrnuje automatizaci a centralizaci procesu zřizování prostředků. Řešení identit by mělo být centralizovanou identitou napříč místním i cloudem a také používat nějakou formu federace identit k udržování centralizovaného ověřování a bezpečnému sdílení a spolupráci s externími uživateli a firmami. Zdroje sahají od operačních systémů a aplikací až po lidi v organizaci nebo s ní spojenou. Organizační strukturu lze změnit tak, aby vyhovovala zásadám a postupům zřizování.

Je také důležité mít řešení identity zaměřené na posílení postavení vašich uživatelů tím, že jim poskytuje samoobslužné prostředí, aby byli produktivní. Vaše řešení identit y je robustnější, pokud umožňuje jednotné přihlašování pro uživatele ve všech prostředcích, které potřebují přístup. Správci na všech úrovních mohou používat standardizované postupy pro správu pověření uživatelů. Některé úrovně správy lze snížit nebo odstranit, v závislosti na šíři řešení správy zřizování. Kromě toho můžete bezpečně distribuovat možnosti správy, ručně nebo automaticky, mezi různé organizace. Správce domény může například obsluhovat pouze osoby a prostředky v této doméně. Tento uživatel může dělat úlohy správy a zřizování, ale není oprávněn k provádění úloh konfigurace, jako je například vytváření pracovních postupů.

## <a name="determine-hybrid-identity-management-tasks"></a>Určení úloh správy hybridní identity
Distribuce administrativních úkolů ve vaší organizaci zvyšuje přesnost a efektivitu správy a zlepšuje rovnováhu pracovního vytížení organizace. Následují pivoty, které definují robustní systém správy identit.

 ![Aspekty správy identit](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Chcete-li definovat úlohy správy hybridní identity, musíte pochopit některé základní charakteristiky organizace, která bude přijímat hybridní identitu. Je důležité porozumět aktuálním úložištím, které se používají pro zdroje identit. Znalostí těchto základních prvků budete mít základní požadavky a na základě toho budete muset položit podrobnější otázky, které vás povedou k lepšímu rozhodnutí o návrhu pro vaše řešení identity.  

Při definování těchto požadavků zajistěte, aby byly zodpovězeny alespoň následující otázky

* Možnosti zřizování: 
  
  * Podporuje řešení hybridní identity robustní systém správy přístupu k účtu a zřizování?
  * Jak budou uživatelé, skupiny a hesla spravováni?
  * Odpovídá správa životního cyklu identity? 
    * Jak dlouho trvá pozastavení platnosti účtu aktualizací hesla?
* Správa licencí: 
  
  * Zpracovává řešení hybridní identity správu licencí?
    * Pokud ano, jaké funkce jsou k dispozici?
  * Zpracovává řešení správu licencí založenou na skupině? 
  
    * Pokud ano, je možné k ní přiřadit skupinu zabezpečení? 
    * Pokud ano, bude adresář cloudu automaticky přiřazovat licence všem členům skupiny? 
    * Co se stane, pokud je uživatel následně přidán do skupiny nebo odebrán ze skupiny, bude licence automaticky přiřazena nebo odebrána podle potřeby? 
* Integrace s dalšími poskytovateli identit třetích stran:
  * Může být toto hybridní řešení integrováno s poskytovateli identit třetích stran pro implementaci jednotného přihlášení?
  * Je možné sjednotit všechny různé poskytovatele identity do soudržného systému identit?
  * Pokud ano, jak a jaké jsou a jaké schopnosti jsou k dispozici?

## <a name="synchronization-management"></a>Správa synchronizace
Jedním z cílů správce identit, aby bylo možné přenést všechny zprostředkovatele identity a udržovat je synchronizované. Data jsou synchronizována na základě autoritativního hlavního zprostředkovatele identity. Ve scénáři hybridní identity se synchronizovaným modelem správy spravujete všechny identity uživatelů a zařízení na místním serveru a synchronizujete účty a volitelně hesla do cloudu. Uživatel zadá stejné heslo místně jako v cloudu a při přihlášení je heslo ověřeno řešením identit. Tento model používá nástroj pro synchronizaci adresářů.

![synchronizace](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) adresářů: Chcete-li správně navrhnout synchronizaci řešení hybridní identity, zajistěte, aby byly zodpovězeny následující otázky:
*    Jaká synchronizační řešení jsou k dispozici pro řešení hybridní identity?
*    Jaké jsou funkce jednotného přihlášení k dispozici?
*    Jaké jsou možnosti federace identit mezi B2B a B2C?

## <a name="next-steps"></a>Další kroky
[Určit strategii přijetí hybridní správy identit](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

