---
title: Mapování materiálu pro formáty modelů
description: Popisuje výchozí převod ze zdrojových formátů modelu na PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96024040"
---
# <a name="material-mapping-for-model-formats"></a>Mapování materiálu pro formáty modelů

Když se zdrojový prostředek [převede jako model](../how-tos/conversion/model-conversion.md), vytvoří konvertor [materiály](../concepts/materials.md) pro každou [síť](../concepts/meshes.md). Způsob vytváření materiálů lze [přepsat](../how-tos/conversion/override-materials.md). Ve výchozím nastavení však převod vytvoří [materiály PBR](../overview/features/pbr-materials.md). Vzhledem k tomu, že každý formát zdrojového souboru, jako je FBX, používá k definování materiálů vlastní konvence, musí být tyto konvence namapovány na parametry materiálu PBR pro vzdálené vykreslování Azure. 

V tomto článku jsou uvedena přesná mapování, která slouží k převodu materiálů ze zdrojových prostředků na běhové materiály.

## <a name="gltf"></a>glTF

Ve vzdáleném vykreslování Azure se podporuje téměř všechno od specifikace glTF 2,0, s výjimkou *EmissiveFactor* a *EmissiveTexture*.

Mapování se zobrazuje v následující tabulce:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   kov                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   hrubá                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   překrytí                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode. neprůhledný  |   alphaClipEnabled = false, Transparent = false |
|   alphaMode. MASK    |   alphaClipEnabled = true, Transparent = false  |
|   alphaMode. BLEND   |   Transparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Každá textura v glTF může mít `texCoord` hodnotu, která je také podporována v materiálech vzdáleného vykreslování Azure.

### <a name="embedded-textures"></a>Vložené textury

Podporují se textury vložené do souborů *\* . bin* nebo *\* . glb* .

### <a name="supported-gltf-extension"></a>Podporované rozšíření glTF

Kromě základní sady funkcí podporuje vzdálené vykreslování Azure následující rozšíření glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): odpovídá [barevným materiálům](../overview/features/color-materials.md). U *vyzařujících* materiálů doporučujeme toto rozšíření použít.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): místo kovových textur nemusíte mít k dispozici textury rozlesku – zrcadlově glossiness. Implementace vzdáleného vykreslování Azure přímo sleduje vzorce převodu z rozšíření.

## <a name="fbx"></a>FBX

Formát FBX je uzavřený – source a FBX materiály nejsou obecně kompatibilní s materiály PBR. FBX používá složitý popis povrchů s mnoha jedinečnými parametry a vlastnostmi a **Ne všechny z nich používá kanál vzdáleného vykreslování Azure**.

> [!IMPORTANT]
> Kanál převodu modelu vzdáleného vykreslování Azure podporuje jenom **FBX 2011 a vyšší**.

Formát FBX definuje konzervativní přístup k materiálům. Oficiální specifikace FBX obsahuje jenom dva typy:

* *Lambert* – to se běžně nepoužívá v době určitou dobu, ale je stále podporováno převodem na Phongova v době převodu.
* *Phongova* – tento typ používá skoro všechny materiály a většina nástrojů pro obsah.

Model Phongova je přesnější a používá se jako *jediný* model pro FBX materiály. Pod ní bude odkazováno jako na *FBX materiál*.

> Maya používá dvě vlastní rozšíření pro FBX definováním vlastních vlastností pro typy PBR a Stingray materiálu. Tyto podrobnosti nejsou zahrnuté ve specifikaci FBX, takže je aktuálně nepodporuje vzdálené vykreslování Azure.

FBX materiály používají koncept difúze-disSpecularLeveling, takže pokud chcete převést z rozptýlené textury na mapu albedo, potřebujeme vypočítat další parametry a odečíst je od difúze.

> Všechny barvy a textury v FBX jsou v prostoru sRGB (označuje se také jako prostor hodnot Gamma), ale vzdálené vykreslování Azure funguje s lineárním mezerou během vizualizace a na konci snímku převede všechno zpátky na místo sRGB. Kanál assetu vzdáleného vykreslování Azure převede vše na lineární místo, které se odešle jako přípravná data do zobrazovací jednotky.

Tato tabulka ukazuje, jak jsou textury mapované z FBX materiálů na materiály pro vzdálené vykreslování Azure. Některé z nich nejsou přímo používány, ale v kombinaci s jinými texturami, které se účastní vzorců (například texturu difúze):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Mapa překrytí   |
| DiffuseColor | *používá se pro albedo, kov* |
| TransparentColor | *používá se pro alfa kanál albedo.* |
| TransparencyFactor | *používá se pro alfa kanál albedo.* |
| Určující | *používá se pro alfa kanál albedo.* |
| SpecularColor | *používá se pro albedo, prokovů, hrubosti* |
| SpecularFactor| *používá se pro albedo, prokovů, hrubosti* |
| ShininessExponent | *používá se pro albedo, prokovů, hrubosti* |
| NormalMap | NormalMap |
| Varn | *převedeno na NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Mapování výše je nejsložitější součást převodu materiálu z důvodu mnoha předpokladů, které je třeba provést. Tyto předpoklady probereme níže.

