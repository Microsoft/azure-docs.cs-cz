---
title: Konfigurace převodu modelů
description: Popis všech parametrů převodu modelu
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681568"
---
# <a name="configure-the-model-conversion"></a>Konfigurace převodu modelů

Tato kapitola dokumentuje možnosti převodu modelu.

## <a name="settings-file"></a>Soubor nastavení

Pokud je soubor s `ConversionSettings.json` názvem nalezen ve vstupním kontejneru vedle vstupního modelu, je použit k poskytnutí dodatečné konfigurace pro proces převodu modelu.

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
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Příkladem `ConversionSettings.json` souboru může být:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametry geometrie

* `scaling`– Tento parametr škáluje model jednotně. Škálování lze použít ke zvětšení nebo zmenšení modelu, například k zobrazení stavebního modelu na nejvyšší úrovni tabulky. Vzhledem k tomu, že vykreslovací modul očekává, že se v měřičích určí jiné důležité použití tohoto parametru, nastanou jiné důležité použití tohoto parametru, když je model definován v různých jednotkách. Například pokud je model definován v centimetrech, pak použití stupnice 0,01 by mělo vykreslovat model ve správné velikosti.
Některé zdrojové formáty dat (například. FBX) poskytují pomocný parametr škálování jednotky. v takovém případě převod implicitně škáluje model na jednotky měřičů. Implicitní škálování, které poskytuje formát zdroje, se použije nad parametrem škálování.
Konečný faktor škálování se aplikuje na vrcholy geometrie a místní transformace uzlů grafu scény. Škálování pro transformaci kořenové entity zůstane beze změny.

* `recenterToOrigin`-Uvádí, že by měl být model převeden tak, aby jeho ohraničovací rámeček byl zarovnán na střed na začátku.
Centrování je důležité, pokud je zdrojový model umístěn daleko od počátku, protože v takovém případě problémy s přesností plovoucí desetinné čárky mohou způsobit vykreslování artefaktů.

