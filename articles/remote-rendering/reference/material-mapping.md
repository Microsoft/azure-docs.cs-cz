---
title: Mapování materiálu pro formáty modelů
description: Popisuje výchozí převod ze zdrojových formátů modelu na materiál PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680385"
---
# <a name="material-mapping-for-model-formats"></a>Mapování materiálu pro formáty modelů

Když je zdrojový datový zdroj [převeden jako model](../how-tos/conversion/model-conversion.md), převaděč vytvoří [materiály](../concepts/materials.md) pro každou [síť](../concepts/meshes.md). Způsob vytváření materiálů lze [přepsat](../how-tos/conversion/override-materials.md). Ve výchozím nastavení však převod vytvoří [materiály PBR](../overview/features/pbr-materials.md). Vzhledem k tomu, že každý formát zdrojového souboru, jako je FBX, používá vlastní konvence k definování materiálů, musí být tyto konvence mapovány na parametry materiálu PBR vzdáleného vykreslování Azure. 

V tomto článku jsou uvedena přesná mapování použitá k převodu materiálů ze zdrojových prostředků na materiály runtime.

## <a name="gltf"></a>glTF

Téměř vše od specifikace glTF 2.0 je podporováno v Azure Remote Rendering, s výjimkou *EmissiveFactor* a *EmissiveTexture*.

V následující tabulce je zobrazeno mapování:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallic metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMapa             |
|   drsnostFaktor   |   Drsnost                |
|   drsnostTextura  |   drsnostMapa             |
|   okluzeFaktor   |   Okluze                |
|   okluzeTextura  |   okluzeMapa             |
|   normalTexture     |   normalMap                |
|   alfaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Každá textura v glTF může mít hodnotu, `texCoord` která je také podporována v materiálech vzdáleného vykreslování Azure.

### <a name="embedded-textures"></a>Vložené textury

Textury vložené * \** do souborů .bin nebo * \*GLB* jsou podporovány.

### <a name="supported-gltf-extension"></a>Podporované rozšíření glTF

Kromě základní sady funkcí podporuje vzdálené vykreslování Azure následující rozšíření glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Odpovídá [barevným materiálům](../overview/features/color-materials.md). Pro *emisivní* materiály se doporučuje použít toto rozšíření.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Místo textur y metalické drsnosti můžete poskytnout textury rozptylového zrcadlového lesku. Implementace vzdáleného vykreslování Azure přímo sleduje vzorce převodu z rozšíření.

## <a name="fbx"></a>FBX

Formát FBX je uzavřený zdroj a materiály FBX nejsou obecně kompatibilní s pbr materiály. FBX používá komplexní popis povrchů s mnoha jedinečnými parametry a vlastnostmi a **ne všechny jsou používány kanálem vzdáleného vykreslování Azure**.

> [!IMPORTANT]
> Kanál převodu modelu vzdáleného vykreslování Azure podporuje jenom **FBX 2011 a vyšší**.

Formát FBX definuje konzervativní přístup k materiálům, v oficiální specifikaci FBX jsou pouze dva typy:

* *Lambert* - Již nějakou dobu se běžně nepoužívá, ale je stále podporován převodem na Phong v době konverze.
* *Phong* - Téměř všechny materiály a většina obsahových nástrojů používají tento typ.

Model Phong je přesnější a používá se jako *jediný* model pro materiály FBX. Pod ním bude označován jako *MATERIÁL FBX*.

> Maya používá dvě vlastní rozšíření pro FBX definováním vlastních vlastností pro typy PBR a Stingray materiálu. Tyto podrobnosti nejsou zahrnuty ve specifikaci FBX, takže není podporovánazure vzdálené vykreslování aktuálně.

FBX Materiály používají koncept Difuzní-zrcadlovce-specularLevel, takže pro převod z difúzní textury na mapu albedo musíme vypočítat další parametry, abychom je odečetli od difúzního.

> Všechny barvy a textury v FBX jsou v prostoru sRGB (označované také jako gamma prostor), ale Vzdálené vykreslování Azure pracuje s lineárním prostorem během vizualizace a na konci rámce převede vše zpět do prostoru sRGB. Kanál prostředků azure vzdálené vykreslování převede vše do lineárního prostoru a odešle je jako připravená data do vykreslovacího zařízení.

Tato tabulka ukazuje, jak jsou textury mapovány z materiálů FBX na materiály vzdáleného vykreslování Azure. Některé z nich nejsou přímo používány, ale v kombinaci s jinými texturami, které se účastní vzorců (například difúzní textura):

| FBX | Azure Remote Rendering |
|:-----|:----|
| Ambientcolor | Mapa okluze   |
| Difuzní barva | *používá pro Albedo, Metalness* |
| Průhledná barva | *používá se pro alfa kanál Albedo* |
| Transparentnost | *používá se pro alfa kanál Albedo* |
| Krytí | *používá se pro alfa kanál Albedo* |
| Zrcadlová barva | *používá pro Albedo, Metalness, Drsnost* |
| Zrcadlový faktor| *používá pro Albedo, Metalness, Drsnost* |
| ShininessExponent | *používá pro Albedo, Metalness, Drsnost* |
| Normální mapa | Normální mapa |
| Narazit | *převedeno na NormalMap* |
| EmissiveBarva | - |
| EmissiveFactor | - |
| Barva odrazu | - |
| PosunutíBarva | - |

