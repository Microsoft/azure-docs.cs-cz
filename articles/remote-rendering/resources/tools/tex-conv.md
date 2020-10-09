---
title: Nástroj pro převod textury TexConv
description: Příručka uživatele pro nástroj příkazového řádku TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680021"
---
# <a name="texconv---texture-conversion-tool"></a>Nástroj pro převod textury TexConv

TexConv je nástroj příkazového řádku pro zpracování textur z typických vstupních formátů, jako jsou PNG, TGA, JPEG a DDS, do optimalizovaných formátů pro spotřebu za běhu.
I když je nejběžnějším scénářem převod jednoho vstupního souboru `A.xxx` do optimalizovaného formátu `B.yyy` , nástroj má mnoho dalších možností pro pokročilé použití.

## <a name="command-line-help"></a>Help příkazového řádku

Spuštění TexConv.exe s `--help` parametrem zobrazí seznam všech dostupných možností. TexConv také při spuštění vytiskne použité možnosti, které vám pomohou pochopit, co dělá. Podrobnosti najdete v tomto výstupu.

## <a name="general-usage"></a>Obecné použití

TexConv vždy vytváří **přesně jeden výstupní** soubor. Může použít **více vstupních** souborů k sestavení výstupu z. Pro sestavení je také potřeba **mapování kanálu**, které mu sdělí, který kanál (*červená, zelená, modrá* nebo *alfa*) má převzít ze vstupního souboru a přesunout ho do kanálu výstupní image.

