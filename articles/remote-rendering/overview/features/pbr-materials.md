---
title: PBR materiály
description: Popisuje typ materiálu PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680255"
---
# <a name="pbr-materials"></a>PBR materiály

*PBR materiály* jsou jedním z podporovaných [typů materiálů](../../concepts/materials.md) v Azure Remote Rendering. Používají se pro [ok,](../../concepts/meshes.md) která by měla přijímat realistické osvětlení.

PBR je zkratka pro **P**hysically **B**ased **R**endering a znamená, že materiál popisuje vizuální vlastnosti povrchu fyzicky přijatelným způsobem, takže realistické výsledky jsou možné za všech světelných podmínek. Většina moderních herních motorů a nástrojů pro tvorbu obsahu podporuje pbr materiály, protože jsou považovány za nejlepší aproximaci reálných scénářů pro vykreslování v reálném čase.

![Přilba glTF vzorový model poskytnuté ARR](media/helmet.png)

PBR materiály však nejsou univerzálním řešením. Existují materiály, které odrážejí barvu odlišně v závislosti na úhlu pohledu. Například některé látky nebo barvy do auta. Tyto druhy materiálů nejsou zpracovány standardní model PBR a nejsou aktuálně podporovány Azure Remote Rendering. To zahrnuje rozšíření PBR, jako je *thin-film* (vícevrstvé povrchy) a *Clear-Coat* (pro automobilové barvy).

## <a name="common-material-properties"></a>Společné vlastnosti materiálu

Tyto vlastnosti jsou společné pro všechny materiály:

* **albedoColor:** Tato barva se násobí jinými barvami, například barvami *albedoMap* nebo *vertex*. Pokud je u materiálu povolena *průhlednost,* alfa kanál se `1` použije k `0` úpravě krytí, což znamená, že je zcela neprůhledné a znamená to zcela průhledné. Výchozí hodnota je bílá.

  > [!NOTE]
  > Když je materiál PBR zcela průhledný, jako dokonale čistý kus skla, stále odráží životní prostředí. Jasné skvrny, jako je slunce, jsou stále viditelné v odrazu. To je jiné pro [barevné materiály](color-materials.md).

* **albedoMap:** [2D textura](../../concepts/textures.md) pro hodnoty albedo na pixel.

* **alphaClipEnabled** a **alphaClipThreshold:** Pokud je *alphaClipEnabled* true, nebudou vykresleny všechny obrazové body, kde je hodnota albedo alpha nižší než *alphaClipThreshold.* Alfa oříznutí lze použít i bez povolení průhlednosti a je mnohem rychlejší k vykreslení. Alfa oříznuté materiály jsou však stále pomalejší než plně neprůhledné materiály. Ve výchozím nastavení je alfa oříznutí zakázáno.

* **textureCoordinateScale** a **textureCoordinateOffset:** Měřítko se vynásobí do souřadnic textury UV, posun se k němu přidá. Lze použít k roztažení a posunu textur. Výchozí měřítko je (1, 1) a posun je (0, 0).

* **useVertexColor:** Pokud síť obsahuje barvy vrcholu a tato volba je povolena, barvy vrcholů sítí se vynásobí do *albedoColor* a *albedoMap*. Ve výchozím nastavení jsou barvy vrcholů zakázány.

