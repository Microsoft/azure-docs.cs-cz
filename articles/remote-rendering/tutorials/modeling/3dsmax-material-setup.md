---
title: Nastavení fyzicky založených materiálů pro vykreslování v 3DSMax
description: Vysvětluje, jak nastavit fyzicky založené vykreslovací materiály v 3DSMax a exportovat je do formátu FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85880086"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Kurz: nastavení fyzicky založených materiálů pro vykreslování v sadě 3D Studio Max

## <a name="overview"></a>Přehled
V tomto kurzu se naučíte:

>[!div class="checklist"]
>
> * Přiřaďte materiály s pokročilým osvětlením na objekty ve scéně.
> * Zpracování vytváření instancí objektů a materiálů
> * Exportujte scénu do formátu FBX a vyberte důležité možnosti.

Vytváření [fyzicky založených materiálů pro vykreslování](../../overview/features/pbr-materials.md) v rámci sady 3D Studio Max (3dsmax) je poměrně jednoduchá úloha. Podobným způsobem je nastavení PBR v dalších aplikacích pro vytváření obsahu, jako je Maya. Tento kurz je průvodcem pro základní nastavení funkce PBR shader a export FBX pro projekty vzdáleného vykreslování Azure.

Ukázková scéna v tomto kurzu obsahuje počet objektů mnohoúhelníkového pole. Přidělují se k různým materiálům, jako jsou dřevěné, kovové, malované kovové, plastová a pryžová. V podstatě řečeno, každý materiál obsahuje všechny nebo většinu následujících textur:

* **Albedo**, což je mapa barev materiálu a nazývá se také **difúze** nebo **BaseColor**.
* **Kov**, který určuje, jestli je materiál kovový a které části jsou kovové. 
* **Hrubá**, která určuje, jak je povrch hrubá nebo hladec.
Ovlivňuje také ostrost nebo blurriness odrazů a světel na povrchu.
* **Normal (normální**), který přidává podrobnosti na povrch bez nutnosti přidávat další mnohoúhelníky Příkladem podrobností může být prodleva a odsazení na kovové ploše nebo zrnitosti ve dřevě.
* **Ambientní překrytí**, který se používá k přidání jemných stínování a stínů kontaktu do modelu. Jedná se o mapu ve stupních šedi, která označuje, které oblasti modelu získají plné osvětlení (bílá) nebo úplné stínování (černá).

## <a name="prepare-the-scene"></a>Příprava scény
V **maximálním počtu 3D studia**je postup pro nastavení materiálu PBR následující.

Chcete-li začít s, jak vidíte v ukázce scény, vytvořili jsme počet objektů box, z nichž každá představuje jiný typ materiálu:

>[!TIP]
>Je potřeba poznamenat, že začnete vytvářet prostředky pro ARR, které používají **měřiče** pro měření.  
>Proto je vhodné nastavit **jednotky systému** scény na **měřiče**. Kromě toho je vhodné při exportu do nastavení jednotek na měřiče v nastaveních exportu FBX.

Následující obrázek znázorňuje postup nastavení jednotek systému na měřiče ve službě maximálního využití sady 3D Studio. V hlavní nabídce přejděte na vlastní nastavení **Customize**  >  **jednotek nastavení**  >  **systémových jednotek** a v rozevíracím seznamu **škálování systémových jednotek** vyberte **měřiče**. 
![systémové jednotky](media/3dsmax/system-units.jpg)

U systémových jednotek nastavených na měřiče můžeme začít vytvářet naše modely. V naší ukázkové scéně vytvoříme několik objektů box, z nichž každá představuje jiný typ materiálu – například kov, pryž, plast atd. 

>[!TIP]
>Při vytváření prostředků se vhodným způsobem pojmenujte podle svých potíží. Díky tomu bude snazší je najít později, pokud má scéna mnoho objektů.

