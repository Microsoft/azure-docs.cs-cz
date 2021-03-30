---
title: 'Vzor: použití značek v definici zásady'
description: Tento vzor Azure Policy poskytuje příklady přidávání parametrizovaných značek nebo dědění značek ze skupiny prostředků v definici zásady.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88545552"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy – vzor: značky

[Značky](../../..//azure-resource-manager/management/tag-resources.md) jsou důležitou součástí správy, uspořádání a řízení prostředků Azure. Azure Policy umožňuje konfigurovat značky na nových a stávajících prostředcích ve velkém měřítku pomocí úloh [změny](../concepts/effects.md#modify) efektu úprav a [nápravy](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Sample 1: parametrizovat značky

Tato definice zásady používá dva parametry, **TagName** a **tagValue** k nastavení toho, co přiřazení zásad hledá ve skupinách prostředků. Tento formát umožňuje použití definice zásad pro libovolný počet kombinací názvů značek a hodnot značek, ale udržuje jenom jednu definici zásady.

> [!NOTE]
> I když je tento vzor definice zásad podobný jako ve [vzoru: Parameters-Sample #1](./pattern-parameters.md#sample-1-string-parameters), Tato ukázka používá skupiny prostředků _All_ a Targeting **Mode** .

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Ukázka 1: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

V této ukázce je **režim** nastaven na _vše_ , protože cílí na skupinu prostředků. Ve většině případů by měl být při práci se značkami nastaven **režim** _Indexed_ . Další informace najdete v tématu [režimy](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

V této části definice zásady `concat` kombinuje parametr parametrů **TagName** a `tags['name']` **pole** formát to-řekněte, aby se vyhodnotila Tato značka pro parametr **tagValue**.
Při použití **notEquals** se v případě, že **značky \[ TagName \]** neshodují s **tagValue**, se aktivuje efekt **Úpravy** .

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

V tomto případě je stejný formát pro použití hodnot parametrizovaných značek použit operací **addOrReplace** k vytvoření nebo aktualizaci značky na požadovanou hodnotu ve vyhodnocené skupině prostředků.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Ukázka 2: dědění hodnoty značky ze skupiny prostředků

Tato definice zásady používá parametr **TagName** k určení hodnoty této značky, která má být děděna z nadřazené skupiny prostředků.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Ukázka 2: vysvětlení

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

V této ukázce je **režim** nastaven na _indexované_ , protože necílí na skupinu prostředků nebo předplatné, i když Získá hodnotu ze skupiny prostředků. Další informace najdete v tématu [režimy](../concepts/definition-structure.md#resource-manager-modes).

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**PolicyRule. Pokud** používá `concat` jako [vzorový #1](#sample-1-parameterize-tags) k vyhodnocení hodnoty **TagName**, ale pomocí `resourceGroup()` funkce ji porovná s hodnotou stejné značky v nadřazené skupině prostředků. Druhá klauzule zde kontroluje, zda má značka ve skupině prostředků hodnotu, která není null.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Tady je hodnota přiřazená k značce **TagName** na prostředku také pomocí `resourceGroup()` funkce k získání hodnoty z nadřazené skupiny prostředků. Tímto způsobem můžete dědit značky z nadřazených skupin prostředků. Pokud jste prostředek již vytvořili, ale nepřidali jste značku, tato definice zásad a [úloha nápravy](../how-to/remediate-resources.md) může aktualizovat existující prostředky.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte další [vzory a předdefinované definice](./index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).