---
title: Materiály
description: Popis nebo vlastnosti materiálu pro vykreslování
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681841"
---
# <a name="materials"></a>Materiály

Materiály jsou [sdílené prostředky](../concepts/lifetime.md) , které definují, jak se vykreslují [sítě](meshes.md) . Materiály slouží k určení, které [textury](textures.md) mají být použity, zda mají být objekty transparentní a jak se bude počítat osvětlení.

Materiály se automaticky vytvoří během [převodu modelu](../how-tos/conversion/model-conversion.md) a jsou přístupné za běhu. Můžete také vytvořit vlastní materiály z kódu a nahradit existující. Tento scénář je vhodný hlavně v případě, že chcete sdílet stejný materiál napříč mnoha oky. Vzhledem k tomu, že se změny materiálu zobrazují na každé síti, která na ni odkazuje, můžete tuto metodu použít k snadnému uplatnění změn.

> [!NOTE]
> Některé případy použití, například zvýraznění vyskladněného objektu, lze provádět úpravou materiálů, ale je mnohem snazší dosáhnout prostřednictvím [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Typy materiálu

Vzdálené vykreslování Azure má dva odlišné typy materiálu:

* [Materiály PBR](../overview/features/pbr-materials.md) se používají pro povrchy, které by se měly vykreslovat jako fyzicky správné. Pro tyto materiály se počítá z reálného osvětlení pomocí *fyzicky založeného vykreslování* (PBR). Aby bylo možné tento typ materiálu využít co nejlépe, je důležité zajistit vysoce kvalitní vstupní data, jako jsou hrubá a normální mapa.

* [Barevné materiály](../overview/features/color-materials.md) se používají pro případy, kdy není žádoucí žádné další osvětlení. Tyto materiály jsou vždy zcela jasné a je snazší je nastavit. Barevné materiály se používají pro data, která by neměla vůbec žádného osvětlení, nebo už jsou v podniku statického osvětlení, jako jsou například modely získané prostřednictvím [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Mřížka vs. přiřazení materiálu MeshComponent

[Sítě](meshes.md) mají jednu nebo více podsítí. Každé podsítě odkazuje na jeden materiál. Tento materiál můžete změnit tak, aby se použil přímo na síti, nebo můžete přepsat, který materiál se má použít pro podmřížku na [MeshComponent](meshes.md#meshcomponent).

Když upravujete materiál přímo v prostředku sítě, tato změna ovlivní všechny instance této sítě. Změna na MeshComponent však ovlivní pouze jednu instanci sítě. Která metoda je vhodnější, závisí na požadovaném chování, ale úpravou MeshComponent je častější přístup.

## <a name="material-classes"></a>Třídy materiálu

Všechny materiály poskytované rozhraním API jsou odvozeny ze základní `Material`třídy. Jejich typ lze dotazovat prostřednictvím `Material.MaterialSubType` nebo přetypováním přímo:

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

* [Materiály PBR](../overview/features/pbr-materials.md)
* [Barevné materiály](../overview/features/color-materials.md)
