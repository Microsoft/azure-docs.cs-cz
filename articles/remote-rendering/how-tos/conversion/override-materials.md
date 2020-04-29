---
title: Potlačení materiálů během převodu modelů
description: Vysvětluje materiál přepisující pracovní postup v době převodu.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681477"
---
# <a name="override-materials-during-model-conversion"></a>Potlačení materiálů během převodu modelů

Během převodu se k definování materiálů [PBR](../../overview/features/pbr-materials.md) používaných nástrojem pro vykreslování použijí nastavení materiálu ve zdrojovém modelu.
V některých případech [výchozí převod](../../reference/material-mapping.md) neposkytuje požadované výsledky a je třeba provést změny.
Když je model převeden pro použití ve vzdáleném vykreslování Azure, můžete poskytnout soubor pro přepsání materiálu a přizpůsobit tak, jak se převod materiálu provádí na základě jednotlivých materiálů.
Část týkající se [konfigurace převodu modelu](configure-model-conversion.md) obsahuje pokyny pro deklaraci názvu souboru přepisu materiálu.

## <a name="the-override-file-used-during-conversion"></a>Soubor přepsání použitý při převodu

Jednoduchým příkladem je řekněme, že krabicový model má jeden materiál s názvem default. Barva albedo musí být upravena pro použití v ARR.
V takovém případě lze `box_materials_override.json` soubor vytvořit takto:

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

`box_materials_override.json` Soubor je umístěn ve vstupním kontejneru a přidá `ConversionSettings.json` se vedle `box.fbx`, který oznamuje převod, kde najít soubor přepsání (viz [konfigurace převodu modelu](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Při převodu modelu se použijí nová nastavení.

### <a name="color-materials"></a>Barevné materiály

[Barevný materiálový](../../overview/features/color-materials.md) model popisuje nepřetržitě stínovaný povrch, který je nezávislý na osvětlení.
To je užitečné pro prostředky vytvořené pomocí Photogrammetry algoritmů, například.
V souborech přepsání materiálu může být materiál deklarovaný jako barevný materiál nastavením `unlit` na. `true`

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

## <a name="json-schema"></a>Schéma JSON

Úplné schéma JSON pro soubory materiálů je uvedeno zde. S výjimkou `unlit` a `ignoreTextureMaps`jsou dostupné vlastnosti podmnožinou vlastností popsaných v oddílech v [materiálech](../../overview/features/pbr-materials.md) s [barevným materiálem](../../overview/features/color-materials.md) a modelu PBR.

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
* [Materiály PBR](../../overview/features/pbr-materials.md)