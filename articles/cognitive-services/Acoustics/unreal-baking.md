---
title: Kurz Unreal zanesli v projektu
titlesuffix: Azure Cognitive Services
description: Tento dokument popisuje proces odeslání akustické zanesli pomocí rozšíření editoru Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854891"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Kurz Unreal zanesli v projektu
Tento dokument popisuje proces odeslání akustické zanesli pomocí rozšíření editoru Unreal.

Zanesli je pět kroků:

1. Vytvoření nebo označení navigační mřížky přehrávače
2. Geometrie akustického příznaku
3. Přiřazení vlastností akustického materiálu geometrii
4. Náhled umístění testu paměti
5. Zanesli

## <a name="open-the-project-acoustics-editor-mode"></a>Otevřete režim editoru akustického projektu

Importujte balíček modulu plug-in pro projekt akustického prostředí do projektu. Nápovědu k tomuto řešení najdete v tématu věnovaném [integraci Unreal](unreal-integration.md) . Jakmile je modul plug-in integrovaný, otevřete uživatelské rozhraní akustického prostředí kliknutím na ikonu nové zvukové režimy.

![Snímek obrazovky s možností režimu akustického Unreal editoru](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Označit objekty actor jako akustické

Karta objekty je první karta, která se zobrazí při otevření režimu akustického zobrazení. Pomocí této karty můžete označit objekty actor na úrovni, které do objektů actor přidávají značky **AcousticsGeometry** nebo **AcousticsNavigation** .

Vyberte jeden nebo více objektů na světovém průřezu nebo použijte část hromadného **výběru** , která vám umožní vybrat všechny objekty určité kategorie. Po výběru objektů použijte oddíl **označování** k použití požadované značky pro vybrané objekty.

Pokud něco nemá značku **AcousticsGeometry** ani **AcousticsNavigation** , bude v simulaci ignorována. Podporují se jenom statické sítě, navigační mřížky a krajiny. Pokud označíte cokoli jiného, bude se ignorovat.

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Části karty objekty

![Obrazovka karty akustické objekty v Unreal](media/unreal-objects-tab-details.png)

1. Tlačítka výběru karet (vybraná karta**objekty** ). Pomocí těchto tlačítek můžete procházet různými kroky při zanesli akustického provozu od shora dolů.
2. Stručný popis toho, co je třeba použít na této stránce.
3. K dispozici jsou selektory pro aktéry na úrovni.
4. Po kliknutí na tlačítko **Vybrat** budou vybrány všechny objekty na úrovni, které odpovídají alespoň jednomu z zkontrolovaných typů actor.
5. Kliknutím na zrušit **Výběr** se zruší aktuální výběr. To je totéž jako při stisknutí klávesy Escape.
6. Pomocí těchto přepínačů vyberte, zda chcete pro vybrané objekty actor použít geometrii nebo navigační značku.
7. Kliknutím na **značku** se přidá vybraná značka všem aktuálně vybraným aktérům.
8. Kliknutím na **Zrušit označení** se odebere vybraná značka ze všech aktuálně vybraných objektů Actors.
9. Kliknutím na **Vybrat značku** smažete aktuální výběr a vyberete všechny objekty actor s aktuálně vybranou značkou.
10. Tyto statistiky ukazují, kolik objektů actor je označeno jednotlivými typy značek.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Překrytíy značek a geometrie reflexe

Otevřete kartu objekty v okně akustické množství. Označte všechny objekty jako akustickou geometrii, pokud by měly occlude, odrážely nebo absorbovat zvuk. Geometrie akustického množství může zahrnovat věci, jako je například uzemnění, zdi, střechy, okna Windows & skla, rugs a velký nábytek. Pro tyto objekty můžete použít libovolnou úroveň složitosti. Vzhledem k tomu, že se scéna před simulací voxelized, vysoce podrobné sítě, jako jsou stromy s mnoha malými listy, nejsou dražší pro zanesli než zjednodušené objekty.

Nezahrnovat věci, které by neměly mít vliv na akustické, například na neviditelné sítě kolizí.

Transformace objektu v době výpočtu sondy (na kartě sondy níže) je opravena ve výsledcích zanesli. Přesunutí libovolného z označených objektů ve scéně bude vyžadovat, aby se výpočet sondy prováděl znovu a aby se scény mohla znovu vylepšovat.

### <a name="create-or-tag-a-navigation-mesh"></a>Vytvoření nebo označení navigační mřížky

Navigační síť slouží k umístění testovacích bodů pro simulaci. Můžete použít navigační mřížku [](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)Unreal, nebo můžete zadat vlastní navigační síť. Je nutné označit alespoň jeden objekt jako **akustickou navigaci**. Pokud používáte navigační mřížku Unreal, ujistěte se, že jste ji vytvořili jako první.

### <a name="acoustics-volumes"></a>Svazky s akustickými objemy ###

Existují další rozšířené možnosti přizpůsobení, které můžete provádět v navigačních oblastech pomocí **akustických svazků**. **Akustické svazky** jsou objekty actor, které můžete přidat do své scény, které umožňují vybrat oblasti, které se mají zahrnout do navigační mřížky a ignorovat je. Objekt actor zpřístupňuje vlastnost, kterou lze přepnout mezi "include" a "Exclude". "Include" svazky zajistí, že se budou brát v úvahu jenom oblasti navigační mřížky a "vyloučit" svazky, které tyto oblasti mají ignorovat. Svazky "vyloučit" se vždycky používají po svazcích "include". Nezapomeňte označit akustické **objemy** , jako je například **Navigace** prostřednictvím běžného procesu na kartě objekty. Tyto objekty actor nejsou automaticky označeny.

![Snímek vlastností svazků s akustickými Hlasitostmi v Unreal](media/unreal-acoustics-volume-properties.png)

"Vyloučit" svazky jsou primárně určeny k poskytnutí jemně odstupňované kontroly nad tím, kam neumísťují sondy pro zpřísnění využití prostředků.

![Snímek obrazovky s vyloučenými objemy akustického objemu v Unreal](media/unreal-acoustics-volume-exclude.png)

"Include" svazky jsou užitečné pro vytváření ručních sekcí scény, například pokud chcete svou scénu rozdělit do několika akustických zón. Například pokud máte velkou scénu, mnoho kilometrů je na druhou a máte dvě oblasti zájmu, na které chcete zanesli akustické množství. Do scény můžete nakreslit dva velké "vložené" svazky a pro každou z nich vytvořit soubory ACE po druhém. Potom můžete ve hře použít aktivační svazky kombinované s voláními podrobného plánu a načíst příslušný soubor ACE, když hráč přiblíží ke každé dlaždici.

**Zvukové svazky** omezují pouze navigaci a ***nikoli*** geometrii. Každý test v rámci "zahrnutého" **akustického svazku** bude při provádění simulace vlnovek stále vyžádat všechny potřebné geometrie mimo svazek. Proto by nemělo dojít k žádným výpadkům v překrytí ani jiným akustickým důsledkům, které pocházejí z přehrávače z jednoho oddílu do druhého.

## <a name="select-acoustic-materials"></a>Vybrat akustické materiály

Jakmile jsou objekty označené, klikněte na tlačítko **materiály** a přejděte na kartu materiály. Tato karta se použije k určení vlastností materiálu pro každý materiál na úrovni. Předtím, než budou všechny objekty actor označeny, bude prázdná.

Akustické materiály ovládají množství zvukové energie, které se projeví zpátky z každého povrchu. Výchozí akustický materiál má absorpci podobnou konkrétní. Akustické projekty naznačují materiály na základě názvu materiálu scény.

Reverberation čas daného materiálu v místnosti je nezávisle na jeho faktor absorpce s největšími materiály, které mají hodnoty absorpce v rozsahu 0,01 až 0,20. Materiály s absorpčními koeficienty nad tímto rozsahem jsou velmi absorpční. Pokud například zvuk místnosti je příliš reverberantý, změňte akustický materiál zdí, patra nebo horní meze na něco z vyšších absorptivity. Přiřazení akustického materiálu se vztahuje na všechny aktéry, které používají daný materiál scény.

![Graf znázorňující zápornou korelaci reverberation času s koeficientem absorpce](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Části karty materiály

![Obrazovka karty akustické objekty v Unreal](media/unreal-materials-tab-details.png)

1. Tlačítko karty **materiály** , které slouží k uvedení této stránky.
2. Stručný popis toho, co je třeba použít na této stránce.
3. Seznam materiálů použitých v úrovni, které jsou pořízeny od objektů actor označených jako **AcousticsGeometry**. Když kliknete na nějaký materiál, vyberou se všechny objekty ve scéně, které tento materiál používají.
4. Zobrazuje akustický materiál, ke kterému byl přiřazen materiál scény. Kliknutím na rozevírací seznam znovu přiřadíte materiál scény k jinému akustickému materiálu.
5. Zobrazuje koeficient akustické absorpce materiálu vybraného v předchozím sloupci. Hodnota nula znamená dokonale odrážet (bez absorpce), zatímco hodnota 1 znamená dokonale absorptive (bez reflexe). Změnou této hodnoty dojde k aktualizaci akustického materiálu (krok #4) na **vlastní**.

Pokud provedete změny v materiálech v rámci scény, budete muset přepnout tabulátory v modulu plug-in akustického projektu, aby se tyto změny projevily na kartě **materiály** .

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a kontrola umístění testu naslouchacího procesu

Po přiřazení materiálů přepněte na kartu sondy .

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Části karty sondy

![Snímek karty sondy akustického testu v Unreal](media/unreal-probes-tab-details.png)

1. Tlačítko karty sondy použité k uvedení této stránky
2. Stručný popis toho, co je potřeba udělat pomocí této stránky
3. Tuto možnost použijte k výběru hrubého nebo jemného rozlišení simulace. Hrubý je rychlejší, ale má určité kompromisy. Podrobnosti najdete v tématu věnovaném [řešení zanesli](bake-resolution.md) .
4. Vyberte umístění, do kterého se mají pomocí tohoto pole umístit datové soubory s akustickými daty. Klikněte na tlačítko s "..." pro použití výběru složky. Další informace o datových souborech najdete v tématu [datové soubory](#Data-Files) níže.
5. Datové soubory pro tuto scénu budou pojmenovány pomocí předpony uvedené zde. Výchozí hodnota je [název úrovně] _AcousticsData.
6. Kliknutím na tlačítko **Vypočítat** voxelize scénu a vypočítejte umístění zkušebních bodů. To se provádí místně na vašem počítači a musí se provést před tím, než zanesli. Po výpočtu sond budou výše uvedené ovládací prvky zakázané a toto tlačítko se změní na **jasné**. Kliknutím na tlačítko **Vymazat** vymažete výpočty a povolíte ovládací prvky, aby bylo možné přepočítat pomocí nových nastavení.

Sondy musí být umístěné prostřednictvím automatizovaného procesu, který je k dispozici na kartě sondy.


### <a name="what-the-calculate-button-calculates"></a>Co vypočítá tlačítko "vypočítat"

Tlačítko **Vypočítat** přijímá všechna data, která jste doposud poskytli (geometrie, navigace, materiály a hrubá/jemné nastavení) a projde několik kroků:

1. Vezme geometrii z mřížek scény a vypočítá Voxel svazek. Voxel svazek je prostorový svazek, který obklopuje celou scénu a skládá se z malých krychlí "voxels". Velikost voxels je určena frekvencí simulace, která je nastavena nastavením **rozlišení simulace** . Každý Voxel je označený jako "otevřený vzduch" nebo obsahuje geometrii scény. Pokud Voxel obsahuje geometrii, Voxel je označen koeficientem absorpce materiálu přiřazeného k této geometrii.
2. Pak pomocí navigačních dat vypočítá akusticky zajímavá místa, kde může hráč přejít. Pokusí se najít přiměřeně malou sadu těchto umístění, která zahrnuje menší oblasti, jako jsou například dveře a předsálí, a pak do místností pro otevření mezer. Pro malé scény je obvykle méně než 100 míst, zatímco velké scény mohou mít až 1000.
3. Pro každé z konečných umístění naslouchacího procesu, které počítá, určí počet parametrů, jako je například "otevřít" prostor, velikost místnosti, v níž se nachází, atd.
4. Výsledky těchto výpočtů jsou uloženy v souborech v umístění, které zadáte (viz [datové soubory](#Data-Files) níže).

V závislosti na velikosti scény a rychlosti počítače můžou tyto výpočty trvat několik minut.

Po dokončení těchto výpočtů můžete zobrazit náhled dat Voxel a míst testovacího bodu, abyste zajistili, že zanesli vám poskytne dobré výsledky. Věci, jako je chybná navigační mřížka nebo chybějící/další geometrie, budou obvykle ve verzi Preview snadno viditelné, takže je můžete opravit.


## <a name="debug-display"></a>Zobrazit ladění

Po dokončení výpočtu sondy se nový objekt actor objeví na světovém obrysu s názvem **AcousticsDebugRenderer**. Zaškrtnutím políček **sondy vykreslování** a **vykreslení Voxels** povolíte zobrazení ladění v zobrazení editoru.

![Snímek obrazovky s informacemi o proobjektech pro vykreslování objektů pro vyladění v editoru Unreal](media/acoustics-debug-renderer.png)

Pokud nevidíte žádné voxels nebo se překrývající testy na úrovni, ujistěte se, že je v zobrazení povolené vykreslování v reálném čase.

![Snímek obrazovky s možností vykreslování v reálném čase v Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels se zobrazí v okně scéna jako zelené kostky kolem zahrnuté geometrie. Voxels, které obsahují jenom Air, se nezobrazují. Kolem celé scény se nachází velký zelený rámeček, který označuje kompletní Voxel svazek, který se použije v simulaci.
Pohybujte kolem své scény a ověřte, jestli má voxels akustickou a occluding geometrii. Také ověřte, že neakustické objekty, jako jsou třeba sítě kolizí, nebyly voxelizedy. Kamera scény musí být v rozmezí od 5 měřičů objektu, aby se voxels zobrazovalo.

Pokud porovnáte voxels vytvořenou s hrubou přesností a přesnější rozlišením, uvidíte, že hrubý voxels bude dvakrát velký.

![Snímek obrazovky s akustickými voxelsy ve verzi Preview v editoru Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Body testu

Body testu jsou synonyma s možnými umístěními přehrávače (naslouchacího procesu). Při pečení vypočítává simulace akustické a propojující všechna možná umístění zdroje do každého bodu sondy. Za běhu se umístění naslouchacího procesu interpoluje mezi okolními zkušebními body.

Je důležité ověřit, zda existují body sondy kdekoli, kde se očekává, že hráč nacestuje na scéně. Body sondy jsou umístěny v navigační síti strojem akustického projektu a nelze je přesunout ani upravit, takže se ujistěte, že navigační síť pokrývá všechna možná umístění přehrávače kontrolou bodů sondy.

![Snímek obrazovky s ukázkami sond v Unreal](media/unreal-probes-preview.png)

Další podrobnosti najdete v tématu věnovaném řešení hrubého a jemného rozlišení v [zanesli](bake-resolution.md) .

## <a name="bake-your-level-using-azure-batch"></a>Zanesli vaší úrovně pomocí Azure Batch

Svou scénu můžete zanesli s výpočetním clusterem v cloudu pomocí služby Azure Batch. Modul plug-in Unrealí projektu se připojuje přímo k Azure Batch k vytvoření instance, správě a odtržení Azure Batch clusteru pro každou zanesli. Na kartě zanesli zadejte svoje přihlašovací údaje Azure, vyberte typ a velikost počítače clusteru a klikněte na zanesli.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Části karty zanesli

![Snímek obrazovky s akustickými Zanesliy v Unreal](media/unreal-bake-tab-details.png)

1. Tlačítko karty zanesli, které slouží k uvedení této stránky
2. Stručný popis toho, co dělat na této stránce.
3. Pole, která se mají zadat v přihlašovacích údajích Azure po vytvoření účtu Azure. Další informace najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
4. Spusťte Azure Portal ke správě předplatných, monitorování využití a zobrazení informací o fakturaci atd. 
5. Typ výpočetního uzlu služby Azure Batch, který se má použít pro výpočet. Typ uzlu musí být podporován vaším umístěním datového centra Azure. Pokud si to nejste jistí, ponechte na **Standard_F8s_v2**.
6. Počet uzlů, které se mají použít pro tento výpočet Číslo, které zde zadáte, bude mít vliv na čas k dokončení zanesli a je omezené vaším přidělením Azure Batchho jádra. Výchozí přidělení umožňuje pouze dva základní uzly a uzel 1 16 Core, ale lze je rozšířit. Další informace o omezeních přidělení základní části najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
7. Zaškrtnutím tohoto políčka nakonfigurujete výpočetní fond tak, aby používal [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Výpočetní uzly s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo mohou být kdykoli přerušeny.
8. Doba, po kterou se očekává, že bude trvat spuštění úlohy v cloudu. To nezahrnuje čas spuštění uzlu. Po spuštění úlohy se jedná o to, jak dlouho by měla být předtím, než se vrátí výsledky. Všimněte si, že toto je pouze odhad.
9. Celková velikost výpočetní doby potřebná ke spuštění simulace. Toto je celková velikost výpočetního času uzlu, který se bude používat v Azure. Další informace o použití této hodnoty najdete v tématu [odhad zanesli nákladů](#Estimating-bake-cost) níže.
10. Kliknutím na tlačítko zanesli odešlete zanesli do cloudu. Když je úloha spuštěná, zobrazí se místo toho **úloha zrušení úlohy** . Pokud na této kartě dojde k nějakým chybám nebo pokud pracovní postup na kartě sondy není dokončený, toto tlačítko se zakáže.
11. Počet testů pro vaši scénu, jak je vypočítáno na kartě **sondy** . Počet sond určuje počet simulací, které je třeba spustit v cloudu. Nemůžete zadat více uzlů, než jsou sondy.
12. Tato zpráva obsahuje informace o aktuálním stavu úlohy nebo o chybách na této kartě, o tom, co tyto chyby jsou.

Vždy můžete získat úplné informace o aktivních úlohách, výpočetních fondech a úložišti na [Azure Portal](https://portal.azure.com).

Zatímco úloha spouští tlačítko **zanesli** , se změní na **Zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Zrušení úlohy nelze vrátit zpět, nebudou k dispozici žádné výsledky a bude vám stále účtován za libovolný výpočetní čas Azure, který se používá před zrušením.

Po spuštění zanesli můžete Unreal zavřít. V závislosti na projektu, typu uzlu a počtu uzlů může zanesli cloudu trvat několik hodin. Stav úlohy zanesli se aktualizuje, když znovu nasadíte projekt a otevřete okno akustické. Pokud byla úloha dokončena, bude stažen výstupní soubor.

Přihlašovací údaje Azure se bezpečně ukládají na vašem místním počítači a jsou přidružené k vašemu projektu Unreal. Používají se výhradně k navázání zabezpečeného připojení k Azure.

### <a name="Estimating-bake-cost"></a>Odhad nákladů na Azure zanesli

K odhadu toho, jaké zanesli budou náklady, vezměte hodnotu zobrazenou pro **Odhadované výpočetní náklady**, což je doba trvání, a vynásobte ji hodinovou sazbou v místní měně vybraného **typu uzlu virtuálního počítače** . Výsledek nebude zahrnovat čas uzlu potřebný k zprovoznění uzlů. Pokud například vyberete **Standard_F8s_v2** pro typ uzlu, který má cenu $0.40/hod, a odhadované náklady na výpočetní výkon jsou 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy budou $0,40 × ~ 4 hodiny = ~ $1,60. Skutečné náklady budou pravděpodobně vyšší než vyšší čas na to, aby bylo možné uzly získat. Náklady na hodinové uzly najdete na stránce s [cenami za Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (pro kategorii vyberte výpočetní optimalizované nebo vysoký výkon).

### <a name="reviewing-the-bake-results"></a>Kontrola výsledků zanesli

Po dokončení zanesli ověřte, že se body voxels a sondy nacházejí v jejich očekávaných umístěních spuštěním modulu plug-in modulu runtime.

## <a name="Data-Files"></a>Datové soubory

Tento modul plug-in vytváří čtyři datové soubory v různých bodech. Za běhu je potřeba jenom jeden z nich a je umístěný ve složce Content/akustické obsah projektu, která se automaticky přidá do cesty k balíčku projektu. Ostatní tři jsou uvnitř zvukové složky dat a nejsou zabaleny.

* **[Project]/Config/ProjectAcoustics.cfg**: Tento soubor ukládá data, která zadáte do polí v uživatelském rozhraní režimu akustického prostředí. Umístění a název tohoto souboru nelze změnit. V tomto souboru jsou uložené další hodnoty, které mají vliv na zanesli, ale jsou pro pokročilé uživatele a neměly by se měnit.
* **[Project]/Content/Acoustics/[level]\_AcousticsData. ACE**: Tento soubor je vytvořený během simulace zanesli a obsahuje vyhledávací data používaná modulem runtime k vykreslování akustických dat scény. Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** . Pokud chcete tento soubor po vytvoření přejmenovat, odstraňte UAsset z projektu Unreal, přejmenujte soubor mimo Unreal v Průzkumníkovi souborů a pak tento soubor znovu importujte do Unreal a vytvořte nový UAsset. Přejmenování UAsset sám na sebe nebude fungovat.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Tento soubor uchovává geometrii akustické voxelized a vlastnosti materiálu. Vypočítáno pomocí tlačítka **Vypočítat** na kartě sondy. Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** .
* **[Project]/plugins/ProjectAcoustics/AcousticsData/[level]\_AcousticsData\_config. XML**: Tento soubor uchovává parametry vypočítané pomocí tlačítka **Vypočítat** na kartě **sondy** . Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** .

Je potřeba dbát, abyste neodstranili soubor *. ACE stažený z Azure. Tento soubor se nedá obnovit s výjimkou úprav scény.

## <a name="next-steps"></a>Další postup
* Prozkoumejte [ovládací prvky návrhu pro Unreal](unreal-workflow.md)
* Prozkoumejte [Koncepty návrhu projektových](design-process.md) řešení

