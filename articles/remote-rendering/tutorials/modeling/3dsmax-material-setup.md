---
title: Nastavení materiálů PBR v nástroji 3ds Max
description: Vysvětluje, jak nastavit fyzicky založené materiály pro vykreslování v 3ds Max a exportovat je do formátu FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96020275"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Kurz: nastavení fyzicky založených materiálů pro vykreslování v 3ds Max

## <a name="overview"></a>Přehled
V tomto kurzu se naučíte:

>[!div class="checklist"]
>
> * Přiřaďte materiály s pokročilým osvětlením pro objekty ve scéně.
> * Zpracování vytváření instancí objektů a materiálů
> * Exportujte scénu do formátu FBX a vyberte důležité možnosti.

> [!Note]
> Postup popsaný v tomto kurzu funguje v 3ds Max 2019 a 3ds Max 2020.
> Změna způsobu, jakým aplikace 3ds Max 2021 exportuje mapy hrbolů znamená, že služba převodu nebude při použití této verze najít normální mapy.

Vytvoření [fyzicky založených materiálů pro vykreslování](../../overview/features/pbr-materials.md) v 3ds Max je jednoduchá úloha. Podobným způsobem je nastavení PBR v dalších aplikacích pro vytváření obsahu, jako je Maya. Tento kurz je průvodcem pro základní nastavení funkce PBR shader a export FBX pro projekty vzdáleného vykreslování Azure.

Ukázková scéna v tomto kurzu obsahuje počet objektů mnohoúhelníkového pole. Přidělují se k různým materiálům, jako jsou dřevo, kov, namalované kovové, plastová a pryžová. V podstatě řečeno, každý materiál obsahuje všechny nebo většinu následujících textur:

* **Albedo**, což je mapa barev materiálu a nazývá se také **difúze** a **BaseColor**.
* **Kov**, který určuje, jestli je materiál kovový a které části jsou kovové. 
* **Hrubá**, která určuje, jak je povrch hrubá nebo hladec.
Ovlivňuje také ostrost nebo blurriness odrazů a světel na povrchu.
* **Normal (normální**), který přidává podrobnosti na povrch bez přidání dalších mnohoúhelníků Příklady podrobností jsou prodlevy a odsazení na kovovém povrchu nebo zrnitosti ve dřevě.
* **Ambientní překrytí**, který se používá k přidání jemných stínování a stínů kontaktu do modelu. Jedná se o mapu ve stupních šedi, která označuje, které oblasti modelu získají plné osvětlení (bílá) nebo úplný odstín (černá).

## <a name="prepare-the-scene"></a>Příprava scény
V nástroji 3ds Max je postup pro nastavení materiálu PBR následující.

Chcete-li začít, vytvoříme počet objektů box, z nichž každá představuje jiný typ materiálu.

>[!TIP]
>Je potřeba si poznamenat, že před zahájením vytváření prostředků pro vzdálené vykreslování používá měřiče měření.  
>
>Je proto dobré nastavit jednotky systému scény na měřiče. Je také vhodné nastavit **jednotky** na měřiče v nastaveních exportu FBX při exportu scény.

Následující snímek obrazovky ukazuje postup nastavení jednotek systému na měřiče v nástroji 3ds Max. 

1. V hlavní nabídce přejděte na vlastní nastavení   >  **jednotek nastavení**  >  **systémové jednotky**. V **systémové jednotce škále** vyberte **měřiče**: ![ snímek obrazovky, který ukazuje, jak nastavit jednotky systému.](media/3dsmax/system-units.jpg)

1. Nyní můžeme začít vytvářet modely. V ukázce scény vytvoříme několik objektů box, z nichž každá představuje jiný typ materiálu. Například kov, pryžový a plast. 

   >[!TIP]
   >Při vytváření assetů je vhodné pojmenovat je vhodným způsobem. To usnadňuje vyhledání později, pokud scéna obsahuje mnoho objektů.

