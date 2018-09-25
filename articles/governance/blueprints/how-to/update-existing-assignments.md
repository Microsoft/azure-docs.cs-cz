---
title: Aktualizace existujícího přiřazení podrobného plánu Azure
description: Další informace o mechanismus pro aktualizace existujícího přiřazení v Azure podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956196"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak aktualizovat existující přiřazení podrobného plánu

Pokud je podrobný plán přiřazen, je možné aktualizovat přiřazení. Existuje několik důvodů, proč aktualizace existujícího přiřazení, včetně:

- Přidání nebo odebrání [uzamčení prostředků](../concepts/resource-locking.md)
- Změňte hodnotu vlastnosti [dynamické parametry](../concepts/parameters.md#dynamic-parameters)
- Upgrade na novější přiřazení **publikováno** verzi podrobný plán

## <a name="updating-assignments"></a>Aktualizují se přiřazení

1. Spusťte službu Azure plány na webu Azure Portal kliknutím na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na **zásady** stránky, klikněte na **plány**.

1. Vyberte **přiřazené podrobné plány** ze stránky na levé straně.

1. V seznamu podrobné plány, kliknutím levého tlačítka myši přiřazení podrobného plánu a pak klikněte na tlačítko **přiřazení aktualizací** tlačítko nebo klikněte pravým tlačítkem na přiřazení podrobného plánu a vyberte **přiřazení aktualizací**.

   ![Aktualizovat přiřazení](../media/update-existing-assignments/update-assignment.png)

1. **Přiřazení podrobného plánu** se načítání stránky předem vyplněné se všemi hodnotami z původního přiřazení. Můžete změnit **verze definice podrobného plánu**, **zámek přiřazení** stavu a všechny dynamické parametry, které existují v definici podrobného plánu. Klikněte na tlačítko **přiřadit** po dokončení změn.

1. Na stránce s podrobnostmi aktualizované přiřazení najdete v článku nový stav. V tomto příkladu jsme přidali **uzamykání** k přiřazení.

   ![Aktualizované přiřazení - uzamčen](../media/update-existing-assignments/updated-assignment.png)

1. Prozkoumejte podrobnosti o dalších **operací přiřazení** pomocí rozevíracího seznamu. Tabulka **spravovaných prostředků** aktualizace podle operace vybrané přiřazení.

   ![Operace přiřazení](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Pravidla pro aktualizují se přiřazení

Nasazení aktualizované přiřazení následuje pár důležitých pravidla. Tato pravidla určují, co se stane pro existující prostředek v závislosti na požadované změny a Typ artefaktu prostředků se nasazení nebo aktualizovat.

- Přiřazení rolí
  - Pokud se změní roli nebo roli assignee (uživatel, skupina nebo aplikace), se vytvoří nové přiřazení role. Přiřazení role už nasazenou zůstává na místě.
- Přiřazení zásad
  - Pokud se změní parametry přiřazení zásady, se aktualizuje existující přiřazení.
  - Pokud jsou změnit definici přiřazení zásad, vytvoří se nové přiřazení zásady. Přiřazení dříve nasazené zásady je ponecháno na místě.
  - Pokud artefaktu přiřazení zásady je odebrán z podrobný plán, přiřazení dříve nasazené zásady zůstává na místě.
- Šablony Azure Resource Manageru
  - Zpracování prostřednictvím Resource Manageru jako šablony **UMÍSTIT**. Protože každý typ prostředku to zpracovává jinak, najdete v dokumentaci jednotlivých zahrnutých prostředků a určit dopad této akce při spuštění pomocí podrobné plány.

## <a name="possible-errors-on-updating-assignments"></a>Možné chyby na aktualizaci přiřazení

Při aktualizaci přiřazení, je možné provádět změny poškozující při spuštění. Příkladem je změna umístění skupiny prostředků, poté, co již byla nasazena. Všechny změny, které jsou podporovány v [Azure Resource Manageru](../../../azure-resource-manager/resource-group-overview.md) může být ale jakákoliv změna, která vedou k chybě prostřednictvím Azure Resource Manageru se také způsobí selhání přiřazení.

Neexistuje žádné omezení na počet opakování, což je přiřazení je možné aktualizovat. Pokud dojde k chybě, buď z důvodu chybný parametr, již existující objekt nebo změnu zakázané pomocí Azure Resource Manageru, proto zjistit chyby a proveďte jinou aktualizaci přiřazení.

## <a name="next-steps"></a>Další postup

- Další informace o [podrobný plán životního cyklu](../concepts/lifecycle.md)
- Vysvětlení použití [statické a dynamické parametry](../concepts/parameters.md)
- Zjistěte, jak přizpůsobit [podrobný plán pořadí řazení](../concepts/sequencing-order.md)
- Zjistěte, jak se využívání [podrobný plán uzamčení prostředků](../concepts/resource-locking.md)
- Řešení problémů při přiřazení podrobného plánu se [obecný postup řešení potíží](../troubleshoot/general.md)