* **isDoubleSided:** Pokud je oboustrannost nastavena na hodnotu true, trojúhelníky s tímto materiálem jsou vykresleny i v případě, že se kamera dívá na jejich zadní tváře. Pro PBR materiály osvětlení je také správně vypočítáno pro zadní plochy. Ve výchozím nastavení je tato možnost zakázána. Viz také [Jednostranné vykreslování](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Vlastnosti materiálu PBR

Základní myšlenkou fyzicky založené horečné interpretace je použití vlastností *BaseColor*, *Metalness*a *Roughness* k napodomelosti široké škály reálných materiálů. Podrobný popis PBR je nad rámec tohoto článku. Další informace o PBR naleznete v [jiných zdrojích](http://www.pbr-book.org). Následující vlastnosti jsou specifické pro materiály PBR:

* **baseColor:** V materiálech PBR se *barva albedo* označuje jako *základní barva*. V Azure Remote Rendering *albedo color* vlastnost je již k dispozici prostřednictvím společné vlastnosti materiálu, takže neexistuje žádná další základní vlastnost barvy.

* **drsnost** a **drsnostMapa:** Drsnost definuje, jak drsný nebo hladký je povrch. Drsné povrchy rozptýlí světlo ve více směrech než hladké povrchy, díky nimž jsou odrazy rozmazané, nikoli ostré. Rozsah hodnot je `0.0` `1.0`od do . Když `roughness` se `0.0`rovná , odrazy budou ostré. Když `roughness` se `0.5`rovná , odrazy se rozmazají.

  Pokud jsou zadány hodnoty drsnosti a mapy drsnosti, konečná hodnota bude součin obou.

* **metalness** a **metalnessMap:** Ve fyzice tato vlastnost odpovídá tomu, zda je povrch vodivý nebo dielektrický. Vodivé materiály mají různé reflexní vlastnosti a mají tendenci být reflexní bez albedo barvy. V materiálech PBR tato vlastnost ovlivňuje, jak velký povrch odráží okolní prostředí. Hodnoty se `0.0` `1.0`pohybují od . Když kovje `0.0`je , albedo barva je plně viditelná a materiál vypadá jako plast nebo keramika. Když kovje `0.5`je , vypadá to, že malovaný kov. Když je `1.0`metalness , povrch téměř úplně ztrácí barvu albedo a odráží pouze okolí. Například, `metalness` pokud `1.0` `roughness` je `0.0` a je pak povrch vypadá jako real-svět zrcadlo.

  Pokud jsou dodány jak metalness hodnota a metalness mapa, konečná hodnota bude součin obou.

  ![metalness a drsnost](./media/metalness-roughness.png)

  Na obrázku výše, koule v pravém dolním rohu vypadá jako skutečný kovový materiál, vlevo dole vypadá jako keramika nebo plast. Barva albedo se také mění podle fyzikálních vlastností. Při zvyšující se drsnosti materiál ztrácí ostrost odrazu.

* **normalMap:** Pro simulaci jemných odstupňovaných detailů lze poskytnout [normální mapu.](https://en.wikipedia.org/wiki/Normal_mapping)

* **okluzeMap** a **aoScale:** [Okolní okluze](https://en.wikipedia.org/wiki/Ambient_occlusion) dělá objekty s štěrbinami vypadat realističtější přidáním stíny na uzavřené oblasti. Hodnota okluze se `0.0` `1.0`pohybuje `0.0` od do , kde se `1.0` rozumí tma (uzavřená) a neznamená žádné okluze. Pokud je 2D textura k dispozici jako mapa okluze, efekt je povolen a *aoScale* funguje jako multiplikátor.

  ![Mapa okluze](./media/boom-box-ao2.gif)

* **transparentní:** U materiálů PBR existuje pouze jedno nastavení průhlednosti: je povoleno nebo není. Krytí je definováno alfa kanálem barvy albedo. Pokud je tato možnost povolena, je k nakreslení poloprůhledných povrchů vyvolán složitější kanál vykreslování. Azure Remote Rendering implementuje [skutečnou transparentnost](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT) nezávislé pořadí.

  Transparentní geometrie je nákladné vykreslit. Pokud potřebujete pouze díry v povrchu, například pro listy stromu, je lepší místo toho použít alfa oříznutí.

  ![Oznámení](./media/transparency.png) o průhlednosti na obrázku výše, jak je koule zcela průhledná, ale odraz je stále viditelný.

  > [!IMPORTANT]
  > Pokud má být některý materiál za běhu přepnut z neprůhledného na průhledný, musí vykreslovací modul použít [režim vykreslení](../../concepts/rendering-modes.md) *TileBasedComposition* . Toto omezení se nevztahuje na materiály, které jsou převedeny jako průhledné materiály začít.

## <a name="technical-details"></a>Technické podrobnosti

Azure Remote Rendering používá Cook-Torrance mikro-faset BRDF s GGX NDF, Schlick Fresnel a GGX Smith koreluje viditelnost termín s lambert difuzní termín. Tento model je v současné době de facto průmyslovým standardem. Podrobnější informace naleznete v tomto článku: [Fyzicky založené renderování - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Alternativou k modelu PBR *metalness-roughness* používanému v Azure Remote Rendering je model PBR *Zrcadlo lesku.* Tento model může představovat širší škálu materiálů. Nicméně, to je dražší, a obvykle nefunguje dobře pro případy v reálném čase.
Není vždy možné převést z *Zrcadlo-leskna* *metalness-drsnost,* jak tam jsou *(Difúzní, zrcadlový)* dvojice hodnot, které nelze převést na *(BaseColor, Metalness)*. Převod v opačném směru je jednodušší a přesnější, protože všechny páry *(BaseColor, Metalness)* odpovídají dobře definovaným *(difúzním, zrcadlovým)* párům.

## <a name="next-steps"></a>Další kroky

* [Barevné materiály](color-materials.md)
* [Textury](../../concepts/textures.md)
* [Ok](../../concepts/meshes.md)
