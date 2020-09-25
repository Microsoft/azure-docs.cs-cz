---
title: Příprava souborů pro převod
description: Doporučení, jak nejlépe umístit soubory do vstupního kontejneru
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318071"
---
# <a name="laying-out-files-for-conversion"></a>Příprava souborů pro převod

Aby bylo možné správně zpracovat Asset, služba převodu musí být schopna najít všechny vstupní soubory.
Ty se skládají z převáděného hlavního souboru assetu a obvykle některých dalších souborů, na které odkazuje cesta v souboru prostředků.
Požadavek na převod assetu má dva parametry, které určují, jak služba převodu nalezne tyto soubory: `input.folderPath` (to je volitelné) a `input.inputAssetPath` .
Jsou plně dokumentovány na stránce [převod REST API](conversion-rest-api.md) .
Pro účely rozložení souborů je důležité si uvědomit, že `folderPath` Určuje kompletní sadu souborů, které jsou k dispozici pro službu Převod při zpracování assetu.

## <a name="placing-files-so-they-can-be-found"></a>Umístění souborů, aby bylo možné je vyhledat

Když zdrojový prostředek využívá externí soubory, cesty k těmto souborům budou uloženy v rámci assetu.
Služba konverze musí interpretovat tyto cesty v systému souborů, který se liší od původního systému souborů v prostředku.
Pokud jsou cesty uloženy jako relativní cesty a relativní umístění mezi zdrojovým prostředkem a soubor, na který odkazuje, je nezměněno, je pro službu převod snadno možné najít odkazovaný soubor.

> [!Note]
> Doporučujeme umístit soubory do vstupního kontejneru, aby relativní umístění souborů byla stejná jako při vytvoření assetu.

> [!Note]
> Upřednostňujeme vytváření prostředků, které nesou relativní cesty.
> Kurz o [Nastavení materiálů pro aplikaci 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) nabízí jako příklad, jak zajistit, aby Asset používal relativní cesty.

## <a name="finding-textures"></a>Hledání textur

V důsledku mnoha způsobů, jak lze vygenerovat prostředky, musí být převodová služba flexibilní.
Konkrétně musí zpracovávat situace, kdy cesty v assetu a umístění textur se přesně neshodují.
Příkladem je, že se generují prostředky obsahující absolutní cesty, protože tyto cesty nikdy nebudou odpovídat systému souborů používaného službou převodu.
Aby bylo možné tuto situaci zvládnout mezi ostatními, používáme k hledání textur nejlepší přístup.

Algoritmus pro lokalizaci textur je následující: vzhledem k cestě, která je uložená v prostředku, najděte nejdelší příponu sub Path, která se při použití jako relativní cesty z umístění zdrojového prostředku používá, a cílí na soubor, který existuje.
Pokud žádná taková dílčí cesta (včetně celé cesty) cílí na soubor, textura se považuje za chybějící.

Vezměte v úvahu následující systém souborů contrived: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Pokud myAsset. FBX odkazuje na texturu s relativní cestou `..\Textures\MyAssetTextures\myTexture.png` , pak služba konverze použije soubor E. Ve skutečnosti může použít kterýkoli ze souborů A, C a E, pokud existují, se upřednostňovaným souborem E, protože se nachází v nejdelší příponě.
Soubory B a D se nikdy nepoužijí, protože není `Textures\myTexture.png` součástí žádné přípony uložené cesty.
Pokud prostředek místo toho obsahuje cesty `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` nebo `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , služba konverze bude schopna najít soubory a a C, pokud existují (předkládání C přes A). Tento způsob však nelze nalézt a soubor bude nutné znovu umístit.
To může být opraveno přesunutím složky textury vedle daného prostředku.

> [!Note]
> Pokud se textury nenaleznou, je možné problém vyřešit tím, že je zajištěno, že Asset je na stejné úrovni jako nějaký podstrom, který obsahuje textury.

## <a name="next-steps"></a>Další kroky

- [Převod modelu](model-conversion.md)
