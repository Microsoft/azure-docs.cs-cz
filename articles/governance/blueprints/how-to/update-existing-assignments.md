---
title: Aktualizace existujícího přiřazení podrobného plánu Azure
description: Další informace o mechanismus pro aktualizace existujícího přiřazení v Azure podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2c9f660e54da50e32ce1d0dc43b0efeacd643c57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093781"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Jak aktualizovat existující přiřazení podrobného plánu

Pokud je podrobný plán přiřazen, je možné aktualizovat přiřazení. Existuje několik důvodů, proč aktualizace existujícího přiřazení, včetně:

- Přidání nebo odebrání [uzamčení prostředků](../concepts/resource-locking.md)
- Změňte hodnotu vlastnosti [dynamické parametry](../concepts/parameters.md#dynamic-parameters)
- Upgrade na novější přiřazení **publikováno** verzi podrobný plán

## <a name="updating-assignments"></a>Aktualizují se přiřazení

1. Klikněte na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na stránce **Zásady** klikněte na **plány**.

1. Vyberte **Přiřazené podrobné plány** ze stránky vlevo.

1. V seznamu plány kliknutím levého tlačítka myši přiřazení podrobného plánu. Klikněte **přiřazení aktualizací** tlačítko nebo klikněte pravým tlačítkem na přiřazení podrobného plánu a vyberte **přiřazení aktualizací**.

   ![Aktualizovat přiřazení](../media/update-existing-assignments/update-assignment.png)

1. **Přiřazení podrobného plánu** se načítání stránky předem vyplněné se všemi hodnotami z původního přiřazení. Můžete změnit **verze definice podrobného plánu**, **zámek přiřazení** stavu a všechny dynamické parametry, které existují v definici podrobného plánu. Klikněte na tlačítko **přiřadit** po dokončení změn.

1. Na stránce s podrobnostmi aktualizované přiřazení najdete v článku nový stav. V tomto příkladu jsme přidali **uzamykání** k přiřazení.

   ![Aktualizované přiřazení - uzamčen](../media/update-existing-assignments/updated-assignment.png)

1. Prozkoumejte podrobnosti o dalších **operací přiřazení** pomocí rozevíracího seznamu. Tabulka **spravovaných prostředků** aktualizace podle operace vybrané přiřazení.

   ![Operace přiřazení](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Pravidla pro aktualizují se přiřazení

Nasazení aktualizované přiřazení následuje pár důležitých pravidla. Tato pravidla určují, co se stane s již nasazených prostředků. Požadované změny a Typ artefaktu prostředku probíhá nasazení nebo aktualizovat určují akce, které pocházejí.

- Přiřazení rolí
  - Pokud se změní roli nebo roli assignee (uživatel, skupina nebo aplikace), se vytvoří nové přiřazení role. Přiřazení rolí již dříve nasadili zůstávají v místě.
- Přiřazení zásad
  - Pokud se změní parametry přiřazení zásady, se aktualizuje existující přiřazení.
  - Pokud je změnit definici přiřazení zásad, vytvoří se nové přiřazení zásady. Přiřazení zásad již dříve nasadili zůstávají v místě.
  - Pokud artefaktu přiřazení zásady je odebrán z podrobný plán, nasadit zásadu, kterou jsou zachovány přiřazení.
- Šablony Azure Resource Manageru
  - Zpracování prostřednictvím Resource Manageru jako šablony **UMÍSTIT**. Protože tato akce zpracovává každý typ prostředku jinak, najdete v dokumentaci jednotlivých zahrnutých prostředků a určit dopad této akce při spuštění pomocí podrobné plány.

## <a name="possible-errors-on-updating-assignments"></a>Možné chyby na aktualizaci přiřazení

Při aktualizaci přiřazení, je možné provádět změny poškozující při spuštění. Příkladem je změna umístění skupiny prostředků, poté, co již byla nasazena. Všechny změny, které jsou podporovány v [Azure Resource Manageru](../../../azure-resource-manager/resource-group-overview.md) může být ale jakákoliv změna, která vedou k chybě prostřednictvím Azure Resource Manageru se také způsobí selhání přiřazení.

Neexistuje žádné omezení na počet opakování, což je přiřazení je možné aktualizovat. Pokud dojde k chybě, zjistit chyby a jiné aktualizace k přiřazení.  Ukázkové scénáře Chyba:

- Chybný parametr
- Již existující objekt
- Změna není podporována službou Azure Resource Manageru

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Zjistěte, jak používat [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)
