---
title: Ověřit pravidla pro dynamické členství ve skupinách (preview) – Azure AD | Dokumenty společnosti Microsoft
description: Jak otestovat členy proti pravidlu členství pro dynamické skupiny ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115518"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Ověření pravidla dynamického členství ve skupině (preview) ve službě Azure Active Directory

Azure Active Directory (Azure AD) teď poskytuje prostředky k ověření pravidel dynamické skupiny (ve verzi Public Preview). Na kartě **Ověřit pravidla** můžete ověřit dynamické pravidlo proti členům vzorové skupiny a potvrdit, že pravidlo funguje podle očekávání. Při vytváření nebo aktualizaci pravidel dynamické skupiny chtějí správci vědět, zda bude členem skupiny uživatel nebo zařízení. To pomáhá vyhodnotit, zda uživatel nebo zařízení splňuje kritéria pravidla a pomoc při řešení potíží, když členství není očekáváno.

## <a name="step-by-step-walk-through"></a>Podrobný návod

Chcete-li začít, přejděte na **Azure Active Directory** > **Groups**. Vyberte existující dynamickou skupinu nebo vytvořte novou dynamickou skupinu a klikněte na dynamická pravidla členství. Poté se zobrazí karta **Ověřit pravidla.**

![Vyhledání karty Ověřit pravidla a začít s existujícím pravidlem](./media/groups-dynamic-rule-validation/validate-tab.png)

Na kartě **Ověřit pravidla** můžete vybrat uživatele, kteří ověří jejich členství. Najednou lze vybrat 20 uživatelů nebo zařízení.

![Přidání uživatelů k ověření existujícího pravidla proti](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Po výběru uživatelů nebo zařízení z výběru a **výběru**se automaticky spustí ověření a zobrazí se výsledky ověření.

![Zobrazit výsledky ověření pravidla](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Výsledky říkají, zda je uživatel členem skupiny nebo ne. Pokud pravidlo není platné nebo došlo k problému se sítí, výsledek se zobrazí jako **Neznámý**. V případě **Neznámé**, podrobná chybová zpráva popisuje problém a potřebné akce.

![Zobrazit podrobnosti o výsledcích ověření pravidla](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Můžete upravit pravidlo a bude spuštěno ověření členství. Chcete-li zjistit, proč uživatel není členem skupiny, klikněte na tlačítko "Zobrazit podrobnosti" a podrobnosti ověření zobrazí výsledek každého výrazu, který tvoří pravidlo. Chcete-li program ukončit, klepněte na tlačítko **OK.**

## <a name="next-steps"></a>Další kroky

- [Pravidla dynamického členství pro skupiny](groups-dynamic-membership.md)
