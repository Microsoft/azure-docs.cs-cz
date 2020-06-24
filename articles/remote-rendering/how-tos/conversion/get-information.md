---
title: Získání informací o převedeném modelu
description: Popis všech parametrů převodu modelu
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 722d3e218272202074820db442ab1592042c7011
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805013"
---
# <a name="get-information-about-a-converted-model"></a>Získání informací o převedeném modelu

Soubor arrAsset, který je vytvořen pomocí služby pro převod, je určen pouze pro využití ve službě vykreslování. Pokud ale chcete získat přístup k informacím o modelu bez spuštění relace vykreslování, může dojít k nějakým časům. Proto služba převodu umístí soubor JSON vedle souboru arrAsset do výstupního kontejneru. Například pokud `buggy.gltf` je soubor převeden, kontejner výstupu bude obsahovat soubor s názvem `buggy.info.json` vedle převedeného prostředku `buggy.arrAsset` . Obsahuje informace o zdrojovém modelu, převedeném modelu a o samotném převodu.

## <a name="example-info-file"></a>Příklad souboru s *informacemi*

Zde je příklad *informačního* souboru vytvořeného převodem souboru s názvem `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informace v informačním souboru

### <a name="the-files-section"></a>Oddíl *Files*

Tato část obsahuje názvy souborů, které jsou k dispozici.

* `input`: Název zdrojového souboru.
* `output`: Název výstupního souboru, pokud uživatel zadal jiný než výchozí název.

### <a name="the-conversionsettings-section"></a>Část *conversionSettings*

V této části je uložený kopii [ConversionSettings](configure-model-conversion.md#settings-file) , která se zadala při převodu modelu.

### <a name="the-inputinfo-section"></a>Část *inputInfo*

V této části jsou zaznamenány informace o formátu zdrojového souboru.

* `sourceAssetExtension`: Přípona souboru zdrojového souboru.
* `sourceAssetFormat`: Popis formátu zdrojového souboru.
* `sourceAssetFormatVersion`: Verze formátu zdrojového souboru.
* `sourceAssetGenerator`: Název nástroje, který vygeneroval zdrojový soubor, pokud je k dispozici.

### <a name="the-inputstatistics-section"></a>Část *inputStatistics*

V této části najdete informace o zdrojové scéně. Mezi hodnotami v této části a odpovídajícími hodnotami v nástroji, který vytvořil zdrojový model, budou často zjištěny rozdíly. Tyto rozdíly se očekávají, protože model se během exportu a převodu změnil.

* `numMeshes`: Počet částí mřížky, kde každá část může odkazovat na jeden materiál.
* `numFaces`: Celkový počet _trojúhelníků_ v celém modelu. Všimněte si, že síť je během převodu triangulated. Toto číslo přispívá k omezení mnohoúhelníku ve [standardní velikosti virtuálních počítačů pro vykreslování](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: Celkový počet vrcholů v celém modelu.
* `numMaterial`: Celkový počet materiálů v celém modelu.
* `numFacesSmallestMesh`: Počet trojúhelníků v nejmenší mřížce modelu.
* `numFacesBiggestMesh`: Počet trojúhelníků v největší mřížce modelu.
* `numNodes`: Počet uzlů v grafu scény modelu.
* `numMeshUsagesInScene`: Kolikrát uzly odkazují na sítě. Na jednu síť se může odkazovat více než jeden uzel.
* `maxNodeDepth`: Maximální hloubka uzlů v grafu scény.

### <a name="the-outputinfo-section"></a>Část *outputInfo*

V této části jsou zaznamenány Obecné informace o vygenerovaném výstupu.

* `conversionToolVersion`: Verze převaděče modelů.
* `conversionHash`: Hodnota hash dat v rámci arrAsset, která může přispívat k vykreslování. Lze použít k pochopení, zda služba převodu při opakovaném spuštění ve stejném souboru vygenerovala jiný výsledek.

### <a name="the-outputstatistics-section"></a>Část *outputStatistics*

V této části jsou zaznamenány informace vypočítané z převedeného prostředku.

* `numMeshPartsCreated`: Počet sítí v arrAsset. Může se lišit od `numMeshes` v `inputStatistics` části, protože vytváření instancí je ovlivněno procesem převodu.
* `numMeshPartsInstanced`: Počet sítí, které se znovu používají v arrAsset.
* `recenteringOffset`: Když `recenterToOrigin` je povolená možnost v [ConversionSettings](configure-model-conversion.md) , je tato hodnota překlad, který by převedl převedený model zpátky do původní pozice.
* `boundingBox`: Hranice modelu.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](model-conversion.md)
* [Konfigurace převodu modelů](configure-model-conversion.md)
