---
title: Textury
description: Pracovní postup prostředků textury
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594413"
---
# <a name="textures"></a>Textury

Textury jsou neměnné [sdílené prostředky](../concepts/lifetime.md). Textury lze načíst ze služby [BLOB Storage](../how-tos/conversion/blob-storage.md) a použít je přímo na modely, jak je znázorněno v [kurzu: Změna prostředí a materiálů](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Nejčastěji se ale textury budou součástí [převedeného modelu](../how-tos/conversion/model-conversion.md), na který odkazují jeho [materiály](materials.md).

## <a name="texture-types"></a>Typy textury

Různé typy textur mají různé případy použití:

* **2D textury** se používají hlavně v [materiálech](materials.md).
* **Cubemaps** se dá použít pro [Nebeský](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Podporované formáty textury

Všechny textury, které má tato šipka mít, musí být ve [formátu dds](https://en.wikipedia.org/wiki/DirectDraw_Surface). Nejlépe s mipmapy a kompresí textur.

## <a name="loading-textures"></a>Načítání textur

Při načítání textury je nutné zadat její očekávaný typ. Pokud se neshoduje typ, načítání textury se nezdařilo.
Načtení textury se stejným identifikátorem URI dvakrát vrátí stejný objekt textury, protože se jedná o [sdílený prostředek](../concepts/lifetime.md).

Podobně jako při načítání modelů existují dvě varianty prostředku textury ve zdrojovém úložišti objektů BLOB:

* Texturu je možné adresovat přímo pomocí parametrů služby Blob Storage v případě, že [je úložiště objektů BLOB propojené s účtem](../how-tos/create-an-account.md#link-storage-accounts). Relevantní funkce načítání v tomto případě je `LoadTextureAsync` s parametrem `LoadTextureOptions` .
* Prostředek textury lze adresovat pomocí identifikátoru URI SAS. Relevantní funkce načítání je `LoadTextureFromSasAsync` s parametrem `LoadTextureFromSasOptions` . Tuto variantu použijte také při načítání [vestavěných textur](../overview/features/sky.md#built-in-environment-maps).

Následující vzorový kód ukazuje, jak načíst texturu:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Všimněte si, že pokud použijete variantu SAS, liší se pouze načtená funkce nebo parametr.

V závislosti na tom, jaká textura má být použita pro, může dojít k omezením pro typ a obsah textury. Například mapa hrubosti materiálu v rámci [PBR](../overview/features/pbr-materials.md) musí být ve stupních šedi.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída textury C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Textura C++ – třída](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Další kroky

* [Materiály](materials.md)
* [Cestovní](../overview/features/sky.md)