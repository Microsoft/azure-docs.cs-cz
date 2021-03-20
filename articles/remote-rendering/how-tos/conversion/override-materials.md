---
title: Potlačení materiálů během převodu modelů
description: Vysvětluje materiál přepisující pracovní postup v době převodu.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576638"
---
# <a name="override-materials-during-model-conversion"></a>Potlačení materiálů během převodu modelů

Nastavení materiálu ve zdrojovém modelu slouží k definování [materiálů PBR](../../overview/features/pbr-materials.md) používaných nástrojem pro vykreslování.
V některých případech [výchozí převod](../../reference/material-mapping.md) neposkytuje požadované výsledky a je třeba provést změny.
Když je model převeden pro použití ve vzdáleném vykreslování Azure, můžete poskytnout soubor přepsání materiálu k přizpůsobení toho, jak se převod materiálu provádí na základě jednotlivých materiálů.
Pokud `<modelName>.MaterialOverrides.json` je v vstupním kontejneru vedle vstupního modelu nalezen soubor s názvem `<modelName>.<ext>` , bude použit jako soubor přepisu materiálu.

## <a name="the-override-file-used-during-conversion"></a>Soubor přepsání použitý při převodu

Jednoduchým příkladem je řekněme, že krabicový model má jeden materiál s názvem default.
Řekněme také, že je potřeba upravit albedo barvu pro použití v ARR.
V takovém případě `box.MaterialOverrides.json` lze soubor vytvořit takto:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json`Soubor je umístěn ve vstupním kontejneru vedle příkazu `box.fbx` , který oznamuje službě převodu, aby se nové nastavení projevilo.

### <a name="color-materials"></a>Barevné materiály

[Barevný materiálový](../../overview/features/color-materials.md) model popisuje nepřetržitě stínovaný povrch, který je nezávislý na osvětlení.
Barevné materiály jsou užitečné pro prostředky vytvořené Photogrammetry algoritmy, například.
V souborech přepsání materiálu může být materiál deklarovaný jako barevný materiál nastavením `unlit` na `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorovat konkrétní mapy textur

Někdy může být vhodné, aby proces převodu ignoroval konkrétní mapy textur. To může být případ, kdy byl model vygenerován pomocí nástroje, který generuje speciální mapy, které modul pro vykreslování nerozpoznal správně. Například "OpacityMap", který se používá k definování jiného než neprůhlednosti nebo modelu, kde "NormalMap" je uložen jako "BumpMap". (V druhém případě chcete ignorovat "NormalMap", což způsobí, že převaděč použije "BumpMap" jako "NormalMap".)

Princip je jednoduchý. Stačí přidat vlastnost s názvem `ignoreTextureMaps` a přidat mapu textury, kterou chcete ignorovat:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Úplný seznam map textur, které můžete ignorovat, najdete níže v níže uvedeném schématu JSON.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Použití stejných přepsání u více materiálů

Ve výchozím nastavení se položka v souboru přepisu materiálu používá, pokud se název shoduje přesně s názvem materiálu.
Vzhledem k tomu, že je poměrně běžné, že se stejné přepsání má vztahovat na více materiálů, můžete volitelně zadat regulární výraz jako název položky.
Pole `nameMatching` má výchozí hodnotu `exact` , ale je možné ji nastavit na hodnotu, aby se `regex` položka mohla vztahovat na všechny odpovídající materiály.
Použitá syntaxe je stejná jako ta, která se používá pro JavaScript. Následující příklad ukazuje přepsání, které se vztahuje na materiály s názvy, jako jsou "Material2", "Material01" a "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Pouze jeden záznam v souboru přepisu materiálu se vztahuje na jeden materiál.
Pokud existuje přesná shoda (tj. není k dispozici `nameMatching` nebo se rovná `exact` ) pro název materiálu, pak se tato položka vybere.
V opačném případě je zvolena první položka regulárního výrazu v souboru, který se shoduje s názvem materiálu.

### <a name="getting-information-about-which-entries-applied"></a>Získání informací o tom, které položky byly aplikovány

Informační [soubor](get-information.md#information-about-a-converted-model-the-info-file) zapsaný do výstupního kontejneru přenáší informace o počtu poskytnutých přepsání a počtu přepsaných materiálů.

## <a name="json-schema"></a>Schéma JSON

Úplné schéma JSON pro soubory materiálů je uvedeno zde. S výjimkou `unlit` a `ignoreTextureMaps` jsou dostupné vlastnosti podmnožinou vlastností popsaných v oddílech v [materiálech](../../overview/features/pbr-materials.md) s [barevným materiálem](../../overview/features/color-materials.md) a modelu PBR.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Další kroky

* [Barevné materiály](../../overview/features/color-materials.md)
* [Materiály PBR](../../overview/features/pbr-materials.md)
