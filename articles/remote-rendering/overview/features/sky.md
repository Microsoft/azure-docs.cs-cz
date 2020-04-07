---
title: Odrazy oblohy
description: Popisuje, jak nastavit mapy prostředí pro odrazy oblohy
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680606"
---
# <a name="sky-reflections"></a>Odrazy oblohy

V Azure Remote Rendering textura oblohy se používá k osvětlení objektů realisticky. U aplikací rozšířené reality by tato textura měla připomínat vaše skutečné okolí, aby objekty vypadaly přesvědčivě. Tento článek popisuje, jak změnit texturu oblohy.

> [!NOTE]
> Textura oblohy je také označována jako *mapa prostředí*. Tyto termíny se používají zaměnitelně.

## <a name="object-lighting"></a>Osvětlení objektů

Azure Remote Rendering využívá *fyzicky založené vykreslování* (PBR) pro realistické výpočty osvětlení. I když můžete do scény přidat [světelné zdroje,](lights.md) má použití dobré textury oblohy největší dopad.

Níže uvedené obrázky ukazují výsledky osvětlení různých povrchů pouze s texturou oblohy:

| Drsnost  | 0                                        | 0,25                                          | 0,5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Nekovové  | ![Dielektrika0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Další informace o modelu osvětlení naleznete v kapitole [materiály.](../../concepts/materials.md)

> [!IMPORTANT]
> Azure Remote Rendering používá texturu oblohy pouze pro modely osvětlení. To nečiní oblohu jako pozadí, protože aplikace rozšířené reality již mají správné pozadí - skutečný svět.

## <a name="changing-the-sky-texture"></a>Změna textury oblohy

Chcete-li změnit mapu prostředí, stačí [načíst texturu](../../concepts/textures.md) a `SkyReflectionSettings`změnit relaci :

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Všimněte `LoadTextureFromSASAsync` si, že varianta se používá výše, protože je načtena předdefinovaná textura. V případě načtení z [propojených úložišť objektů blob](../../how-tos/create-an-account.md#link-storage-accounts)použijte variantu. `LoadTextureAsync`

## <a name="sky-texture-types"></a>Typy textur oblohy

Jako mapy prostředí můžete použít *[jak kaučuky,](https://en.wikipedia.org/wiki/Cube_mapping)* tak *2D textury.*

Všechny textury musí být v [podporovaném formátu textury](../../concepts/textures.md#supported-texture-formats). Nemusíte poskytovat mipmapy pro textury oblohy.

### <a name="cube-environment-maps"></a>Mapy prostředí Krychle

Pro referenci, zde je nezabalená datová mapa:

![Nezabalená datová mapa](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Použití `LoadTextureFromSASAsync` `TextureType.CubeMap` s načtením textur cubemap.

### <a name="sphere-environment-maps"></a>Mapy prostředí koule

Při použití 2D textury jako mapy prostředí musí být obraz v [sférickém souřadnicovém prostoru](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Obraz oblohy v sférických souřadnicích](media/spheremap-example.png)

Použití `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` s načtením map sférického prostředí.

## <a name="built-in-environment-maps"></a>Mapy vestavěného prostředí

Azure Remote Rendering poskytuje několik předdefinovaných map prostředí, které jsou vždy k dispozici. Všechny vestavěné mapy prostředí jsou cubemaps.

|Identifikátor                         | Popis                                              | Obrázek                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Různé proužky světla, světlé vnitřní základní osvětlení    | ![Autoservis](media/autoshop.png)
|builtin://BoilerRoom               | Jasné nastavení vnitřního světla, více okenních světel      | ![Kotelna](media/boiler-room.png)
|builtin://ColorfulStudio           | Různě barevná světla ve středním osvětlení vnitřního nastavení  | ![BarevnéStudio](media/colorful-studio.png)
|builtin://Hangar                   | Mírně jasné okolní sálové světlo                     | ![Malý doutník](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Ztlumené vnitřní nastavení se světlým kontrastem              | ![IndustrialPipeandValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Denní osvětlení místnosti, jasné světlo v oblasti oken     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Tmavá noční obloha a země s mnoha okolními světly   | ![SataraNoc](media/satara-night.png)
|builtin://SunnyVondelpark          | Jasné sluneční světlo a kontrast stínu                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasné světlo oblohy s mírným osvětlením terénu            | ![Syferfontein (Fr.)](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Mírně proměnlivé slunce a stín                         | ![TearsofSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Večerní západ slunce se blíží soumraku                    | ![BenátkyZápad slunce](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Jasné, svěží zelené a bílé světlé tóny, tlumená půda | ![WhippleCreekRegionálnípark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Den s jasným okolním zemním světlem                 | ![Řeka Winter](media/winter-river.png)
|builtin://DefaultSky               | Stejné jako TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Další kroky

* [Lights](../../overview/features/lights.md)
* [Materials](../../concepts/materials.md)
* [Textury](../../concepts/textures.md)
* [Nástroj příkazového řádku TexConv](../../resources/tools/tex-conv.md)
