---
title: Nastavení materiálů PBR (Physically Based Rendering) v Maya
description: Vysvětluje, jak nastavit fyzicky založené vykreslovací materiály v Maya a exportovat je do formátu FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977821"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Kurz: nastavení fyzicky vycházejících materiálů pro vykreslování v Maya

## <a name="overview"></a>Přehled
V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přiřaďte materiály s pokročilým modelem osvětlení objektům ve scéně.
> * zpracování vytváření instancí objektů a materiálů
> * Exportujte scénu do formátu FBX a důležité možnosti pro výběr.

Vytváření [fyzicky vycházejících materiálů pro vykreslování (PBR)](../../overview/features/pbr-materials.md) v nástroji `Maya` je poměrně rovný úkol, podobně jako v mnoha směrech až do PBR nastavování v jiných aplikacích pro vytváření obsahu, jako je `3DS Max` . Následující kurz je průvodcem základní funkce pro vytváření a FBX exportu PBR pro projekty ARR. 

Ukázková scéna v tomto kurzu obsahuje řadu `Polygon Box` objektů, kterým byl přiřazený určitý počet různých materiálů – dřevěné, kovové, malované kovové, plastová a pryžová. V podstatě řečeno, každý materiál obsahuje všechny nebo většinu následujících textur 

* `Albedo`, je mapa barev materiálu, která se také označuje jako `Diffuse` nebo`BaseColor`
* `Metalness`, který určuje, zda je materiál kovový a které části jsou kovové. 
* `Roughness`, který určuje, jak je povrch hrubá nebo hladec, ovlivňuje ostrost nebo blurriness odrazů a světel na povrchu.
* `Normals`, které přidávají podrobnosti na povrch, například na základě prodlev a odsazení na kovové ploše nebo zrn v dřevě, aniž by bylo nutné přidávat další mnohoúhelníky.
* `Ambient Occlusion`, který se používá k přidání jemných stínování a stínů kontaktu do modelu. Je to mapa odstínů šedi, která označuje, které oblasti modelu získají plné osvětlení (bílá) nebo úplný odstín (černá). 

## <a name="prerequisites"></a>Požadavky
* `Autodesk Maya 2017`nebo novější

## <a name="setting-up-materials-in-the-scene"></a>Nastavení materiálů na scéně
V Maya je postup pro nastavení materiálu PBR následující:

Chcete-li začít s, jak vidíte v ukázce scény, vytvořili jsme počet objektů box, z nichž každá představuje jiný typ materiálu. Všimněte si, jak je znázorněno na obrázku níže, aby každý z těchto objektů byl dán vlastním odpovídajícím názvem. 

> Je potřeba si vyhodnotit, ještě než začnete vytvářet prostředky pro vzdálené vykreslování Azure (ARR), které používá měřiče měření, a směr je v ose Y. Proto je vhodné nastavit jednotky scény v Maya na měřiče. Kromě toho je vhodné při exportu do nastavení jednotek na měřiče v nastaveních exportu FBX. 

> [!TIP]
Je vhodné pojmenovat prostředky modelu odpovídajícím způsobem, obvykle s relevantním typem části nebo materiálu, protože názvy usnadňují navigaci v případě velkého množství objektů.

![Názvy objektů](media/object-names.jpg)

Po vytvoření nebo získání textur – v závislosti na vašich potřebách možná budete chtít vytvořit jedinečné textury pro model v aplikacích Texturing, jako `Quixel Suite` je, `Photoshop` nebo, `Substance Suite` nebo můžete použít obecné textury dlážděnní z jiných zdrojů, můžete je použít v modelu následujícím způsobem:

* V zobrazení scény vyberte svůj model nebo geometrii a klikněte na něj pravým tlačítkem. V nabídce, která se zobrazí, klikněte na`Assign New Material`
* V `Assign New Material` možnostech přejít na `Maya` > `Stingray PBS` . Tato akce přiřadí vašemu modelu materiál PBR. 

V nástroji je `Maya 2020` k dispozici několik různých shaderů PBR `Maya Standard Surface` , `Arnold Standard Surface` a `Stingray PBR` . `Maya Standard Surface Shader`Ještě není možné exportovat pomocí `FBX plugin 2020` , zatímco se `Arnold Standard Surface Shader` dá exportovat se soubory FBX. Ve většině ostatních ohledech je to stejné jako `Maya Standard Surface Shader` a je analogické jako `Physical Material` v `3D Studio Max` .

**`The Stingray PBR Shader`** je kompatibilní s mnoha jinými aplikacemi a nejvíc přesně odpovídá požadavkům nástroje `ARR` a je podporován od verze `Maya 2017` . Také je vhodné, aby byl tento typ materiálů vizuálů v zobrazeních podobný tomu, co bude v ARR později vizuálně vizuální.

![Materiál Stingray](media/stingray-material.jpg)