1. Přejmenujte objekty, jak je znázorněno na následujícím snímku obrazovky: 

   ![Snímek obrazovky, který ukazuje, jak přejmenovat objekty.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Přiřazení materiálů

Teď, když máme v naší scéně nějaké objekty, můžeme v tomto případě několik datových krychlí zahájit instalaci PBR:

1. Na hlavním panelu nástrojů vyberte ikonu **editoru materiálů** , jak je znázorněno na následujícím snímku obrazovky. Můžete také vybrat **M** na klávesnici a otevřít Editor. Editor materiálů má dva režimy, které můžete vybrat v seznamu **režimy** : režim **editoru kompaktních materiálů** a režim **editoru surovin** . Vzhledem k tomu, že je tato scéna relativně jednoduchá, použijeme kompaktní režim.

1. V editoru materiálů uvidíte řadu koulí. Tyto koule jsou materiály. Jeden z těchto materiálů přiřadíme každému objektu (každé pole) ve scéně. Chcete-li přiřadit materiály, nejprve vyberte jeden z objektů v hlavním zobrazení. Pak vyberte první koule v editoru materiálů. Po přiřazení k objektu se vybraný materiál zvýrazní, jak je znázorněno na následujícím obrázku.

1. Vyberte **přiřadit materiál k výběru**, jak je znázorněno na obrázku. Materiál je nyní přiřazen k vybranému objektu.

   ![Snímek obrazovky, který ukazuje, jak přiřadit materiály.](media/3dsmax/assign-material.jpg)

    V editoru materiálů si můžete vybrat z nejrůznějších typů materiálu v závislosti na vašich potřebách. Ve výchozím nastavení je typ materiálu obvykle nastaven na **Standard** . Tento materiál je základním materiálem, který není vhodný pro instalaci PBR. Musíme proto změnit typ materiálu na PBR. Fyzický materiál je upřednostňovaným materiálem 3DS pro projekty vzdáleného vykreslování Azure.

1. V editoru materiálů vyberte kartu **Standard** . V **prohlížeči materiál/mapa** vyberte **fyzický materiál**. Tato akce převede přiřazený **standardní** materiál na fyzický materiál PBR.

   ![Snímek obrazovky, který ukazuje, jak změnit materiál.](media/3dsmax/physical-material.jpg)

    V editoru materiálů se nyní zobrazí vlastnosti fyzického materiálu, jak je znázorněno na následujícím snímku obrazovky. Nyní můžete začít přiřazovat textury k assetu.

   ![Snímek obrazovky, který zobrazuje seznam textur.](media/3dsmax/textures-list.jpg)

Jak vidíte, existuje celá řada map a textur, které lze přidat do materiálu. Pro tento kurz používáme v materiálech jenom pět slotů pro texturu.

>[!TIP]
>Je dobré pojmenovat materiály vhodně, jak je znázorněno na předchozím snímku obrazovky.

Způsob generování textur se může lišit v závislosti na preferencích nebo použití. Například můžete chtít použít textury dělení na sebe, které lze použít na jakýkoli Asset. Nebo může být nutné, aby určité části projektu nebo assetu měly vlastní sady textur. Možná budete chtít použít obecné textury dlaždic, které můžete získat online. Můžete je také vytvořit sami v aplikacích, jako je Photoshop, Quixel Suite a sada látek.

Než začneme přiřazovat textury, musíme zvážit souřadnice textury assetu (UVW). Je osvědčeným postupem použití všech textur na model, aby bylo zajištěno, že je model rozbalen. (Textury se nezobrazí správně, aniž by došlo ke správnému rozbalení UV.) To je obzvláště důležité pro naše účely, protože chceme v našem modelu použít mapu překrytí (AO). Na rozdíl od Stingray shaderu v Maya nemá fyzický materiál v 3ds Max vyhrazený slot AO s texturou. Proto použijeme mapu AO na jinou pozici. Aby ho bylo možné použít odděleně od ostatních textur (například textur dláždění), přiřadíme mu vlastní kanál mapy UVW. 

Začneme přiřazením modifikátoru UVW pro rozbalení do modelu, jak je znázorněno na následujícím snímku obrazovky. 

- V editoru vlastností vybraných objektů vyberte Seznam modifikátorů. V rozevíracím seznamu, který se zobrazí, přejděte dolů a vyberte **rozbalení uvw**. Tato akce aplikuje na Asset modifikátor UVW Unwrap.
![Snímek obrazovky, který ukazuje, jak vybrat rozbalení UVW](media/3dsmax/unwrap-modifier.jpg)

  Kanál mapy je nastaven na hodnotu 1. Obvykle budete provádět hlavní rozbalení v kanálu mapy 1. V tomto případě byl objekt rozbalení bez překrývání souřadnic textury (UV).
![Snímek obrazovky, který ukazuje rozbalení souřadnic textury (UVW).](media/3dsmax/unwrapped-uvw.jpg)

Dalším krokem je vytvoření druhého kanálu mapy UV.

1. Zavřete editor UV, pokud je otevřený. V části **kanál** v nabídce **Upravit UVs** změňte číslo kanálu na **2**. Kanál mapování 2 je očekávaným kanálem pro mapy AO. 

1. V dialogovém okně **Upozornění na změnu kanálu** můžete buď **přesunout** existující UVs z kanálu 1 do nového kanálu 2, nebo **opustit** existující UVs, které vytvoří nové UV rozbalení automaticky. Vyberte **opustit** pouze v případě, že plánujete vytvořit nové UV rozbalení pro rozvržení Ao, které se liší od UVs na mapě kanálu 1. (Například pokud chcete použít textury dělení na sebe v kanálu 1.) V tomto kurzu přesuneme UVs z kanálu One na Channel 2, protože nepotřebujeme upravovat nový kanál UV.

   >[!NOTE]
   >I když jste zkopírovali (přesunuli) UV rozbalení z mapového kanálu 1 na mapový kanál 2, můžete provést všechny nezbytné úpravy nového kanálu UVs, aniž by to ovlivnilo původní kanál mapy.

   ![Snímek obrazovky, který zobrazuje upozornění na změnu kanálu.](media/3dsmax/channel-change.jpg)

Teď, když jsme vytvořili nový kanál mapy, se můžeme vrátit na fyzický materiál v editoru materiálů a začít přidávat do něj naše textury. Nejprve přidáme mapu AO, protože existuje další krok, který mu umožní pracovat správně. Až se mapa AO připojí k našemu materiálu, musíme ji nakonfigurovat tak, aby používala mapový kanál 2.

Jak bylo uvedeno dříve, není k dispozici žádné vyhrazené místo pro mapy AO v fyzických materiálech 3DS. Místo toho použijeme mapu AO pro slot **hrubosti difúze** .

1. V seznamu **obecných map** fyzického materiálu vyberte možnost **bez mapového** slotu vedle **difúze** a načtěte svoji mapu Ao.

1. Ve vlastnostech textury AO je kanál mapy ve výchozím nastavení nastaven na hodnotu **1** . Změňte tuto hodnotu na **2**. Tato akce dokončí kroky nutné k přidání mapy AO.

   >[!IMPORTANT]
   >Toto je důležitý krok, zejména v případě, že se vaše UVs v kanálu 2 liší od těch, které jsou v kanálu 1, protože osoba AO nebude správně namapována, pokud je vybrán špatný kanál.

   ![Snímek obrazovky, který ukazuje, jak přiřadit mapu AO](media/3dsmax/assign-ao-map.jpg)

Teď k materiálu PBR přiřadíme normální mapu. Tato akce se trochu liší od procesu v Maya. Normální mapa se neaplikuje přímo na slot mapy hrbolů. (Ve fyzickém materiálu 3DS není k dispozici žádný normální mapový slot.) Místo toho přidáte normální mapu do normálního modifikátoru mapy, který je do normálního slotu zapojen.

1. V části **speciální mapy** vlastností fyzického materiálu (v editoru materiálů) vyberte možnost **bez mapového** slotu vedle **mapy hrbolů**. 

1. V **prohlížeči materiál/mapa** Najděte a vyberte **normální nárazník**. Tato akce přidá pro materiál **normální modifikátor nárazníku** .

1. V **normálním modifikátoru hrbolů** vyberte **bez mapování** vedle **normální**. Najděte a načtěte normální mapu.

1. Ujistěte se, že je metoda nastavená na hodnotu **tangens**. (Mělo by být ve výchozím nastavení.) V případě potřeby přepněte na **Překlopit zelenou (Y)**.

   ![Snímek obrazovky, který ukazuje, jak vybrat normální nárazník. ](media/3dsmax/normal-bump.jpg)
    ![ Snímek obrazovky, který ukazuje načtení normální mapy.](media/3dsmax/load-normal-map.jpg)

Když je normální mapa správně přiřazená, můžeme zbývající textury přiřadit k dokončení nastavení fyzického materiálu. Tento proces je jednoduchý. Neexistují žádná zvláštní nastavení, která by bylo potřeba zvážit. Následující snímek obrazovky ukazuje úplnou sadu textur přiřazených k materiálu: 

![Snímek obrazovky, který zobrazuje úplnou sadu textur přiřazených k materiálu.](media/3dsmax/all-textures.jpg)

Teď, když se vytvářejí a nastavují materiály PBR, se zamyslete nad objekty vytváření instancí ve scéně. Instance podobné objekty ve scéně, jako jsou ořechy, šrouby, šrouby a pračky. Všechny objekty, které jsou stejné, mohou vracet významné úspory z hlediska velikosti souboru. Instance hlavního objektu mohou mít vlastní měřítko, rotaci a transformace, takže je můžete podle potřeby umístit do scény. V 3ds Max je proces vytváření instancí jednoduchý.

1. V hlavním zobrazení vyberte objekt nebo objekty, které chcete exportovat.

1. Podržte klávesu **SHIFT** a přetáhněte prostředky směrem nahoru pomocí nástroje transformace (přesunout). 

1. V dialogovém okně **možnosti klonování** nastavte **objekt** na **instance** a pak vyberte **OK**:

   ![Snímek obrazovky dialogového okna možnosti klonování.](media/3dsmax/instance-object.jpg)

Tato akce vytvoří instanci objektu, kterou lze přesunout, otočit nebo škálovat nezávisle na své nadřazené a jiné instanci tohoto nadřazeného prvku.

>[!IMPORTANT]
>Všechny změny, které provedete v případě, že jste provedli v režimu dílčích objektů, jsou přenášeny do všech instancí objektu. Takže pokud pracujete s komponentami instance objektu, jako jsou vrcholy a mnohoúhelníkové obličeje, ujistěte se, že chcete, aby všechny změny, které provedete, ovlivnily všechny instance. Mějte na paměti, že libovolný objekt s instancemi lze kdykoli vytvořit v jedinečném objektu. 

>[!TIP]
>Při vytváření instancí ve scéně je vhodné při procházení vytvořit instance. Nahrazování kopií u instancí objektů později je obtížné. 

Jedna poslední věc, kterou je třeba zvážit před tím, než se přesunete k procesu exportu, je způsob, jakým můžete chtít zabalit svou scénu nebo prostředek ke sdílení. V ideálním případě, Pokud předáte Asset pro klienty nebo členy týmu, budete chtít, aby mohli prostředek otevřít a zobrazit, protože by měl být zobrazený s minimálním množstvím Fuss. Proto je důležité zachovat cesty textury assetu relativně k souboru scény. Pokud cesty textury pro váš prostředek ukazují na místní disk nebo absolutní cestu nebo umístění, nebudou načteny do scény, pokud se otevřou na jiném počítači, a to i v případě, že soubor. Max je ve stejné složce jako textury. Vytváření cest k texturám relativně v 3ds Max tento problém řeší a je poměrně jednoduché.

1. Na hlavním panelu nástrojů   >    >  **přepněte přepínač sledování assetu** odkazů na soubor. 

1. V okně sledování assetů uvidíte všechny nebo většinu textur, které jste použili pro materiály PBR uvedené ve sloupci **mapy/shadery** .

1. Vedle nich se ve sloupci **Úplná cesta** zobrazí cesta k umístění textur, což je pravděpodobně cesta k umístění v místním počítači.

1. Nakonec se zobrazí sloupec s názvem **stav**. Tento sloupec označuje, zda daná textura byla umístěna a použita pro vaši scénu. Označí texturu jedním z těchto podmínek: **OK**, **Nalezeno** nebo **soubor chybí**. První dva označují, že se soubor našel a načetl. Poslední zjevně znamená, že nástroj pro sledování nedokázal soubor najít.
 
   ![Snímek obrazovky zobrazující okno Sledování prostředků](media/3dsmax/texture-paths.jpg)

Při prvním otevření okna pro sledování prostředků si můžete všimnout, že ne všechny textury jsou uvedeny. Nejedná se o nic, co se týká. Spuštění prostřednictvím procesu hledání cesty jednou nebo dvakrát obvykle vyhledává všechny textury scény. Proces vyhledávání cest je následující: 

1. V okně sledování assetů podržte stisknutou klávesu **SHIFT** a vyberte horní texturu v seznamu **mapy/shadery** a pokračujte podržením **klávesy SHIFT** a vyberte poslední texturu v seznamu. Tato akce vybere všechny textury v seznamu. Vybrané textury jsou zvýrazněny modře. (Viz předchozí snímek obrazovky.)

1. Klikněte pravým tlačítkem na výběr a vyberte **nastavit cestu**.

1. V poli **zadat cestu prostředku** vyberte místní cestu k texturám a nahraďte ji `.\` .  Vyberte **OK**. 

    Okno Sledování prostředků se aktualizuje tak, jak je znázorněno na následujícím snímku obrazovky. Tato aktualizace může nějakou dobu trvat, v závislosti na tom, kolik textur je ve vaší scéně, a na tom, jak velkou má vaše scéna.
![Screensthot, která zobrazuje aktualizované okno Sledování prostředků.](media/3dsmax/resolve-textures.jpg)

Všimněte si, že sloupec **Úplná cesta** je nyní prázdný. To znamená, že scéna již nehledá příslušné textury v konkrétním (absolutním) umístění. Vždy je najde, pokud je soubor. Max nebo související soubor FBX ve stejné složce jako textury. 

>[!NOTE]
>Možná budete muset tento proces opakovat několikrát, abyste našli a vyřešili všechny textury a cesty. Nejedná se o nic, co se týká. Pouze opakujte tento postup, dokud nebudou pro všechny relevantní prostředky účtovány. V některých případech se některé soubory nenaleznou. V takovém případě jednoduše vyberte všechny prostředky v seznamu a pak vyberte **odebrat chybějící cesty**. (Viz předchozí obrázek.)

## <a name="fbx-export"></a>Export FBX

Teď, když jsme vytvořili relativní cesty k texturám, můžeme přejít k FBX exportu. Proces je znovu jednoduchý a můžete ho provést několika způsoby. 

>[!TIP]
>Pokud nechcete exportovat celou scénu, je vhodné vybrat možnost exportovat pouze prostředky, které potřebujete. Na scénách náročných na prostředky může export trvat dlouhou dobu.
>
>Pokud jste použili modifikátory, jako je Turbosmooth nebo Open SubDiv, je vhodné je před exportem sbalit, protože můžou během exportu způsobit problémy. Před sbalením si nezapomeňte svoji scénu Uložit. 

1. Ve scéně vyberte prostředky, které chcete exportovat. Na hlavním panelu nástrojů přejdete na možnost  >    >  **Export** exportu souborů.

1. V dialogovém okně **Vybrat soubor pro export** zadejte nebo vyberte název výstupního souboru. V seznamu **Uložit jako typ** vyberte **Autodesk (*. FBX)**. Tato akce otevře okno Export FBX.

  >[!IMPORTANT] 
  >Pokud jste ve své scéně vytvořili instance, je důležité vybrat možnost **zachovat instance** v nastaveních exportu FBX. 

  ![Snímek obrazovky, který ukazuje, jak exportovat do FBX.](media/3dsmax/fbx-export.jpg)

  Pamatujte si, že existuje několik způsobů, jak soubor exportovat. Pokud je záměrem sdílet FBX spolu s jeho soubory textury ve složce nebo adresáři, nastavení zobrazená na následujícím snímku obrazovky by měla dobře fungovat. 

   Pokud nechcete sdílet velké složky/adresáře textur spolu s FBX, můžete se rozhodnout pro vložení textur do FBX. Pokud vložíte textury, bude do jednoho FBX přidána celá aktiva, včetně textur. Tím se váš export zkombinuje do jediného assetu, ale soubor FBX se v důsledku toho podstatně zvětší.

   >[!IMPORTANT]
   >Pokud výsledný soubor FBX je větší než 2,4 GB, minimální verze zadaná v nastaveních exportu FBX by měla být 2016 nebo novější. (Viz předchozí snímek obrazovky.) Novější verze mají 64 bitovou podporu, takže podporují větší soubory.

1. Pokud chcete exportovat scénu se zahrnutými texturami, v okně Export * FBX vyberte **vložit médium**. 

1. Vyberte zbývající nastavení a pak vyberte **OK**:

    ![Snímek obrazovky zobrazující nastavení exportu FBX](media/3dsmax/fbx-settings.jpg)


   Při exportu do FBX při použití fyzického materiálu se pravděpodobně po výběru **OK** v okně exportu FBX zobrazí následující upozornění: 

   ![Snímek obrazovky s upozorněním, že chyba exportu materiálu se nezdařila](media/3dsmax/export-warnings.jpg)

   Toto upozornění vás informuje, že exportované materiály nemusí být kompatibilní s jinými balíčky softwaru. Vzhledem k tomu, že fyzický materiál je kompatibilní se vzdáleným vygenerováním Azure, nemusíte si dělat starosti s tímto upozorněním. 

1. Výběrem **OK** dokončete proces a zavřete okno.

## <a name="conclusion"></a>Závěr

Obecně platí, že tento typ materiálu vypadá realističtější, protože je založen na skutečné fyzika světla. Vytvoří další moderní efekt, aby se v reálném světě zdá, že scéna existuje.

## <a name="next-steps"></a>Další kroky

Nyní víte, jak nastavit materiály s pokročilým osvětlením pro objekty ve scéně. Naučíte se také, jak exportovat objekty do formátu FBX, který podporuje vzdálené vykreslování Azure. Dalším krokem je převod souboru FBX a jeho vizualizace ve vzdáleném vykreslování Azure.

>[!div class="nextstepaction"]
>[Rychlý start: Převod modelu pro vykreslování](../../quickstarts\convert-model.md)
