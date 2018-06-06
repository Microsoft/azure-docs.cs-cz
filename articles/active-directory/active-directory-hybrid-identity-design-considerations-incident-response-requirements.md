---
title: Návrhu hybridní identity - požadavků na reakce na incidenty Azure | Microsoft Docs
description: Určení schopnosti sledování a hlášení pro hybridní řešení identit, které můžete využít k provádění akcí na zjišťovat a zmírňovat potenciální hrozby IT
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 09dfa0bd01a7de95ba6ce3093ddf589595116aa6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801054"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Stanovení požadavků na reakce na incidenty pro vaše řešení hybridní identity
Střední a velké organizace s největší pravděpodobností bude mít [reakcí na incidenty zabezpečení](https://technet.microsoft.com/library/cc700825.aspx) v mechanismy, které pomáhají IT odpovídajícím způsobem proveďte akce na úroveň incidentu. Systém správy identit je důležitou součástí při proces reakcí na incidenty, protože je možné použít ke identifikace, kdo provedl konkrétní akce na cíli. Hybridní řešení identit musí být schopen poskytnout funkce monitorování a vytváření sestav, které můžete využít k provádění akcí na zjišťovat a zmírňovat potenciální hrozbu IT. V plánu reakcí na incidenty typické máte následující fáze jako součást plánu:

1. Počáteční hodnocení.
2. Incidentu komunikaci.
3. Ovládací prvek poškození a snížení rizika.
4. Identifikace co bylo ohrožení zabezpečení a závažnosti.
5. Zachování důkaz.
6. Oznámení odpovídající stranám.
7. Obnovení systému.
8. Dokumentace.
9. Poškození a náklady na hodnocení.
10. Proces a plán revize.

Při identifikaci co IT oddělení byl ohrožení zabezpečení a závažnost fáze, bude potřeba k identifikaci systémy, které ohrožený, soubory, které mají přístup k a zjistit, zda rozlišuje těchto souborů. Hybridní identity systému byste měli mít ke splnění těchto požadavků, které chcete určit uživatele, který tyto změny. 

## <a name="monitoring-and-reporting"></a>Monitorování a vytváření sestav
Kolikrát systém identit může také pomoci v prvním posouzení fázi, hlavně v případě, že systém má integrovaný auditování a funkce vytváření sestav. Během počáteční hodnocení správce IT musí být schopen identifikovat podezřelé aktivity nebo systému byste měli mít k aktivační události, které se automaticky podle předem nakonfigurovaná úloh. Řada aktivit by to znamenat možný útok, ale v ostatních případech chybně nakonfigurovaný systém může vést k počet falešně pozitivních zjištění systému zjišťování neoprávněných vniknutí. 

Systém správy identit by měly pomáhat správcům IT identifikovat a sestavy těchto podezřelých aktivit. Tyto technické požadavky lze splnit obvykle monitorování všech systémů a nutnosti vykazovací funkci, můžete upozorňující na potenciální hrozby. Při návrhu řešení hybridní identity při vezme v úvahu požadavky reakce na incidenty, použijte níže uvedené otázky:

* Má vaše společnost reakce na incidenty zabezpečení v místě?
  * Pokud ano, je aktuální systém správy identity použít jako součást procesu
* Potřebuje vaše společnost k identifikaci podezřelé pokusů o přihlášení od uživatelů na různých zařízeních?
* Potřebuje vaše společnost zjistit potenciální ohrožení zabezpečení přihlašovacích údajů uživatele?
* Potřebuje vaše společnost auditovat přístup a akce uživatele?
* Potřebuje vaše společnost vědět, když uživatel resetovat heslo?

## <a name="policy-enforcement"></a>Vynucení zásad
Během kontroly poškození a snížení rizika – fáze je potřeba rychle snížit skutečných a možných důsledcích útoku. Tato akce, který bude trvat v tomto okamžiku můžete provést rozdíl mezi menší a hlavní jeden. Přesná reakce bude záviset na vaší organizace a povahy útoku, který jste čelí. Pokud počáteční assessment dospělo k závěru, že došlo k ohrožení účet, musíte se k vynucení zásad pro blokování tento účet. To je pouze příklad, kdy bude využít systém správy identit. Při návrhu hybridní řešení identit při vezměte v úvahu, jak zásad se vynutí reagování na probíhající incident použijte níže uvedené otázky:

* Má vaše společnost zavedené zásady uživatelům bloku před přístupem sítě v případě potřeby?
  * Pokud ano, umožňuje aktuální řešení integraci s systém správy hybridní identity, který chcete použít?
* Potřebuje vaše společnost pro vynucení podmíněného přístupu pro uživatele, kteří jsou do karantény? 

> [!NOTE]
> Zajistěte, aby poznamenejte každou odpověď a pochopit důvody odpověď. [Definování strategie ochrany dat](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) budou přenášeny po dostupných možností a výhod i nevýhod jednotlivých možností.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Definování strategie ochrany dat](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

