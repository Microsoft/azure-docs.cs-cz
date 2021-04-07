---
title: Odrazy oblohy
description: Popisuje, jak nastavit mapy prostředí pro reflektore nebe.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594176"
---
# <a name="sky-reflections"></a>Odrazy oblohy

Ve vzdáleném vykreslování Azure se k světlování objektů používá nebeská textura. Pro rozšířené aplikace reality by tato textura měla vypadat jako v reálném okolí, aby se objekty zobrazovaly jako přesvědčivé. Tento článek popisuje, jak změnit texturu nebe.

> [!NOTE]
> Textura nebe se také označuje jako *Mapa prostředí*. Tyto výrazy se používají zaměnitelné.

## <a name="object-lighting"></a>Osvětlení objektů

Vzdálené vykreslování Azure využívá pro realistické výpočty světla *fyzicky založené vykreslování* (PBR). I když můžete do scény přidat [světlé zdroje](lights.md) , s využitím dobré textury nebe má největší dopad.

Následující obrázky znázorňují výsledky osvětlení různých povrchů pouze s texturou nebe:

| Hrubá  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Jiný než kov  | ![Dielectric, hrubosti = 0](media/dielectric-0.png)   | ![Dielectric, hrubosti = 0,25](media/dielectric-0.25.png)  | ![Dielectric, hrubosti = 0.5](media/dielectric-0.5.png)  | ![Dielectric, hrubosti = 0,75](media/dielectric-0.75.png)  | ![Dielectric, hrubosti = 1](media/dielectric-1.png)  |
| Metal      | ![Kov, Hrubost = 0](media/metallic-0.png)  | ![Kov, Hrubost = 0,25](media/metallic-0.25.png)    | ![Kov, Hrubost = 0,5](media/metallic-0.5.png)    | ![Kov, Hrubost = 0,75](media/metallic-0.75.png)    | ![Kov, Hrubost = 1](media/metallic-1.png)    |

Další informace o modelu osvětlení naleznete v kapitole [materiály](../../concepts/materials.md) .

> [!IMPORTANT]
> Vzdálené vykreslování Azure používá texturu nebe jenom pro modely osvětlení. Nevykresluje nebe jako pozadí, protože rozšíření aplikace pro rozšířené reality již mají správné pozadí – reálný svět.

## <a name="changing-the-sky-texture"></a>Změna textury nebe

Pokud chcete změnit mapu prostředí, stačí, když budete potřebovat [Načíst texturu](../../concepts/textures.md) a změnit relaci `SkyReflectionSettings` :

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

Všimněte si, že je `LoadTextureFromSasAsync` použita varianta, protože je načtena integrovaná textura. V případě načítání z [propojených úložišť objektů BLOB](../../how-tos/create-an-account.md#link-storage-accounts)použijte `LoadTextureAsync` variantu.

## <a name="sky-texture-types"></a>Typy textury nebe

Jako mapy prostředí můžete použít jak *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* , tak *2D textury* .

Všechny textury musí být v [podporovaném formátu textury](../../concepts/textures.md#supported-texture-formats). Nemusíte zadávat mipmapy pro textury nebe.

### <a name="cube-environment-maps"></a>Mapy prostředí krychle

Pro referenci je zde uveden nezabalený cubemap:

![Nezabalená cubemap](media/Cubemap-example.png)

Použijte `RenderingSession.Connection.LoadTextureAsync` /  `LoadTextureFromSasAsync` `TextureType.CubeMap` k načtení cubemap textur.

### <a name="sphere-environment-maps"></a>Mapy prostředí sphere

Při použití 2D textury jako mapy prostředí musí být obrázek v [kulové souřadnicovém prostoru](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Obrázek nebe v kulových souřadnicích](media/spheremap-example.png)

Použijte `RenderingSession.Connection.LoadTextureAsync` `TextureType.Texture2D` k načtení map sféry prostředí.

## <a name="built-in-environment-maps"></a>Předdefinované mapy prostředí

Vzdálené vykreslování Azure poskytuje několik předdefinovaných map prostředí, které jsou vždycky dostupné. Všechna integrovaná mapování prostředí jsou cubemaps.

|Identifikátor                         | Description                                              | Obrázek                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Spektrum pruhů světla, jasného základního osvětlení interiéru    | ![Skybox k osvětlení objektu pomocí technologie autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Světlé světlo – nastavení, více indikátorů okna      | ![BoilerRoom Skybox, který se používá k osvětlení objektu](media/boiler-room.png)
|builtin://ColorfulStudio           | Proměnlivé barevné světla v případě středně světlého nastavení interiéru  | ![ColorfulStudio Skybox, který se používá k osvětlení objektu](media/colorful-studio.png)
|builtin://Hangar                   | Středně jasné světlé prostředí okolí                     | ![SmallHangar Skybox, který se používá k osvětlení objektu](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Tmavé nastavení vnitřního doběhu s kontrastem v tmavém světle              | ![IndustrialPipeAndValve Skybox, který se používá k osvětlení objektu](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Denní okolní místnost – světlá, světlá oblast okna     | ![Lebombo Skybox, který se používá k osvětlení objektu](media/lebombo.png)
|builtin://SataraNight              | Tmavě noční nebe a uzemnění s mnoha okolními kvadranty   | ![SataraNight Skybox, který se používá k osvětlení objektu](media/satara-night.png)
|builtin://SunnyVondelpark          | Světlé světlo a stínový kontrast                      | ![SunnyVondelpark Skybox, který se používá k osvětlení objektu](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasný Nebeský světlo se středním osvětlením            | ![Syferfontein Skybox, který se používá k osvětlení objektu](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Středně proměnlivý Sun a barevný stín                         | ![TearsOfSteelBridge Skybox, který se používá k osvětlení objektu](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Dusk večer pro lehké přístupu                    | ![VeniceSunset Skybox, který se používá k osvětlení objektu](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Světlé, Lush – zelená a bílá světla, ztlumená země | ![WhippleCreekRegionalPark Skybox, který se používá k osvětlení objektu](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Daytime s jasným okolním světlem                 | ![WinterRiver Skybox, který se používá k osvětlení objektu](media/winter-river.png)
|builtin://DefaultSky               | Stejné jako TearsOfSteelBridge                               | ![DefaultSky Skybox, který se používá k osvětlení objektu](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Vlastnost C# RenderingConnection. SkyReflectionSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection:: SkyReflectionSettings ()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>Další kroky

* [Světla](../../overview/features/lights.md)
* [Materiály](../../concepts/materials.md)
* [Textury](../../concepts/textures.md)
