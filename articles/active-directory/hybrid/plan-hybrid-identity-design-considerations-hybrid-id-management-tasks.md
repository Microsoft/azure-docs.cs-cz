---
title: Úloh správy návrhu hybridní identity – Azure | Dokumentace Microsoftu
description: Pomocí řízení podmíněného přístupu Azure Active Directory ověří zvláštní podmínky, kterou vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Po splnění těchto podmínek, uživatel ověří a povolí se přístup k aplikaci.
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
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: f6e5ca57eaa4a4a1975c0295933fc91e5726ae4c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487927"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plánování životního cyklu hybridní Identity
Identita je jedním ze základů strategii enterprise mobility a aplikaci přístup. Jestli se přihlašujete vaše mobilní zařízení nebo aplikaci SaaS, vaše identita je klíčem k získání přístupu ke všemu, co. Na nejvyšší úrovni řešení pro správu identit zahrnuje sjednocení a synchronizaci mezi vaší identity úložišť, což zahrnuje automatizuje a centralizuje proces zřizování prostředků. Řešení identit by měl být centralizované identitou napříč místním i cloudovým a zároveň použít určitou formu federace identit pro zachovat centralizované ověřování a bezpečně sdílet a spolupracovat s externími uživateli a firmy. Prostředky v rozsahu od operačních systémů a aplikací na osoby ve ani pod něj nespadá s organizací. Organizační struktura může být upraven, aby odpovídal zřizování zásad a postupů.

Je také důležité, aby byly přizpůsobeny Zlepšete možnosti svých uživatelů a poskytovat jim samoobslužné možnosti zajistit produktivitu řešení identitám. Řešení identity je výkonnější, že pokud to umožňuje jednotné přihlašování pro uživatele přes všechny prostředky, které potřebují přístup. Správci na všech úrovních standardizované postupy můžete použít ke správě přihlašovacích údajů uživatele. Některé úrovní správy lze omezit nebo vyloučit, podle toho, kontejnerových nástrojů zřizování řešení pro správu. Kromě toho můžete bezpečně distribuovat možností správy, ručně nebo automaticky, mezi různými organizacemi. Správce domény může například sloužit pouze lidé a prostředky v této doméně. Tento uživatel může provádět úkoly správy a zřizování, ale nemá oprávnění k provádění úloh konfigurace, jako je například vytváření pracovních postupů.

## <a name="determine-hybrid-identity-management-tasks"></a>Určení úloh správy hybridní Identity
Distribuce úloh správy ve vaší organizaci zlepšuje přesnost a efektivitu správy a vyrovnávání zatížení v organizaci. Následující části jsou pivoty, které definují systému správy identit v robustní.

 ![](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Chcete-li definovat úkoly správy hybridní identity, musíte pochopit některé základní vlastnosti organizace, která bude přijímat hybridní identita. Je důležité, abyste pochopili aktuální úložišť se používají pro identitu zdroje. Musíte znát tyto základní prvky, bude mít základní požadavky a na základě, že budete muset klást podrobnější otázky, které vás přesměruje lepší rozhodnutí o návrhu pro vaše řešení identit.  

Při definování těchto požadavků, ujistěte se, že alespoň na následující otázky jsou zodpovězeny.

* Možnosti zřizování: 
  
  * Podporuje řešení hybridní identity účtu robustní správu přístupu a zřizování systému?
  * Jak se uživatelé, skupiny a hesla, které pokud chcete spravovat?
  * Správa životního cyklu identit je responzivní? 
    * Jak dlouho pozastavení účtu aktualizace hesla trvá?
* Správa licencí: 
  
  * Nemá licenci správy hybridních identit řešení popisovačů?
    * Pokud ano, jaké možnosti jsou k dispozici?
* Řešení postupovat při správě na základě skupin licencí? 
  
      - Pokud ano, je možné přiřadit skupinu zabezpečení? 
       - Pokud ano, cloudovým adresářem automaticky přiřadí licence všem členům skupiny? 
        - Co se stane, pokud uživatel je následně přidány do nebo ze skupiny odebrány, se licence se automaticky přiřazovat a odebírat podle potřeby? 
* Integrace s ostatními zprostředkovateli identity třetí strany:
* Je možné integrovat toto hybridní řešení pomocí poskytovatelů identit třetích stran k implementaci jednotného přihlašování?
* Je možné sjednocení všech zprostředkovatelů jinou identitou systém získá na ucelenosti identity?
* Pokud ano, jak a které jsou, a jaké možnosti jsou k dispozici?

## <a name="synchronization-management"></a>Správa synchronizace
Jedním z cílů identity Manageru, abyste mohli přenést všechny poskytovatele identit a zajistěte jejich synchronizaci. Zachovat synchronizaci dat založené na zprostředkovateli autoritativní hlavní identity. V hybridním scénáři identity, s modelem synchronizované správy spravovat všechny identity uživatelů a zařízení v místním serveru a synchronizujte účty a volitelně hesel do cloudu. Uživatel zadá stejné heslo místní dělají v cloudu, a při přihlášení, ověření hesla podle řešení identit. Tento model používá nástroje pro synchronizaci adresáře.

![](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) Správné návrhu synchronizace hybridní řešení identit Ujistěte se, že jsou odpovědi na následující otázky: • co jsou k dispozici pro hybridní řešení identit synchronizace řešení?
• Jaké jsou dostupné možnosti jednotného přihlašování?
• Jaké jsou možnosti pro federaci mezi B2B a B2C?

## <a name="next-steps"></a>Další postup
[Určení strategie přijetí správy hybridní identity](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

