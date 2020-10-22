---
title: Ověřit pravidla pro členství v dynamické skupině (Preview) – Azure AD | Microsoft Docs
description: Testování členů na základě pravidla členství pro dynamické skupiny v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbbe37938249ee02c7deb4081a96aa1be2d8b7cb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375818"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Ověření pravidla členství v dynamické skupině (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) nyní poskytuje způsob, jak ověřit pravidla dynamické skupiny (ve verzi Public Preview). Na kartě **ověřit pravidla** můžete ověřit své dynamické pravidlo proti ukázkovým členům skupiny a potvrdit, že pravidlo funguje podle očekávání. Správci chtějí při vytváření nebo aktualizaci pravidel dynamických skupin zjistit, jestli uživatel nebo zařízení bude členem skupiny. Tato možnost pomáhá vyhodnotit, jestli uživatel nebo zařízení splňuje kritéria pravidla a pomoc při řešení potíží, když členství neočekáváte.

## <a name="step-by-step-walk-through"></a>Podrobný postup – procházení

Začněte tím, že přejdete na **Azure Active Directory**  >  **skupiny**. Vyberte existující dynamickou skupinu nebo vytvořte novou dynamickou skupinu a klikněte na pravidla dynamického členství. Pak se můžete podívat na kartu **pravidla pro ověření** .

![Najděte kartu pravidla pro ověření a začněte s existujícím pravidlem.](./media/groups-dynamic-rule-validation/validate-tab.png)

Na kartě **ověřit pravidla** můžete vybrat uživatele a ověřit členství. v jednom okamžiku může být vybráno 20 uživatelů nebo zařízení.

![Přidat uživatele, aby ověřili stávající pravidlo proti](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Po výběru uživatelů nebo zařízení z výběru **Vyberte možnost**automaticky spustit ověřování a zobrazí se výsledky ověření.

![Zobrazit výsledky ověření pravidla](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Výsledky informují o tom, jestli je uživatel členem skupiny, nebo ne. Pokud pravidlo není platné nebo dojde k potížím se sítí, výsledek bude zobrazen jako **Neznámý**. V případě **neznámé**se zobrazí podrobná chybová zpráva s popisem problému a potřebných akcí.

![Zobrazit podrobnosti o výsledcích ověření pravidla](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Můžete upravit pravidlo a ověření členství se spustí. Pokud chcete zjistit, proč uživatel není členem skupiny, klikněte na Zobrazit podrobnosti a v podrobnostech o ověření se zobrazí výsledek každého výrazu, který pravidlo sestavuje. Kliknutím na tlačítko **OK** ukončete.

## <a name="next-steps"></a>Další kroky

- [Dynamická pravidla členství pro skupiny](groups-dynamic-membership.md)