S vaším materiálem přiřazeným k vašemu assetu a s názvem odpovídajícím způsobem můžete nyní pokračovat v přiřazování různých textur. Následující obrázky podrobně popisují, kde se jednotlivé typy textur vejdou do materiálu PBR. `Stingray PBR`Materiály vám umožní vybrat atributy, které můžete aktivovat, takže před tím, než budete mít `plug in` mapy textur, budete muset aktivovat relevantní atributy: 

![Nastavení materiálu](media/material-setup.jpg)

> [!TIP]
Je dobré pojmenovat materiály odpovídajícím způsobem a vzít v úvahu jejich použití nebo typ. Materiál, který má být použit v jedinečné části, může být pro danou část pojmenován, zatímco materiál, který lze použít v širším počtu oblastí, lze pojmenovat pro jeho vlastnosti nebo typ.

Přiřaďte textury následujícím způsobem:

![Nastavení textury](media/texture-setup.jpg)

Když vytvoříte a nastavíte vaše materiály PBR, zamyslete se nad vytvářením [instancí objektů](../../how-tos/conversion/configure-model-conversion.md#instancing) ve scéně. Vytváření instancí podobných objektů ve vaší scéně – například NUTS, šrouby, šrouby umyvadel – v podstatě všechny objekty, které jsou stejné, můžou přinést významné úspory z hlediska velikosti souboru. Instance hlavního objektu mohou mít vlastní měřítko, rotaci a transformace, takže je možné je umístit podle potřeby na scéně. V Maya je proces vytváření instancí jednoduchý.

* V `Edit` nabídce přejděte na adresu `Duplicate Special` a otevřete. `Options` 
* V `Duplicate Special` přepínačích možnosti `Geometry Type` od `Copy` do `Instance` klikněte na 
* Klikněte na `Duplicate Special`.

![Vytváření instancí](media/instancing.jpg)

Tato akce vytvoří instanci objektu, kterou lze přesunout otočený nebo škálovat nezávisle na své nadřazené a jiné instanci tohoto nadřazeného prvku. 
>Všechny změny, které provedete v průběhu v režimu komponent, se ale přenesou do všech instancí vašeho objektu, takže pokud pracujete s instancemi objektů – vrcholy, mnohoúhelníky atd., je nejdřív potřeba, abyste všechny změny, které provedete, ovlivnily všechny tyto instance.

Ve vzorové scéně byly objekty každého jednotlivého pole instance. Tato akce bude mít při exportu scény do formátu FBX význam.

![Přehled scény](media/scene-overview.jpg)

>Osvědčenými postupy týkajícími se vytváření instancí v rámci scény je jejich vytvoření při přechodu na sebe, protože pozdější nahrazování kopií u instancí objektů je mimořádně obtížné. 

## <a name="fbx-export-process"></a>Proces exportu FBX

Nyní můžeme přejít k FBX exportu vašich prostředků scény a scény. Obecně řečeno, dává smysl při exportu prostředků do výběru pouze pro export těchto objektů nebo prostředků z vaší scény, kterou požadujete. Pokud máte na scéně 100 objektů, ale chcete použít pouze 30 z nich, neexistuje žádný bod v exportu celé scény. Takže pokud si nejste spokojeni exportovat celou scénu, udělejte si výběr a pokračujte na:

* `File` > `Export Selection`a v dialogovém okně pro export přejít na konec a nastavte `Files of Type` na `FBX Export` . Toto okno zveřejní nastavení exportu FBX. Primární nastavení pro export FBX jsou na obrázku níže zvýrazněná červeně.

![Export FBX](media/FBX-exporting.jpg)

V závislosti na vašich požadavcích – například můžete chtít odeslat Asset klientovi, ale nechcete odesílat velké počty souborů textury s Assetem, můžete zvolit vložení textur do exportovaného souboru FBX. Tato možnost znamená, že máte jenom jeden soubor, který se má zabalit, ale významně zvětší velikost tohoto FBX prostředku. Možnost vkládání textur můžete povolit přepnutím na `Embed Media` možnost, jak je znázorněno níže.

> [!TIP]
> Všimněte si, že soubor v tomto případě byl pojmenován tak, aby odrážel tuto podmínku. To je dobrým zvykem způsob, jak udržet přehled o prostředcích. 

Po dokončení nastavení konfigurace pro export klikněte na tlačítko Exportovat výběr v pravém dolním rohu.

![Vložení média](media/embedding-media.jpg)

## <a name="conclusion"></a>Závěr

Obecně platí, že tento typ materiálu vypadá realističtější, protože je založený na skutečné fyzika světla. Vytvoří další moderní efekt, který se nachází v reálném světě.

## <a name="next-steps"></a>Další kroky

Nyní znáte nejdůležitější funkce pro nastavení materiálů s pokročilým osvětlením na objekty ve scéně a jejich export do formátu FBX, který je podporován funkcí ARR. Dalším krokem je převod souboru FBX a vizualizace v ARR.

> [!div class="nextstepaction"]
> [Rychlý Start: převod modelu pro vykreslování](../../quickstarts\convert-model.md)