Některé níže používané definice:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Red ∗ 0,2125 +  `Specular` . Zelená ∗ 0,7154 + `Specular` . Blue ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Red<sup>2</sup> + 0,587 * `Diffuse` . Zelená<sup>2</sup> + 0,114 * `Diffuse` . Modrá<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Red<sup>2</sup> + 0,587 * `Specular` . Zelená<sup>2</sup> + 0,114 * `Specular` . Modrá<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Červená, `Specular` . Zelená, `Specular` . Novák

Vzorec SpecularIntensity se získá [odsud.](https://en.wikipedia.org/wiki/Luma_(video))
Vzorec jasu je popsán v této [specifikaci](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Hrubá

`Roughness` vypočítá se z `Specular` a `ShininessExponent` pomocí [tohoto vzorce](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). Vzorec je aproximace hrubosti od exponentu Phongova odlesků:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Kov

`Metalness` se počítá z `Diffuse` a `Specular` používá tento [vzorec ze specifikace glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Tady je, že jsme vyřešili rovnici: AX<sup>2</sup> + BX + C = 0.
V podstatě dielectric povrch odráží přibližně 4% světla a zbytek je rozptýlen. Kovové povrchy nereflektují žádné světlo jako difúzi, ale všechny s odlesky.
Tento vzorec má několik nevýhod, protože neexistuje způsob, jak rozlišovat lesklé plasty a lesklé kovové povrchy. Předpokládáme většinu času, kdy plocha má kovové vlastnosti, a v důsledku toho se lesklé plastové/pryžové plochy nemusí považovat za očekávané.

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

`Albedo` je vypočítán z `Diffuse` , `Specular` a `Metalness` .

Jak je popsáno v části kov, dielectric povrchy odráží přibližně 4% světla.  
Tady je příklad, jak lineárně interpoluje barvy mezi `Dielectric` a, a to `Metal` pomocí `Metalness` hodnoty jako faktoru. Pokud je k `0.0` dispozici rozptyl, pak v závislosti na odlesku bude tato barva buď tmavá (Pokud je zrcadlově velká), nebo se difúze nemění (Pokud není k dispozici žádná odlesk). Pokud je k dishodnotě značná hodnota, pak barva difúze zmizí a bude mít za následek odlesky barev.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` byl vypočítán pomocí výše uvedeného vzorce, ale alfa kanál vyžaduje další výpočty. Formát FBX je Vague o transparentnosti a má mnoho způsobů, jak ho definovat. Různé nástroje obsahu používají různé metody. Nápad je sjednotit do jednoho vzorce. V případě, že se některé assety nevytváří běžným způsobem, jsou některé z nich nesprávně zobrazené jako průhledné.

Tato akce je vypočítána z `TransparentColor` , `TransparencyFactor` , `Opacity` :

Pokud `Opacity` je definován, použijte ji přímo: `AlbedoAlpha`  =  `Opacity` Else  
Pokud `TransparencyColor` je definován, pak `AlbedoAlpha` = 1,0-(( `TransparentColor` . Červená + `TransparentColor` . Zelená + `TransparentColor` . Modrý)/3,0) else  
IF `TransparencyFactor` , then `AlbedoAlpha` = 1,0- `TransparencyFactor`

Konečná `Albedo` Barva má čtyři kanály a kombinaci `AlbedoRGB` s `AlbedoAlpha` .

### <a name="summary"></a>Souhrn

Pokud je zde shrnuto, `Albedo` bude se velmi blízko původní `Diffuse` , pokud `Specular` bude téměř nula. V opačném případě bude povrch vypadat jako kovový povrch a ztratí barvu difúze. Povrch se bude podobat a odrazit, pokud `ShininessExponent` je dostatečně velký a `Specular` jasný. V opačném případě bude plocha vypadat hrubě a zlomek odráží prostředí.

### <a name="known-issues"></a>Známé problémy

* Aktuální vzorec nefunguje dobře pro jednoduchou barevnou geometrii. Pokud `Specular` je dostatečně jasný, pak všechny geometrií se odrážejí kovové povrchy bez jakékoli barvy. Alternativním řešením je, aby se snížilo `Specular` na 30% z původního nebo na použití nastavení převodu [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Materiály v obsahu PBR byly nedávno přidány k `Maya` `3DS Max` nástrojům a vytváření obsahu. Používají vlastní uživatelsky definované vlastnosti černého pole k předání do FBX. Vzdálené vykreslování Azure tyto další vlastnosti nepřečte, protože nejsou zdokumentováné a formát je uzavřený.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](../how-tos/conversion/model-conversion.md)
* [Přepsání materiálů během převodu modelu](../how-tos/conversion/override-materials.md)
