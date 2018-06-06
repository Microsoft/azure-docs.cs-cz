---
title: Návrhu hybridní identity - úloh správy Azure | Microsoft Docs
description: Azure Active Directory pomocí podmíněného řízení přístupu, zkontroluje konkrétní podmínky, kterou vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Po splnění těchto podmínek je uživatel ověřený a přistupovat k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5fcb229690827c7bd508aa0d27dd68b169b4e25e
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801322"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plánování životního cyklu hybridní Identity
Identita je jedním ze základů strategie enterprise mobility a aplikaci přístup. Jestli se přihlašujete vaše mobilní zařízení nebo na aplikaci SaaS, vaší identity je klíč k získání přístupu k vše. Na nejvyšší úrovni do řešení pro správu identity zahrnuje sjednotit a synchronizaci mezi vaší identity úložiště, což zahrnuje automatizaci a centralizuje proces zřizování prostředků. Řešení identity by měl být centralizované identity v rámci místní a cloudové a také použít nějaký způsob federaci identit a udržovat centralizované ověřování a bezpečně sdílet spolupracovat s externími uživateli a podnikům. Prostředky v rozsahu od operačních systémů a aplikací na osoby ve nebo spojit s organizace. Organizační struktura může být upraven, aby odpovídal zřizování zásady a postupy.

Je také důležité, aby byly do řešení identity zaměřené na základě kterých vaše uživatele a poskytovat jim samoobslužné služby prostředí, abyste zajistili jejich produktivitu. Řešení identity je robustnější, že pokud umožňuje jednotné přihlašování pro uživatele mezi všechny prostředky, které potřebují přístup. Správce na všech úrovních, můžete použít standardizované postupy pro správu přihlašovací údaje uživatele. Některé úrovně správy lze omezit nebo vyloučit, v závislosti na šířky zřizování řešení pro správu. Kromě toho můžete bezpečně distribuovat možnosti správy, ručně nebo automaticky, mezi různými organizacemi. Správce domény může například sloužit pouze osoby a prostředky v této doméně. Tento uživatel může provádět úkoly správy a zřizování, ale nemá oprávnění k provádění úloh konfigurace, jako je například vytváření pracovních postupů.

## <a name="determine-hybrid-identity-management-tasks"></a>Určení úlohy správy hybridní Identity
Distribuci úloh správy ve vaší organizaci zlepšuje přesnost a efektivnosti správy a vyrovnávání zátěže organizace. Toto jsou pivotů, které definují systém správy robustní identity.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

K definování úlohy správy hybridní identity, musíte pochopit některé základní znaky v organizaci, která bude mít přijetí hybridní identita. Je důležité si uvědomit, aktuální úložiště používá pro identitu zdroje. Musíte znát tyto základní prvky, mají základní požadavky a na základě, že budete muset klást podrobnější otázky, které budou vést k lepší rozhodnutí o návrhu pro vaše řešení Identity.  

Při definování těchto požadavků, ujistěte se, že alespoň na následující otázky jsou zodpovězeny.

* Možnosti zřizování: 
  
  * Podporuje řešení hybridní identity robustní účet správu přístupu a zřizování systému?
  * Jak jsou uživatelé, skupiny a hesla, které budete spravovat?
  * Správa životního cyklu identity reaguje? 
    * Jak dlouho pozastavení účtu aktualizace hesla trvá?
* Správa licencí: 
  
  * Zajišťuje správu licencí popisovače řešení hybridní identity?
    * Pokud ano, jaké funkce jsou k dispozici?
* Zpracovávat řešení správy na základě skupiny licencí? 
  
      - Pokud ano, je možné přiřadit skupinu zabezpečení? 
       - Pokud ano, bude cloudového adresáře automaticky přiřadit licence na všechny členy skupiny? 
        - Co se stane, pokud uživatel je následně přidány do nebo ze skupiny odstraněni, bude licence automaticky přiřadit nebo odebrat podle potřeby? 
* Integrace s jiných poskytovatelů identit třetích stran:
* Toto hybridní řešení se dá integrovat s poskytovatelů identit třetích stran k implementaci jednotné přihlašování?
* Je možné sjednocení všech poskytovatelů jinou identitu do systému získá na ucelenosti identity?
* Pokud ano, jak a které jsou jejich a funkcích, které jsou k dispozici?

## <a name="synchronization-management"></a>Synchronizace správy
Jedním z cílů identity Manageru, abyste mohli dostat všech poskytovatelů identit a udržovat je synchronizován. Zachovat data synchronizovala podle zprostředkovatele autoritativní hlavní identity. V hybridním scénáři identity s modelem synchronizované správu spravovat všechny identity uživatelů a zařízení v místním serveru a synchronizovat účty a, volitelně hesel do cloudu. Uživatel zadá stejné heslo místní, tak v cloudu, a při přihlášení, ověření hesla řešení identity. Tento model používá nástroj pro synchronizaci adresáře.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Správné návrh synchronizace hybridní řešení identit zkontrolujte odpovědi na tyto otázky: • co jsou k dispozici pro hybridní řešení identit synchronizace řešení?
• Co jsou jednotného přihlašování možnosti dostupné?
• Jaké jsou možnosti pro federaci identit mezi B2B a B2C?

## <a name="next-steps"></a>Další postup
[Určení strategie přijetí správy hybridní identity](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

