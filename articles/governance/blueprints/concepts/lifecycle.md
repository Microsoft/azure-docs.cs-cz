---
title: Pochopení životního cyklu podrobného plánu
description: Seznamte se s životním cyklem, na který odkazuje definice podrobného plánu, a podrobnosti o jednotlivých fázích, včetně aktualizace a odebírání přiřazení podrobného plánu.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918513"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Pochopení životního cyklu Azure Blueprint

Podobně jako u mnoha prostředků v Azure má plán v plánech Azure typický a přirozený životní cyklus. Vytváří, nasazují a konečně odstranily, pokud už nejsou potřeba nebo nejsou relevantní. Plány Azure podporují standardní operace životního cyklu. Pak je na nich vytvoří, aby poskytovala další úrovně stavu, které podporují běžné kanály průběžné integrace a průběžného nasazování pro organizace, které spravují svou infrastrukturu jako kód – klíčový prvek v DevOps.

Pokud chcete plně porozumět podrobným plánům a fázím, pokryjeme standardní životní cyklus:

> [!div class="checklist"]
> - Vytvoření a úprava podrobného plánu
> - Publikování podrobného plánu
> - Vytvoření a úprava nové verze podrobného plánu
> - Publikování nové verze podrobného plánu
> - Odstranění konkrétní verze podrobného plánu
> - Odstraňuje se podrobný plán

## <a name="creating-and-editing-a-blueprint"></a>Vytvoření a úprava podrobného plánu

Při vytváření podrobného plánu přidejte do něj artefakty, uložte ho do skupiny pro správu nebo předplatného a zadejte jedinečný název a jedinečnou verzi. Podrobný plán je nyní v režimu **konceptu** a nelze jej ještě přiřadit. V režimu **konceptu** se může i nadále aktualizovat a měnit.

Nepublikovaný podrobný plán v režimu **konceptu** zobrazuje na stránce **definice** podrobného plánu jinou ikonu než ty, které byly **publikovány**. **Nejnovější verze** se zobrazuje jako **koncept** pro tyto dosud publikované plány.

Vytvořte a upravte podrobný plán pomocí [Azure Portal](../create-blueprint-portal.md#create-a-blueprint) nebo [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikování podrobného plánu

Jakmile provedete všechny plánované změny podrobného plánu v režimu **konceptu** , je možné je **publikovat** a zpřístupnit pro přiřazení. **Publikovanou** verzi podrobného plánu nelze změnit. Po **publikování** se v podrobném plánu zobrazí s jinou ikonou než s **Koncepty** a ve sloupci **nejnovější verze** se zobrazí zadané číslo verze.

Publikovat podrobný plán pomocí [Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) nebo [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Vytvoření a úprava nové verze podrobného plánu

**Publikovanou** verzi podrobného plánu nelze změnit. Novou verzi podrobného plánu je však možné do stávajícího podrobného plánu přidat a upravit podle potřeby. Změny v existujícím podrobném plánu proveďte úpravou. Když se nové změny uloží, plán nyní obsahuje **nepublikované změny**. Tyto změny představují novou **Konceptovou** verzi podrobného plánu.

Upravte podrobný plán pomocí [Azure Portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikování nové verze podrobného plánu

Každá upravená verze podrobného plánu musí být **publikována** před tím, než bude možné ji přiřadit. Pokud byly provedené **nepublikované změny** v podrobném plánu, ale ne **publikovány**, je na stránce Upravit podrobný plán k dispozici tlačítko **publikovat plán** . Pokud tlačítko není viditelné, podrobný plán již byl **publikován** a neobsahuje žádné **nepublikované změny**.

> [!NOTE]
> Jeden podrobný plán může mít několik **publikovaných** verzí, které je možné přiřadit k předplatným.

Pokud chcete publikovat podrobný plán s **nepublikovanými změnami**, použijte stejný postup pro publikování nového podrobného plánu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Odstranění konkrétní verze podrobného plánu

Každá verze podrobného plánu je jedinečný objekt a lze jej **publikovat** jednotlivě. V takovém případě lze také odstranit každou verzi podrobného plánu. Odstranění verze podrobného plánu nemá žádný vliv na jiné verze tohoto podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verzi, kterou chcete odebrat.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na stránce vlevo vyberte **definice** podrobného plánu a pomocí možností filtru Najděte plán, ve kterém chcete odstranit verzi. Výběrem této stránky otevřete stránku pro úpravu.

1. Vyberte kartu **publikované verze** a vyhledejte verzi, kterou chcete odstranit.

1. Klikněte pravým tlačítkem na verzi, kterou chcete odstranit, a vyberte **Odstranit tuto verzi**.

## <a name="deleting-the-blueprint"></a>Odstraňuje se podrobný plán

Základní plán bude také možné odstranit. Odstraněním základního plánu se odstraní také všechny verze podrobného plánu tohoto podrobného plánu, včetně **konceptu** a **publikovaných** modrotisky. Stejně jako u odstranění verze podrobného plánu neodstraní základní plán obnovení stávající přiřazení žádné z verzí podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verzi, kterou chcete odebrat.

Odstraňte podrobný plán pomocí [Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) nebo [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Přiřazení

V životním cyklu je několik bodů, které je možné přiřadit k předplatnému. Po **publikování** režimu verze podrobného plánu lze tuto verzi přiřadit k předplatnému. Tento životní cyklus umožňuje používat verze podrobného plánu a aktivně je přiřazovat během vývoje novější verze.

Vzhledem k tomu, že se přiřadí verze plánů, je důležité pochopit, kde jsou přiřazené, a s parametry, se kterými byly přiřazeny. Parametry mohou být buď statické, nebo dynamické. Další informace najdete v tématu [statické a dynamické parametry](./parameters.md).

### <a name="updating-assignments"></a>Aktualizace přiřazení

Při přiřazení podrobného plánu se přiřazení dá aktualizovat. Existuje několik důvodů Aktualizace existujícího přiřazení, včetně:

- Přidat nebo odebrat [uzamykání prostředků](./resource-locking.md)
- Změna hodnoty [dynamických parametrů](./parameters.md#dynamic-parameters)
- Upgradujte přiřazení na novější **publikovanou** verzi podrobného plánu.

Informace o postupu najdete v tématu [aktualizace existujících přiřazení](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Zrušení přiřazení přiřazení

Pokud již podrobný plán není potřebný, může být nepřiřazený ze skupiny pro správu nebo předplatného. Při zrušení přiřazení podrobného plánu dojde k následujícímu:

- Odebrání [uzamykání prostředků](./resource-locking.md) podrobného plánu
- Odstranění objektu přiřazení podrobného plánu
- Podmíněného Pokud se použila **spravovaná identita přiřazená systémem** , odstraní se taky.

> [!NOTE]
> Všechny prostředky nasazené přiřazením podrobného plánu zůstávají v platnosti, ale již nejsou chráněny plány Azure.

## <a name="next-steps"></a>Další kroky

- Principy použití [statických a dynamických parametrů](./parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](./resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)