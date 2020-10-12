---
title: Materiály PBR
description: Popisuje typ materiálu PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: ad7fc7d9d02cd9a9a6fe74534a7c674fe0ac778d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893250"
---
# <a name="pbr-materials"></a>Materiály PBR

*Materiály PBR* jsou jedním z podporovaných [typů materiálu](../../concepts/materials.md) ve vzdáleném vykreslování Azure. Používají se pro [sítě](../../concepts/meshes.md) , které by měly přijímat reálné osvětlení.

PBR představuje pro **P**hysically **B**ased **R**endering a to znamená, že materiál popisuje vizuální vlastnosti povrchu fyzicky plausible, takže reálné výsledky jsou možné v rámci všech světelných podmínek. Většina moderních herních modulů a nástrojů pro vytváření obsahu podporují PBR materiály, protože se považují za nejlepší odhad scénářů reálného světa pro vykreslování v reálném čase.

![GlTF Vzorový model, který je vykreslený pomocí ARR](media/helmet.png)

Materiály PBR nejsou univerzálním řešením, i když. V závislosti na úhlu zobrazení existují materiály, které odrážejí barvu odlišně. Například některé prostředky infrastruktury nebo nátěry automobilů. Tyto druhy materiálů nejsou zpracovávány standardním modelem PBR a aktuálně nejsou podporovány pomocí vzdáleného vykreslování Azure. To zahrnuje rozšíření PBR, jako jsou *tenké filmy* (vícevrstvé povrchy) a *clear-nátěr* (pro nátěry automobilů).

## <a name="common-material-properties"></a>Společné vlastnosti materiálu

Tyto vlastnosti jsou společné pro všechny materiály:

* **albedoColor:** Tato barva se vynásobí jinými barvami, jako je například *albedoMap* nebo * :::no-loc text="vertex "::: Colors*. Pokud je pro materiál povolená *průhlednost* , alfa kanál se použije k úpravě krytí, což znamená, že je `1` plně neprůhledný a má velmi `0` transparentní význam. Výchozí hodnota je bílá.

  > [!NOTE]
  > Když je materiál PBR plně transparentní, jako dokonale čistá část skla, stále odráží prostředí. Jasné skvrny, jako je slunce, jsou pořád viditelné v reflexi. To se pro [barevné materiály](color-materials.md)liší.

* **albedoMap:** [2D textura](../../concepts/textures.md) pro hodnoty albedo na pixel.

* **alphaClipEnabled** a **alphaClipThreshold:** Pokud má *alphaClipEnabled* hodnotu true, všechny pixely, kde je hodnota albedo alfa nižší než *alphaClipThreshold* , se nevykreslí. Výstřižek alfa lze použít i bez nutnosti průhlednosti a je mnohem rychlejší pro vykreslování. Oříznuté materiály alfa jsou stále pomalejší pro vykreslování než plně neprůhledné materiály, ale. Ve výchozím nastavení je oříznutí alfa zakázané.

* **textureCoordinateScale** a **textureCoordinateOffset:** stupnice se vynásobí souřadnicemi textury UV, do které se přidá posun. Dá se použít k roztažení a posunutí textur. Výchozí měřítko je (1, 1) a posun je (0, 0).

* **useVertexColor:** Pokud mřížka obsahuje :::no-loc text="vertex"::: barvy a tato možnost je povolená, :::no-loc text="vertex"::: vynásobí se barva ok v *albedoColor* a *albedoMap*. Ve výchozím nastavení je *useVertexColor* zakázaný.

* **isDoubleSided:** Pokud je vlastnost sidedness nastavená na hodnotu true, budou se tyto trojúhelníky s tímto materiálem vykreslovat i v případě, že fotoaparát hledá své zadní plošky. Pro zadní plošky se také vypočítává osvětlení materiálů PBR. Ve výchozím nastavení je tato možnost zakázána. Viz také [ :::no-loc text="Single-sided"::: vykreslování](single-sided-rendering.md).

* **TransparencyWritesDepth:** Pokud je pro materiál nastaven příznak TransparencyWritesDepth a materiál je transparentní, objekty, které tento materiál používají, budou také přispívat do konečné vyrovnávací paměti. Zobrazit v další části příznak materiálu PBR *Průhledný* Povolení této funkce se doporučuje v případě, že váš případ použití potřebuje další plausibleou [fázi reprojekce](late-stage-reprojection.md) plně transparentních scén. U smíšených neprůhledných a transparentních scén může toto nastavení způsobit implausible reanalýzování nebo artefakty reprojekce. Z tohoto důvodu je výchozím a doporučeným nastavením pro obecný případ použití zakázání tohoto příznaku. Napsané hodnoty hloubky jsou pořízeny ze vrstvy hloubkového pixelu objektu, který je nejblíže kameře.

## <a name="pbr-material-properties"></a>Vlastnosti materiálu PBR

