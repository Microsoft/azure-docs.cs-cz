---
title: Entity
description: Definice entit v oboru rozhraní Azure Remote Rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681945"
---
# <a name="entities"></a>Entity

*Entita* představuje pohyblivý objekt v prostoru a je základním stavebním kamenem vzdáleně vykresleného obsahu.

## <a name="entity-properties"></a>Vlastnosti entity

Entity mají transformaci definovanou pozicí, otočením a měřítkem. Samy o sobě entity nemají žádné pozorovatelné funkce. Místo toho chování je přidán prostřednictvím komponent, které jsou připojeny k entitám. Například připojení [komponenty CutPlaneComponent](../overview/features/cut-planes.md) vytvoří rovinu řezu na pozici entity.

Nejdůležitějším aspektem samotné entity je hierarchie a výsledná hierarchická transformace. Pokud je například ke sdílené nadřazené entitě připojeno více entit jako podřízených entit, všechny tyto entity lze přesunout, otočit a změnit jejich měřítko v souběhem změnou transformace nadřazené entity.

Entita je jednoznačně vlastněna nadřazenou položkou, což znamená, že když je nadřazená jednotka zničena pomocí `Entity.Destroy()`aplikace , jsou její podřízené položky a všechny připojené [součásti](components.md). Odebrání modelu ze scény se tedy `Destroy` provádí voláním na kořenovém `AzureSession.Actions.LoadModelAsync()` uzlu modelu, `AzureSession.Actions.LoadModelFromSASAsync()`vráceném nebo jeho variantou SAS .

Entity jsou vytvořeny při načtení obsahu serveru nebo když uživatel chce přidat objekt do scény. Pokud například uživatel chce přidat rovinu řezu, aby vizualizoval vnitřek sítě, může vytvořit entitu, kde by měla existovat rovina, a pak do ní přidat komponentu roviny řezu.

## <a name="query-functions"></a>Funkce dotazu

Existují dva typy funkcí dotazu na entity: synchronní a asynchronní volání. Synchronní dotazy lze použít pouze pro data, která je k dispozici na straně klienta a nezahrnuje mnoho výpočtů. Příklady jsou dotazování na součásti, relativní transformace objektů nebo vztahy nadřazený/podřízený. Asynchronní dotazy se používají pro data, která jsou uložena pouze na serveru nebo zahrnuje další výpočty, které by byly příliš nákladné pro spuštění na straně klienta. Příklady jsou dotazy prostorových hranic nebo metadatové dotazy.

### <a name="querying-components"></a>Dotazování na součásti

Chcete-li najít součást určitého `FindComponentOfType`typu, použijte :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Dotazování transformací

Transformační dotazy jsou synchronní volání na objekt. Je důležité si uvědomit, že transformace dotazované prostřednictvím rozhraní API jsou místní prostor transformace, vzhledem k nadřazenému objektu. Výjimky jsou kořenové objekty, pro které jsou identické místní a světový prostor.

> [!NOTE]
> Neexistuje žádné vyhrazené rozhraní API pro dotazování transformace světového prostoru libovolných objektů.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Dotazování prostorových hranic

Dotazy na hranice jsou asynchronní volání, která pracují v hierarchii celých objektů a používají jednu entitu jako kořenovou. Podívejte se na vyhrazenou kapitolu o [objektových mezích](object-bounds.md).

### <a name="querying-metadata"></a>Dotazování metadat

Metadata jsou další data uložená na objektech, která je serverem ignorována. Metadata objektu jsou v podstatě sadou párů (název, hodnota), kde _hodnota_ může být číselného, logického nebo typu řetězce. Metadata lze exportovat spolu s modelem.

Dotazy metadat jsou asynchronní volání na konkrétní entitu. Dotaz vrátí pouze metadata jedné entity, nikoli sloučené informace dílčího grafu.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

Dotaz bude úspěšný i v případě, že objekt neobsahuje žádná metadata.

## <a name="next-steps"></a>Další kroky

* [Komponenty](components.md)
* [Hranice objektů](object-bounds.md)
