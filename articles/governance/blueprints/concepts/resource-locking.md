---
title: Vysvětlení uzamčení prostředků
description: Další informace o možnosti uzamčení k ochraně prostředků při přiřazování podrobný plán.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2e281896d45ada8010f24a1f18265a8cdd523d31
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696979"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Vysvětlení resource zamykání v Azure plány

Vytvoření konzistentního prostředí ve velkém měřítku je pouze skutečně cenná tehdy, když je mechanismus pro udržování této konzistence. Tento článek vysvětluje, jak funguje uzamčení prostředků v Azure plány.

## <a name="locking-modes-and-states"></a>Režimy zamykání a stavy

Režim uzamčení se vztahuje na přiřazení podrobného plánu a má tři možnosti: **Není zamknout**, **jen pro čtení**, nebo **neodstraňujte**. Režim uzamčení je nakonfigurovali během nasazení artefaktů během přiřazení podrobného plánu. Aktualizují se přiřazení podrobného plánu můžete nastavit jiný režim uzamčení.
Režimy zamykání, však nelze změnit mimo podrobné plány.

Prostředky vytvořené v přiřazení podrobného plánu artefakty mají čtyř stavů: **Není uzamčen**, **jen pro čtení**, **nelze upravit nebo odstranit**, nebo **nelze odstranit**. Každý typ artefaktu může být v **není uzamčen** stavu. V následující tabulce můžete použít k určení stavu prostředku:

|Mode|Typ zdroje artefaktů|Stav|Popis|
|-|-|-|-|
|Nezamykat|*|Není uzamčen|Prostředky nejsou chráněny podrobné plány. Tento stav se používá také pro prostředky, které jsou přidány do **jen pro čtení** nebo **neodstraňujte** artefaktu skupinu prostředků z mimo přiřazení podrobného plánu.|
|Jen pro čtení|Skupina prostředků|Nelze upravit nebo odstranit|Skupina prostředků je jen pro čtení a nejde upravit značky pro skupinu prostředků. **Není uzamčen** prostředky můžete přidat, přesunout, změnit ani odstranit z této skupiny prostředků.|
|Jen pro čtení|Skupina prostředků bez|Jen pro čtení|Prostředek se nedá změnit žádným způsobem – žádné změny a nelze odstranit.|
|Neodstraňujte|*|Nelze odstranit|Prostředky můžete změnit, ale nejde odstranit. **Není uzamčen** prostředky můžete přidat, přesunout, změnit ani odstranit z této skupiny prostředků.|

## <a name="overriding-locking-states"></a>Přepsání zamykání stavu

Je obvykle možné někomu, s odpovídající [řízení přístupu na základě rolí](../../../role-based-access-control/overview.md) (RBAC) v rámci předplatného, jako je například roli "Vlastník", bude moct změnit ani odstranit všechny prostředky. Tento přístup neplatí, když platí plány uzamčení jako součást nasazeného přiřazení. Pokud přiřazení byl nastaven s **jen pro čtení** nebo **neodstraňujte** možnost, není v případě předplatné vlastníkem blokované akci může provést na chráněný prostředek.

Tato bezpečnostní opatření chrání konzistence definované podrobného plánu a prostředí, ve kterém bylo navrženo vytvořte náhodnému nebo programové odstranění nebo změnu.

## <a name="removing-locking-states"></a>Odebrání uzamčení stavy

Pokud bude nutné k úpravě nebo odstranění prostředku chráněny přiřazení, existují dva způsoby, jak to provést.

- Aktualizuje se přiřazení podrobného plánu zamykání režimu **není zámek**
- Odstranit přiřazení podrobného plánu

Při odebrání přiřazení, se odeberou zámky vytvořené podrobné plány. Ale prostředek se zachovají a bude nutné odstranit normálním způsobem.

## <a name="how-blueprint-locks-work"></a>Jak uzamkne podrobný plán práce

RBAC [zamítnout přiřazení](../../../role-based-access-control/deny-assignments.md) Odepřít akce platí pro zdroje artefaktů během přiřazování podrobný plán vybrali přiřazení **jen pro čtení** nebo **neodstraňujte** možnost. Akce odepřít přidává spravovanou identitu přiřazení podrobného plánu a lze odebrat pouze ze zdroje artefaktů ve stejném spravovanou identitu. Tato bezpečnostní opatření vynucuje mechanismus zamykání a zabraňuje odebrání zámku podrobného plánu mimo podrobné plány.

> [!IMPORTANT]
> Azure Resource Manager ukládá do mezipaměti podrobnosti o přiřazení role po dobu až 30 minut. V důsledku toho odeprete přiřazení Odepřít akce u prostředků podrobného plánu nemusí být hned v plný vliv. Během tohoto časového období je možné odstranit prostředek má být chráněn zámky podrobného plánu.

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)