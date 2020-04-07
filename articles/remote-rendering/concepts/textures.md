---
title: Textury
description: Pracovní postup prostředku textury
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681659"
---
# <a name="textures"></a>Textury

Textury jsou neměnný [sdílený prostředek](../concepts/lifetime.md). Textury lze načíst z [úložiště objektů blob](../how-tos/conversion/blob-storage.md) a použít přímo na modely, jak je znázorněno v [kurzu: Změna prostředí a materiálů](../tutorials/unity/changing-environment-and-materials.md). Nejčastěji však textury budou součástí [převedeného modelu](../how-tos/conversion/model-conversion.md), kde jsou odkazovány jeho [materiály](materials.md).

## <a name="texture-types"></a>Typy textur

Různé typy textur mají různé případy použití:

* **2D textury** se používají hlavně v [materiálech](materials.md).
* **Cubemaps** lze použít pro [oblohu](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Podporované formáty textur

Všechny textury dané ARR musí být ve [formátu DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Nejlépe s mipmapy a komprese textury. Pokud chcete automatizovat proces převodu, podívejte se na [nástroj příkazového řádku TexConv.](../resources/tools/tex-conv.md)

## <a name="loading-textures"></a>Načítání textur

Při načítání textury je nutné zadat její očekávaný typ. Pokud se text neshoduje, zatížení textury se nezdaří.
Načtení textury se stejným identifikátorem URI dvakrát vrátí stejný objekt textury, protože se jedná o [sdílený prostředek](../concepts/lifetime.md).

Podobně jako načítací modely existují dvě varianty adresování prostředku textury ve zdrojovém úložišti objektů blob:

* Datový zdroj textury lze adresovat pomocí identifikátoru URI SAS. Příslušná načítací funkce je `LoadTextureFromSASAsync` s parametrem `LoadTextureFromSASParams`. Tuto variantu použijte také při načítání [vestavěných textur](../overview/features/sky.md#built-in-environment-maps).
* Texturu lze adresovat parametry úložiště objektů blob přímo v případě, že [úložiště objektů blob je propojené s účtem](../how-tos/create-an-account.md#link-storage-accounts). Příslušná načítací funkce je `LoadTextureAsync` v tomto případě s parametrem `LoadTextureParams`.

Následující ukázkový kód ukazuje, jak načíst texturu prostřednictvím identifikátoru URI SAS (nebo vestavěné textury) – všimněte si, že pouze funkce/parametr načítání se liší pro druhý případ:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

V závislosti na tom, k čemu má být textura použita, mohou existovat omezení pro typ textury a obsah. Například mapa drsnosti [materiálu PBR](../overview/features/pbr-materials.md) musí mít stupně šedi.

> [!CAUTION]
> Všechny *asynchronní* funkce v ARR vrátí objekty asynchronní operace. Je nutné uložit odkaz na tyto objekty, dokud nebude operace dokončena. V opačném případě c# uvolňování může odstranit operaci brzy a nikdy dokončit. Ve vzorovém kódu nad členskou proměnnou "_textureLoad" se používá k uložení odkazu, dokud nedorazí událost *Completed.*

## <a name="next-steps"></a>Další kroky

* [Materials](materials.md)
* [Obloze](../overview/features/sky.md)
