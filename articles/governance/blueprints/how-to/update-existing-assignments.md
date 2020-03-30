---
title: Aktualizace existujícího přiřazení z portálu
description: Přečtěte si o mechanismu pro aktualizaci existujícího přiřazení podrobného plánu z portálu v Azure Blueprints.
ms.date: 11/21/2019
ms.topic: how-to
ms.openlocfilehash: 2c1a24399d8fa1529665bfa60d1b889a84a29a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264658"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak aktualizovat existující přiřazení podrobného plánu

Při přiřazení podrobného plánu lze přiřazení aktualizovat. Aktualizace existujícího přiřazení má několik důvodů, například:

- Přidání nebo odebrání [uzamčení prostředků](../concepts/resource-locking.md)
- Změna hodnoty [dynamických parametrů](../concepts/parameters.md#dynamic-parameters)
- Upgrade přiřazení na novější **publikovanou** verzi podrobného plánu

## <a name="updating-assignments"></a>Aktualizace přiřazení

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **Přiřazené podrobné plány.**

1. V seznamu podrobných plánů klikněte levým tlačítkem myši na přiřazení podrobného plánu. Potom klepněte na tlačítko **Aktualizovat přiřazení** nebo klikněte pravým tlačítkem myši na přiřazení podrobného plánu a vyberte **aktualizovat přiřazení**.

   ![Aktualizace existujícího přiřazení podrobného plánu](../media/update-existing-assignments/update-assignment.png)

1. Stránka **Přiřadit podrobný plán** se načte předvyplněná všemi hodnotami z původního přiřazení.
   Můžete změnit **verzi definice podrobného plánu**, stav **přiřazení zámku** a všechny dynamické parametry, které existují v definici podrobného plánu. Po dokončení změn klikněte na **Přiřadit.**

1. Na stránce s aktualizovanými podrobnostmi o přiřazení se podívejte na nový stav. V tomto příkladu jsme přidali **uzamčení** přiřazení.

   ![Aktualizováno existující přiřazení podrobného plánu – změněn režim uzamčení](../media/update-existing-assignments/updated-assignment.png)

1. Podrobnosti o **dalších operacích přiřazení** naleznete v rozevíracím seznamu. Tabulka **Spravovaných prostředků** se aktualizuje podle vybrané operace přiřazení.

   ![Operace přiřazení podrobného plánu](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Pravidla pro aktualizaci přiřazení

Nasazení aktualizovaných přiřazení se řídí několika důležitými pravidly. Tato pravidla určují, co se stane s již nasazenými prostředky. Požadovaná změna a typ prostředku artefaktu, který se nasazuje nebo aktualizuje, určují, které akce jsou prováděny.

- Přiřazení rolí
  - Pokud se role nebo postupník role (uživatel, skupina nebo aplikace) změní, vytvoří se nové přiřazení role. Přiřazení rolí, která byla dříve nasazena, jsou ponechána na místě.
- Přiřazení zásad
  - Pokud se změní parametry přiřazení zásad, existující přiřazení se aktualizuje.
  - Pokud dojde ke změně definice přiřazení zásad, vytvoří se nové přiřazení zásad.
    Přiřazení zásad, která byla dříve nasazena, jsou ponechána na místě.
  - Pokud artefakt přiřazení zásad je odebrán z podrobného plánu, nasazená přiřazení zásad jsou ponechány na místě.
- Šablony Azure Resource Manageru
  - Šablona je zpracována prostřednictvím Správce prostředků jako **PUT**. Jako každý typ prostředku zpracovává tuto akci odlišně, zkontrolujte dokumentaci pro každý zahrnutý prostředek k určení dopadu této akce při spuštění podrobné plány.

## <a name="possible-errors-on-updating-assignments"></a>Možné chyby při aktualizaci přiřazení

Při aktualizaci přiřazení je možné provést změny, které se při spuštění přeruší. Příkladem je změna umístění skupiny prostředků po jeho nasazení. Všechny změny, které jsou podporovány [Správce prostředků Azure](../../../azure-resource-manager/management/overview.md) lze provést, ale všechny změny, které by vedly k chybě prostřednictvím Správce prostředků Azure bude také mít za následek selhání přiřazení.

Neexistuje žádné omezení, kolikrát lze přiřazení aktualizovat. Pokud dojde k chybě, určete chybu a proveďte další aktualizaci přiřazení.  Příklad y chybových scénářů:

- Chybný parametr
- Již existující objekt
- Změna, kterou správce prostředků Azure nepodporuje

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [životním cyklu podrobného plánu](../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../concepts/resource-locking.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).