Výše uvedené mapování je nejsložitější částí převodu materiálu kvůli mnoha předpokladům, které je třeba provést. Tyto předpoklady probíráme níže.

Některé definice použité níže:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Červená - 0,2125 + `Specular`. Zelená - 0,7154 + `Specular`. Modrá - 0,0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Červená<sup>2</sup> + 0,587 * `Diffuse`. Zelená<sup>2</sup> + 0,114 * `Diffuse`. Modrá<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Červená<sup>2</sup> + 0,587 * `Specular`. Zelená<sup>2</sup> + 0,114 * `Specular`. Modrá<sup>2</sup>
* `SpecularStrength`= max(`Specular`. Červená, `Specular`. Zelená, `Specular`. Modrá)

Vzorec SpecularIntensity se získá [odtud](https://en.wikipedia.org/wiki/Luma_(video)).
Vzorec jasu je popsán v této [specifikaci](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Drsnost

`Roughness`se vypočítá `Specular` `ShininessExponent` z [tohoto vzorce](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)a podle ní . Vzorec je aproximací drsnosti z Phongova zrcadlového exponentu:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalost

`Metalness`se vypočítá `Diffuse` `Specular` z tohoto vzorce a pomocí tohoto [vzorce ze specifikace GLTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Myšlenka je, že řešíme rovnici: Ax<sup>2</sup> + Bx + C = 0.
V podstatě, dielektrické povrchy odrážejí kolem 4% světla v zrcadlovým způsobem, a zbytek je difuzní. Kovové povrchy neodrážejí žádné světlo difúzním způsobem, ale všechny zrcadlovým způsobem.
Tento vzorec má několik nevýhod, protože neexistuje žádný způsob, jak rozlišovat mezi lesklým plastovým a lesklým kovovým povrchem. Předpokládáme, že většinu času má povrch kovové vlastnosti, a proto lesklé plastové / pryžové povrchy nemusí vypadat podle očekávání.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`je vypočítán `Diffuse` `Specular`z `Metalness`, , a .

Jak je popsáno v sekci Metalness, dielektrické povrchy odrážejí přibližně 4% světla.  
Cílem je lineárně interpolovat `Dielectric` `Metal` mezi `Metalness` a barvy pomocí hodnoty jako faktor. Pokud je `0.0`metalness , pak v závislosti na zrcadlení to bude buď tmavá barva (pokud je zrcadlo vysoké) nebo difuzní nezmění (pokud není přítomen žádný zrcadlo). Pokud je kovokost velkou hodnotou, pak difuzní barva zmizí ve prospěch zrcadlové barvy.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`byl vypočítán výše uvedeným vzorcem, ale alfa kanál vyžaduje další výpočty. Formát FBX je vágní o transparentnosti a má mnoho způsobů, jak ji definovat. Různé nástroje pro obsah používají různé metody. Myšlenka je sjednotit je do jednoho vzorce. To dělá některé prostředky nesprávně zobrazeny jako transparentní, i když, pokud nejsou vytvořeny běžným způsobem.

Vypočítá se z `TransparentColor` `TransparencyFactor`, `Opacity`, :

pokud `Opacity` je definována, pak `AlbedoAlpha`  =  `Opacity` ji použít přímo: jinak  
pokud `TransparencyColor` je definována, pak `AlbedoAlpha` =`TransparentColor`1,0 - (( . Červená `TransparentColor`+ . Zelená `TransparentColor`+ . Modrá) / 3.0) jinde  
pokud `TransparencyFactor`, `AlbedoAlpha` pak = 1,0 -`TransparencyFactor`

Konečná `Albedo` barva má čtyři kanály, které kombinují `AlbedoRGB` s `AlbedoAlpha`.

### <a name="summary"></a>Souhrn

Chcete-li shrnout zde, `Albedo` bude `Diffuse`velmi `Specular` blízko k originálu , pokud se blíží nule. V opačném případě bude povrch vypadat jako kovový povrch a ztratí rozptýlenou barvu. Povrch bude vypadat více leštěné a reflexní, pokud `ShininessExponent` je dostatečně velký a `Specular` je jasný. V opačném případě bude povrch vypadat drsně a sotva odráží životní prostředí.

### <a name="known-issues"></a>Známé problémy

* Aktuální vzorec nefunguje dobře pro jednoduchou barevnou geometrii. Pokud `Specular` je dostatečně jasný, pak všechny geometrie se reflexní kovové povrchy bez jakékoliv barvy. Řešení zde je snížit `Specular` na 30% z originálu nebo použít nastavení převodu [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Do nástrojů `Maya` pro `3DS Max` tvorbu obsahu byly nedávno přidány materiály PBR. Používají vlastní uživatelem definované vlastnosti černéskříňky, aby je předali FBX. Azure Remote Rendering nečte tyto další vlastnosti, protože nejsou zdokumentovány a formát je uzavřený zdroj.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](../how-tos/conversion/model-conversion.md)
* [Převládající materiály během převodu modelu](../how-tos/conversion/override-materials.md)
