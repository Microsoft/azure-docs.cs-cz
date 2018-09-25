---
title: Vysvětlení resource zamykání v Azure plány
description: Další informace o možnosti uzamčení k ochraně prostředků při přiřazování podrobný plán.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973248"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Vysvětlení resource zamykání v Azure plány

Vytvoření konzistentního prostředí ve velkém měřítku se zobrazí pouze skutečně cenná tehdy, když je mechanismus pro zajištění, že je zachována konzistence. Tento článek vysvětluje, jak funguje uzamčení prostředků v Azure plány.

## <a name="locking-modes-and-states"></a>Režimy zamykání a stavy

Režim uzamčení se vztahuje na přiřazení podrobného plánu a má jenom dvě možnosti: **žádný** nebo **všechny prostředky**. Tím se nakonfiguruje během přiřazení podrobného plánu a po přiřazení se úspěšně použila k předplatnému se nedá změnit.

Prostředky vytvořené v rámci definice artefaktů v rámci podrobného plánu přiřadit k předplatnému mají tři stavy: **není uzamčen**, **jen pro čtení**, nebo **nelze upravit nebo odstranit**. Může mít jakýkoli druh artefaktu **není uzamčen** stavu. Skupina jiných prostředků artefakty jsou však jako **jen pro čtení** a skupiny prostředků jsou jako **nelze upravit nebo odstranit**. To je zásadní rozdíl v tom, jak se spravují tyto prostředky.

**Jen pro čtení** stav je přesně tak, jak je definováno: prostředku nejde změnit žádným způsobem – žádné změny a nelze odstranit. **Nelze upravit nebo odstranit** nuancovanější vzhledem k povaze "kontejneru" skupiny prostředků. Objekt skupiny prostředků je jen pro čtení, ale je možné vytvořit, aktualizace a odstranění prostředků v rámci prostředku skupiny – tak dlouho, dokud nejsou součástí žádné přiřazení podrobného plánu se **jen pro čtení** zamknout stavu.

## <a name="overriding-locking-states"></a>Přepsání zamykání stavu

I když je obvykle možné někomu, s odpovídající [řízení přístupu na základě rolí](../../../role-based-access-control/overview.md) (RBAC) v rámci předplatného, jako je například roli 'Owner' bude schopná změna nebo odstranění všech prostředků, to neplatí, když podrobné plány. platí uzamčení jako součást nasazeného přiřazení. Pokud přiřazení byl nastaven s **Zámek** možnost, není v případě předplatné vlastníka můžete změnit zahrnuté prostředky.

Tím se zajistí ochrana konzistence definované podrobného plánu a prostředí, ve kterém bylo navrženo vytvořte náhodnému nebo programové odstranění nebo změnu.

## <a name="removing-locking-states"></a>Odebrání uzamčení stavy

Pokud bude potřeba odstraňte prostředky vytvořené podle přiřazení, je jediný způsob, jak je odstranit nejprve odebrat přiřazení. Při odebrání přiřazení, se odeberou zámky vytvořené podrobné plány. Prostředek, ale se zachovají a potom budete muset odstranit prostřednictvím normální znamená, že uživatel s příslušnými oprávněními.

## <a name="how-blueprint-locks-work"></a>Jak uzamkne podrobný plán práce

Roli RBAC `denyAssignments` platí pro zdroje artefaktů během přiřazování podrobný plán vybrali přiřazení **Zámek** možnost. Role se přidal spravovanou identitu přiřazení podrobného plánu a lze odebrat pouze ze zdroje artefaktů ve stejné spravovaná identita. To vynutí mechanismus zamykání a zabrání pokusům o odebrat zámek proti podrobného plánu mimo podrobné plány. Odebrání role a zámek je možné jenom tak, že odeberete přiřazení podrobného plánu, který může provádět jenom uživatelé, kteří mají příslušná oprávnění.

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](lifecycle.md)
- Vysvětlení použití [statické a dynamické parametry](parameters.md)
- Zjistěte, jak přizpůsobit [podrobný plán pořadí řazení](sequencing-order.md)
- Zjistěte, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md)
- Řešení problémů při přiřazení podrobného plánu se [obecný postup řešení potíží](../troubleshoot/general.md)