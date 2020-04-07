---
title: Konfigurace převodu modelu
description: Popis všech parametrů převodu modelu
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681568"
---
# <a name="configure-the-model-conversion"></a>Konfigurace převodu modelu

V této kapitole jsou dokumenty o možnostech převodu modelu.

## <a name="settings-file"></a>Soubor nastavení

Pokud je `ConversionSettings.json` ve vstupním kontejneru vedle vstupního modelu nalezen ý soubor, slouží k poskytnutí další konfigurace pro proces převodu modelu.

Obsah souboru by měl splňovat následující schéma json:

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

Ukázkový `ConversionSettings.json` soubor může být:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametry geometrie

* `scaling`- Tento parametr měří model rovnoměrně. Změna měřítka lze zvětšit nebo zmenšit model, například k zobrazení modelu budovy na desce tabulky. Vzhledem k tomu, že vykreslovací modul očekává délky, které mají být zadány v metrech, další důležité použití tohoto parametru nastane, když je model definován v různých jednotkách. Pokud je například model definován v centimetrech, mělo by použití měřítka 0,01 vykreslit model ve správné velikosti.
Některé formáty zdrojových dat (například .fbx) poskytují nápovědu pro změnu měřítka jednotky, v takovém případě převod implicitně škáluje model na jednotky měřiče. Implicitní změna měřítka poskytovaná zdrojovým formátem bude použita nad parametrem měřítka.
Konečný faktor měřítka se aplikuje na vrcholy geometrie a lokální transformace uzlů grafu scény. Změna měřítka pro transformaci kořenové entity zůstane nezměněna.

* `recenterToOrigin`- uvádí, že model by měl být převeden tak, aby jeho ohraničovací rámeček byl vycentrován na počátek.
Centrování je důležité, pokud je zdrojový model posunut daleko od počátku, protože v takovém případě problémy s přesností s plovoucí desetinnou desetinnou desetinnou desetinnou tágo může způsobit vykreslování artefakty.

