---
title: Správa hybridní identity – úlohy správy Azure | Microsoft Docs
description: Azure AD zkontroluje konkrétní podmínky, které vyberete při ověřování uživatele, a před povolením přístupu k aplikaci pomocí podmíněného řízení přístupu.
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
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986528"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plán pro hybridní životní cyklus identity
Identita je jednou ze základů vaší strategie Enterprise mobility a přístupu k aplikacím. Bez ohledu na to, jestli se přihlašujete k mobilnímu zařízení nebo aplikaci SaaS, je vaše identita klíčem k získání přístupu ke všem. V nejvyšší úrovni zahrnuje řešení pro správu identit sjednocení a synchronizaci mezi úložišti identity, což zahrnuje automatizaci a centralizaci procesu zřizování prostředků. Řešení identity by mělo být centralizovanou identitou v místním prostředí a cloudu a taky používat určitou formu federace identit k údržbě centralizovaného ověřování a bezpečnému sdílení a spolupráci s externími uživateli a podniky. Prostředky jsou v rozsahu od operačních systémů a aplikací až po lidi, kteří jsou členy organizace. Organizační strukturu se dá změnit tak, aby vyhovovala zásadám zřizování a postupům.

Je také důležité mít řešení identity, které uživatelům umožňuje poskytovat samoobslužná prostředí pro zajištění produktivity. Vaše řešení identity je robustnější, pokud umožňuje jednotné přihlašování pro uživatele ve všech prostředcích, které potřebují k přístupu. Správci na všech úrovních můžou použít standardizované postupy pro správu přihlašovacích údajů uživatelů. V závislosti na šířce řešení pro správu zřizování je možné některé úrovně správy snížit nebo vyloučit. Kromě toho můžete bezpečně distribuovat možnosti správy, ručně nebo automaticky, mezi různými organizacemi. Správce domény může například obsluhovat pouze osoby a prostředky v této doméně. Tento uživatel může provádět úlohy správy a zřizování, ale není autorizován provádět úlohy konfigurace, jako je vytváření pracovních postupů.

## <a name="determine-hybrid-identity-management-tasks"></a>Určení úloh správy hybridních identit
Distribuce úloh správy ve vaší organizaci zlepšuje přesnost a efektivitu správy a zlepšuje rovnováhu mezi úlohami organizace. Níže jsou uvedené pivoty, které definují robustní systém pro správu identit.

 ![požadavky správy identit](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Pokud chcete definovat hybridní úlohy správy identit, musíte pochopit některé základní charakteristiky organizace, které budou přijímat hybridní identitu. Je důležité pochopit aktuální úložiště, která se používají pro zdroje identity. Díky pochopení těchto základních prvků budete mít základní požadavky a na základě toho, že budete muset klást podrobnější otázky, které vám pomůžou lépe navrhovat rozhodnutí pro vaše řešení identity.  

Při definování těchto požadavků zajistěte, aby byly zodpovězeny alespoň následující otázky.

* Možnosti zřizování: 
  
  * Podporuje řešení hybridní identity robustní správu přístupu k účtu a systém zřizování?
  * Jak se budou uživatelé, skupiny a hesla spravovat?
  * Reaguje Správa životního cyklu identit? 
    * Jak dlouho trvá pozastavení účtu aktualizace hesel?
* Správa licencí: 
  
  * Zpracovává řešení hybridní identity správu licencí?
    * Pokud ano, jaké funkce jsou k dispozici?
  * Zpracovává řešení správu licencí na základě skupin? 
  
    * Pokud ano, je možné k ní přiřadit skupinu zabezpečení? 
    * Pokud ano, bude adresář cloudu automaticky přiřazovat licence všem členům skupiny? 
    * Co se stane, když se uživatel později přidá nebo odebere ze skupiny, automaticky se přiřadí nebo odebere licence podle potřeby? 
* Integrace s jinými zprostředkovateli identity třetích stran:
  * Dá se toto hybridní řešení integrovat s poskytovateli identit třetích stran a implementovat jednotné přihlašování?
  * Je možné sjednotit všechny různé zprostředkovatele identity do systému soudržných identit?
  * Pokud ano, jak a které jsou a jaké možnosti jsou k dispozici?

## <a name="synchronization-management"></a>Správa synchronizace
Jedním z cílů správce identit, aby bylo možné přenést všechny zprostředkovatele identity a zachovat jejich synchronizaci. Data jsou synchronizovaná na základě autoritativního poskytovatele hlavní identity. V případě hybridních identit můžete s synchronizovaným modelem správy spravovat všechny identity uživatelů a zařízení na místním serveru a synchronizovat účty a volitelně také hesla ke cloudu. Uživatel zadá stejné heslo místně, jako v cloudu, a při přihlášení se ověří heslo pomocí řešení identity. Tento model používá nástroj pro synchronizaci adresářů.

![synchronizace adresářů ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) do správného návrhu synchronizace řešení hybridní identity zajistěte, aby byly zodpovězeny následující otázky:
*    Jaká jsou řešení synchronizace dostupná pro řešení hybridní identity?
*    Jaké jsou možnosti jednotného přihlašování k dispozici?
*    Jaké jsou možnosti federace identit mezi B2B a B2C?

## <a name="next-steps"></a>Další kroky
[Určení strategie přijetí správy hybridních identit](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)

