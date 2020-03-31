---
title: Pochopení životního cyklu podrobného plánu
description: Přečtěte si o životním cyklu, kterým prochází definice podrobného plánu, a podrobnosti o jednotlivých fázích, včetně aktualizace a odebrání přiřazení podrobného plánu.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd5cb7d085744377cf12998f14c994fb1dcd2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74404581"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Pochopení životního cyklu Azure Blueprintu

Stejně jako mnoho prostředků v rámci Azure, podrobný plán v Azure Blueprints má typický a přirozený životní cyklus. Jsou vytvořeny, nasazeny a nakonec odstraněny, když již nejsou potřebné nebo relevantní.
Podrobné plány podporují standardní operace životního cyklu. Pak na nich staví na tom, aby poskytovaldalší úrovně stavu, které podporují společnou průběžnou integraci a kanály průběžného nasazení pro organizace, které spravují svou infrastrukturu jako kód – což je klíčový prvek v devops.

Abychom plně porozuměli plánu a fázím, pokryjeme standardní životní cyklus:

> [!div class="checklist"]
> - Vytvoření a úprava podrobného plánu
> - Publikování podrobného plánu
> - Vytvoření a úprava nové verze podrobného plánu
> - Publikování nové verze podrobného plánu
> - Odstranění konkrétní verze podrobného plánu
> - Odstranění podrobného plánu

## <a name="creating-and-editing-a-blueprint"></a>Vytvoření a úprava podrobného plánu

Při vytváření podrobného plánu do něj přidejte artefakty, uložte do skupiny pro správu nebo předplatného a zadejte jedinečný název a jedinečnou verzi. Podrobný plán je nyní v režimu **konceptu** a ještě nelze přiřadit. V režimu **Koncept** může být nadále aktualizován a měněn.

Nikdy publikovaný podrobný plán v režimu **Koncept** zobrazí jinou ikonu na stránce **Definice podrobného plánu** než ty, které byly **publikovány**. **Nejnovější verze** je zobrazena jako **Koncept** pro tyto nikdy nepublikované plány.

Vytvořte a upravte podrobný plán pomocí [portálu Azure nebo](../create-blueprint-portal.md#create-a-blueprint) [rozhraní REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikování podrobného plánu

Jakmile byly provedeny všechny plánované změny podrobného plánu v režimu **konceptu,** může být **publikován a** zpřístupněn pro přiřazení. **Publikovanou** verzi podrobného plánu nelze změnit. Po **publikování**se podrobný plán zobrazí s jinou ikonou než **Podrobné** plány a zobrazí číslo zadaná verze ve sloupci **Nejnovější verze.**

Publikujte podrobný plán pomocí [portálu Azure nebo](../create-blueprint-portal.md#publish-a-blueprint) [rozhraní REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Vytvoření a úprava nové verze podrobného plánu

**Publikovanou** verzi podrobného plánu nelze změnit. Nová verze podrobného plánu však mohou být přidány do existující hospodařících plánů a upravit podle potřeby. Proveďte změny existujícího podrobného plánu jeho úpravou. Po uložení nových změn má podrobný plán nyní **nepublikované změny**. Tyto změny jsou nové **verze konceptu** podrobného plánu.

Upravte podrobný plán pomocí [portálu Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikování nové verze podrobného plánu

Každá upravená verze podrobného plánu musí být **publikována před** jeho přiřazením. Pokud byly provedeny **nepublikované změny** podrobného plánu, ale nebyly **publikovány**, tlačítko **Publikovat podrobný plán** je k dispozici na stránce podrobného plánu úprav. Pokud tlačítko není viditelné, podrobný plán již byl **publikován** a nemá žádné **nepublikované změny**.

> [!NOTE]
> Jeden podrobný plán může mít více **publikovaných** verzí, které lze přiřadit k předplatným.

Chcete-li publikovat podrobný plán s **nepublikované změny**, použijte stejný postup pro publikování nového podrobného plánu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Odstranění konkrétní verze podrobného plánu

Každá verze podrobného plánu je jedinečný objekt a může být jednotlivě **Publikováno**. Jako takové lze odstranit také každou verzi podrobného plánu. Odstranění verze podrobného plánu nemá žádný vliv na jiné verze tohoto podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a potom odstraňte verzi, kterou chcete odebrat.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **definice podrobného plánu** a pomocí možností filtru vyhledejte podrobný plán, jehož chcete odstranit. Vyberte ji, chcete-li otevřít stránku pro úpravy.

1. Vyberte kartu **Publikované verze** a vyhledejte verzi, kterou chcete odstranit.

1. Chcete-li odstranit, klepněte pravým tlačítkem myši na verzi a vyberte **příkaz Odstranit tuto verzi**.

## <a name="deleting-the-blueprint"></a>Odstranění podrobného plánu

Základní podrobný plán lze také odstranit. Odstraněnízákladní podrobný plán také odstraní všechny verze podrobného plánu tohoto podrobného plánu, včetně **konceptu** a **publikovaných** podrobných plánů. Stejně jako při odstranění verze podrobného plánu odstranění základní ho podrobný plán neodebere existující přiřazení některé z verzí podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a potom odstraňte verzi, kterou chcete odebrat.

Odstraňte podrobný plán pomocí [portálu Azure nebo](../create-blueprint-portal.md#delete-a-blueprint) [rozhraní REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Přiřazení

Během životního cyklu je několik bodů, které lze přiřadit k předplatnému. Pokud je publikován režim verze podrobného **plánu**, pak tato verze může být přiřazena k odběru. Tento životní cyklus umožňuje verze podrobného plánu, které mají být použity a aktivně přiřazeny při vývoji novější verze.

Při přiřazují se verze podrobných plánů, je důležité pochopit, kde jsou přiřazeny a s jakými parametry byly přiřazeny. Parametry mohou být statické nebo dynamické. Další informace naleznete v [tématu statické a dynamické parametry](parameters.md).

### <a name="updating-assignments"></a>Aktualizace přiřazení

Při přiřazení podrobného plánu lze přiřazení aktualizovat. Aktualizace existujícího přiřazení má několik důvodů, například:

- Přidání nebo odebrání [uzamčení prostředků](resource-locking.md)
- Změna hodnoty [dynamických parametrů](parameters.md#dynamic-parameters)
- Upgrade přiřazení na novější **publikovanou** verzi podrobného plánu

Informace o tom, jak, naleznete [v tématu aktualizace existujících přiřazení](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Nepodepisování přiřazení

Pokud plán plánu již není potřeba, může být unassigned ze skupiny pro správu nebo předplatného. Během zrušení podrobného plánu dochází k následujícím:

- Odebrání [uzamčení prostředků podrobného plánu](resource-locking.md)
- Odstranění objektu přiřazení podrobného plánu
- (Podmíněné) Pokud byla **použita spravovaná identita přiřazená systémem,** je také odstraněna.

> [!NOTE]
> Všechny prostředky nasazené přiřazení podrobného plánu zůstávají na místě, ale už nejsou chráněné azure blueprints.

## <a name="next-steps"></a>Další kroky

- Pochopit, jak používat [statické a dynamické parametry](parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).