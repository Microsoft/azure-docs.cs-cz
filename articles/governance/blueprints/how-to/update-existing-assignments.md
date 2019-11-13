---
title: Aktualizace existujícího přiřazení z portálu
description: Přečtěte si o mechanismu Aktualizace existujícího přiřazení z portálu v Azure modrotisky.
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: f48f8cfb33a05e2bf8dcbe097d3a9eb3a5ebb9db
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960362"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak aktualizovat existující přiřazení podrobného plánu

Při přiřazení podrobného plánu se přiřazení dá aktualizovat. Existuje několik důvodů Aktualizace existujícího přiřazení, včetně:

- Přidat nebo odebrat [uzamykání prostředků](../concepts/resource-locking.md)
- Změna hodnoty [dynamických parametrů](../concepts/parameters.md#dynamic-parameters)
- Upgradujte přiřazení na novější **publikovanou** verzi podrobného plánu.

## <a name="updating-assignments"></a>Aktualizace přiřazení

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky klikněte levým na přiřazení podrobného plánu. Pak klikněte na tlačítko **Aktualizovat přiřazení** nebo klikněte pravým tlačítkem na přiřazení podrobného plánu a vyberte **Aktualizovat přiřazení**.

   ![Aktualizuje existující přiřazení podrobného plánu.](../media/update-existing-assignments/update-assignment.png)

1. Stránka **přiřadit podrobný plán** načte předem vyplněnou všechny hodnoty z původního přiřazení. Můžete změnit **verzi definice**podrobného plánu, stav **přiřazení zámku** a všechny dynamické parametry, které existují v definici podrobného plánu. Až provedete změny, klikněte na **přiřadit** .

1. Na stránce aktualizované Podrobnosti přiřazení se podívejte na nový stav. V tomto příkladu jsme přidali **uzamykání** k přiřazení.

   ![Aktualizace existujícího přiřazení podrobného plánu – změna režimu zámku](../media/update-existing-assignments/updated-assignment.png)

1. Prozkoumejte podrobnosti o dalších **operacích přiřazení** pomocí rozevíracího seznamu. Tabulka s aktualizacemi **spravovaných prostředků** podle vybrané operace přiřazení

   ![Operace přiřazení přiřazení podrobného plánu](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Pravidla aktualizace přiřazení

Nasazení aktualizovaných přiřazení následuje několik důležitých pravidel. Tato pravidla určují, co se stane s již nasazenými prostředky. Požadovaná změna a typ prostředku artefaktu, který se nasazuje nebo aktualizuje, určují, které akce se mají vzít.

- Přiřazení rolí
  - Pokud se změní role nebo zmocněnec role (uživatel, skupina nebo aplikace), vytvoří se nové přiřazení role. Přiřazení rolí, které už byly nasazené, jsou ponechány na svém místě.
- Přiřazení zásad
  - Pokud se změní parametry přiřazení zásady, existující přiřazení se aktualizuje.
  - Pokud se změní definice přiřazení zásady, vytvoří se nové přiřazení zásady. Přiřazení zásad nasazené dříve jsou ponechány na svém místě.
  - Pokud se artefakt přiřazení zásad odebere z podrobného plánu, nasadí se nasazená přiřazení zásad.
- Šablony Azure Resource Manageru
  - Šablona je zpracována prostřednictvím Správce prostředků jako **Put**. Vzhledem k tomu, že každý typ prostředku zpracovává tuto akci odlišně, prostudujte si dokumentaci ke všem zahrnutým prostředkům, abyste zjistili dopad této akce při spuštění pomocí modrotisky.

## <a name="possible-errors-on-updating-assignments"></a>Možné chyby při aktualizaci přiřazení

Při aktualizaci přiřazení je možné provést změny, které se při spuštění přeruší. Příkladem je změna umístění skupiny prostředků poté, co již byla nasazena. Všechny změny, které jsou podporovány nástrojem [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) , mohou být provedeny, ale všechny změny, které by způsobily chybu prostřednictvím Azure Resource Manager, budou také mít za následek selhání přiřazení.

Neexistuje žádné omezení počtu, kolikrát může být přiřazení aktualizováno. Pokud dojde k chybě, určete chybu a proveďte další aktualizaci přiřazení.  Příklady scénářů chyb:

- Chybný parametr
- Již existující objekt
- Změna není podporována nástrojem Azure Resource Manager

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)