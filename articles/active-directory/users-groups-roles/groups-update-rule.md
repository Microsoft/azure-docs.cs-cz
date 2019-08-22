---
title: Aktualizace a Správa pravidla dynamické skupiny a řešení potíží s členstvím Azure Active Directory | Microsoft Docs
description: Postup vytvoření pravidla členství ve skupině v Azure Portal najdete v části stav.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657343"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Aktualizace dynamické skupiny pro správu členství v Azure Active Directory

V Azure Active Directory (Azure AD) můžete pomocí pravidel určit členství ve skupině na základě vlastností uživatele nebo zařízení. V tomto článku se dozvíte, jak nastavit pravidlo pro dynamickou skupinu v Azure Portal.
Dynamické členství je podporováno pro skupiny zabezpečení nebo skupiny Office 365. Při použití pravidla členství ve skupině se vyhodnotí atributy uživatelů a zařízení pro shody s pravidlem členství. Když se změní atribut uživatele nebo zařízení, zpracují se všechna pravidla dynamické skupiny v organizaci pro změny členství. Uživatelé a zařízení se přidají nebo odeberou, pokud splňují podmínky pro skupinu.

Příklady syntaxe, podporovaných vlastností, operátorů a hodnot pravidla členství najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Aktualizace pravidla členství ve skupině

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který se nachází v roli globální správce, Správce služby Intune nebo Správce uživatelů v tenantovi.
1. Vyberte **skupiny** > **všechny skupiny**.
1. Vyberte skupinu a otevřete její profil.
1. Na stránce profil pro skupinu vyberte **dynamická pravidla členství**. Tvůrce pravidel podporuje až pět výrazů. Chcete-li přidat šest nebo jakékoli následné výrazy, je nutné použít textové pole.

   ![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Chcete-li zobrazit vlastnosti vlastního rozšíření, které jsou k dispozici pro pravidlo členství:
   1. Vyberte **získat vlastnosti vlastního rozšíření** .
   2. Zadejte ID aplikace a pak vyberte **aktualizovat vlastnosti**.
1. Po aktualizaci pravidla vyberte **Uložit**.

Pokud pravidlo, které jste zadali, není platné, zobrazí se v oznámení Azure na portálu vysvětlení, proč se pravidlo nedalo zpracovat. Pečlivě si přečtěte, abyste zjistili, jak pravidlo opravit.

## <a name="check-processing-status-for-a-rule"></a>Zkontroluje stav zpracování pravidla.

Stav zpracování členství a datum poslední aktualizace můžete zobrazit na stránce **Přehled** pro danou skupinu.
  
  ![zobrazení stavu dynamické skupiny](./media/groups-create-rule/group-status.png)

Pro stav **zpracování členství** lze zobrazit následující stavové zprávy:

* **Vyhodnocení**:  Byla přijata změna skupiny a probíhá vyhodnocení aktualizací.
* **Zpracovává**se: Probíhá zpracování aktualizací.
* **Aktualizace dokončena**: Zpracování bylo dokončeno a všechny použitelné aktualizace byly provedeny.
* **Chyba zpracování**:  Zpracování se nepodařilo dokončit kvůli chybě při vyhodnocování pravidla členství.
* **Aktualizace**pozastavena: Správci pozastavili aktualizace pravidla dynamického členství. MembershipRuleProcessingState je nastavené na pozastaveno.

Pro stav **Poslední aktualizace členství** se můžou zobrazit tyto stavové zprávy:

* **Datum a čas**: Čas poslední aktualizace členství.
* **Probíhá**: Aktuálně probíhá aktualizace.
* **Neznámý**: Čas poslední aktualizace nelze načíst. Tato skupina může být nová.

Pokud při zpracovávání pravidla členství pro určitou skupinu dojde k chybě, zobrazí se v horní části **stránky přehled** pro danou skupinu výstraha. Pokud není možné zpracovat žádné nedokončené dynamické aktualizace členství pro všechny skupiny v tenantovi za více než 24 hodin, zobrazí se v horní části **všech skupin**výstraha.

![zpracování upozornění na chybové zprávy](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Další postup

Tyto články poskytují další informace o práci s dynamickými skupinami v Azure AD.

* Úplný odkaz na strukturu dynamického pravidla najdete v tématu [syntaxe pravidla dynamického členství](groups-dynamic-membership.md).
* [Vytvořte skupinu statických členství a přidejte členy](../fundamentals/active-directory-groups-create-azure-portal.md).
