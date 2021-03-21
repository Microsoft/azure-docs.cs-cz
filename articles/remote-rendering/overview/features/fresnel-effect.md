---
title: Fresnelův efekt
description: Stránka s vysvětlením funkcí pro Fresnelova poklesu materiálový efekt
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594108"
---
# <a name="fresnel-effect"></a>Fresnelův efekt

Funkce materiálu Fresnelova poklesu účinku je nefyzicky Opravná, ad hoc efekt. Tato funkce je založena na fyzickém pozorování objektů, které se v těchto úhlech stávají více odrážejí. Fresnelova poklesu odraz je už fyzicky zahrnutý v [materiálovém modelu PBR](../../overview/features/pbr-materials.md) používaném ve vzdáleném vykreslování Azure. Funkce Fresnelova poklesu účinku je naopak pouze doplňková Barva efektu bez závislosti na [indikátorech](../../overview/features/lights.md) nebo v [prostředí nebe](../../overview/features/sky.md).

Fresnelova poklesu efekt dává ovlivněným objektům barevné roztéká kolem jejich okrajů. Informace o přizpůsobení efektu a příklady výsledků vykreslování najdete v následujících oddílech.

## <a name="enabling-the-fresnel-effect"></a>Povolení Fresnelova poklesuho efektu

Chcete-li použít funkci Fresnelova poklesu účinek, je nutné ji povolit na příslušných materiálech. Můžete ji povolit nastavením FresnelEffect bitu [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) pro [materiál PBR](../../overview/features/pbr-materials.md). Stejný vzor platí pro [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) a [barevný materiál](../../overview/features/color-materials.md). V části Ukázky kódu najdete ukázku použití.

Po povolení se efekt Fresnelova poklesu okamžitě zobrazí. Ve výchozím nastavení bude září bílá (1, 1, 1, 1) a mít exponent 1. Tato nastavení můžete přizpůsobit pomocí níže uvedených parametrů setter.

## <a name="customizing-the-effect-appearance"></a>Přizpůsobení vzhledu efektu

V současné době je možné efekt Fresnelova poklesu přizpůsobit na materiál pomocí následujících vlastností:

| Vlastnost materiálu | Typ | Vysvětlení |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Barva, která se přidávají maximálně jako Fresnelova poklesu. Kanál alfa se aktuálně ignoruje. |
| FresnelEffectExponent | float | Rozprostření Fresnelova poklesu. Rozsahy od 0,01 (rozprostření do celého objektu) na 10 (pouze gracing úhly). |

V praxi budou různá nastavení barev a exponentů vypadat takto:

![Příklady efektů Fresnelova poklesu](./media/fresnel-effect-examples.png)

Exponent Fresnelova poklesuho efektu se postupně zvyšuje od 1 do 10 pro každý řádek barvy. Provedete to tak, že Fresnelova poklesu rozkáže na okraje zobrazených objektů. Fresnelova poklesu efekt je také neovlivněn průhledností, jak vidíte v následujícím příkladu:

![Příklady transparentnosti efektu Fresnelova poklesu](./media/fresnel-effect-transparent-examples.png)

Jak je znázorněno, objekty na diagonále jsou plně transparentní, ale Fresnelova poklesuský. Tento efekt napodobá fyzicky založené Fresnelova poklesu v tomto ohledu, který je také přítomen na těchto snímcích obrazovky.

## <a name="code-samples"></a>Ukázky kódů

Následující ukázky kódu ukazují povolení a přizpůsobení Fresnelova poklesu efektu pro [materiál PBR](../../overview/features/pbr-materials.md) i pro [barevný materiál](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [PbrMaterialFeatures C++](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [ColorMaterialFeatures C++](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Další kroky

* [Materiály](../../concepts/materials.md)
* [Materiály PBR](../../overview/features/pbr-materials.md)
* [Barevné materiály](../../overview/features/color-materials.md)