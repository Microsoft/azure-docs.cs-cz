---
title: Přidání značek do digitálních vláken
titleSuffix: Azure Digital Twins
description: Viz jak implementovat značky u digitálních vláken.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9a1a55bdf21b74116450ca32f66d891f1aa206d3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045406"
---
# <a name="add-tags-to-digital-twins"></a>Přidání značek do digitálních vláken 

Můžete použít koncept značek k dalšímu identifikaci a kategorizaci digitálních vláken. Konkrétně uživatelé můžou chtít replikovat značky z existujících systémů, jako jsou [Haystack značky](https://project-haystack.org/doc/TagModel), v rámci svých instancí digitálních vláken Azure. 

Tento dokument popisuje vzory, které lze použít k implementaci značek pro digitální vlákna.

Značky jsou nejprve přidány jako vlastnosti v rámci [modelu](concepts-models.md) , který popisuje digitální vlákna. Tato vlastnost je pak nastavena na hodnotu vlákna, když je vytvořena na základě modelu. Potom lze značky použít v [dotazech](concepts-query-language.md) k identifikaci a filtrování vláken.

## <a name="marker-tags"></a>Značky značek 

**Značka značky** je jednoduchý řetězec, který se používá k označení nebo kategorizaci digitálního vlákna, například "Blue" nebo "Red". Tento řetězec je název značky a značky značek nemají žádnou smysluplnou hodnotu – značka je podstatná, a to pouze podle jejich přítomnosti (nebo absence). 

### <a name="add-marker-tags-to-model"></a>Přidání značek značek do modelu 

Značky značek jsou modelovány jako mapy [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z `string` do `boolean` . Logická hodnota `mapValue` se ignoruje, protože přítomnost značky je důležitá. 

Zde je výňatek z neznámého modelu, který implementuje značku značky jako vlastnost:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Přidat značky značky do digitálních vláken

Jakmile `tags` je vlastnost součástí modelu digitálního vlákna, můžete nastavit značku značky v poli digitálního vlákna nastavením hodnoty této vlastnosti. 

Tady je příklad, který naplní značku `tags` pro tři vlákna:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Dotaz se značkami značek

Po přidání značek do digitálních vláken lze značky použít k filtrování vláken v dotazech. 

Tady je dotaz, který získá všechny vlákna označené jako "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Můžete také kombinovat značky pro složitější dotazy. Tady je dotaz pro získání všech vláken, která jsou kulatá a ne červená: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Značky hodnot 

**Označení hodnoty** je pár klíč-hodnota, který se používá k poskytnutí hodnoty pro každou značku, například `"color": "blue"` nebo `"color": "red"` . Jakmile je vytvořena značka hodnoty, lze ji také použít jako značku značky ignorováním hodnoty značky. 

### <a name="add-value-tags-to-model"></a>Přidání značek hodnot do modelu 

Značky hodnot jsou modelovány jako mapy [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z `string` do `string` . `mapKey`A `mapValue` jsou významné. 

Zde je výňatek z neznámého modelu, který implementuje značku hodnoty jako vlastnost:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Přidání značek hodnot do digitálních vláken

Stejně jako u značek značek můžete nastavit značku hodnoty v poli digitálního vlákna nastavením hodnoty této `tags` vlastnosti z modelu. Chcete-li použít Tag hodnoty jako značku značky, lze nastavit `tagValue` pole na prázdnou řetězcovou hodnotu ( `""` ). 

Tady je příklad, který naplní hodnotu `tags` pro tři vlákna:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Všimněte si, že `red` a `purple` jsou používány jako značky značek v tomto příkladu.

### <a name="query-with-value-tags"></a>Dotaz s použitím značek hodnot

Stejně jako u značek značek můžete použít značky hodnot k filtrování vláken v dotazech. Značky hodnot a značky značek lze také použít společně.

Z výše uvedeného příkladu `red` se používá jako značka značky. Mějte na paměti, že se jedná o dotaz, který získá všechny vlákna označené jako "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Tady je dotaz pro získání všech entit, které jsou malé (značka hodnoty), a ne červeně: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o navrhování a správě digitálních dvojitých modelů:
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)

Přečtěte si další informace o dotazování na dvojitou graf:
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)