---
title: Pochopení životního cyklu Azure podrobného plánu
description: Další informace o životním cyklu, který prochází podrobný plán a podrobnosti o každé fázi.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991535"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Pochopení životního cyklu Azure podrobného plánu

Stejně jako mnoho prostředků v rámci Azure má podrobný plán v Azure plány typický a přirozené životního cyklu. Jste vytvořili, nasazení a nakonec odstraněn, když už nebude potřebné nebo relevantní.
Plány podporuje tradiční operace CRUD (vytváření, čtení, aktualizace nebo odstranění) životního cyklu, ale také staví na aby poskytovaly vyšší úroveň stavu, které podporují společný průběžné integrace nebo průběžného nasazování (CI/CD) kanálů pro použití ti Správa svou infrastrukturu pomocí kódu – klíčovým elementem v DevOps nazývá infrastruktura jako kód (IaC).

Abyste úplně pochopili podrobný plán a fáze, si probereme standardní životní cyklus:

> [!div class="checklist"]
> - Vytváření a úpravy podrobný plán
> - Publikovat podrobný plán
> - Vytváření a úpravy na novou verzi podrobný plán
> - Publikování na novou verzi podrobný plán
> - Odstraňuje se na konkrétní verzi nástroje podrobný plán
> - Odstraňuje se podrobný plán

## <a name="creating-and-editing-a-blueprint"></a>Vytváření a úpravy podrobný plán

Při vytváření podrobný plán, přidejte artefakty, uložit do skupiny pro správu a zadat jedinečný název a jedinečné verze. V tomto okamžiku se podrobný plán **koncept** režimu a ještě nemůžete přiřadit. Nicméně během činnosti v **koncept** režimu můžete dál aktualizovat a změnit.

Podrobný plán v **koncept** režim, který se nikdy nepublikoval bude zobrazeno na jinou ikonu **definice podrobného plánu** stránky než ty, které byly **publikováno**. **Nejnovější verzi** se zobrazí také jako **koncept** tyto nikdy publikovaného podrobné plány.

Vytvořte a upravte plán, podle kterého se [webu Azure portal](../create-blueprint-portal.md#create-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publikovat podrobný plán

Jakmile se všechny požadované změny byly provedeny na podrobný plán v **koncept** režimu, může být **publikováno** a k dispozici pro přiřazení. **Publikováno** verzi podrobný plán se nedá změnit.
Jednou **publikováno**, podrobný plán se zobrazí s ikonou jiný než **koncept** podrobné plány a zobrazí číslo verze zadaná v **nejnovější verzi** sloupce.

Publikovat podrobný plán se [webu Azure portal](../create-blueprint-portal.md#publish-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Vytváření a úpravy na novou verzi podrobný plán

I když **publikováno** verzi podrobný plán se nedá změnit, nová verze podrobný plán lze přidávat do existující plán a podle potřeby upravit. Je to tím, že změny na stávající podrobného plánu. Pokud už je podrobný plán **publikováno**, po uložení se tyto změny, zobrazí se jako **nepublikované změny** v seznamu definice podrobného plánu. Ukládají se změny uloží **koncept** verzi podrobný plán.

Upravit plán, podle kterého se [webu Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publikování na novou verzi podrobný plán

Stejně jako první verzi plán, podle kterého se **publikováno** k přiřazení, musí být všechny následné verze tento stejný plán, podle kterého **publikováno** předtím, než je možné přiřadit. Při **nepublikované změny** byly provedeny podrobný plán, ale dosud nebyly **publikováno**, **publikovat podrobný plán** tlačítko je k dispozici na stránce Upravit podrobného plánu. Pokud tlačítko nevidíte, je podrobný plán už **publikováno**, ale nemá žádné **nepublikované změny**.

> [!NOTE]
> Jeden plán může mít více **publikováno** verze, které může každý dají přiřadit k předplatným.

Postup, jak publikovat podrobný plán s **nepublikované změny** jako novou verzi existujícího podrobného plánu jsou stejné jako postup, jak publikovat nové podrobného plánu.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Odstraňuje se na konkrétní verzi nástroje podrobný plán

Každá verze plán, podle kterého je jedinečný objekt a může být zvlášť **publikováno**. Zároveň to znamená, že každou verzi podrobný plán odstranit. Odstraňuje se verze podrobný plán nemá žádný vliv na jiné verze této matrice.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verze, kterou chcete odebrat.

1. Spusťte službu Azure plány na webu Azure Portal kliknutím na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na **zásady** stránky, klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně a použití možnosti filtru k vyhledání plán, podle kterého chcete odstranit verzi. Klikněte na něj pro otevření stránky pro úpravu.

1. Klikněte na tlačítko **publikované verze** kartu a vyhledejte verzi, kterou chcete odstranit.

1. Klikněte pravým tlačítkem na verzi odstranit a vyberte **odstranit tuto verzi**.

## <a name="deleting-the-blueprint"></a>Odstraňuje se podrobný plán

Základní podrobného plánu se taky dají odstranit. Odstranění core podrobného plánu také odstraní všechny verze podrobného plánu z této matrice, bez ohledu na to **koncept** nebo **publikováno** stav. Jako v odstranění verzi podrobný plán, odstraňuje se plán core nedojde k odebrání existující přiřazení všech verzí podrobného plánu.

> [!NOTE]
> Není možné odstranit podrobný plán, který má aktivní přiřazení. Nejprve odstraňte přiřazení a pak odstraňte verze, kterou chcete odebrat.

Odstranit podrobný plán se [webu Azure portal](../create-blueprint-portal.md#delete-a-blueprint) nebo [rozhraní REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Přiřazení

Existuje několik bodů během životního cyklu, který plán, podle kterého je možné přiřadit k předplatnému.
Vždy, když je režim verzi podrobný plán **publikováno**, pak tuto verzi je možné přiřadit k předplatnému. I když dojde **koncept** verzi podrobného plánu, pokud jsou v jedné nebo více verzí podrobného plánu **publikováno** režimu a pak každou z těchto **publikováno** verze je k dispozici pro přiřazení. To umožňuje verze podrobný plán používat a aktivně přiřazen, zatímco je vyvíjena novější verze.

Jelikož se přidělují verzích plány, je důležité pochopit, kde jsou přiřazení a jaké parametry se přiřadil s. Parametry může být buď statická nebo dynamická. Další informace najdete v tématu [statické a dynamické parametry](parameters.md).

### <a name="updating-assignments"></a>Aktualizují se přiřazení

Pokud je podrobný plán přiřazen, je možné aktualizovat přiřazení. Existuje několik důvodů, proč aktualizace existujícího přiřazení, včetně:

- Přidání nebo odebrání [uzamčení prostředků](resource-locking.md)
- Změňte hodnotu vlastnosti [dynamické parametry](parameters.md#dynamic-parameters)
- Upgrade na novější přiřazení **publikováno** verzi podrobný plán

Další informace o postupu [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Další postup

- Vysvětlení použití [statické a dynamické parametry](parameters.md)
- Zjistěte, jak přizpůsobit [podrobný plán pořadí řazení](sequencing-order.md)
- Zjistěte, jak se využívání [podrobný plán uzamčení prostředků](resource-locking.md)
- Zjistěte, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md)
- Řešení problémů při přiřazení podrobného plánu se [obecný postup řešení potíží](../troubleshoot/general.md)