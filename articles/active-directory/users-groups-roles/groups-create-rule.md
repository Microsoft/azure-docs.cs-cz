---
title: Vytvoření dynamické skupiny a zkontrolovat stav ve službě Azure Active Directory | Dokumentace Microsoftu
description: Jak vytvořit pravidla členství ve skupinách na webu Azure Portal, jak zkontrolovat stav.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/02/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 9a2eb8ab4e3ee65e97de578c825bf106aee1b829
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480336"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Vytvoření dynamické skupiny a zkontrolovat stav

Ve službě Azure Active Directory (Azure AD) můžete vytvořit skupiny s použitím pravidla pro určení členství na základě vlastností uživatele nebo zařízení. Když atributy uživatele nebo zařízení změní, Azure AD vyhodnocuje všechny dynamické skupiny pravidel v tenantovi Azure AD a provede všechny přidá nebo odebere. Pokud uživatel nebo zařízení splňuje pravidlo pro skupinu, se přidají jako člen a pokud již uživatel pravidlo, se odeberou.

Tento článek podrobně popisuje, jak nastavit pravidlo, na webu Azure Portal pro dynamické členství pro skupiny zabezpečení nebo skupiny Office 365. Příklady syntaxe pravidla a úplný seznam podporovaných vlastností, operátory a hodnoty pro pravidlo členství, naleznete v tématu [pravidla dynamického členství pro skupiny ve službě Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Chcete-li vytvořit pravidla členství ve skupině

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce uživatelských účtů.
2. Vyberte **skupiny**.
3. Vyberte **všechny skupiny**a vyberte **novou skupinu**.

   ![Přidat novou skupinu](./media/groups-create-rule/new-group-creation.png)

4. Na **skupiny** okně zadejte název a popis nové skupiny. Vyberte **typ členství** buď **dynamický uživatel** nebo **dynamické zařízení**, v závislosti na tom, jestli chcete vytvořit pravidlo pro uživatele nebo zařízení a pak vyberte **Přidat dynamický dotaz**. Můžete použít Tvůrce pravidlo k vytvoření jednoduché pravidlo nebo pravidla členství napsat sami. Tento článek obsahuje další informace o dostupných atributů uživatelů a zařízení, stejně jako příklady pravidel členství.

   ![Přidat dynamické pravidlo členství](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Po vytvoření pravidla, vyberte **přidat dotaz** v dolní části okna.
6. Vyberte **vytvořit** na **skupiny** okno pro vytvoření skupiny.

> [!TIP]
> Vytvoření skupiny selže, pokud pravidlo, které jste zadali, byl nesprávně vytvořený nebo není platný. Oznámení se zobrazí v pravém dolním rohu portálu, který obsahuje vysvětlení, proč nebylo možné zpracovat pravidlo. Přečtěte si ho pečlivě, abyste pochopili, jak je potřeba upravit pravidlo, aby byl platný.

## <a name="check-processing-status-for-a-membership-rule"></a>Zkontrolujte stav zpracování pravidla členství

Zobrazí členství na zpracování stavu a datum poslední aktualizace **přehled** stránce pro skupinu.
  
  ![Zobrazení stavu dynamické skupiny](./media/groups-create-rule/group-status.png)

Tyto stavové zprávy lze zobrazit pro **členství zpracování** stavu:

* **Vyhodnocení**: byla přijata změnu skupiny a aktualizace se právě vyhodnocována.
* **Zpracování**: aktualizace se právě zpracovává.
* **Dokončena aktualizace**: bylo dokončeno zpracování a provedli všechny použitelné aktualizace.
* **Chyba zpracování**: došlo k chybě při vyhodnocování pravidel členství a nešlo ho dokončit zpracování.
* **Pozastavená aktualizace**: aktualizace byla pozastavena správcem pravidlo dynamického členství. MembershipRuleProcessingState nastavená na "Pozastaveno".

Tyto stavové zprávy lze zobrazit pro **členství poslední aktualizace** stavu:

* &lt;**Datum a čas**&gt;: poslední čas aktualizace členství.
* **V průběhu**: momentálně probíhají aktualizace.
* **Neznámý**: Nelze získat čas poslední aktualizace. Může být příčinou nově vytvářené skupiny.

Pokud dojde k chybě při zpracování pravidla členství pro konkrétní skupinu, upozornění se zobrazí v horní **stránka s přehledem** pro skupinu. Pokud ne čekající členství v dynamické aktualizace lze zpracovat pro všechny skupiny v rámci tenanta pro další pak 24 hodin, upozornění se zobrazí v horní **všechny skupiny**.

![zpracování chybová zpráva](./media/groups-create-rule/processing-error.png)

Tyto články poskytují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
