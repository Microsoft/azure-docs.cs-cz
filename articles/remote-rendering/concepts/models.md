---
title: Modely
description: Popisuje, co je model ve vzdáleném vykreslování Azure.
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ff69486ab24c999e40b0afc13c91d6f729c352a0
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206556"
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

* Model může být adresován identifikátorem URI SAS. Relevantní funkce načítání je `LoadModelFromSASAsync` s parametrem `LoadModelFromSASParams` . Tuto variantu použijte také při načítání [předdefinovaných modelů](../samples/sample-model.md).
* Model je možné adresovat přímo pomocí parametrů služby Blob Storage, pokud [je úložiště objektů BLOB propojené s účtem](../how-tos/create-an-account.md#link-storage-accounts). Relevantní funkce načítání v tomto případě je `LoadModelAsync` s parametrem `LoadModelParams` .

Následující fragmenty kódu ukazují, jak načíst modely pomocí kterékoli funkce. Chcete-li načíst model pomocí identifikátoru URI SAS, použijte kód podobný následujícímu:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Chcete-li načíst model přímo pomocí jeho parametrů úložiště objektů blob, použijte kód podobný následujícímu fragmentu kódu:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Následně můžete procházet hierarchii entit a upravit entity a komponenty. Načtení stejného modelu několikrát vytvoří více instancí, z nichž každá má vlastní kopii struktury entity nebo komponenty. Vzhledem k tomu, že sítě, materiály a textury jsou [sdílené prostředky](../concepts/lifetime.md), jejich data nebudou znovu načtena, ale. Proto se vytváření instancí modelu více než jednou zaznamená poměrně malým nároky na paměť.

> [!CAUTION]
> Všechny *asynchronní* funkce v ARR vrací objekty asynchronní operace. Odkaz na tyto objekty je nutné uložit, dokud není operace dokončena. V opačném případě může systém uvolňování paměti C# operaci nejdříve odstranit a nemůže být nikdy dokončen. V ukázkovém kódu nad rámec použití *operátoru await* zaručuje, že místní proměnná ' loadOp ' drží odkaz, dokud není dokončeno načítání modelu. Pokud jste však místo toho použili *dokončenou* událost, bude nutné uložit asynchronní operaci do členské proměnné.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# RemoteManager. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelasync)
* [C# RemoteManager. LoadModelFromSASAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelfromsasasync)
* [C++ RemoteManager:: LoadModelAsync ()](/cpp/api/remote-rendering/remotemanager#loadmodelasync)
* [C++ RemoteManager:: LoadModelFromSASAsync ()](/cpp/api/remote-rendering/remotemanager#loadmodelfromsasasync)

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Sítě](meshes.md)