---
title: Vysvětlení resource zamykání v Azure plány
description: Další informace o možnosti uzamčení k ochraně prostředků při přiřazování podrobný plán.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139438"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Vysvětlení resource zamykání v Azure plány

Vytvoření konzistentního prostředí ve velkém měřítku je pouze skutečně cenná tehdy, když je mechanismus pro udržování této konzistence. Tento článek vysvětluje, jak funguje uzamčení prostředků v Azure plány.

## <a name="locking-modes-and-states"></a>Režimy zamykání a stavy

Režim uzamčení se vztahuje na přiřazení podrobného plánu a má jenom dvě možnosti: **žádný** nebo **všechny prostředky**. Režim uzamčení se nakonfiguruje během přiřazení podrobného plánu a po přiřazení se úspěšně použila k předplatnému se nedá změnit.

Prostředky vytvořené v přiřazení podrobného plánu artefakty mají tři stavy: **není uzamčen**, **jen pro čtení**, nebo **nelze upravit nebo odstranit**. Každý artefakt může být v **není uzamčen** stavu. Ale mají skupiny bez prostředků artefakty **jen pro čtení** a skupiny prostředků mají **nelze upravit nebo odstranit** stavy. Tento rozdíl je zásadní rozdíl v tom, jak se spravují tyto prostředky.

**Jen pro čtení** stav je přesně tak, jak je definováno: prostředku nejde změnit žádným způsobem – žádné změny a nelze odstranit. **Nelze upravit nebo odstranit** nuancovanější vzhledem k povaze "kontejneru" skupiny prostředků. Objekt skupiny prostředků je jen pro čtení, ale je možné provádět změny bez uzamčení prostředků v rámci skupiny prostředků.

## <a name="overriding-locking-states"></a>Přepsání zamykání stavu

Je obvykle možné někomu, s odpovídající [řízení přístupu na základě rolí](../../../role-based-access-control/overview.md) (RBAC) v rámci předplatného, jako je například roli "Vlastník", bude moct změnit ani odstranit všechny prostředky. Tento přístup neplatí, když platí plány uzamčení jako součást nasazeného přiřazení. Pokud přiřazení byl nastaven s **Zámek** možnost, není v případě předplatné vlastníka můžete změnit zahrnuté prostředky.

Tato bezpečnostní opatření chrání konzistence definované podrobného plánu a prostředí, ve kterém bylo navrženo vytvořte náhodnému nebo programové odstranění nebo změnu.

## <a name="removing-locking-states"></a>Odebrání uzamčení stavy

Pokud bude nutné odstraňte prostředky vytvořené podle přiřazení, je způsob, jak je odstranit, nejprve odebrat přiřazení. Při odebrání přiřazení, se odeberou zámky vytvořené podrobné plány. Ale prostředek se zachovají a bude nutné odstranit normálním způsobem.

## <a name="how-blueprint-locks-work"></a>Jak uzamkne podrobný plán práce

Roli RBAC `denyAssignments` platí pro zdroje artefaktů během přiřazování podrobný plán vybrali přiřazení **Zámek** možnost. Role se přidal spravovanou identitu přiřazení podrobného plánu a lze odebrat pouze ze zdroje artefaktů ve stejné spravovaná identita. Tato bezpečnostní opatření vynucuje mechanismus zamykání a zabraňuje odebrání zámku podrobného plánu mimo podrobné plány. Odebrání role a zámek je možné jenom tak, že odeberete přiřazení podrobného plánu, který může provádět jenom uživatelé, kteří mají příslušná oprávnění.

> [!IMPORTANT]
> Azure Resource Manager ukládá do mezipaměti podrobnosti o přiřazení role po dobu až 30 minut. V důsledku toho `denyAssignments` na podrobný plán prostředky nemusí být okamžitě v plný vliv. Během tohoto časového období je možné odstranit prostředek má být chráněn zámky podrobného plánu.

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)