Nejpřímější příkazový řádek s přímým lomítkem je:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` Určuje výstupní soubor a formát.
- `-in0` Určuje první vstupní bitovou kopii.
- `-rgba` informuje o tom, že výstupní Image by měla používat všechny čtyři kanály a že by se měly z této vstupní image považovat za 1:1.

## <a name="multiple-input-files"></a>Více vstupních souborů

Chcete-li sestavit výstup z více vstupních souborů, zadejte každý vstupní soubor pomocí `-in` Možnosti s rostoucím číslem:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Při sestavování cubemap z 2D textury může jeden použít také,,,, `-right` `-left` `-top` `-bottom` `-front` , `-back` nebo `-px` , `-nx` , `-py` , `-ny` , `-pz` , `-nz` .

Aby bylo možné tyto vstupy namapovat do výstupního souboru, je třeba provést správné mapování kanálu.

## <a name="channel-mappings"></a>Mapování kanálů

Možnosti mapování kanálů určují, ze kterého vstupu se mají vyplňovat dané výstupní kanály. Pro každý kanál můžete zadat vstup jednotlivě takto:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Tady by se měly vyplňovat kanály RGB výstupu s použitím první vstupní image, ale červená a modrá se budou zaměnit. Kanál alfa výstupu by byl vyplněn hodnotami z červeného kanálu druhé vstupní image.

Určování mapování pro každý kanál samostatně poskytuje největší flexibilitu. Pro usnadnění práce se stejným způsobem můžete zapsat pomocí operátorů "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Výstupní kanály

K dispozici jsou následující možnosti mapování kanálů:

- `-r`, `-g` , `-b` , `-a` : Tyto určují přiřazení jednoho kanálu.
- `-rg` : Zadejte přiřazení červeného a zeleného kanálu.
- `-rgb` : Zadejte červené, zelené a modré přiřazení kanálu.
- `-rgba` : Určuje všechna čtyři přiřazení kanálu.

V případě, že se zmiňuje jenom kanál R, RG nebo RGB, dá TexConv pokyn k vytvoření výstupního souboru s pouze 1, 2 nebo 3 kanály.

### <a name="input-swizzling"></a>Vstupní swizzling

Když se uvede, která vstupní textura by měla vyplnit výstupní kanál, jedna může Swizzle vstup:

- `-rgba in0` je ekvivalentem `-rgba in0.rgba`
- `-rgba in0.bgra` bude Swizzle vstupní kanály
- `-rgb in0.rrr` provede duplikaci červeného kanálu do všech kanálů.

Jedním z nich může být také vyplňování kanálů buď černou, nebo bílou:

- `-rgb in0 -a white` vytvoří výstupní texturu 4 kanálu, ale nastaví alfa na plně neprůhledné.
- `-rg black -b white` vytvoří zcela modrou texturu.

## <a name="common-options"></a>Společné možnosti

Nejzajímavější možnosti jsou uvedeny níže. Další možnosti jsou uvedeny v `TexConv --help` .

### <a name="output-type"></a>Typ výstupu

- `-type 2D` : Výstup bude běžný 2D obraz.
- `-type Cubemap` : Výstup bude cubemap obrázek. Podporováno pouze pro výstupní soubory DDS. Když je tato možnost zadána, může jedna sestavit cubemap z 6 běžných 2D vstupních obrázků.

### <a name="image-compression"></a>Komprese obrázků

- `-compression none` : Výstupní bitová kopie bude nekomprimovaná.
- `-compression medium` : Pokud je to podporováno, výstupní image bude používat kompresi, aniž by došlo ke ztrátě příliš velkého množství kvality.
- `-compression high` : Pokud je to podporováno, výstupní obrázek bude používat kompresi a kvalitu usmrcení místo menšího souboru.

### <a name="mipmaps"></a>Mipmapy

Ve výchozím nastavení TexConv generuje mipmapy, když ho podporuje formát výstupu.

- `-mipmaps none` : Mipmapy se nevygeneruje.
- `-mipmaps Linear` : Pokud se podporuje, mipmapy se vygeneruje pomocí pole filtru.

### <a name="usage-srgb--gamma-correction"></a>Použití (korekce sRGB/Gamma)

`-usage`Možnost určuje účel výstupu, a proto oznamuje TexConv, zda má být pro vstupní a výstupní soubory použita korekce gama. Použití má vliv pouze na kanály RGB. Kanál alfa se vždycky považuje za "lineární" hodnoty. Pokud není určeno použití, pokusí se režim auto zjistit využití z formátu a názvu první vstupní image. Například výstupní formáty Single a Dual Channel jsou vždy lineární. Podívejte se na výstup a zjistěte, jaké rozhodnutí TexConv.

- `-usage Linear` : Výstupní obrázek obsahuje hodnoty, které nepředstavuje barvy. To je obvykle případ pro kovové a hrubou texturu a také pro všechny druhy masek.

- `-usage Color` : Výstupní obrázek představuje barvu, jako je například difúze/albedo Maps. Ve výstupní hlavičce DDS se nastaví příznak sRGB.

- `-usage HDR` : Výstupní soubor by měl pro kódování použít více než 8 bitů na pixel. V důsledku toho jsou všechny hodnoty uloženy v lineárním prostoru. U textur HDR nezáleží na tom, zda data představují barvy nebo jiná data.

- `-usage NormalMap` : Výstupní obrázek představuje normální mapu v oboru tečny. Hodnoty budou normalizované a mipmap výpočty budou mírně optimalizované.

- `-usage NormalMap_Inverted` : Výstupem je normální mapa na tečně s písmenem Y ukazujícím v opačném směru než vstup.

### <a name="image-rescaling"></a>Změna měřítka obrázku

- `-minRes 64` : Určuje minimální rozlišení výstupu. Pokud je vstupní obrázek menší, bude zvětšený.
- `-maxRes 1024` : Určuje maximální rozlišení výstupu. Pokud je vstupní obrázek větší, bude downscaled.
- `-downscale 1` : Pokud je tato hodnota větší než 0, vstupní obrázky budou poloviční v rozlišení N krát. Použijte to pro použití celkového snížení kvality.

## <a name="examples"></a>Příklady

### <a name="convert-a-color-texture"></a>Převod barevné textury

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Převod normální mapy

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Vytvoření cubemap HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Skvělým zdrojem pro HDR cubemaps je [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Zanesli více obrázků do jednoho

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrakce jednoho kanálu

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
