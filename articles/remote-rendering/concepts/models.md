---
title: Modely
description: Popisuje, co je model ve vzdáleném vykreslování Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681724"
---
# <a name="models"></a>Modely

*Model* ve vzdáleném vykreslování Azure odkazuje na reprezentaci celého objektu, která se skládá z [entit](entities.md) a [součástí](components.md). Modely jsou hlavní způsob, jak získat vlastní data do služby vzdáleného vykreslování.

## <a name="model-structure"></a>Struktura modelu

Model má přesně [jednu entitu](entities.md) jako jeho kořenový uzel. Níže, že může mít libovolnou hierarchii podřízených entit. Při načítání modelu je vrácen odkaz na tuto kořenovou entitu.

Každá entita může mít připojené [součásti.](components.md) V nejběžnějším případě mají entity *Součásti meshcomponents*, které odkazují na [prostředky sítě](meshes.md).

## <a name="creating-models"></a>Vytváření modelů

Vytváření modelů pro modul runtime je dosaženo [převodem vstupních modelů](../how-tos/conversion/model-conversion.md) z formátů souborů, jako jsou FBX a GLTF. Proces převodu extrahuje všechny prostředky, jako jsou textury, materiály a ok, a převede je na optimalizované formáty runtime. Rovněž extrahuje strukturální informace a převede je na strukturu grafu entity/součásti ARR.

> [!IMPORTANT]
>
> [Převod modelu](../how-tos/conversion/model-conversion.md) je jediný způsob, jak vytvořit [mše](meshes.md). Přestože sítě mohou být sdíleny mezi entitami za běhu, neexistuje žádný jiný způsob, jak získat síť do běhu, než načítání modelu.

## <a name="loading-models"></a>Nakládací modely

Po převodu modelu, může být načten z úložiště objektů blob Azure do runtime.

Existují dvě odlišné načítací funkce, které se liší způsobem, jakým je prostředek adresován ve úložišti objektů blob:

* Model lze řešit jeho Identifikátor URI SAS. Příslušná načítací funkce je `LoadModelFromSASAsync` s parametrem `LoadModelFromSASParams`. Tuto variantu použijte i při načítání [vestavěných modelů](../samples/sample-model.md).
* Model lze řešit parametry úložiště objektů blob přímo, v případě, že [úložiště objektů blob je propojeno s účtem](../how-tos/create-an-account.md#link-storage-accounts). Příslušná načítací funkce je `LoadModelAsync` v tomto případě s parametrem `LoadModelParams`.

Následující fragmenty kódu ukazují, jak načíst modely s některou z funkcí. Chcete-li načíst model pomocí identifikátoru URI SAS, použijte kód, jako je následující:

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

Pokud chcete načíst model přímo pomocí parametrů úložiště objektů blob, použijte kód podobný následujícímu fragmentu:

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

    // ... (identical to the SAS URI snippet above)
}
```

Poté můžete procházet hierarchií entit a upravovat entity a součásti. Načtení matného modelu vícekrát vytvoří více instancí, z nichž každá má vlastní kopii entity/struktury komponenty. Vzhledem k tomu, že ok, materiály a textury jsou [sdílené prostředky](../concepts/lifetime.md), jejich data nebudou načtena znovu, ačkoli. Proto vytváření konkretizace modelu více než jednou vznikne relativně málo paměti režie.

> [!CAUTION]
> Všechny *asynchronní* funkce v ARR vrátí objekty asynchronní operace. Je nutné uložit odkaz na tyto objekty, dokud nebude operace dokončena. V opačném případě c# uvolňování může odstranit operaci brzy a nikdy dokončit. V ukázkovém kódu výše použití *await* zaručuje, že místní proměnná "loadOp" obsahuje odkaz, dokud není dokončeno načítání modelu. Pokud byste však místo toho použili událost *Completed,* bylo by nutné uložit asynchronní operaci do členské proměnné.

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Ok](meshes.md)
