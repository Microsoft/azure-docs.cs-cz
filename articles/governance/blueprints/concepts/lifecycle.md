---
title: Pochopení životního cyklu Azure podrobného plánu
description: Další informace o životním cyklu, který prochází podrobný plán a podrobnosti o každé fázi.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 882279019a5f321c6af9beab1f4d0f220781bc5c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094155"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Pochopení životního cyklu Azure podrobného plánu

Stejně jako mnoho prostředků v rámci Azure má podrobný plán v Azure plány typický a přirozené životního cyklu. Jste vytvořili, nasazení a nakonec odstraněn, když už nebude potřebné nebo relevantní.
Plány podporuje operace standardní životního cyklu. Potom staví na nich další úrovně stavu, které podporují běžné průběžnou integraci a průběžné nasazování pro organizace, které spravují infrastrukturu jako kód – klíčovým elementem v DevOps.

Abyste úplně pochopili podrobný plán a fáze, si probereme standardní životní cyklus:

> [!div class="checklist"]
> - Vytváření a úpravy podrobný plán
> - Publikovat podrobný plán
> - Vytváření a úpravy na novou verzi podrobný plán
> - Publikování na novou verzi podrobný plán
> - Odstraňuje se na konkrétní verzi nástroje podrobný plán
> - Odstraňuje se podrobný plán

## <a name="creating-and-editing-a-blueprint"></a>Vytváření a úpravy podrobný plán

Při vytváření podrobný plán, přidejte artefakty, uložit do skupiny pro správu a zadat jedinečný název a jedinečné verze. Podrobný plán je nyní v **koncept** režimu a ještě nemůžete přiřadit.
Během činnosti v **koncept** režimu, můžete dál aktualizovat a změnit.

A nemá být nikdy publikováno podrobného plánu v **koncept** režim zobrazuje na jinou ikonu **definice podrobného plánu** stránky než těch, které byly **publikováno**. **Nejnovější verzi** se také zobrazí jako **koncept** tyto nikdy publikovaného podrobné plány.

Vytvořte a upravte plán, podle kterého se [webu Azure portal](../create-blueprint-portal.md#create-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikovat podrobný plán

Jakmile se všechny plánované změny byly provedeny na podrobný plán v **koncept** režimu, může být **publikováno** a k dispozici pro přiřazení. **Publikováno** verzi podrobný plán se nedá změnit.
Jednou **publikováno**, podrobný plán se zobrazí s ikonou jiný než **koncept** podrobné plány a zobrazí číslo verze zadaná v **nejnovější verzi** sloupce.

Publikovat podrobný plán se [webu Azure portal](../create-blueprint-portal.md#publish-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Vytváření a úpravy na novou verzi podrobný plán

A **publikováno** verzi podrobný plán se nedá změnit. Novou verzi podrobný plán ale můžete přidat do existující plán a podle potřeby upravit. Změny existujících podrobného plánu pomocí úprav. Po uložení nové změny se podrobný plán má teď **nepublikované změny**. Tyto změny jsou novou **koncept** verzi podrobný plán.

Upravit plán, podle kterého se [webu Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikování na novou verzi podrobný plán

Musí být každý upravenou verzí podrobný plán **publikováno** předtím, než je možné přiřadit. Když **nepublikované změny** plán, podle kterého se provedly, ale ne **publikováno**, **publikovat podrobný plán** tlačítko je k dispozici na stránce Upravit podrobný plán. Pokud tlačítko nevidíte, je podrobný plán už **publikováno** a nemá žádné **nepublikované změny**.

> [!NOTE]
> Jeden plán může mít více **publikováno** verze, které může každý dají přiřadit k předplatným.

Chcete-li publikovat podrobný plán s **nepublikované změny**, použijte stejný postup k publikování nové podrobného plánu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Odstraňuje se na konkrétní verzi nástroje podrobný plán

Každá verze plán, podle kterého je jedinečný objekt a může být zvlášť **publikováno**. V důsledku toho každá verze plán, podle kterého se taky dají odstranit. Odstraňuje se verze podrobný plán nemá žádný vliv na jiné verze této matrice.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verze, kterou chcete odebrat.

1. Klikněte na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na stránce **Zásady** klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně a použití možnosti filtru k vyhledání plán, podle kterého chcete odstranit verzi. Klikněte na něj pro otevření stránky pro úpravu.

1. Klikněte na tlačítko **publikované verze** kartu a vyhledejte verzi, kterou chcete odstranit.

1. Klikněte pravým tlačítkem na verzi odstranit a vyberte **odstranit tuto verzi**.

## <a name="deleting-the-blueprint"></a>Odstraňuje se podrobný plán

Základní podrobného plánu se taky dají odstranit. Odstraněním core podrobného plánu se odstraní také všechny verze této matrice, včetně podrobného plánu **koncept** a **publikováno** podrobné plány. Jako v odstranění verzi podrobný plán, odstraňuje se plán core nedojde k odebrání existující přiřazení všech verzí podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verze, kterou chcete odebrat.

Odstranit podrobný plán se [webu Azure portal](../create-blueprint-portal.md#delete-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Přiřazení

Existuje několik bodů během životního cyklu, které plán, podle kterého je možné přiřadit k předplatnému. Pokud je režim verzi podrobný plán **publikováno**, pak tuto verzi je možné přiřadit k předplatnému. Životní cyklus umožňuje verze podrobného plánu použít a aktivně přiřazen, zatímco je vyvíjena novější verze.

Jelikož se přidělují verzích plány, je důležité pochopit, kde jsou přiřazení a jaké parametry se přiřadil s. Parametry může být buď statická nebo dynamická. Další informace najdete v tématu [statické a dynamické parametry](parameters.md).

### <a name="updating-assignments"></a>Aktualizují se přiřazení

Pokud je podrobný plán přiřazen, je možné aktualizovat přiřazení. Existuje několik důvodů, proč aktualizace existujícího přiřazení, včetně:

- Přidání nebo odebrání [uzamčení prostředků](resource-locking.md)
- Změňte hodnotu vlastnosti [dynamické parametry](parameters.md#dynamic-parameters)
- Upgrade na novější přiřazení **publikováno** verzi podrobný plán

Další informace o postupu [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Další postup

- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Zjistěte, jak používat [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)