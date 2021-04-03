---
title: Konfigurace převodu modelů
description: Popis všech parametrů převodu modelu
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205315"
---
# <a name="configure-the-model-conversion"></a>Konfigurace převodu modelů

Tato kapitola dokumentuje možnosti převodu modelu.

## <a name="settings-file"></a>Soubor nastavení

Pokud `<modelName>.ConversionSettings.json` je v vstupním kontejneru vedle vstupního modelu nalezen soubor s názvem `<modelName>.<ext>` , bude použit k poskytnutí další konfigurace pro proces převodu modelu.
Například `box.ConversionSettings.json` by se mělo použít při převodu `box.gltf` .

Obsah souboru by měl vyhovovat následujícímu schématu JSON:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Příkladem souboru `box.ConversionSettings.json` může být:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametry geometrie

* `scaling` – Tento parametr škáluje model jednotně. Škálování lze použít ke zvětšení nebo zmenšení modelu, například k zobrazení stavebního modelu na nejvyšší úrovni tabulky.
Škálování je také důležité, pokud je model definován v jiných jednotkách než měřiče, protože vykreslovací modul očekává měřiče.
Například pokud je model definován v centimetrech, pak použití stupnice 0,01 by mělo vykreslovat model ve správné velikosti.
Některé zdrojové formáty dat (například. FBX) poskytují pomocný parametr škálování jednotky. v takovém případě převod implicitně škáluje model na jednotky měřičů. Implicitní škálování, které poskytuje formát zdroje, se použije nad parametrem škálování.
Konečný faktor škálování se aplikuje na vrcholy geometrie a místní transformace uzlů grafu scény. Škálování pro transformaci kořenové entity zůstane beze změny.

* `recenterToOrigin` -Uvádí, že by měl být model převeden tak, aby jeho ohraničovací rámeček byl zarovnán na střed na začátku.
Pokud je zdrojový model umístěn daleko od počátku, mohou problémy s přesností s plovoucí desetinnou čárkou způsobit vykreslování artefaktů.
Centrování modelu může v této situaci pomáhat.

