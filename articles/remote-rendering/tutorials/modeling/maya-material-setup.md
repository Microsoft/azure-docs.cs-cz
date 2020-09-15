---
title: Nastavení fyzicky založených materiálů pro vykreslování v Maya
description: Vysvětluje, jak nastavit fyzicky založené vykreslovací materiály v Maya a exportovat je do formátu FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 43c11575be084354e4c9287b44b5847d4d086aff
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084392"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Kurz: nastavení fyzicky vycházejících materiálů pro vykreslování v Maya

## <a name="overview"></a>Přehled
V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přiřaďte materiály s pokročilým osvětlením na objekty ve scéně.
> * Zpracování vytváření instancí objektů a materiálů
> * Exportujte scénu do formátu FBX a vyberte důležité možnosti.

Vytváření [fyzicky založených materiálů pro vykreslování (PBR)](../../overview/features/pbr-materials.md) v Maya je poměrně jednoduchá úloha. Podobným způsobem je nastavení PBR v dalších aplikacích pro vytváření obsahu, jako je například 3DS Max. Tento kurz je průvodcem pro základní nastavení funkce PBR shader a export FBX pro projekty vzdáleného vykreslování Azure. 

Ukázková scéna v tomto kurzu obsahuje počet objektů mnohoúhelníkového pole. Přidělují se k různým materiálům, jako jsou dřevěné, kovové, malované kovové, plastová a pryžová. V podstatě řečeno, každý materiál obsahuje všechny nebo většinu následujících textur:

* **Albedo**, což je mapa barev materiálu a nazývá se také **difúze** nebo **BaseColor**.
* **Kov**, který určuje, jestli je materiál kovový a které části jsou kovové. 
* **Hrubá**, která určuje, jak hrubá nebo hladká plocha je a ovlivňuje ostrost nebo blurriness odrazů a světel na povrchu.
* **Normal (normální**), který přidává podrobnosti na povrch bez nutnosti přidávat další mnohoúhelníky Příkladem podrobností může být prodleva a odsazení na kovové ploše nebo zrnitosti ve dřevě.
* **Ambientní překrytí**, který se používá k přidání jemných stínování a stínů kontaktu do modelu. Jedná se o mapu ve stupních šedi, která označuje, které oblasti modelu získají plné osvětlení (bílá) nebo úplné stínování (černá). 

## <a name="prerequisites"></a>Předpoklady
* Autodesk Maya 2017 nebo novější

## <a name="set-up-materials-in-the-scene"></a>Nastavení materiálů na scéně
Tady je postup nastavení materiálu PBR v Maya.

Jak vidíte v ukázce scény, vytvořili jsme počet objektů box. Každý objekt představuje jiný typ materiálu. Jak je znázorněno na obrázku, každý z těchto objektů byl dán vlastním vhodným názvem.

Vzdálené vykreslování Azure používá měřiče měření a směr nahoru je osa Y. Než začnete vytvářet prostředky, doporučujeme, abyste v Maya nastavili jednotky scény na měřiče. Pro export nastavte jednotky na měřiče v nastaveních exportu FBX.

> [!TIP]
> Poskytněte modelu prostředky odpovídající názvy na základě relevantního typu části nebo materiálu. Smysluplné názvy usnadňují procházení vysoce náročných scén objektů.

![Názvy objektů](media/object-names.jpg)

Po vytvoření nebo získání některých textur můžete také vytvořit jedinečné textury. Můžete použít aplikace Texturing, jako je Quixel Suite, PhotoShop nebo sada látek, nebo získat obecné textury dlaždic z jiných zdrojů.

Použití textur pro model:

1. V zobrazení scény vyberte svůj model nebo geometrii a klikněte na něj pravým tlačítkem. V zobrazené nabídce vyberte **přiřadit nový materiál**.
1. V dialogovém okně **přiřadit nový materiál** přejít na **Maya**  >  **Stingray PBS**. Tato akce přiřadí vašemu modelu materiál PBR. 

V Maya 2020 je k dispozici několik různých shaderů PBR. Patří mezi ně **Maya úrovně Standard**, **Arnold Standard**a **Stingray PBR**. **Maya úrovně Standard Surface** se ještě neexportují prostřednictvím modulu plug-in FBX 2020. **Arnold standardní Surface shaderu** se dá exportovat pomocí souborů FBX. Ve většině případů je stejný jako **Maya shader Standard Surface**. Je obdobou pro **fyzický materiál** v nástroji 3D Studio Max.

**Shader STINGRAY PBR** je kompatibilní s mnoha dalšími aplikacemi a nejlépe odpovídá požadavkům vzdáleného vykreslování Azure. Podporuje se od Maya 2017. Když je tento typ materiálu v zobrazení vizuálně vizuální, je podobný jako u toho, co je ve vzdáleném vykreslování Azure vizuální.

![Stingray materiál](media/stingray-material.jpg)