* `opaqueMaterialDefaultSidedness`-Modul vykreslování předpokládá, že neprůhledné materiály jsou oboustranné.
Pokud to není zamýšlené chování, tento parametr by měl být nastaven na "SingleSided". Další informace najdete v tématu [vykreslování s jednou stranou](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Přepsání materiálu

* `material-override`– Tento parametr umožňuje zpracovat [přizpůsobení materiálů během převodu](override-materials.md).

### <a name="color-space-parameters"></a>Parametry barevného prostoru

Vykreslovací modul očekává, že hodnoty barev budou v lineárním prostoru.
Pokud je model definován pomocí prostoru hodnot gamma, pak tyto možnosti by měly být nastaveny na hodnotu true.

* `gammaToLinearMaterial`-Převést barvy materiálu z prostoru gamma na lineární místo
* `gammaToLinearVertex`-Převést barvy vrcholů z prostoru gamma na lineární místo

> [!NOTE]
> Pro soubory FBX jsou tato nastavení standardně `true` nastavená na. U všech ostatních typů souborů je `false`výchozí hodnota.

### <a name="scene-parameters"></a>Parametry scény

* `sceneGraphMode`-Definuje, jak se převede graf scény ve zdrojovém souboru:
  * `dynamic`(výchozí): všechny objekty v souboru se zveřejňují jako [entity](../../concepts/entities.md) v rozhraní API a dají se transformovat nezávisle. Hierarchie uzlů za běhu je shodná se strukturou ve zdrojovém souboru.
  * `static`: Všechny objekty jsou zpřístupněny v rozhraní API, ale nelze je transformovat nezávisle.
  * `none`: Graf scény je sbalený do jednoho objektu.

Každý režim má jiný běhový výkon. V `dynamic` režimu se náklady na výkon lineárně škálují s počtem [entit](../../concepts/entities.md) v grafu, a to i v případě, že se nepřesouvají žádné součásti. Mělo by být použito pouze při přesunu částí, které jsou pro aplikaci nezbytné, například pro animaci "zobrazení rozpadu".

`static` Režim provede export celého grafu scény, ale části v tomto grafu mají konstantní transformaci vzhledem ke své kořenové části. Kořenový uzel objektu je však stále možné přesunout, otočit nebo škálovat bez značných nákladů na výkon. Kromě toho [prostorové dotazy](../../overview/features/spatial-queries.md) vrátí jednotlivé části a každá část se dá upravit pomocí [přepsání stavu](../../overview/features/override-hierarchical-state.md). V tomto režimu je režie za modul runtime na objekt zanedbatelná. Je ideální pro velké scény, kde stále potřebujete kontrolu na jednotlivé objekty, ale ne žádné transformační změny pro jednotlivé objekty.

V `none` režimu je minimální režie za běhu a také mírně lepší doba načítání. V tomto režimu není možné kontrolovat nebo transformovat jednotlivé objekty. Případy použití jsou například modely Photogrammetry, které nemají smysluplný graf scény na prvním místě.

> [!TIP]
> Mnoho aplikací načte více modelů. V závislosti na tom, jak se bude používat, byste měli optimalizovat parametry převodu pro každý model. Pokud například chcete zobrazit model automobilu, který může uživatel oddělit a prozkoumat podrobněji, je nutné ho převést do `dynamic` režimu. Pokud ale kromě toho chcete auto umístit do prostředí pro zobrazení místnosti, tento model je možné převést pomocí `sceneGraphMode` nastavení na `static` nebo dokonce. `none`

### <a name="physics-parameters"></a>Parametry fyzika

* `generateCollisionMesh`– Pokud potřebujete podporu [prostorových dotazů](../../overview/features/spatial-queries.md) pro model, je nutné tuto možnost povolit. V nejhorším případě může vytvoření sítě kolizí zdvojnásobit dobu převodu. Modely s kolize sítí trvá delší dobu načítání a při použití grafu `dynamic` scény mají také vyšší režii na výkon modulu runtime. Pro celkový optimální výkon byste měli zakázat tuto možnost u všech modelů, na kterých nepotřebujete prostorové dotazy.

### <a name="unlit-materials"></a>Unlit materiály

* `unlitMaterials`– Ve výchozím nastavení bude převod preferovat vytváření [materiálů PBR](../../overview/features/pbr-materials.md). Tato možnost dává konvertoru pokyn, aby se všechny materiály nacházely jako s [barevnými materiály](../../overview/features/color-materials.md) . Pokud máte data, která už jsou v rámci osvětlení, jako jsou například modely vytvořené prostřednictvím Photogrammetry, tato možnost umožňuje rychle vyhovět správné konverzi pro všechny materiály bez nutnosti [potlačit jednotlivé materiály](override-materials.md) jednotlivě.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Převod ze starších formátů FBX pomocí modelu Phongova materiálu

* `fbxAssumeMetallic`– Starší verze formátu FBX definují své materiály pomocí modelu Phongova materiálu. Proces převodu musí odvodit, jak se tyto materiály mapují na [model PBR](../../overview/features/pbr-materials.md)vykreslovacího modulu. Obvykle to funguje dobře, ale nejednoznačnost může nastat, když materiál nemá žádné textury, vysoké odlesky a nešedou barvu albedo barvy. V tomto případě musí převod zvolit mezi stanovením priorit horních hodnot, a to tak, že definuje vysoce odrážející kovový materiál, kde barva albedo vyhodnotí pryč nebo nastaví prioritu albedo barvy, což definuje něco jako lesklý barevný plast. Ve výchozím nastavení předpokládá proces převodu, že vysoce odlesky hodnot implikuje kovový materiál v případech, kdy se nejednoznačnost uplatní. Tento parametr může být nastaven na `false` hodnotu pro přepnutí na opak.

### <a name="coordinate-system-overriding"></a>Přepisování systému souřadnic

* `axis`– Pro přepsání souřadnic systémových jednotek-vektory. Výchozí hodnoty jsou `["+x", "+y", "+z"]`. Teoreticky má formát FBX hlavičku, kde jsou tyto vektory definovány, a převod používá tyto informace k transformaci scény. Formát glTF definuje také pevný systém souřadnic. V praxi některé prostředky mají buď nesprávné informace v hlavičce nebo byly uloženy s jinou konvencí souřadnicového systému. Tato možnost umožňuje přepsat souřadnicový systém pro kompenzaci. Například: `"axis" : ["+x", "+z", "-y"]` vyměňuje osu Z a osu Y a zachová změnu hodnoty pera systému tím, že se obrátí směr osy Y.

### <a name="vertex-format"></a>Formát vrcholu

Je možné upravit formát vrcholu pro síť a opravit tak úspory paměti. Dolní nároky na paměť umožňují načíst větší modely nebo dosáhnout vyššího výkonu. V závislosti na vašich datech ale může špatný formát významně ovlivnit kvalitu vykreslování.

> [!CAUTION]
> Změna formátu vrcholu by měla být poslední možnost, pokud se už modely nevejdou do paměti, nebo když se optimalizuje kvůli nejlepšímu možnému výkonu. Změny můžou snadno zavést artefakty pro vykreslování, Jak zjevná, tak i drobné. Pokud si nejste jisti, co se má vyhodnotit, neměli byste výchozí nastavení měnit.

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

Vynucením komponenty na `NONE`je zaručeno, že výstupní síť nemá příslušný datový proud.

#### <a name="component-formats-per-vertex-stream"></a>Formáty komponent na datový proud vrcholu

Tyto formáty jsou povolené pro příslušné součásti:

| Součást vrcholu | Podporované formáty (tučné = výchozí) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, žádný |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **žádný**|
|běžnou| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|pokračuje| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, žádné |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, žádné |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, žádné |

#### <a name="supported-component-formats"></a>Podporované formáty komponent

Paměťové nároky na formáty jsou následující:

| Formát | Popis | Počet bajtů na vrchol |
|:-------|:------------|:---------------|
|32_32_FLOAT|Úplná přesnost plovoucí desetinné čárky se dvěma komponentami|8
|16_16_FLOAT|poloviční přesnost s plovoucí desetinnou čárkou pro dvě komponenty|4
|32_32_32_FLOAT|Úplná přesnost s plovoucí desetinnou čárkou pro tři součásti|12
|16_16_16_16_FLOAT|poloviční přesnost s plovoucí desetinnou čárkou pro čtyři komponenty|8
|8_8_8_8_UNSIGNED_NORMALIZED|bajt se čtyřmi komponentami, normalizovaný na `[0; 1]` rozsah|4
|8_8_8_8_SIGNED_NORMALIZED|bajt se čtyřmi komponentami, normalizovaný na `[-1; 1]` rozsah|4

#### <a name="best-practices-for-component-format-changes"></a>Osvědčené postupy pro změny formátu součásti

* `position`: Je vzácná, že omezená přesnost je dostačující. **16_16_16_16_FLOAT** zavádí znatelné artefakty kvantizační, i pro malé modely.
* `normal`, `tangent`, `binormal`: Obvykle se tyto hodnoty mění dohromady. Pokud nejsou k dispozici znatelné světelné artefakty, které jsou výsledkem normálního kvantizační, neexistuje žádný důvod ke zvýšení jejich přesnosti. V některých případech ale může být tato součást nastavena na **none**:
  * `normal`, `tangent`a `binormal` jsou potřebné pouze v případě, že by měl být osvětlen alespoň jeden materiál v modelu. V ARR se jedná o případ, kdy se v modelu kdykoli používá [materiál PBR](../../overview/features/pbr-materials.md) .
  * `tangent`a `binormal` jsou potřebné pouze v případě, že některé z materiálů osvětleny používají normální texturu mapy.
* `texcoord0`, `texcoord1` : Souřadnice textury mohou používat omezenou přesnost (**16_16_FLOAT**), pokud jejich hodnoty zůstávají v `[0; 1]` rozsahu a pokud mají určené textury maximální velikost 2048 x 2048 pixelů. Pokud dojde k překročení těchto omezení, kvalita mapování textur se zachová.

#### <a name="example"></a>Příklad

Předpokládejme, že máte model Photogrammetry, který má vloženými osvětlení do textur. Vše potřebné k vykreslení modelu je pozice vrcholu a souřadnice textury.

Ve výchozím nastavení má převaděč předpokládat, že v modelu můžete chtít používat i materiály PBR, takže vygeneruje `normal`data, `tangent`a `binormal` za vás. V důsledku toho je `position` využití paměti na vrcholu (12 bajtů) `texcoord0` + (8 bajtů) `normal` + (4 bajty) `tangent` + (4 bajty) `binormal` + (4 bajt) = 32 bajtů. Větší modely tohoto typu můžou snadno mít spoustu milionů vrcholů v důsledku modelů, které můžou zabírat více GB paměti. Takové velké objemy dat budou mít vliv na výkon a dokonce i nedostatek paměti.

S vědomím, že v modelu nikdy nepotřebujete dynamické osvětlení a že všechny souřadnice textury jsou `[0; 1]` v rozsahu, můžete nastavit `normal`, `tangent`, a `binormal` na `NONE` a `texcoord0` na poloviční přesnost (`16_16_FLOAT`), výsledkem je pouze 16 bajtů na vrchol. Rozřezání dat sítě na polovinu umožňuje načíst větší modely a potenciálně zvyšuje výkon.

## <a name="typical-use-cases"></a>Typické případy použití

Hledání správného nastavení importu pro daný případ použití může být zdlouhavý proces. Na druhé straně nastavení převodu může mít významný dopad na výkon modulu runtime.

Existují určité třídy případů použití, které mají nárok na konkrétní optimalizace. Některé příklady jsou uvedeny níže.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Případ použití: vizualizace architektury/Velká vnější mapy

* Tyto typy scén mají být statické, což znamená, že nepotřebují pohyblivé části. V `sceneGraphMode` důsledku toho může být nastavení nastaveno `static` na nebo `none`dokonce, což zvyšuje výkon modulu runtime. V `static` režimu můžete kořenový uzel scény pořád přesunout, otočit a škálovat, například k dynamickému přepínání mezi 1:1 škálou (pro první zobrazení osob) a v horním zobrazení tabulky.

* Když potřebujete přesunout části kolem, obvykle to znamená, že potřebujete podporu pro raycasts nebo jiné [prostorové dotazy](../../overview/features/spatial-queries.md), abyste je mohli vybrat na prvním místě. Na druhé straně platí, že pokud nechcete něco přesunout, je pravděpodobné, že nepotřebujete, aby se účastnil prostorových dotazů, a proto může `generateCollisionMesh` příznak vypnout. Tento přepínač má výrazný dopad na časy převodu, časy načítání a také náklady na aktualizaci za běhu za sekundu.

* Pokud aplikace nepoužívá [vyjmuté plochy](../../overview/features/cut-planes.md), `opaqueMaterialDefaultSidedness` příznak by měl být vypnutý. Nárůst výkonu je obvykle 20% až 30%. Vyjmuté roviny je stále možné používat, ale při hledání vnitřních částí objektů nebudou fungovat žádné pozadí, což vypadá s čítačem. Další informace najdete v tématu [vykreslování s jednou stranou](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Případ použití: Photogrammetry modely

Při vykreslování modelů Photogrammetry obvykle není nutné mít graf scény, takže můžete nastavit `sceneGraphMode` na. `none` Vzhledem k tomu, že tyto modely zřídka obsahují složitý graf scény, který začíná, by měl být dopad této možnosti nedůležitý, i když.

Vzhledem k tomu, že se osvětlení již vloženými do textur, není nutné provádět dynamické osvětlení. Proto:

* Nastavením `unlitMaterials` příznaku `true` zapnete všechny materiály na unlit [barevné materiály](../../overview/features/color-materials.md).
* Odstraňte nepotřebná data z formátu vrcholu. Podívejte se na výše uvedený [příklad](#example) .

### <a name="use-case-visualization-of-compact-machines-etc"></a>Případ použití: vizualizace kompaktních počítačů atd.

V těchto případech mají modely často velmi vysoký detail v rámci malých objemů. Zobrazovací jednotka je silně optimalizovaná tak, aby tyto případy dobře zpracovávala. Většina optimalizací uvedených v předchozím případu použití se ale netýká:

* Jednotlivé části by měly být vybrané a pohyblivé, takže `sceneGraphMode` musí být ponecháno `dynamic`na.
* Přetypování do ray jsou obvykle nedílnou součástí aplikace, takže je potřeba vygenerovat sítě kolizí.
* Vyjmuté roviny vypadají lépe `opaqueMaterialDefaultSidedness` s povoleným příznakem.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](model-conversion.md)
* [Barevné materiály](../../overview/features/color-materials.md)
* [Materiály PBR](../../overview/features/pbr-materials.md)
* [Potlačení materiálů během převodu modelů](override-materials.md)
