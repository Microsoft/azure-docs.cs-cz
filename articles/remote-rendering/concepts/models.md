---
title: Modely
description: Popisuje, co je model ve vzdáleném vykreslování Azure.
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593870"
---
# <a name="models"></a>Modely

*Model* ve vzdáleném vykreslování Azure odkazuje na úplné reprezentace objektů tvořené [entitami](entities.md) a [komponentami](components.md). Modely představují hlavní způsob, jak získat vlastní data do služby vzdáleného vykreslování.

## <a name="model-structure"></a>Struktura modelu

Model má právě jednu [entitu](entities.md) jako svůj kořenový uzel. Pod tím, že může mít libovolnou hierarchii podřízených entit. Při načítání modelu je vrácen odkaz na tuto kořenovou entitu.

Každá entita může mít připojené [součásti](components.md) . V nejběžnějších případech mají entity *MeshComponents*, které odkazují na [prostředky sítě](meshes.md).

## <a name="creating-models"></a>Vytváření modelů

Vytváření modelů pro modul runtime je dosaženo [převodem vstupních modelů](../how-tos/conversion/model-conversion.md) z formátů souborů, jako jsou FBX a GLTF. Proces převodu extrahuje všechny prostředky, jako jsou textury, materiály a sítě, a převede je na optimalizované formáty modulu runtime. Také extrahuje strukturální informace a převede je na strukturu grafu entit/komponent v ARR.

> [!IMPORTANT]
> [Převod modelu](../how-tos/conversion/model-conversion.md) je jediným způsobem, jak vytvořit [sítě](meshes.md). I když lze sdílet sítě mezi entitami za běhu, neexistuje žádný jiný způsob, jak získat síť do modulu runtime, kromě načítání modelu.

## <a name="loading-models"></a>Načítání modelů

Po převodu modelu je možné ho načíst z Azure Blob Storage do modulu runtime.

Existují dvě odlišné funkce načítání, které se liší podle způsobu, jakým se prostředek řeší v úložišti objektů BLOB:

* Model je možné adresovat přímo pomocí parametrů služby Blob Storage, pokud [je úložiště objektů BLOB propojené s účtem](../how-tos/create-an-account.md#link-storage-accounts). Relevantní funkce načítání v tomto případě je `LoadModelAsync` s parametrem `LoadModelOptions` .
* Model může být adresován identifikátorem URI SAS. Relevantní funkce načítání je `LoadModelFromSasAsync` s parametrem `LoadModelFromSasOptions` . Tuto variantu použijte také při načítání [předdefinovaných modelů](../samples/sample-model.md).

Následující fragmenty kódu ukazují, jak načíst modely pomocí kterékoli funkce. Chcete-li načíst model pomocí jeho parametrů úložiště objektů blob, použijte kód podobný následujícímu:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Pokud chcete model načíst pomocí tokenu SAS, použijte kód podobný následujícímu fragmentu kódu:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Následně můžete procházet hierarchii entit a upravit entity a komponenty. Načtení stejného modelu několikrát vytvoří více instancí, z nichž každá má vlastní kopii struktury entity nebo komponenty. Vzhledem k tomu, že sítě, materiály a textury jsou [sdílené prostředky](../concepts/lifetime.md), jejich data nebudou znovu načtena, ale. Proto se vytváření instancí modelu více než jednou zaznamená poměrně malým nároky na paměť.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Sítě](meshes.md)