* `opaqueMaterialDefaultSidedness` -Modul vykreslování předpokládá, že neprůhledné materiály jsou oboustranné.
Pokud tento předpoklad nemá hodnotu true konkrétního modelu, tento parametr by měl být nastaven na hodnotu "SingleSided". Další informace najdete v tématu [ :::no-loc text="single sided"::: vykreslování](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Odstranění duplicit materiálu

* `deduplicateMaterials` – Tento parametr povoluje nebo zakazuje automatickou odstraňování duplicit materiálů, které sdílejí stejné vlastnosti a textury. Odstranění duplicit proběhne po zpracování přepsání materiálu. Tato možnost je ve výchozím nastavení povolená.

* Pokud i po odstranění duplicitních dat model obsahuje více než 65 535 materiálů, služba se pokusí sloučit materiály s podobnými vlastnostmi. Jako poslední možnost, že všechny materiály překračující tento limit budou nahrazeny červeným materiálem o chybách.

![Obrázek ukazuje dvě krychle 68 921 barevných trojúhelníků.](media/mat-dedup.png?raw=true)

Dvě datové krychle 68 921 barevných trojúhelníků. Left: před odstraněním duplicit s barevnými materiály 68 921. Right: po odstranění duplicit s barevnými materiály 64 000. Limit je 65 535 materiálů. (Viz [omezení](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Parametry barevného prostoru

Vykreslovací modul očekává, že hodnoty barev budou v lineárním prostoru.
Pokud je model definován pomocí prostoru hodnot gamma, pak tyto možnosti by měly být nastaveny na hodnotu true.

* `gammaToLinearMaterial` -Převést barvy materiálu z prostoru gamma na lineární místo
* `gammaToLinearVertex` -Převést :::no-loc text="vertex"::: barvy z hodnoty gamma na lineární místo

> [!NOTE]
> Pro soubory FBX jsou tato nastavení standardně nastavená na `true` . U všech ostatních typů souborů je výchozí hodnota `false` .

### <a name="scene-parameters"></a>Parametry scény

* `sceneGraphMode` -Definuje, jak se převede graf scény ve zdrojovém souboru:
  * `dynamic` (výchozí): všechny objekty v souboru se zveřejňují jako [entity](../../concepts/entities.md) v rozhraní API a dají se transformovat nezávisle. Hierarchie uzlů za běhu je shodná se strukturou ve zdrojovém souboru.
  * `static`: Všechny objekty jsou zpřístupněny v rozhraní API, ale nelze je transformovat nezávisle.
  * `none`: Graf scény je sbalený do jednoho objektu.

Každý režim má jiný běhový výkon. V `dynamic` režimu se náklady na výkon lineárně škálují s počtem [entit](../../concepts/entities.md) v grafu, a to i v případě, že se nepřesouvají žádné součásti. `dynamic`Režim použití pouze v případě, že je nutné přesunout součásti jednotlivě, například pro animaci zobrazení rozpadu.

`static`Režim provede export celého grafu scény, ale části v tomto grafu mají konstantní transformaci vzhledem ke své kořenové části. Kořenový uzel objektu je však stále možné přesunout, otočit nebo škálovat bez značných nákladů na výkon. Kromě toho [prostorové dotazy](../../overview/features/spatial-queries.md) vrátí jednotlivé části a každá část se dá upravit pomocí [přepsání stavu](../../overview/features/override-hierarchical-state.md). V tomto režimu je režie za modul runtime na objekt zanedbatelná. Je ideální pro velké scény, kde stále potřebujete kontrolu na jednotlivé objekty, ale ne žádné transformační změny pro jednotlivé objekty.

V `none` režimu je minimální režie za běhu a také mírně lepší doba načítání. V tomto režimu není možné kontrolovat nebo transformovat jednotlivé objekty. Případy použití jsou například modely Photogrammetry, které nemají smysluplný graf scény na prvním místě.

> [!TIP]
> Mnoho aplikací načte více modelů. V závislosti na tom, jak se bude používat, byste měli optimalizovat parametry převodu pro každý model. Pokud například chcete zobrazit model automobilu, který může uživatel oddělit a prozkoumat podrobněji, je nutné ho převést do `dynamic` režimu. Pokud ale kromě toho chcete auto umístit do prostředí pro zobrazení místnosti, tento model je možné převést pomocí `sceneGraphMode` nastavení na `static` nebo dokonce `none` .

### <a name="physics-parameters"></a>Parametry fyzika

* `generateCollisionMesh` – Pokud potřebujete podporu [prostorových dotazů](../../overview/features/spatial-queries.md) pro model, je nutné tuto možnost povolit. V nejhorším případě může vytvoření sítě kolizí zdvojnásobit dobu převodu. Modely s kolize sítí trvá delší dobu načítání a při použití `dynamic` grafu scény mají také vyšší režii na výkon modulu runtime. Pro celkový optimální výkon byste měli zakázat tuto možnost u všech modelů, na kterých nepotřebujete prostorové dotazy.

### <a name="unlit-materials"></a>Unlit materiály

* `unlitMaterials` – Ve výchozím nastavení bude převod preferovat vytváření [materiálů PBR](../../overview/features/pbr-materials.md). Tato možnost dává konvertoru pokyn, aby se všechny materiály nacházely jako s [barevnými materiály](../../overview/features/color-materials.md) . Pokud máte data, která už jsou v rámci osvětlení, jako jsou například modely vytvořené prostřednictvím Photogrammetry, tato možnost umožňuje rychle vyhovět správné konverzi pro všechny materiály bez nutnosti [potlačit jednotlivé materiály](override-materials.md) jednotlivě.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Převod ze starších formátů FBX pomocí modelu Phongova materiálu

* `fbxAssumeMetallic` – Starší verze formátu FBX definují své materiály pomocí modelu Phongova materiálu. Proces převodu musí odvodit, jak se tyto materiály mapují na [model PBR](../../overview/features/pbr-materials.md)vykreslovacího modulu. Obvykle to funguje dobře, ale nejednoznačnost může nastat, když materiál nemá žádné textury, vysoké odlesky a nešedou barvu albedo barvy. V tomto případě musí převod zvolit mezi stanovením priorit horních hodnot, a to tak, že definuje vysoce odrážející kovový materiál, kde barva albedo vyhodnotí pryč nebo nastaví prioritu albedo barvy, což definuje něco jako lesklý barevný plast. Ve výchozím nastavení předpokládá proces převodu, že vysoce odlesky hodnot implikuje kovový materiál v případech, kdy se nejednoznačnost uplatní. Tento parametr může být nastaven na hodnotu `false` pro přepnutí na opak.

### <a name="coordinate-system-overriding"></a>Přepisování systému souřadnic

* `axis` – Pro přepsání souřadnic systémových jednotek-vektory. Výchozí hodnoty jsou `["+x", "+y", "+z"]` . Teoreticky má formát FBX hlavičku, kde jsou tyto vektory definovány, a převod používá tyto informace k transformaci scény. Formát glTF definuje také pevný systém souřadnic. V praxi některé prostředky mají buď nesprávné informace v hlavičce nebo byly uloženy s jinou konvencí souřadnicového systému. Tato možnost umožňuje přepsat souřadnicový systém pro kompenzaci. Například: `"axis" : ["+x", "+z", "-y"]` vyměňuje osu Z a osu Y a zachová změnu hodnoty pera systému tím, že se obrátí směr osy Y.

### <a name="node-meta-data"></a>Meta data uzlu

* `metadataKeys` – Umožňuje zadat klíče vlastností metadat uzlu, které chcete zachovat ve výsledku převodu. Můžete zadat přesné klíče nebo zástupné znaky. Zástupné klíče mají formát "ABC *" a odpovídají libovolnému klíči, který začíná "ABC". Podporované typy hodnot metadat jsou `bool` , `int` , `float` a `string` .

    Pro soubory GLTF tato data pocházejí z [objektu Extras na uzlech](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Pro soubory FBX data pocházejí z `Properties70` dat `Model nodes` . Další podrobnosti najdete v dokumentaci k nástroji 3D Asset.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: formátovat

Je možné upravit :::no-loc text="vertex"::: Formát sítě a opravit tak úspory paměti v obchodě. Dolní nároky na paměť umožňují načíst větší modely nebo dosáhnout vyššího výkonu. V závislosti na vašich datech ale může špatný formát významně ovlivnit kvalitu vykreslování.

> [!CAUTION]
> Změna :::no-loc text="vertex"::: formátu by měla být poslední možnost, když se modely do paměti již nevejdou, nebo když se optimalizuje z hlediska nejlepšího možného výkonu. Změny můžou snadno zavést artefakty pro vykreslování, Jak zjevná, tak i drobné. Pokud si nejste jisti, co se má vyhodnotit, neměli byste výchozí nastavení měnit.

Tyto úpravy jsou možné:

* Konkrétní datové proudy lze explicitně zahrnout nebo vyloučit.
* Přesnost datových proudů může být zmenšena, aby se snížila nároky na paměť.

Následující `vertex` oddíl v `.json` souboru je nepovinný. Pro každou část, která není explicitně určena, se služba převodu vrátí k výchozímu nastavení.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Vynucením komponenty na `NONE` je zaručeno, že výstupní síť nemá příslušný datový proud.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Formáty komponent na :::no-loc text="vertex"::: datový proud

Tyto formáty jsou povolené pro příslušné součásti:

| :::no-loc text="Vertex"::: (komponenta) | Podporované formáty (tučné = výchozí) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT** 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, žádný |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **žádný**|
|běžnou| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|pokračuje| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, žádné |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, žádné |

#### <a name="supported-component-formats"></a>Podporované formáty komponent

Paměťové nároky na formáty jsou následující:

| Formát | Description | Počet bajtů za sekundu :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|Úplná přesnost plovoucí desetinné čárky se dvěma komponentami|8
|16_16_FLOAT|poloviční přesnost s plovoucí desetinnou čárkou pro dvě komponenty|4
|32_32_32_FLOAT|Úplná přesnost s plovoucí desetinnou čárkou pro tři součásti|12
|16_16_16_16_FLOAT|poloviční přesnost s plovoucí desetinnou čárkou pro čtyři komponenty|8
|8_8_8_8_UNSIGNED_NORMALIZED|bajt se čtyřmi komponentami, normalizovaný na `[0; 1]` Rozsah|4
|8_8_8_8_SIGNED_NORMALIZED|bajt se čtyřmi komponentami, normalizovaný na `[-1; 1]` Rozsah|4

#### <a name="best-practices-for-component-format-changes"></a>Osvědčené postupy pro změny formátu součásti

* `position`: Je vzácná, že omezená přesnost je dostačující. **16_16_16_16_FLOAT** zavádí znatelné artefakty kvantizační, i pro malé modely.
* `normal`, `tangent` , `binormal` : Obvykle se tyto hodnoty mění dohromady. Pokud nejsou k dispozici znatelné světelné artefakty, které jsou výsledkem normálního kvantizační, neexistuje žádný důvod ke zvýšení jejich přesnosti. V některých případech ale může být tato součást nastavena na **none**:
  * `normal`, `tangent` a `binormal` jsou potřebné pouze v případě, že by měl být osvětlen alespoň jeden materiál v modelu. V ARR se jedná o případ, kdy se v modelu kdykoli používá [materiál PBR](../../overview/features/pbr-materials.md) .
  * `tangent` a `binormal` jsou potřebné pouze v případě, že některé z materiálů osvětleny používají normální texturu mapy.
* `texcoord0`, `texcoord1` : Souřadnice textury mohou používat omezenou přesnost (**16_16_FLOAT**), pokud jejich hodnoty zůstávají v `[0; 1]` rozsahu a pokud mají určené textury maximální velikost 2048 x 2048 pixelů. Pokud dojde k překročení těchto omezení, kvalita mapování textur se zachová.

#### <a name="example"></a>Příklad

Předpokládejme, že máte model Photogrammetry, který má vloženými osvětlení do textur. Vše, co je potřeba k vykreslení modelu, jsou :::no-loc text="vertex"::: pozice a souřadnice textury.

Ve výchozím nastavení má převaděč předpokládat, že v modelu můžete chtít používat i materiály PBR, takže vygeneruje `normal` `tangent` data, a `binormal` za vás. V důsledku toho je využití paměti na vrcholu `position` (12 bajtů) + `texcoord0` (8 bajtů) + `normal` (4 bajty) + `tangent` (4 bajty) + `binormal` (4 bajt) = 32 bajtů. Větší modely tohoto typu můžou snadno mít spoustu milionů :::no-loc text="vertices"::: výsledků v modelech, které můžou zabírat více GB paměti. Takové velké objemy dat budou mít vliv na výkon a dokonce i nedostatek paměti.

S vědomím, že pro model nikdy nepotřebujete dynamické osvětlení a že všechny souřadnice textury jsou v `[0; 1]` rozsahu, můžete nastavit `normal` , `tangent` , a `binormal` na `NONE` a `texcoord0` na polovinu přesnosti ( `16_16_FLOAT` ), což má za následek jenom 16 bajtů :::no-loc text="vertex"::: . Rozřezání dat sítě na polovinu umožňuje načíst větší modely a potenciálně zvyšuje výkon.

## <a name="memory-optimizations"></a>Optimalizace paměti

Spotřeba paměti načteného obsahu se může stát kritickým bodem v systému vykreslování. Pokud je datová část paměti příliš velká, může dojít k ohrožení výkonu vykreslování nebo k tomu, že se model nenačte úplně. Tento článek popisuje některé důležité strategie, které snižují nároky na paměť.

### <a name="instancing"></a>Vytváření instancí

Vytváření instancí je koncept, ve kterém jsou opakovaně použity sítě pro součásti s odlišnými prostorovými transformacemi, a to na rozdíl od každé části odkazující na svou vlastní jedinečnou geometrii. Vytváření instancí má významný vliv na nároky na paměť.
Příklady případů použití pro vytváření instancí jsou šrouby v modelu motoru nebo židle v modelu architektury.

> [!NOTE]
> Vytváření instancí může zlepšit spotřebu paměti (a tím i časy načítání), ale vylepšení na straně výkonu vykreslování jsou nevýznamná.

Služba konverze ctí vytváření instancí, pokud jsou části ve zdrojovém souboru označeny odpovídajícím způsobem. Převod však neprovádí další hloubkovou analýzu dat sítě k identifikaci opakovaně použitelných částí. Nástroj pro vytváření obsahu a jeho exportní kanál jsou tedy rozhodujícím kritériem pro správné nastavení vytváření instancí.

Jednoduchý způsob, jak otestovat, zda jsou informace o vytváření instancí při převodu zachované, je podívat se na [výstupní statistiky](get-information.md#example-info-file)konkrétně na `numMeshPartsInstanced` člena. Pokud je hodnota pro `numMeshPartsInstanced` větší než nula, znamená to, že se sítě sdílí mezi instancemi.

#### <a name="example-instancing-setup-in-3ds-max"></a>Příklad: vytváření instancí v instalaci v nástroji 3ds Max

Modul [Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) obsahuje odlišné režimy klonování objektů **`Copy`** , **`Instance`** které se **`Reference`** chovají jinak, s ohledem na vytváření instancí v exportovaném `.fbx` souboru.

![Klonování v 3ds Max](./media/3dsmax-clone-object.png)

* **`Copy`** : V tomto režimu se naklonuje síť, takže se nepoužijí žádné vytváření instancí ( `numMeshPartsInstanced` = 0).
* **`Instance`** : Tyto dva objekty sdílejí stejnou síť, takže se používají vytváření instancí ( `numMeshPartsInstanced` = 1).
* **`Reference`** : V geometrií lze použít rozdílné modifikátory, takže Exportér vybírá konzervativní přístup a nepoužívá vytváření instancí ( `numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Režim skládání na základě hloubky

Pokud se jedná o problém, nakonfigurujte modul pro vykreslování pomocí [režimu kompozice na základě hloubky](../../concepts/rendering-modes.md#depthbasedcomposition-mode). V tomto režimu je datová část GPU distribuována mezi více GPU.

### <a name="decrease-vertex-size"></a>Zmenšit velikost vrcholu

Jak je popsáno v části [osvědčené postupy pro změny formátu komponent](configure-model-conversion.md#best-practices-for-component-format-changes) , úprava formátu vrcholu může snížit nároky na paměť. Tato možnost by však měla být poslední možností.

### <a name="texture-sizes"></a>Velikosti textury

V závislosti na typu scénáře mohou množství dat textury převážit velikost paměti používané pro data sítě. Modely Photogrammetry jsou kandidáti.
Konfigurace převodu neposkytuje způsob, jak automaticky škálovat textury. V případě potřeby je potřeba škálovat texturu jako krok předběžného zpracování na straně klienta. Krok převodu ale vyberte vhodný [Formát komprese textury](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11):

* `BC1` pro neprůhledné textury barev
* `BC7` pro zdrojové textury barev s alfa kanálem

Vzhledem k tomu, že formát `BC7` má dvojnásobek paměti `BC1` , která je v porovnání s, je důležité zajistit, aby vstupní textury neposkytovaly alfa kanál zbytečně.

## <a name="typical-use-cases"></a>Typické případy použití

Hledání správného nastavení importu pro daný případ použití může být zdlouhavý proces. Na druhé straně nastavení převodu může mít významný dopad na výkon modulu runtime.

Existují určité třídy případů použití, které mají nárok na konkrétní optimalizace. Některé příklady jsou uvedeny níže.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Případ použití: vizualizace architektury/Velká vnější mapy

* Tyto typy scén mají být statické, což znamená, že nepotřebují pohyblivé části. V důsledku toho `sceneGraphMode` může být nastavení nastaveno na `static` nebo dokonce `none` , což zvyšuje výkon modulu runtime. V `static` režimu můžete kořenový uzel scény pořád přesunout, otočit a škálovat, například k dynamickému přepínání mezi 1:1 škálou (pro první zobrazení osob) a v horním zobrazení tabulky.

* Když potřebujete přesunout části kolem, obvykle to znamená, že potřebujete podporu pro raycasts nebo jiné [prostorové dotazy](../../overview/features/spatial-queries.md), abyste je mohli vybrat na prvním místě. Na druhé straně platí, že pokud nechcete něco přesunout, je pravděpodobné, že nepotřebujete, aby se účastnil prostorových dotazů, a proto může `generateCollisionMesh` příznak vypnout. Tento přepínač má výrazný dopad na časy převodu, časy načítání a také náklady na aktualizaci za běhu za sekundu.

* Pokud aplikace nepoužívá [vyjmuté plochy](../../overview/features/cut-planes.md), `opaqueMaterialDefaultSidedness` příznak by měl být vypnutý. Nárůst výkonu je obvykle 20% až 30%. Vyjmuté roviny je stále možné používat, ale při hledání vnitřních částí objektů nebudou fungovat žádné pozadí, což vypadá s čítačem. Další informace najdete v tématu [ :::no-loc text="single sided"::: vykreslování](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Případ použití: Photogrammetry modely

Při vykreslování modelů Photogrammetry obvykle není nutné mít graf scény, takže můžete nastavit na `sceneGraphMode` `none` . Vzhledem k tomu, že tyto modely zřídka obsahují složitý graf scény, který začíná, by měl být dopad této možnosti nedůležitý, i když.

Vzhledem k tomu, že se osvětlení již vloženými do textur, není nutné provádět dynamické osvětlení. Proto:

* Nastavením `unlitMaterials` příznaku `true` zapnete všechny materiály na unlit [barevné materiály](../../overview/features/color-materials.md).
* Odstraňte nepotřebná data z formátu vrcholu. Podívejte se na výše uvedený [příklad](#example) .

### <a name="use-case-visualization-of-compact-machines-etc"></a>Případ použití: vizualizace kompaktních počítačů atd.

V těchto případech mají modely často velmi vysoký detail v rámci malých objemů. Zobrazovací jednotka je silně optimalizovaná tak, aby tyto případy dobře zpracovávala. Většina optimalizací uvedených v předchozím případu použití se ale netýká:

* Jednotlivé části by měly být vybrané a pohyblivé, takže `sceneGraphMode` musí být ponecháno na `dynamic` .
* Přetypování do ray jsou obvykle nedílnou součástí aplikace, takže je potřeba vygenerovat sítě kolizí.
* Vyjmuté roviny vypadají lépe s `opaqueMaterialDefaultSidedness` povoleným příznakem.

## <a name="deprecated-features"></a>Zastaralé funkce:

Poskytování nastavení pomocí souboru filename, který není specifický pro model, `conversionSettings.json` je stále podporováno, ale zastaralé.
Místo toho prosím použijte název specifický pro daný model `<modelName>.ConversionSettings.json` .

Použití `material-override` nastavení k identifikaci [souboru přepisu materiálu](override-materials.md) v souboru nastavení převodu je stále podporováno, ale zastaralé. Místo toho prosím použijte název specifický pro daný model `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Další kroky

* [Převod modelu](model-conversion.md)
* [Barevné materiály](../../overview/features/color-materials.md)
* [Materiály PBR](../../overview/features/pbr-materials.md)
* [Potlačení materiálů během převodu modelů](override-materials.md)