Základní nápad, který je fyzicky založený na *vykreslování, je*použití *BaseColor*, vlastností odolnosti a vlastností *hrubosti* k emulaci široké škály reálných materiálů. Podrobný popis PBR překračuje rozsah tohoto článku. Další informace o PBR najdete v tématu [Další zdroje](http://www.pbr-book.org). Následující vlastnosti jsou specifické pro materiály PBR:

* **baseColor:** V materiálech PBR se *Barva albedo* označuje jako *základní barva*. Ve vzdáleném vykreslování Azure je vlastnost *albedo Color* již přítomna prostřednictvím vlastností společných materiálů, takže neexistuje žádná další základní vlastnost Color.

* **hrubá** a **roughnessMapost:** hrubá rovina definuje, jak je povrch hrubá nebo hladec. Hrubá rovina rozrážejí světlo ve více směrech než hladké povrchy, což rozostří fuzzy, nikoli ostré. Rozsah hodnoty je od `0.0` do `1.0` . Pokud `roughness` se rovná `0.0` , odrazy budou ostré. Při `roughness` rovnosti se `0.5` odrazy stanou neostrými.

  Pokud je zadána hodnota hrubosti i mapa hrubosti, bude konečná hodnota součinem těchto dvou.

* **kov** a **metalnessMap:** v fyzikě Tato vlastnost odpovídá tomu, zda je povrch v činnosti nebo dielectric. Tyto materiály mají různé reflektované vlastnosti a jsou obvykle odrážetelné bez albedo barvy. V materiálech PBR Tato vlastnost ovlivňuje, jak velký povrch odráží okolní prostředí. Rozsah hodnot od `0.0` do `1.0` . Když je kov `0.0` , barva albedo je plně viditelná a materiál vypadá jako plasty nebo keramické. `0.5`V případě, že je kov, vypadá to, že se jedná o namalované kovové. Když je kov `1.0` , povrch téměř zcela ztratí svoji albedo barvu a odráží jenom okolí. Například pokud `metalness` je a, `1.0` `roughness` `0.0` pak povrch vypadá jako skutečný zrcadlový svazek.

  Pokud jsou dodána hodnota kovového a mapa kovu, bude konečná hodnota součinem těchto dvou.

  ![Koule vykreslené s různou hodnotou kovu a hrubosti](./media/metalness-roughness.png)

  Ve výše uvedeném obrázku vypadá koule v pravém dolním rohu jako materiál reálného materiálu, vlevo dole vypadá jako keramické nebo plast. Barva albedo se také mění podle fyzických vlastností. Díky zvýšení hrubé hodnoty materiál ztratí ostrost reflexe.

* **normalMap:** Pro simulaci jemně odstupňované podrobností lze zadat [normální mapu](https://en.wikipedia.org/wiki/Normal_mapping) .

* **occlusionMap** a **aoScale:** [ambientní překrytí](https://en.wikipedia.org/wiki/Ambient_occlusion) zpřístupňuje objekty s crevicesmi mnohem realisticky přidáním stínů do zastíněna oblastí. Překrytí rozsah hodnot od `0.0` do `1.0` , kde `0.0` znamená tmavost (zastíněna) a znamená, že `1.0` Occlusions. Pokud je 2D textura k dispozici jako mapa překrytí, je efekt povolen a *aoScale* funguje jako násobitel.

  ![Objekt vykreslený pomocí a bez ambientního překrytíu](./media/boom-box-ao2.gif)

* **transparentní:** U materiálů PBR existuje jenom jedno nastavení transparentnosti: je povolené nebo ne. Neprůhlednost je definována alfa kanálem albedo Color. Pokud je povoleno, je vyvolán složitější kanál vykreslování pro kreslení částečně průhledných ploch. Vzdálené vykreslování Azure implementuje true [transparentnost nezávislého řádu](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT).

  Transparentní geometrie je nákladné pro vykreslování. Pokud potřebujete pouze otvory na povrchu, například pro listy stromu, je vhodnější místo toho použít alfa ořez.

  ![Koule vykreslené s nulovým oznámením o plné transparentnosti na ](./media/transparency.png) obrázku výše, jak je koule úplně průhledná, ale odraz je stále viditelný.

  > [!IMPORTANT]
  > Pokud by měl být nějaký materiál přepnut z neprůhledných na transparentní za běhu, zobrazovací jednotka musí používat [režim vykreslování](../../concepts/rendering-modes.md) *TileBasedComposition* . Toto omezení se nevztahuje na materiály, které jsou převáděny jako průhledné materiály na začátek.

## <a name="technical-details"></a>Technické podrobnosti

Vzdálené vykreslování Azure používá Cook-Torrance mikroaspektů BRDF s GGX NDF, Schlick Fresnelova poklesu a GGX Smith koreluje výraz viditelnosti s výrazem Lambert difúze. Tento model je v současnosti ve standardním odvětví. Podrobnější informace najdete v tomto článku: [vykreslování fyzicky na bázi – Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Alternativou k modelu *hrubých kovů* , který se používá při vzdáleném vykreslování Azure, je model *zrcadlové Glossiness* PBR. Tento model může představovat širší škálu materiálů. Je ale dražší a obvykle nefunguje dobře pro případy v reálném čase.
Není vždy možné převést z *odlesk-Glossiness* na *kov –* protože existují páry hodnot *(difúze, odlesky)* , které se nedají převést na *(BaseColor, metaling)*. Převod v druhém směru je jednodušší a přesnější, protože všechny páry *(BaseColor, kov)* odpovídají dobře definovaným dvojicím *(difúzi, odleskům)* .

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# PbrMaterial](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RemoteManager. CreateMaterial ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Třída C++ PbrMaterial](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [C++ RemoteManager:: CreateMaterial ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Další kroky

* [Barevné materiály](color-materials.md)
* [Textury](../../concepts/textures.md)
* [Sítě](../../concepts/meshes.md)
