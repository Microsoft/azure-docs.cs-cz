---
title: TexConv - Nástroj pro konverzi textur
description: Uživatelská příručka pro nástroj příkazového řádku TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680021"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Nástroj pro konverzi textur

TexConv je nástroj příkazového řádku pro zpracování textur z typických vstupních formátů, jako jsou PNG, TGA, JPEG a DDS, do optimalizovaných formátů pro spotřebu za běhu.
Zatímco nejběžnější scénář je převést jeden `A.xxx` vstupní soubor `B.yyy`do optimalizovaného formátu , nástroj má mnoho dalších možností pro pokročilé použití.

## <a name="command-line-help"></a>Nápověda k příkazovému řádku

Spuštění texconv.exe `--help` s parametrem zobrazí všechny dostupné možnosti. Kromě toho TexConv vytiskne použité možnosti při spuštění, aby pomohl pochopit, co dělá. Podrobnosti naleznete v tomto výstupu.

## <a name="general-usage"></a>Obecné použití

TexConv vždy produkuje **přesně jeden výstupní** soubor. Může použít **více vstupních** souborů k sestavení výstupu z. Pro sestavení také potřebuje **mapování kanálu**, které mu říká, který kanál *(červená, zelená, modrá* nebo *alfa)* má odevztužit, ze kterého vstupního souboru, a přesunout jej do kanálu, do kterého výstupního obrazu.

