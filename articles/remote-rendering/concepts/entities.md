---
title: Entity
description: Definice entit v oboru rozhraní API vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7981a28db23ab8c0aed05013dd260ffd97a11c07
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758720"
---
# <a name="entities"></a>Entity

*Entita* představuje Pohyblivý objekt v prostoru a je základním stavebním blokem vzdáleně vykresleného obsahu.

## <a name="entity-properties"></a>Vlastnosti entit

Entity mají transformaci definovanou polohou, otočením a škálováním. Vlastními entitami nemají žádné pozorovatelské funkce. Místo toho je chování přidáno prostřednictvím komponent, které jsou připojeny k entitám. Například připojením [CutPlaneComponent](../overview/features/cut-planes.md) se vytvoří vyjmutá rovina na pozici entity.

Nejdůležitější aspekt samotné entity je hierarchie a výsledná hierarchická transformace. Například pokud je více entit propojeno jako podřízené pro sdílenou nadřazenou entitu, všechny tyto entity lze přesunout, otočit a škálovat v úlohách změnou transformace nadřazené entity.

Entita je jedinečně vlastněna její nadřazenou položkou, což znamená, že pokud je Nadřazená aktivita zničena s `Entity.Destroy()` , tak jsou její podřízené a všechny připojené [součásti](components.md). Proto je odebrání modelu z scény provedeno voláním `Destroy` na kořenový uzel modelu, vrácený `AzureSession.Actions.LoadModelAsync()` nebo jeho variantou SAS `AzureSession.Actions.LoadModelFromSASAsync()` .

Entity se vytvoří, když server načte obsah nebo když chce uživatel přidat objekt do scény. Pokud chce například uživatel přidat vyjmutou plochu k vizualizaci vnitřku sítě, může uživatel vytvořit entitu, kde by měla existovat plocha, a pak do ní přidat komponentu vyjmuté plochy.

## <a name="query-functions"></a>Funkce dotazů

Existují dva typy funkcí dotazu pro entity: synchronní a asynchronní volání. Synchronní dotazy lze použít pouze pro data, která jsou k dispozici v klientovi a která nezahrnují množství výpočtů. Příklady jsou dotazování na součásti, relativní transformace objektů nebo vztahy nadřazenosti a podřízenosti. Asynchronní dotazy jsou používány pro data, která se nachází pouze na serveru, nebo zahrnují další výpočet, který by byl příliš nákladný ke spuštění na klientovi. Příklady jsou prostorové datové meze dotazy nebo dotazy na metadata.

### <a name="querying-components"></a>Dotazování na součásti

Chcete-li najít komponentu určitého typu, použijte `FindComponentOfType` :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = *entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Dotazování transformací

Transformační dotazy jsou synchronní volání objektu. Je důležité si uvědomit, že transformace, které se dotazují prostřednictvím rozhraní API, jsou transformace místních prostorů vzhledem k nadřazenému objektu. Výjimky jsou kořenové objekty, pro které je místní prostor a světový prostor stejný.

> [!NOTE]
> Neexistuje žádné vyhrazené rozhraní API pro dotazování na transformaci celého objektu na světové místo.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = *entity->Position();
Quaternion rotation = *entity->Rotation();
```


### <a name="querying-spatial-bounds"></a>Dotazování na prostorové meze

Vázané dotazy jsou asynchronní volání, která fungují v celé hierarchii objektů a používají jednu entitu jako kořen. Viz vyhrazená kapitola o [objektových vazbách](object-bounds.md).

### <a name="querying-metadata"></a>Dotazování na metadata

Metadata jsou další data uložená v objektech, která server ignorují. Metadata objektu jsou v podstatě sada dvojic (název, hodnota), kde _hodnota_ může být číselná, logická nebo řetězcová. Metadata lze exportovat pomocí modelu.

Dotazy na metadata jsou asynchronní volání konkrétní entity. Dotaz vrátí pouze metadata jedné entity, nikoli sloučené informace dílčího grafu.

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

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->IsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = *query->Result();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->AsInt64();

            // ...
        }
    });
```

Dotaz bude úspěšný i v případě, že objekt neobsahuje žádná metadata.

## <a name="next-steps"></a>Další kroky

* [Komponenty](components.md)
* [Hranice objektů](object-bounds.md)
