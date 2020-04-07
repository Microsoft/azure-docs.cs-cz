---
title: Získání informací o převedeném modelu
description: Popis všech parametrů převodu modelu
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681516"
---
# <a name="get-information-about-a-converted-model"></a>Získání informací o převedeném modelu

Soubor arrAsset vytvořený konverzní službou je určen výhradně ke spotřebě asanační službou. Může však napodobuje, když chcete získat přístup k informacím o modelu bez spuštění relace vykreslování. Proto služba převodu umístí soubor JSON vedle souboru arrAsset ve výstupním kontejneru. Pokud je například `buggy.gltf` soubor převeden, bude výstupní kontejner `buggy.info.json` obsahovat soubor `buggy.arrAsset`volaný vedle převedeného datového zdroje . Obsahuje informace o zdrojovém modelu, převedeném modelu a o samotném převodu.

## <a name="example-info-file"></a>Ukázkový *informační* soubor

Zde je příklad *informačnísoubor* vytvořený převodem `buggy.gltf`souboru s názvem :

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

### <a name="the-files-section"></a>Sekce *Soubory*

Tato část obsahuje poskytnuté názvy souborů.

* `input`: Název zdrojového souboru.
* `output`: Název výstupního souboru, pokud uživatel zadal nevýchozí název.

### <a name="the-conversionsettings-section"></a>Oddíl *conversionSettings*

Tato část obsahuje kopii [ConversionSettings](configure-model-conversion.md#settings-file) určené při převodu modelu.

### <a name="the-inputinfo-section"></a>Sekce *inputInfo*

Tato část zaznamenává informace o formátu zdrojového souboru.

* `sourceAssetExtension`: Přípona souboru zdrojového souboru.
* `sourceAssetFormat`: Popis formátu zdrojového souboru.
* `sourceAssetFormatVersion`: Verze formátu zdrojového souboru.
* `sourceAssetGenerator`: Název nástroje, který vygeneroval zdrojový soubor, pokud je k dispozici.

### <a name="the-inputstatistics-section"></a>Sekce *inputStatistics*

Tato část obsahuje informace o zdrojové scéně. Často dochází k nesrovnalostem mezi hodnotami v této části a ekvivalentními hodnotami v nástroji, který zdrojový model vytvořil. Tyto rozdíly se očekávají, protože model získá změny během exportu a převodu kroky.

* `numMeshes`: Počet částí sítě, kde každý díl může odkazovat na jeden materiál.
* `numFaces`: Celkový počet _trojúhelníků_ v celém modelu. Všimněte si, že síť je triangulována během převodu.
* `numVertices`: Celkový počet vrcholů v celém modelu.
* `numMaterial`: Celkový počet materiálů v celém modelu.
* `numFacesSmallestMesh`: Počet trojúhelníků v nejmenší síti modelu.
* `numFacesBiggestMesh`: Počet trojúhelníků v největší síti modelu.
* `numNodes`: Počet uzlů v grafu scény modelu.
* `numMeshUsagesInScene`: Počet odkazů uzlů na ok. Více než jeden uzel může odkazovat na stejnou síť.
* `maxNodeDepth`: Maximální hloubka uzlů v grafu scény.

### <a name="the-outputinfo-section"></a>Sekce *outputInfo*

Tato část zaznamenává obecné informace o generovaném výstupu.

* `conversionToolVersion`: Verze převodníku modelu.
* `conversionHash`: Hodnota hash dat v rámci arrAsset, která může přispět k vykreslování. Lze použít k pochopení, zda služba převodu přinesla jiný výsledek při opětovném spuštění ve stejném souboru.

### <a name="the-outputstatistics-section"></a>Sekce *outputStatistics*

Tato část zaznamenává informace vypočítané z převedeného majetku.

* `numMeshPartsCreated`: Počet ok v arrAsset. Může se `numMeshes` lišit `inputStatistics` od v části, protože instance je ovlivněna procesem převodu.
* `numMeshPartsInstanced`: Počet ok, které jsou znovu použity v arrAsset.
* `recenteringOffset`: Pokud `recenterToOrigin` je povolena možnost v [Nastavení převodu,](configure-model-conversion.md) tato hodnota je překlad, který by přesunul převedený model zpět do původní polohy.
* `boundingBox`: Hranice modelu.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](model-conversion.md)
* [Konfigurace převodu modelu](configure-model-conversion.md)