S vaším materiálem přiřazeným k assetu a s názvem odpovídajícím způsobem teď můžete přiřadit různé textury. Následující obrázky znázorňují, kde se jednotlivé typy textur vejdou do materiálu PBR. Materiál Stingray PBR umožňuje vybrat atributy, které můžete aktivovat. Předtím, než budete moci připojit mapy textur, je nutné aktivovat příslušné atributy.

![Nastavení materiálu](media/material-setup.jpg)

Pojmenujte své materiály vhodně tím, že vezmete v úvahu jejich použití nebo typ. Materiál, který se používá v jedinečné části, může být pro danou část pojmenován. Materiál, který se používá v širším rozsahu oblastí, může být pojmenován pro jeho vlastnosti nebo typ.

Přiřaďte textury, jak je znázorněno na obrázku.

![Nastavení textury](media/texture-setup.jpg)

S vytvořenými a nastavenými materiály PBR zvažte vytváření [instancí objektů](../../how-tos/conversion/configure-model-conversion.md#instancing) ve scéně. Vytváření instancí podobných objektů ve vaší scéně, jako jsou ořechy, šrouby, šrouby a pračky, vydává významné úspory velikosti souborů. Instance hlavního objektu mohou mít vlastní měřítko, rotaci a transformace, aby je bylo možné umístit podle potřeby do scény. 

V Maya je proces vytváření instancí jednoduchý.

1. V nabídce **Úpravy** přejděte na **duplikovat jinak** a otevřete možnosti.
1. V dialogovém okně **Duplikovat zvláštní možnosti** vyberte pro **typ geometrie** možnost **instance** . 
1. Vyberte **duplikovat jinak**.

   ![Vytváření instancí](media/instancing.jpg)

Tato akce vytvoří instanci objektu. Můžete ho přesunout, otočit nebo škálovat nezávisle na jeho nadřazeném objektu a dalších instancích tohoto nadřazeného objektu. 

Všechny změny, které provedete v případě, že je v režimech komponent, jsou přenášeny do všech instancí objektu. Můžete například pracovat s komponentami s instancemi objektů, jako jsou vrcholy a mnohoúhelníkové obličeje. Ujistěte se, že chcete, aby všechny změny, které jste provedli, ovlivnily všechny tyto instance. 

Ve vzorové scéně byly jednotlivé objekty jednotlivých polí instance. Tato akce bude mít při exportu scény do formátu FBX význam.

![Přehled scény](media/scene-overview.jpg)

> [!TIP]
> Při procházení můžete vytvářet instance v rámci scény. Nahrazování kopií u instancí objektů později je mimořádně obtížné. 

## <a name="fbx-export-process"></a>Proces exportu FBX

Pojďme přejít k FBX exportu vašich prostředků scény nebo scény. Při exportu assetů dává smysl vybrat pouze objekty nebo prostředky z scény, kterou chcete exportovat. Například můžete mít 100 objektů ve scéně. Pokud chcete použít pouze 30 z nich, neexistuje žádný bod v exportu celé scény. 

Chcete-li vybrat:

1. Chcete- **File**li  >  otevřít dialogové okno **Export výběru** , vyberte možnost přejít na**Výběr exportu** souboru.
1. V poli **soubory typu** vyberte **exportovat FBX** a zobrazte nastavení exportu FBX. Primární nastavení pro export FBX jsou v obrázku zvýrazněna červeně.

   ![Export FBX](media/FBX-exporting.jpg)

V závislosti na vašich požadavcích můžete například chtít odeslat Asset klientovi. Možná nebudete chtít odesílat do assetu velký počet souborů textury. Můžete zvolit vložení textur do exportovaného souboru FBX. Tato možnost znamená, že máte jenom jeden soubor, který se má zabalit, ale velikost tohoto prostředku FBX se výrazně zvyšuje. Můžete povolit možnost vkládání textur výběrem možnosti pro **vložení média** , jak je znázorněno na obrázku.

> [!TIP]
> V této ukázce byl soubor pojmenován, aby odrážel tuto podmínku. Tento styl pojmenování je dobrým způsobem, jak sledovat vaše prostředky. 

Po nastavení konfigurace pro export vyberte **Exportovat výběr** v pravém dolním rohu.

![Vložení média](media/embedding-media.jpg)

## <a name="conclusion"></a>Závěr

Obecně platí, že tento typ materiálu vypadá realističtější, protože je založen na skutečné fyzika světla. Vytvoří další moderní efekt, aby se v reálném světě zdá, že scéna existuje.

## <a name="next-steps"></a>Další kroky

Nyní víte, jak nastavit materiály s pokročilým osvětlením pro objekty ve scéně. Dozvíte se taky, jak exportovat objekty do formátu FBX, který podporuje vzdálené vykreslování Azure. Dalším krokem je převod souboru FBX a jeho vizualizace ve vzdáleném vykreslování Azure.

> [!div class="nextstepaction"]
> [Rychlý Start: převod modelu pro vykreslování](../../quickstarts\convert-model.md)