* `opaqueMaterialDefaultSidedness`- Vykreslovací modul předpokládá, že neprůhledné materiály jsou oboustranné.
Pokud to není zamýšlené chování, tento parametr by měl být nastaven na "SingleSided". Další informace naleznete v [tématu jednostranné vykreslování](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Přepsání materiálu

* `material-override`- Tento parametr umožňuje přizpůsobit zpracování materiálů [během převodu](override-materials.md).

### <a name="color-space-parameters"></a>Parametry barevného prostoru

Vykreslovací modul očekává, že hodnoty barev budou v lineárním prostoru.
Pokud je model definován pomocí gama prostoru, pak by tyto volby měly být nastaveny na hodnotu true.

* `gammaToLinearMaterial`- Převést barvy materiálu z gama prostoru na lineární prostor
* `gammaToLinearVertex`- Převést barvy vrcholů z gama prostoru na lineární prostor

> [!NOTE]
> U souborů FBX jsou `true` tato nastavení ve výchozím nastavení nastavena na. Pro všechny ostatní typy souborů `false`je výchozí .

### <a name="scene-parameters"></a>Parametry scény

* `sceneGraphMode`- Definuje, jak je graf scény ve zdrojovém souboru převeden:
  * `dynamic`(výchozí): Všechny objekty v souboru jsou vystaveny jako [entity](../../concepts/entities.md) v rozhraní API a mohou být transformovány nezávisle. Hierarchie uzlů za běhu je shodná se strukturou ve zdrojovém souboru.
  * `static`: Všechny objekty jsou vystaveny v rozhraní API, ale nelze transformovat nezávisle.
  * `none`: Graf scény je sbalen do jednoho objektu.

Každý režim má jiný výkon za běhu. V `dynamic` režimu se náklady na výkon lineárně škálují s počtem [entit](../../concepts/entities.md) v grafu, i když není přesunuta žádná součást. Měl by být používán pouze v případě, že pohyb jednotlivých částí je pro aplikaci nezbytný, například pro animaci "zobrazení rozpadu".

Režim `static` exportuje graf celé scény, ale části uvnitř tohoto grafu mají konstantní transformaci vzhledem k jeho kořenové části. Kořenový uzel objektu však lze stále přesouvat, otáčet nebo měnit měřítko bez významných nákladů na výkon. Prostorové [dotazy](../../overview/features/spatial-queries.md) navíc vrátí jednotlivé části a každá část může být upravena [pomocí přepsání stavu](../../overview/features/override-hierarchical-state.md). V tomto režimu je režie za běhu na objekt zanedbatelná. Je ideální pro velké scény, kde stále potřebujete kontrolu na objekt, ale žádné změny transformace na objekt.

Režim `none` má nejméně runtime režie a také o něco lepší doby načítání. Kontrola nebo transformace jednotlivých objektů není v tomto režimu možná. Případy použití jsou například fotogrammetrické modely, které nemají smysluplný graf scény na prvním místě.

> [!TIP]
> Mnoho aplikací načte více modelů. Parametry převodu pro každý model byste měli optimalizovat v závislosti na tom, jak bude použit. Chcete-li například zobrazit model vozu, aby jej uživatel podrobně rozebíral a kontroloval, `dynamic` je třeba jej převést v režimu. Pokud však chcete vůz umístit do prostředí předváděcí místnosti, `sceneGraphMode` lze `static` tento `none`model převést s nastavenou na nebo sudou .

### <a name="physics-parameters"></a>Fyzikální parametry

* `generateCollisionMesh`- Pokud potřebujete podporu pro [prostorové dotazy](../../overview/features/spatial-queries.md) na modelu, tato možnost musí být povolena. V nejhorším případě může vytvoření kolizní sítě zdvojnásobit dobu převodu. Načtení modelů s kolizními sítěmi `dynamic` trvá déle a při použití grafu scény mají také vyšší režii za běhu. Pro celkový optimální výkon byste měli zakázat tuto možnost na všech modelech, na kterých nepotřebujete prostorové dotazy.

### <a name="unlit-materials"></a>Neosvětlené materiály

* `unlitMaterials`- Ve výchozím nastavení bude konverze přednost vytvoření [PBR materiálů](../../overview/features/pbr-materials.md). Tato volba říká konvertoru, aby místo toho zacházel se všemi materiály jako s [barevnými materiály.](../../overview/features/color-materials.md) Pokud máte data, která již obsahují osvětlení, jako jsou modely vytvořené pomocí fotogrammetrie, tato možnost umožňuje rychle vynutit správný převod pro všechny materiály, aniž byste museli [přepsat každý materiál](override-materials.md) jednotlivě.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Převod ze starších formátů FBX s materiálovým modelem Phong

* `fbxAssumeMetallic`- Starší verze formátu FBX definují své materiály pomocí modelu materiálu Phong. Proces převodu musí odvodit, jak se tyto materiály mapují na [model PBR](../../overview/features/pbr-materials.md)vykreslovače . Obvykle to funguje dobře, ale nejednoznačnost může nastat, když materiál nemá žádné textury, vysoké odlesky a nešedou barvu albedo. Za těchto okolností, konverze musí vybrat mezi prioritou vysoké zrcadlové hodnoty, definování vysoce reflexní, kovový materiál, kde albedo barva rozpouští pryč, nebo priority albedo barvu, definování něco jako lesklý barevný plast. Ve výchozím nastavení proces převodu předpokládá, že vysoce odleskové hodnoty implikují kovový materiál v případech, kdy platí nejednoznačnost. Tento parametr lze `false` nastavit tak, aby přepnul na opačný.

### <a name="coordinate-system-overriding"></a>Přepsání souřadnicového systému

* `axis`- Přepsat souřadnicové systémové jednotkové vektory. Výchozí hodnoty `["+x", "+y", "+z"]`jsou . Teoreticky má formát FBX záhlaví, kde jsou definovány tyto vektory a převod používá tyto informace k transformaci scény. Formát glTF také definuje pevný souřadný systém. V praxi mají některé prostředky v záhlaví nesprávné informace nebo byly uloženy s jinou konvencí souřadnicového systému. Tato možnost umožňuje přepsat souřadný systém pro kompenzaci. Například: `"axis" : ["+x", "+z", "-y"]` vymění osu Z a osu Y a zachová předání souřadnicového systému obrácením směru osy Y.

### <a name="vertex-format"></a>Formát vrcholu

Je možné upravit formát vrcholu pro síť, pro obchodování přesnost pro úsporu paměti. Nižší nároky na paměť umožňuje načíst větší modely nebo dosáhnout lepšího výkonu. V závislosti na datech však může mít nesprávný formát významný vliv na kvalitu vykreslování.

> [!CAUTION]
> Změna formátu vrcholu by měla být poslední možností, když se modely již nevejdou do paměti, nebo při optimalizaci pro nejlepší možný výkon. Změny lze snadno zavést vykreslování artefakty, a to jak ty zřejmé a jemné ty. Pokud nevíte, na co si dát pozor, neměli byste měnit výchozí nastavení.

Tyto úpravy jsou možné:

* Konkrétní datové proudy mohou být explicitně zahrnuty nebo vyloučeny.
* Přesnost datových proudů lze snížit snížit nároky na paměť.

Následující `vertex` část v `.json` souboru je volitelná. Pro každou část, která není explicitně zadána, služba převodu přejde zpět na výchozí nastavení.

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

Vynucením součásti `NONE`do , je zaručeno, že výstupní síť nemá příslušný datový proud.

#### <a name="component-formats-per-vertex-stream"></a>Formáty komponent na datový proud vrcholu

Tyto formáty jsou povoleny pro příslušné součásti:

| Komponenta vrcholu | Podporované formáty (tučné = výchozí) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|barva0| **8_8_8_8_UNSIGNED_NORMALIZED**, ŽÁDNÝ |
|barva1| 8_8_8_8_UNSIGNED_NORMALIZED, **ŽÁDNÝ**|
|Normální| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ŽÁDNÝ |
|Tangens| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ŽÁDNÝ |
|binormální| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ŽÁDNÝ |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, ŽÁDNÝ |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, ŽÁDNÝ |

#### <a name="supported-component-formats"></a>Podporované formáty součástí

Paměťové stopy formátů jsou následující:

| Formát | Popis | Bajty na vrchol |
|:-------|:------------|:---------------|
|32_32_FLOAT|dvousložková plná přesnost plovoucí hospo-|8
|16_16_FLOAT|dvousložková přesnost s polovičním plovoucím bodem|4
|32_32_32_FLOAT|třísložková plná přesnost plovoucího bodu|12
|16_16_16_16_FLOAT|čtyřsložková půlstožná přesnost plovoucího bodu|8
|8_8_8_8_UNSIGNED_NORMALIZED|čtyřsložkový bajt, normalizovaný na `[0; 1]` rozsah|4
|8_8_8_8_SIGNED_NORMALIZED|čtyřsložkový bajt, normalizovaný na `[-1; 1]` rozsah|4

#### <a name="best-practices-for-component-format-changes"></a>Doporučené postupy pro změny formátu komponenty

* `position`: Je vzácné, že je dostatečná snížená přesnost. **16_16_16_16_FLOAT** zavádí znatelné kvantizace artefakty, a to i pro malé modely.
* `normal`, `tangent` `binormal`, : Obvykle se tyto hodnoty mění společně. Pokud neexistují znatelné světelné artefakty, které jsou výsledkem normální kvantizace, není důvod zvyšovat jejich přesnost. V některých případech však tyto součásti lze nastavit na **NONE**:
  * `normal`, `tangent`a `binormal` jsou potřebné pouze v případě, že by měl být rozsvícen alespoň jeden materiál v modelu. V ARR je to případ, kdy je na modelu kdykoli použit [materiál PBR.](../../overview/features/pbr-materials.md)
  * `tangent`a `binormal` jsou potřebné pouze v případě, že některý z osvětlených materiálů používá normální strukturu mapy.
* `texcoord0`, `texcoord1` : Souřadnice textury mohou používat sníženou `[0; 1]` přesnost **(16_16_FLOAT),** když jejich hodnoty zůstanou v rozsahu a když adresované textury mají maximální velikost 2048 x 2048 pixelů. Pokud jsou tyto limity překročeny, bude utrpět kvalita mapování textur.

#### <a name="example"></a>Příklad

Předpokládejme, že máte fotogrammetrický model, který má osvětlení pečené do textur. K vykreslení modelu jsou zapotřebí jen pozice vrcholů a souřadnice textury.

Ve výchozím nastavení převaděč musí předpokládat, že můžete chtít použít PBR `normal`materiály `tangent`na `binormal` modelu v určitém okamžiku, tak to bude generovat , a data pro vás. V důsledku toho využití paměti `position` na vrchol je (12 `texcoord0` bajtů) `normal` + (8 bajtů) + (4 bajty) + `tangent` (4 bajty) + `binormal` (4 bajty) = 32 bajtů. Větší modely tohoto typu může mít snadno mnoho milionů vrcholů, což má za následek modely, které mohou zabírají více gigabajtů paměti. Takové velké množství dat bude mít vliv na výkon a může dokonce dojít k nedostatku paměti.

S vědomím, že nikdy nepotřebujete dynamické osvětlení modelu, a `[0; 1]` s vědomím, `normal` `tangent`že `binormal` všechny `NONE` `texcoord0` souřadnice`16_16_FLOAT`textury jsou v dosahu, můžete nastavit přesnost , a na polovinu přesnosti ( ), což má za následek pouze 16 bajtů na vrchol. Snížení dat sítě na polovinu umožňuje načíst větší modely a potenciálně zlepšuje výkon.

## <a name="typical-use-cases"></a>Typické případy použití

Nalezení dobrého nastavení importu pro daný případ použití může být zdlouhavý proces. Na druhou stranu nastavení převodu může mít významný vliv na výkon za běhu.

Existují určité třídy případů použití, které se kvalifikují pro konkrétní optimalizace. Některé příklady jsou uvedeny níže.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Případ použití: Architektonická vizualizace / velké venkovní mapy

* Tyto typy scén mají tendenci být statické, což znamená, že nepotřebují pohyblivé části. V souladu `sceneGraphMode` s tím `static` lze `none`nastavit nebo dokonce , což zlepšuje výkon za běhu. V `static` režimu lze kořenový uzel scény stále přesouvat, otáčet a měnit jeho velikost, například dynamicky přepínat mezi měřítkem 1:1 (pro zobrazení z pohledu první osoby) a horním pohledem tabulky.

* Pokud potřebujete přesunout části, to obvykle také znamená, že potřebujete podporu pro raycasts nebo jiné [prostorové dotazy](../../overview/features/spatial-queries.md), takže si můžete vybrat tyto části na prvním místě. Na druhou stranu, pokud nemáte v úmyslu něco přesunout, je vysoká šance, že ji také nepotřebujete k `generateCollisionMesh` účasti na prostorových dotazech, a proto můžete příznak vypnout. Tento přepínač má významný vliv na časy převodu, doby načítání a také náklady na aktualizaci za dobu běhu na snímek.

* Pokud aplikace nepoužívá [roviny řezu](../../overview/features/cut-planes.md) `opaqueMaterialDefaultSidedness` , příznak by měl být vypnutý. Zvýšení výkonu je obvykle 20%-30%. Při pohledu do vnitřních částí objektů, které vypadají neintuitivně, se stále dají řezané roviny používat, ale při pohledu do vnitřních částí objektů nebudou zadní plochy. Další informace naleznete v [tématu jednostranné vykreslování](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Případ použití: Modely fotogrammetrie

Při vykreslování modelů fotogrammetrie obvykle není potřeba `sceneGraphMode` `none`pro graf scény, takže můžete nastavit na . Vzhledem k tomu, že tyto modely zřídka obsahují komplexní graf scény, dopad této možnosti by však měl být nevýznamný.

Vzhledem k tomu, osvětlení je již pečené do textur, není potřeba žádné dynamické osvětlení. Proto:

* Nastavte `unlitMaterials` příznak, `true` aby se všechny materiály [změnily](../../overview/features/color-materials.md)na neosvětlené barevné materiály .
* Odeberte nepotřebná data z formátu vrcholu. Viz výše uvedený [příklad.](#example)

### <a name="use-case-visualization-of-compact-machines-etc"></a>Případ použití: Vizualizace kompaktních strojů atd.

V těchto případech použití mají modely často velmi vysoké detaily v rámci malého objemu. Vykreslovač je silně optimalizován pro dobré zpracování takových případů. Většina optimalizací uvedených v předchozím případě použití se zde však nevztahuje:

* Jednotlivé části by měly být volitelné `sceneGraphMode` a pohyblivé, takže musí být ponechány na `dynamic`.
* Ray odlitky jsou obvykle nedílnou součástí aplikace, takže kolizní ok musí být generovány.
* Řezané roviny `opaqueMaterialDefaultSidedness` vypadají lépe s povolenou vlajkou.

## <a name="next-steps"></a>Další kroky

* [Převod modelu](model-conversion.md)
* [Barevné materiály](../../overview/features/color-materials.md)
* [PBR materiály](../../overview/features/pbr-materials.md)
* [Přepsat materiály během převodu modelu](override-materials.md)