Nejvíce přímočarý příkazový řádek je tento:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`určuje výstupní soubor a formát
- `-in0`určuje první vstupní obraz
- `-rgba`informuje, že výstupní obraz by měl používat všechny čtyři kanály a že by měly být odebrány 1:1 ze vstupního obrazu

## <a name="multiple-input-files"></a>Více vstupních souborů

Chcete-li sestavit výstup z více vstupních `-in` souborů, zadejte každý vstupní soubor pomocí volby s rostoucím počtem:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Při sestavování krychle map z 2D `-right`textur `-left` `-top`lze `-bottom` `-front`také `-back` `-px`použít `-nx` `-py`, `-ny` `-pz`, `-nz`, , nebo , , , , , .

Chcete-li mapovat tyto vstupy do výstupního souboru, je potřeba správné mapování kanálu.

## <a name="channel-mappings"></a>Mapování kanálů

Možnosti mapování kanálů určují, ze kterého vstupu mají být vyplníny dané výstupní kanály. Vstup pro každý kanál můžete určit individuálně takto:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Zde by rgb kanály výstupu byly vyplněny pomocí prvního vstupního obrazu, ale červená a modrá se prohodí. Alfa kanál výstupu by byl vyplněn hodnotami z červeného kanálu druhého vstupního obrazu.

Určení mapování pro každý kanál zvlášť poskytuje největší flexibilitu. Pro pohodlí totéž lze napsat pomocí "swizzling" operátory:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Výstupní kanály

K dispozici jsou následující možnosti mapování kanálů:

- `-r`, `-g` `-b`, `-a` , : Tyto zadání přiřazení jednoho kanálu
- `-rg`: Určete přiřazení červeného a zeleného kanálu.
- `-rgb`: Zadejte přiřazení červeného, zeleného a modrého kanálu.
- `-rgba`: Určuje všechna čtyři přiřazení kanálů.

Zmiňuje pouze R, RG nebo RGB kanál, instruuje TexConv vytvořit výstupní soubor pouze s 1, 2 nebo 3 kanály, resp.

### <a name="input-swizzling"></a>Vstupní swizzling

Když je uvedeno, která vstupní textura by měla vyplnit, který výstupní kanál, lze swizzle vstup:

- `-rgba in0`je ekvivalentní`-rgba in0.rgba`
- `-rgba in0.bgra`bude swizzle vstupní kanály
- `-rgb in0.rrr`duplikuje červený kanál do všech kanálů

Jeden může také vyplnit kanály buď černé nebo bílé:

- `-rgb in0 -a white`vytvoří 4kanálovou výstupní texturu, ale nastaví alfa na zcela neprůhlednou
- `-rg black -b white`vytvoří zcela modrou texturu

## <a name="common-options"></a>Běžné možnosti

Nejzajímavější možnosti jsou uvedeny níže. Další možnosti `TexConv --help`jsou uvedeny v aplikaci .

### <a name="output-type"></a>Typ výstupu

- `-type 2D`: Výstupem bude běžný 2D obraz.
- `-type Cubemap`: Výstupem bude obraz cubemap. Podporováno pouze pro výstupní soubory DDS. Když je to toto zadáno, lze sestavit cubemap z 6 běžných 2D vstupních obrazů.

### <a name="image-compression"></a>Komprese obrazu

- `-compression none`: Výstupní obraz bude nekomprimovaný.
- `-compression medium`: Pokud je podporován, bude výstupní obraz používat kompresi, aniž by byla obětována příliš vysoká kvalita.
- `-compression high`: Pokud je podporován, bude výstupní obraz používat kompresi a kvalitu obětování ve prospěch menšího souboru.

### <a name="mipmaps"></a>Mipmapy

Ve výchozím nastavení TexConv generuje mipmaps, když to podporuje výstupní formát.

- `-mipmaps none`: Mipmapy nebudou generovány.
- `-mipmaps Linear`: Pokud je podporováno, budou mipmapy generovány pomocí filtru rámečku.

### <a name="usage-srgb--gamma-correction"></a>Použití (korekce sRGB / gama)

Tato `-usage` možnost určuje účel výstupu a tedy texconv říká, zda má použít gama korekci na vstupní a výstupní soubory. Použití ovlivňuje pouze kanály RGB. Alfa kanál je vždy považován za "lineární" hodnoty. Pokud použití není zadáno, režim "auto" se pokusí zjistit použití z formátu a názvu souboru prvního vstupního obrazu. Například jednokanálové a dvoukanálové výstupní formáty jsou vždy lineární. Zkontrolujte výstup, abyste zjistili, jaké rozhodnutí TexConv učinil.

- `-usage Linear`: Výstupní obraz obsahuje hodnoty, které nepředstavují barvy. To je typicky případ pro kovové a drsnosti textury, stejně jako všechny druhy masek.

- `-usage Color`: Výstupní obraz představuje barvu, například mapy difuzní/albedo. Příznak sRGB bude nastaven ve výstupní hlavičce DDS.

- `-usage HDR`: Výstupní soubor by měl pro kódování používat více než 8 bitů na pixel. V důsledku toho jsou všechny hodnoty uloženy v lineárním prostoru. U textur HDR nezáleží na tom, zda data představují barvu nebo jiná data.

- `-usage NormalMap`: Výstupní obraz představuje normální mapu tangencionálního prostoru. Hodnoty budou normalizovány a mipmap výpočtu bude mírně optimalizován.

- `-usage NormalMap_Inverted`: Výstup je normální mapa tangenciálního prostoru s ukazatelem Y v opačném směru než vstup.

### <a name="image-rescaling"></a>Změna měřítka obrazu

- `-minRes 64`: Určuje minimální rozlišení výstupu. Pokud je vstupní obraz menší, dojde k jeho zvětšení.
- `-maxRes 1024`: Určuje maximální rozlišení výstupu. Pokud je vstupní obraz větší, dojde k jeho zmenšení.
- `-downscale 1`: Pokud je tato velikost větší než 0, budou vstupní obrazy v časech Rozlišení N sníženy na polovinu. Použijte toto použít celkové snížení kvality.

## <a name="examples"></a>Příklady

### <a name="convert-a-color-texture"></a>Převod barevné textury

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Převést normální mapu

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Vytvoření mapy datové kosti HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Skvělým zdrojem pro HDR cubemaps je [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Upéct více obrázků do jednoho

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrahování jednoho kanálu

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
