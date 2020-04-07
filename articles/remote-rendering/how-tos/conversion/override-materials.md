---
title: Přepsat materiály během převodu modelu
description: Vysvětluje materiál přepsání pracovního postupu v době převodu
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681477"
---
# <a name="override-materials-during-model-conversion"></a>Přepsat materiály během převodu modelu

Při převodu se nastavení materiálu ve zdrojovém modelu používá k definování [materiálů PBR používaných](../../overview/features/pbr-materials.md) vykreslovacím.
Někdy [výchozí převod](../../reference/material-mapping.md) neposkytuje požadované výsledky a je třeba provést změny.
Když je model převeden pro použití ve vzdáleném vykreslování Azure, můžete poskytnout soubor přepsání materiálu přizpůsobit způsob převodu materiálu na základě pro daný materiál.
Oddíl o [konfiguraci převodu modelu](configure-model-conversion.md) obsahuje pokyny pro deklarování názvu souboru přepsání materiálu.

## <a name="the-override-file-used-during-conversion"></a>Přepsání souboru použitého při převodu

Jako jednoduchý příklad řekněme, že model krabice má jeden materiál, nazvaný "Výchozí". Albedo barva musí být upravena pro použití v ARR.
V tomto případě `box_materials_override.json` lze vytvořit soubor následujícím způsobem:

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

Soubor `box_materials_override.json` je umístěn ve vstupním `ConversionSettings.json` kontejneru `box.fbx`a a je přidán vedle , který říká převod, kde najít přepsat soubor (viz [Konfigurace převodu modelu](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Po převodu modelu se použije nové nastavení.

### <a name="color-materials"></a>Barevné materiály

Model [barevného materiálu](../../overview/features/color-materials.md) popisuje neustále stínovaný povrch, který je nezávislý na osvětlení.
To je užitečné pro prostředky provedené algoritmy Photogrammetry, například.
V souborech přepsání materiálu může být materiál deklarován jako barevný materiál nastavením `unlit` na `true`.

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

Někdy můžete chtít, aby proces převodu ignoroval konkrétní mapy textur. To může být případ, kdy byl váš model generován nástrojem, který generuje speciální mapy, které vykreslovací modul správně nerozumí. Například "OpacityMap", který se používá k definování něco jiného než krytí, nebo model, kde "NormalMap" je uložen jako "BumpMap". (V druhém případě chcete ignorovat "NormalMap", což způsobí, že převaděč použít "BumpMap" jako "NormalMap".)

Princip je jednoduchý. Stačí přidat vlastnost `ignoreTextureMaps` s názvem a přidat všechny textury mapy, které chcete ignorovat:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Úplný seznam map textur, které můžete ignorovat, najdete v níže uvedeném schématu JSON.

## <a name="json-schema"></a>Schéma JSON

Úplné schéma JSON pro soubory materiálů je uvedeno zde. S výjimkou `unlit` vlastností a `ignoreTextureMaps`jsou dostupné vlastnosti podmnožinou vlastností popsaných v částech [o barevných materiálech](../../overview/features/color-materials.md) a modelech materiálů [PBR.](../../overview/features/pbr-materials.md)

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
* [PBR materiály](../../overview/features/pbr-materials.md)