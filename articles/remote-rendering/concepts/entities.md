---
title: Entity
description: Definice entit v oboru rozhraní API vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c49c7bfaa7714dda902d05537fbe3d8a55d5abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613916"
---
# <a name="entities"></a>Entity

*Entita* představuje Pohyblivý objekt v prostoru a je základním stavebním blokem vzdáleně vykresleného obsahu.

## <a name="entity-properties"></a>Vlastnosti entit

Entity mají transformaci definovanou polohou, otočením a škálováním. Vlastními entitami nemají žádné pozorovatelské funkce. Místo toho je chování přidáno prostřednictvím komponent, které jsou připojeny k entitám. Například připojením [CutPlaneComponent](../overview/features/cut-planes.md)  se vytvoří vyjmutá rovina na pozici entity.

Nejdůležitější aspekt samotné entity je hierarchie a výsledná hierarchická transformace. Například pokud je více entit propojeno jako podřízené pro sdílenou nadřazenou entitu, všechny tyto entity lze přesunout, otočit a škálovat v úlohách změnou transformace nadřazené entity. `enabled`Stav entity lze také použít k vypnutí viditelnosti a reakcí na přetypování paprsků pro úplný dílčí graf v hierarchii.

Entita je jedinečně vlastněna její nadřazenou položkou, což znamená, že pokud je Nadřazená aktivita zničena s `Entity.Destroy()` , tak jsou její podřízené a všechny připojené [součásti](components.md). Proto je odebrání modelu z scény provedeno voláním `Destroy` na kořenový uzel modelu, vrácený `AzureSession.Actions.LoadModelAsync()` nebo jeho variantou SAS `AzureSession.Actions.LoadModelFromSASAsync()` .

Entity se vytvoří, když server načte obsah nebo když chce uživatel přidat objekt do scény. Pokud chce například uživatel přidat vyjmutou plochu k vizualizaci vnitřku sítě, může uživatel vytvořit entitu, kde by měla existovat plocha, a pak do ní přidat komponentu vyjmuté plochy.

## <a name="create-an-entity"></a>Vytvoření entity

Chcete-li přidat novou entitu do scény, například předat jako kořenový objekt pro načítání modelů nebo připojit k ní komponenty, použijte následující kód:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

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
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
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
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
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
        if (query->GetIsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = query->GetResult();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->GetAsInt64();

            // ...
        }
    });
```

Dotaz bude úspěšný i v případě, že objekt neobsahuje žádná metadata.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída entity C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RemoteManager. CreateEntity ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createentity)
* [Třída entity C++](https://docs.microsoft.com/cpp/api/remote-rendering/entity)
* [C++ RemoteManager:: CreateEntity ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createentity)

## <a name="next-steps"></a>Další kroky

* [Komponenty](components.md)
* [Hranice objektů](object-bounds.md)
