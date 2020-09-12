---
title: Odrazy oblohy
description: Popisuje, jak nastavit mapy prostředí pro reflektore nebe.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613747"
---
# <a name="sky-reflections"></a>Odrazy oblohy

Ve vzdáleném vykreslování Azure se k světlování objektů používá nebeská textura. Pro rozšířené aplikace reality by tato textura měla vypadat jako v reálném okolí, aby se objekty zobrazovaly jako přesvědčivé. Tento článek popisuje, jak změnit texturu nebe.

> [!NOTE]
> Textura nebe se také označuje jako *Mapa prostředí*. Tyto výrazy se používají zaměnitelné.

## <a name="object-lighting"></a>Osvětlení objektů

Vzdálené vykreslování Azure využívá pro realistické výpočty světla *fyzicky založené vykreslování* (PBR). I když můžete do scény přidat [světlé zdroje](lights.md) , s využitím dobré textury nebe má největší dopad.

Následující obrázky znázorňují výsledky osvětlení různých povrchů pouze s texturou nebe:

| Hrubá  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Jiný než kov  | ![Dielectric, hrubosti = 0](media/dielectric-0.png)   | ![Dielectric, hrubosti = 0,25](media/dielectric-0.25.png)  | ![Dielectric, hrubosti = 0.5](media/dielectric-0.5.png)  | ![Dielectric, hrubosti = 0,75](media/dielectric-0.75.png)  | ![Dielectric, hrubosti = 1](media/dielectric-1.png)  |
| Metal      | ![Kov, Hrubost = 0](media/metallic-0.png)  | ![Kov, Hrubost = 0,25](media/metallic-0.25.png)    | ![Kov, Hrubost = 0,5](media/metallic-0.5.png)    | ![Kov, Hrubost = 0,75](media/metallic-0.75.png)    | ![Kov, Hrubost = 1](media/metallic-1.png)    |

Další informace o modelu osvětlení naleznete v kapitole [materiály](../../concepts/materials.md) .

> [!IMPORTANT]
> Vzdálené vykreslování Azure používá texturu nebe jenom pro modely osvětlení. Nevykresluje nebe jako pozadí, protože rozšíření aplikace pro rozšířené reality již mají správné pozadí – reálný svět.

## <a name="changing-the-sky-texture"></a>Změna textury nebe

Pokud chcete změnit mapu prostředí, stačí, když budete potřebovat [Načíst texturu](../../concepts/textures.md) a změnit relaci `SkyReflectionSettings` :

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
            }
        });
}

```

Všimněte si, že je `LoadTextureFromSASAsync` použita varianta, protože je načtena integrovaná textura. V případě načítání z [propojených úložišť objektů BLOB](../../how-tos/create-an-account.md#link-storage-accounts)použijte `LoadTextureAsync` variantu.

## <a name="sky-texture-types"></a>Typy textury nebe

Jako mapy prostředí můžete použít jak *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* , tak *2D textury* .

Všechny textury musí být v [podporovaném formátu textury](../../concepts/textures.md#supported-texture-formats). Nemusíte zadávat mipmapy pro textury nebe.

### <a name="cube-environment-maps"></a>Mapy prostředí krychle

Pro referenci je zde uveden nezabalený cubemap:

![Nezabalená cubemap](media/Cubemap-example.png)

Použijte `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` `TextureType.CubeMap` k načtení cubemap textur.

### <a name="sphere-environment-maps"></a>Mapy prostředí sphere

Při použití 2D textury jako mapy prostředí musí být obrázek v [kulové souřadnicovém prostoru](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Obrázek nebe v kulových souřadnicích](media/spheremap-example.png)

Použijte `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` k načtení map sféry prostředí.

## <a name="built-in-environment-maps"></a>Předdefinované mapy prostředí

Vzdálené vykreslování Azure poskytuje několik předdefinovaných map prostředí, které jsou vždycky dostupné. Všechna integrovaná mapování prostředí jsou cubemaps.

|Identifikátor                         | Popis                                              | Obrázek                                                      |
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

* [Vlastnost C# RemoteManager. SkyReflectionSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ RemoteManager:: SkyReflectionSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Další kroky

* [Světla](../../overview/features/lights.md)
* [Materiály](../../concepts/materials.md)
* [Textury](../../concepts/textures.md)
* [Nástroj příkazového řádku TexConv](../../resources/tools/tex-conv.md)
