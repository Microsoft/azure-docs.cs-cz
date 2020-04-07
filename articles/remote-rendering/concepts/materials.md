---
title: Materials
description: Popis materiálu kasáren a vlastnosti materiálu
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681841"
---
# <a name="materials"></a>Materials

Materiály jsou [sdílené prostředky,](../concepts/lifetime.md) které definují způsob [vykreslení mše.](meshes.md) Materiály se používají k určení, které [textury](textures.md) se mají použít, zda mají být objekty průhledné a jak se bude počítat osvětlení.

Materiály jsou automaticky vytvářeny během [převodu modelu](../how-tos/conversion/model-conversion.md) a jsou přístupné za běhu. Můžete také vytvořit vlastní materiály z kódu a nahradit stávající materiály. Tento scénář je obzvláště smysl, pokud chcete sdílet stejný materiál v mnoha síťech. Vzhledem k tomu, že změny materiálu jsou viditelné na každé síti, která na něj odkazuje, lze tuto metodu použít ke snadnému použití změn.

> [!NOTE]
> Některé případy použití, jako je například zvýraznění vyskladněný objekt lze provést úpravou materiálů, ale jsou mnohem jednodušší dosáhnout prostřednictvím [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Typy materiálů

Vzdálené vykreslování Azure má dva odlišné typy materiálů:

* [PBR materiály](../overview/features/pbr-materials.md) se používají pro povrchy, které by měly být vykresleny jako fyzicky správné, jak je to možné. Realistické osvětlení se vypočítá pro tyto materiály pomocí *fyzicky založené vykreslování* (PBR). Chcete-li tento typ materiálu využít na maximum, je důležité poskytnout vysoce kvalitní vstupní data, jako je drsnost a normální mapy.

* [Barevné materiály](../overview/features/color-materials.md) se používají v případech, kdy není žádoucí žádné další osvětlení. Tyto materiály jsou vždy plné jasné a snadněji se nastavují. Barevné materiály se používají pro data, která by buď neměla mít žádné osvětlení vůbec, nebo již obsahuje statické osvětlení, jako jsou modely získané prostřednictvím [fotogrammetrie](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Přiřazení materiálu Mesh vs. MeshComponent

[Síť má jednu](meshes.md) nebo více podmořských sítí. Každá podsíť odkazuje na jeden materiál. Můžete změnit materiál, který chcete použít buď přímo na síti, nebo můžete přepsat, který materiál se má použít pro podmřížku na [součásti mesh .](meshes.md#meshcomponent)

Když upravíte materiál přímo na prostředku sítě, tato změna ovlivní všechny výskyty této sítě. Změna na součásti MeshComponent však ovlivní pouze tuto jednu instanci sítě. Metoda, která je vhodnější závisí na požadované chování, ale úprava MeshComponent je běžnější přístup.

## <a name="material-classes"></a>Třídy materiálů

Všechny materiály poskytované rozhraníAPI jsou odvozeny ze základní třídy `Material`. Jejich typ může být `Material.MaterialSubType` dotazován prostřednictvím nebo jejich přímým obsazením:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Další kroky

* [PBR materiály](../overview/features/pbr-materials.md)
* [Barevné materiály](../overview/features/color-materials.md)