![přejmenovat – objekty](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Přiřazení materiálů

S některými objekty vytvořenými v naší scéně – v tomto případě několik datových krychlí můžeme instalaci PBR spustit:

* Na hlavním panelu nástrojů klikněte na ikonu **editoru materiálů** , jak je znázorněno na následujícím obrázku. K otevření editoru můžete také na klávesnici stisknout **M** . Editor materiálů má dva režimy, které lze vybrat v rozevíracím seznamu **režimy** – režim **editoru kompaktních materiálů** a režim **SLAT materiál** . Vzhledem k tomu, že je tato scéna poměrně jednoduchá, použijeme **kompaktní režim**.

* V editoru materiálů se zobrazí řada koulí – tyto oblasti jsou naše materiály. Jeden z těchto materiálů přiřadíme každému objektu – box – v naší scéně. Chcete-li provést toto přiřazení, vyberte nejprve jeden z objektů v hlavním zobrazení. Po provedení tohoto výběru klikněte na první koule v okně editoru materiálů. Po přiřazení k objektu se vybraný materiál zvýrazní, jak je znázorněno na následujícím obrázku.

* Klikněte na tlačítko **přiřadit materiál k výběru** , jak je znázorněno na obrázku. Vybraný materiál byl nyní přiřazen k vybranému objektu.
![přiřazení materiálu](media/3dsmax/assign-material.jpg)

V editoru materiálů můžete vybrat typy materiálů z nejrůznějšího výběru v závislosti na použitém případu použití. Ve výchozím nastavení je typ materiálu obvykle nastaven na **Standard** . Tento materiál je základní materiál, který není vhodný pro instalaci PBR, takže je potřeba změnit typ materiálu na obsah PBR. Upřednostňovaným **3DSMax** materiálem pro projekty vzdáleného vykreslování Azure je **fyzický materiál**.

* V editoru materiálů klikněte na kartu **standardní** a v prohlížeči materiál/mapa, který otevře výběr **fyzického materiálu**. Tato akce převede přiřazené **standardní** materiály na **fyzický materiál**PBR.
![fyzický materiál](media/3dsmax/physical-material.jpg)

* V editoru materiálů se teď zobrazí vlastnosti fyzického materiálu (viz níže) a můžeme začít přiřazovat textury k assetu.
![textury – seznam](media/3dsmax/textures-list.jpg)

Jak je vidět na obrázku výše, je k dispozici celá řada map a textur, které lze přidat do materiálu. Pro naše účely použijeme v materiálu jenom pět slotů.

>[!TIP]
>Je vhodné vhodně pojmenovat materiály, jak je znázorněno na obrázku výše.

Nyní můžeme začít přiřazovat textury k našemu materiálu. Způsob generování textur se může lišit v závislosti na preferencích nebo dokonce i v závislosti na využití. Například může být příjemné použít rozdlaždicové textury, které lze použít na jakýkoli Asset, nebo můžete vyžadovat, aby určité části projektu nebo prostředku měly vlastní sadu textur. Můžete chtít použít obecné textury dlaždic získané online nebo je vytvořit sami v aplikacích, jako je **Photoshop**, **Quixel Suite**, **sada látek** atd. 

Než začneme přiřazovat textury, ale budeme muset vzít v úvahu souřadnice textury prostředků (UVW). I když je doporučený postup při použití jakýchkoli textur pro model, aby se zajistilo, že se model nebalí (textura se nezobrazí správně bez správného rozbalení UV), je důležité pro naše účely, pokud hodláte použít **překrytí** mapu v našem modelu. Na rozdíl od **Stingray shaderu** v **Maya**nemá **fyzický materiál** v **3dsmax** vyhrazenou oblast pro texturu **překrytí v okolí** . Proto použijeme mapu AO na jiný slot a umožníme ji používat odděleně od ostatních textur (například textury rozdlaždice), přiřadíme jí vlastní kanál mapy UVW. 

Začneme přiřazením **modifikátoru uvw rozbalení** do našeho modelu, jak je znázorněno níže:

* V editoru vlastností vybraných objektů klikněte na seznam modifikátorů a v rozevíracím seznamu, který se otevře, přejděte dolů a vyberte rozbalení UVW. Tato akce použije pro náš Asset modifikátor UVW pro rozbalení.
![zrušit zalamování – modifikátor](media/3dsmax/unwrap-modifier.jpg)

* Kanál mapy je nastaven na jeden. Je v kanálu mapy, který se obvykle provede vaším hlavním rozbalením. V našem případě byl objekt rozbalení bez překrývání souřadnic textury (UV).
![nezabalená – uvw](media/3dsmax/unwrapped-uvw.jpg)

Dalším krokem je vytvoření druhého kanálu mapy UV.

* Zavřete editor UV, pokud je otevřený a v části kanál v nabídce **Upravit UV** změňte číslo kanálu na dvě. Kanál mapování 2 je očekávaným kanálem pro překrytí mapy pro okolní prostředí. 

* V dialogovém okně **Upozornění na změnu kanálu** , které se otevře, se nabídne možnost buď **přesunout** existující UV na kanál 1 do nového kanálu 2, nebo **opustit** stávající UV 's, který vytvoří nové **UV rozbalení** automaticky. Pouze vyberte **opustit** , pokud chcete vytvořit nové **UV rozbalení** pro překrytí mapu, která se liší od UV kanálu na mapě 1 (například pokud chcete použít textury dělení na sebe v kanálu 1). Pro naše účely **přesunete** UV z kanálu One na Channel 2, protože nepotřebujeme upravovat nový kanál UV.

>[!NOTE]
>I v případě, že jste zkopírovali **přesunuté** – UV rozbalení z mapy kanálu 1 na mapový kanál 2, můžete provést všechny nezbytné úpravy v novém UV kanálu, aniž by to ovlivnilo původní kanál mapy.

![Změna kanálu](media/3dsmax/channel-change.jpg)

S nově vytvořeným kanálem mapování se můžete vrátit na fyzický materiál v editoru materiálů a začít přidávat do něj naše textury. Nejdřív přidáme mapu Ambient překrytí (**Ao**), protože je k dispozici další krok, který jim umožní pracovat správně. Jakmile se mapa AO připojí k našemu materiálu, musíme mu dát pokyn, aby používal kanál mapy 2.

* Jak bylo zmíněno dříve, není k dispozici žádný vyhrazený slot pro mapy AO v **3dsmax fyzickém materiálu**. Místo toho použijeme mapu AO pro oblast **hrubosti difúze** .

* V seznamu **generických map** fyzického materiálu klikněte na slot pro **hrubou mapu difúze**a načtěte vaši mapu Ao. **No Map**

* Ve vlastnostech textury AO se ve výchozím nastavení zobrazí kanál mapy nastavený na hodnotu **1** . Změňte tuto hodnotu na **2**. Tato akce dokončí kroky nezbytné pro přidání vaší ambientní mapy překrytí.

>[!IMPORTANT]
>Toto je důležitý krok, zejména v případě, že se UV v kanálu 2 liší od těch, které jsou v kanálu 1, protože osoba AO nebude správně mapována s nesprávným kanálem.

![přiřazení – Ao – mapa](media/3dsmax/assign-ao-map.jpg)

Nyní se budeme přiřazovat jako běžná mapa s naším materiálem PBR. Tato akce se trochu liší od **Maya** v tom, že normální mapování se neaplikuje přímo na slot mapy hrbolů (v **fyzických materiálech 3dsmax** není žádná slot normálního mapování), ale místo toho se přidá do normálního modifikátoru mapy, který je zapojený **do normálního slotu** .

* V části **speciální mapy** u vlastností fyzického materiálu (v editoru materiálů) klikněte na možnost **Nepoužívat** mapový úsek mapy **hrbolů**. 

* V prohlížeči materiál/mapa vyhledejte a klikněte na **normální nárazník**. Tato akce přidá modifikátor **normálního nárazníku** pro náš materiál.

* V **normálním modifikátoru hrbolů** klikněte **Normal**na normální **není žádné mapování** , najděte a pak načtěte svoji normální mapu.

* Ověřte, že je metoda nastavená na hodnotu **tangens** (ve výchozím nastavení by měla být standardně) a v případě potřeby přepínač **Překlopit zelenou (Y)**.

![normální – zatížení nárazníku ](media/3dsmax/normal-bump.jpg)
 ![ – normální – mapa](media/3dsmax/load-normal-map.jpg)

Když je naše běžná mapa správně přiřazená, můžeme pro dokončení nastavení fyzického materiálu přejít k přiřazení zbývajících textur. Tento proces je jednoduchý proces bez speciálního nastavení, které by bylo potřeba vzít v úvahu. Následující obrázek znázorňuje kompletní sadu textur přiřazených k našim materiálům: ![ všechny – textury](media/3dsmax/all-textures.jpg)

Když vytvoříte a nastavíte vaše materiály PBR, zamyslete se nad vytvářením instancí objektů ve scéně. Vytváření instancí podobných objektů ve vaší scéně – například NUTS, šrouby, šrouby umyvadel – v podstatě všechny objekty, které jsou stejné, můžou přinést významné úspory z hlediska velikosti souboru. Instance hlavního objektu mohou mít vlastní měřítko, rotaci a transformace, takže je možné je umístit podle potřeby na scéně. V **maximálním počtu 3D studia**je proces vytváření **instancí** jednoduchý.

* V hlavním zobrazení vyberte objekt nebo objekty, které chcete exportovat.

* Podržte **SHIFT** a přetáhněte prostředky nahoru pomocí nástroje Transform (přesunout). 

* V dialogu **možnosti klonování** , které se otevře, nastavte **objekt** na **instanci** a klikněte na **OK**. 
![instance – objekt](media/3dsmax/instance-object.jpg)

Tato akce vytvoří instanci objektu, kterou lze přesunout otočený nebo škálovat nezávisle na své nadřazené a jiné instanci tohoto nadřazeného prvku.

>[!IMPORTANT]
>Všechny změny, které provedete v případě, že se v režimu podobjektu provedete, se přenesou do všech instancí objektu, takže pokud pracujete s instancemi objektů – vrcholy, mnohoúhelníky atd., je nejdřív potřeba, abyste všechny změny, které provedete, ovlivnily všechny tyto instance. Mějte na paměti, že libovolný objekt s instancemi lze kdykoli vytvořit v jedinečném objektu. 

>[!TIP]
>Osvědčenými postupy týkajícími se vytváření instancí ve vaší scéně je jejich vytvoření při tom, jak budete procházet, protože později je nahrazování **kopií** u instancí objektů velmi obtížné. 

Jedna poslední věc, kterou je potřeba vzít v úvahu před tím, než se přesunete k procesu exportu, je způsob, jakým můžete chtít zabalit svou scénu nebo prostředek ke sdílení. V ideálním případě Pokud předáte Asset k klientovi nebo členovi týmu, budete chtít, aby mohli prostředek otevřít a zobrazit, protože by měl být zobrazený s minimálním množstvím Fuss. Je proto důležité zachovat cesty textury assetů relativně k souboru scény. Pokud cesty textur pro váš prostředek odkazují na místní disk nebo na absolutní cestu nebo umístění, nebudou načteny do scény, pokud se otevřou na jiném počítači, a to i v případě, že se soubor **. Max** koná ve stejné složce jako textury. Vzhledem k tomu, že je maximální velikost cesty k texturám v rámci sady 3D Studio, tento problém se vyřeší a je poměrně jednoduché.

* Na hlavním panelu nástrojů přepněte **File**  >  **Reference**  >  **přepínač sledování assetu**odkazů na soubor. 

* V prohlížeči sledování assetů, který se otevře, uvidíte všechny nebo většinu textur, které jste použili u vašich materiálů PBR uvedených ve sloupci **mapy/shadery** .

* Vedle sebe ve sloupci **Úplná cesta** se zobrazí cesta k umístění svých textur, což je největší pravděpodobný způsob jejich umístění v místním počítači.

* Nakonec se zobrazí sloupec s názvem **stav**. Tento sloupec indikuje, jestli se daná textura umístila a nastavila na scénu, nebo ne, a označí tuto texturu jedním z následujících podmínek: **OK**, **Nalezeno**nebo **soubor chybí**. První dva označují, že soubor byl nalezen a načten, zatímco poslední zjevně znamená, že nástroj pro sledování nedokázal najít soubor.
![textury – cesty](media/3dsmax/texture-paths.jpg)

Můžete si všimnout, že ne všechny textury jsou uvedeny v modulu Sledování assetů při prvním otevření. Neexistují žádné informace o tom, jak je spuštěno prostřednictvím procesu hledání cesty jednou nebo dvakrát obvykle vyhledává všechny textury scény. Proces vyhledávání cest je následující: 

* V okně sledování assetů klikněte **Shift**v seznamu + **mapy/shadery** **na** horní texturu a pokračujte podržením klávesy Shift na poslední texturu v seznamu. Tato akce vybere všechny textury v seznamu. Vybrané textury budou nyní zvýrazněny modře (viz obrázek výše).

* Klikněte pravým tlačítkem na výběr a v místní nabídce, která se otevře, vyberte **nastavit cestu**.

* V poli **zadat cestu assetu** , které se otevře, vyberte místní cestu k zobrazeným texturám a nahraďte ji následujícím `.\` a klikněte na **OK**. 

* Po určitém časovém intervalu (což se bude lišit v závislosti na tom, kolik textur je ve scéně a na jakém obrázku je vaše scéna) by se měl sledování prostředků vyřešit takto (viz obrázek).
![vyřešit – textury](media/3dsmax/resolve-textures.jpg)

Všimněte si, že sloupec **Úplná cesta** je nyní prázdný. To znamená, že scéna již nehledá příslušné textury v konkrétním (absolutním) umístění, ale vždy je najde, pokud je maximální soubor nebo související soubor FBX umístěn ve stejné složce jako textury. 

>[!NOTE]
>Někdy se může stát, že budete muset tento proces několikrát opakovat, abyste našli a vyřešili všechny textury a cesty. Nejedná se o nic, co by se to stalo, stačí opakovat, dokud nebudou pro všechny relevantní prostředky účtovány. Může se také jednat o případ, že některé soubory již nejsou nalezeny. V takovém případě jednoduše vyberte všechny assety v seznamu a klikněte na **odebrat chybějící cesty** (viz obrázek výše).

## <a name="fbx-export"></a>Export FBX

Když se sledování prostředků dokončí, můžeme teď přejít k FBX exportu. Proces je opět jednoduchý a lze jej provést několika způsoby. 

>[!TIP]
>Je dobré postupovat, pokud nechcete exportovat celou scénu, vyberete možnost exportovat pouze ty prostředky, které jsou potřeba. V rámci zvláště náročných zdrojů na pozadí může export trvat dlouhou dobu, takže bude smysl exportovat jenom to, co potřebujete.
>
>Doporučuje se, abyste používali modifikátory, jako je **Turbosmooth** nebo **Open SubDiv** atd. před exportem, protože mohou během exportu způsobit problémy. Před provedením této akce vždycky uložte scénu. 

* V rámci scény vyberte prostředky, které chcete exportovat, a na hlavním panelu nástrojů přejít na exportovat **File**  >  **Export**  >  **vybraný** soubor exportovat export.

* V dialogovém okně **Vybrat soubor pro export** zadejte nebo vyberte název výstupního souboru a v možnosti **Uložit jako typ** vyberte **Autodesk (*. FBX)**. Tato akce otevře nabídku export FBX. 

* Nezapomeňte, že pokud jste ve scéně vytvořili instance, které jsou důležité pro **zachování instancí** v nastaveních exportu FBX. 
![FBX – export](media/3dsmax/fbx-export.jpg)

Mějte na paměti, že dřív bylo zmíněno, že existuje několik způsobů, jak soubor exportovat. Pokud je při exportu záměr, aby se FBX sdílel spolu se svými texturými soubory ve složce nebo adresáři, pak se nastavení uvedené na obrázku níže musí použít a dobře fungovat. Jakmile vyberete nastavení, klikněte na tlačítko **OK**.
![FBX – nastavení](media/3dsmax/fbx-settings.jpg)

Pokud však nebudete chtít sdílet velké složky/adresáře textur spolu s FBX, můžete zvolit **vložení** TEXTUR do FBX. To znamená, že do jediného FBXu budou přidány celé jednotlivé textury Asset-. Mějte na paměti, že ale zkombinujete váš export do jediného assetu, který v důsledku toho bude soubor FBX podstatně větší.

>[!IMPORTANT]
>Pokud je váš výsledný FBX soubor větší než 2.4 GB, minimální verze nastavení exportu FBX (viz výše) by měla být 2016 nebo novější. Vzhledem k tomu, že novější verze mají 64 bitovou podporu, a proto podporují větší soubory.

* V nastavení Export FBX přepněte na * * vložit médium a potom kliknutím na **OK** exportujte do zahrnutých textur. 

Při exportu do FBX při použití fyzického materiálu se po kliknutí na OK v dialogovém okně pro export pravděpodobně zobrazí následující automaticky otevíraná okna s upozorněním: ![ Export – upozornění](media/3dsmax/export-warnings.jpg)

Toto upozornění jednoduše informuje uživatele o tom, že exportované materiály nemusí být kompatibilní s jinými balíčky softwaru. Vzhledem k tomu, že fyzický materiál je kompatibilní se vzdáleným vygenerováním Azure, nemusíte nic dělat. Jednoduše klikněte na **OK** a dokončete proces a zavřete okno.

## <a name="conclusion"></a>Závěr

Obecně platí, že tento typ materiálu vypadá realističtější, protože je založen na skutečné fyzika světla. Vytvoří další moderní efekt, aby se v reálném světě zdá, že scéna existuje.

## <a name="next-steps"></a>Další kroky

Nyní víte, jak nastavit materiály s pokročilým osvětlením pro objekty ve scéně. Dozvíte se taky, jak exportovat objekty do formátu FBX, který podporuje vzdálené vykreslování Azure. Dalším krokem je převod souboru FBX a jeho vizualizace ve vzdáleném vykreslování Azure.

>[!div class="nextstepaction"]
>[Rychlý Start: převod modelu pro vykreslování](../../quickstarts\convert-model.md)