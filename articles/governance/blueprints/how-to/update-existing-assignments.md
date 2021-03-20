---
title: Aktualizace existujícího přiřazení z portálu
description: Přečtěte si o mechanismu Aktualizace existujícího přiřazení podrobného plánu z portálu v Azure Modrotiskys.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: c383ebedaf83b3a52062c91f98b816c3baf6618e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919389"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak aktualizovat existující přiřazení podrobného plánu

Při přiřazení podrobného plánu se přiřazení dá aktualizovat. Existuje několik důvodů Aktualizace existujícího přiřazení, včetně:

- Přidat nebo odebrat [uzamykání prostředků](../concepts/resource-locking.md)
- Změna hodnoty [dynamických parametrů](../concepts/parameters.md#dynamic-parameters)
- Upgradujte přiřazení na novější **publikovanou** verzi podrobného plánu.

## <a name="updating-assignments"></a>Aktualizace přiřazení

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na levé straně vyberte **přiřazené plány** .

1. V seznamu modrotisky vyberte přiřazení podrobného plánu. Pak použijte tlačítko **Aktualizovat přiřazení** nebo klikněte pravým tlačítkem na přiřazení podrobného plánu a vyberte **Aktualizovat přiřazení**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Snímek stránky přiřazení podrobného plánu se zvýrazněným tlačítkem aktualizovat přiřazení" border="false":::

1. Stránka **přiřadit podrobný plán** načte předem vyplněnou všechny hodnoty z původního přiřazení. Můžete změnit **verzi definice** podrobného plánu, stav **přiřazení zámku** a všechny dynamické parametry, které existují v definici podrobného plánu. Po dokončení změn vyberte **přiřadit** .

1. Na stránce aktualizované Podrobnosti přiřazení se podívejte na nový stav. V tomto příkladu jsme přidali **uzamykání** k přiřazení.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Snímek obrazovky aktualizovaného přiřazení podrobného plánu ukazující změnu režimu zámku" border="false":::

1. Prozkoumejte podrobnosti o dalších **operacích přiřazení** pomocí rozevíracího seznamu. Tabulka s aktualizacemi **spravovaných prostředků** podle vybrané operace přiřazení

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Snímek obrazovky aktualizovaného přiřazení podrobného plánu znázorňující operace přiřazení a jejich stav" border="false":::

## <a name="rules-for-updating-assignments"></a>Pravidla aktualizace přiřazení

Nasazení aktualizovaných přiřazení následuje několik důležitých pravidel. Tato pravidla určují, co se stane s již nasazenými prostředky. Požadovaná změna a typ prostředku artefaktu, který se nasazuje nebo aktualizuje, určují, které akce se mají vzít.

- Přiřazení rolí
  - Pokud se změní role nebo zmocněnec role (uživatel, skupina nebo aplikace), vytvoří se nové přiřazení role. Přiřazení rolí, které už byly nasazené, jsou ponechány na svém místě.
- Přiřazení zásad
  - Pokud se změní parametry přiřazení zásady, existující přiřazení se aktualizuje.
  - Pokud se změní definice přiřazení zásady, vytvoří se nové přiřazení zásady.
    Přiřazení zásad nasazené dříve jsou ponechány na svém místě.
  - Pokud se artefakt přiřazení zásad odebere z podrobného plánu, nasadí se nasazená přiřazení zásad.
- Šablony Azure Resource Manager (šablony ARM)
  - Šablona je zpracována prostřednictvím Správce prostředků jako **Put**. Vzhledem k tomu, že každý typ prostředku zpracovává tuto akci odlišně, prostudujte si dokumentaci ke všem zahrnutým prostředkům, abyste zjistili dopad této akce při spuštění pomocí modrotisky.

## <a name="possible-errors-on-updating-assignments"></a>Možné chyby při aktualizaci přiřazení

Při aktualizaci přiřazení je možné provést změny, které se při spuštění přeruší. Příkladem je změna umístění skupiny prostředků poté, co již byla nasazena. Všechny změny, které jsou podporovány nástrojem [Správce prostředků](../../../azure-resource-manager/management/overview.md) , mohou být provedeny, ale všechny změny, které by způsobily chybu prostřednictvím Správce prostředků, budou také mít za následek selhání přiřazení.

Neexistuje žádné omezení počtu, kolikrát může být přiřazení aktualizováno. Pokud dojde k chybě, určete chybu a proveďte další aktualizaci přiřazení.  Příklady scénářů chyb:

- Chybný parametr
- Již existující objekt
- Změna není podporována nástrojem Správce prostředků

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)