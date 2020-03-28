---
title: Kurz projektu Akustika Neskutečné pečení
titlesuffix: Azure Cognitive Services
description: Tento dokument popisuje proces odesílání akustiky péct pomocí Unreal editor rozšíření.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854891"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Kurz projektu Akustika Neskutečné pečení
Tento dokument popisuje proces odesílání akustiky péct pomocí Unreal editor rozšíření.

Existuje pět kroků k tomu péct:

1. Vytvoření nebo označení navigační sítě přehrávače
2. Geometrie akustiky tagů
3. Přiřazení vlastností akustických materiálů geometrii
4. Umístění náhledové sondy
5. Pečeme

## <a name="open-the-project-acoustics-editor-mode"></a>Otevření režimu editoru Project Acoustics

Importujte balíček modulu plug-in Project Acoustics do projektu. Nápovědu k tomu najdete v tématu [Neskutečná integrace.](unreal-integration.md) Jakmile je plugin integrován, otevřete uzly akustiky kliknutím na novou ikonu režimu akustiky.

![Snímek obrazovky s možností Režim ubezečné editoru Akustiky](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag herci pro akustiku

Karta Objekty je první karta, která se zobrazí při otevření režimu akustiky. Tato karta slouží k označení objekty actor ve vaší úrovni, která přidá **AcousticsGeometry** nebo **AcousticsNavigation** značky objekty actor.

Vyberte jeden nebo více objektů v části World Outliner nebo použijte část **Hromadný výběr,** abyste pomohli vybrat všechny objekty určité kategorie. Jakmile jsou objekty vybrány, použijte oddíl **Tagování** k aplikování požadované značky na vybrané objekty.

Pokud něco nemá ani **AcousticsGeometry** ani **AcousticsNavigation** tag, bude ignorována v simulaci. Podporována jsou pouze statická oka, nav oválná a krajinná. Pokud označíte něco jiného, bude ignorována.

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Díly kartě Objekty

![Snímek obrazovky s kartou Objekty akustiky v neskutečném](media/unreal-objects-tab-details.png)

1. Tlačítka pro výběr tabulátoru (vybraná karta**Objekty).** Pomocí těchto tlačítek projít různé kroky dělá akustika péct, shora dolů.
2. Stručný popis toho, co je třeba udělat pomocí této stránky.
3. Dostupné voliče pro aktéry na úrovni.
4. Klepnutím na **tlačítko Vybrat** vyberete všechny objekty v úrovni, které odpovídají alespoň jednomu z kontrolovaných typů objektů actor.
5. Kliknutím na **Odznačit vše** vymažete aktuální výběr. To je stejné jako bít únikový klíč.
6. Pomocí těchto přepínacích tlačítek zvolte, zda chcete použít značku Geometrie nebo Navigace na vybrané objekty actor.
7. Kliknutím na **značku** přidáte vybranou značku všem aktuálně vybraným aktérům.
8. Kliknutím na **zrušit tag** odeberete vybranou značku ze všech aktuálně vybraných aktérů.
9. Kliknutím na **Vybrat tagoluse** vymaže aktuální výběr a vyberete všechny aktéry s aktuálně vybranou značkou.
10. Tyto statistiky ukazují, kolik aktérů je označeno každým typem značky.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag akustika okluze a reflexe geometrie

Otevřete kartu Objekty v okně Akustika. Označte všechny objekty jako geometrii akustiky, pokud by měly okluzovat, odrážet nebo absorbovat zvuk. Geometrie akustiky může zahrnovat věci jako země, stěny, střechy, okna & okenní sklo, koberce a velký nábytek. Pro tyto objekty můžete použít libovolnou úroveň složitosti. Vzhledem k tomu, že scéna je voxelized před simulací, vysoce detailní ok, jako jsou stromy s mnoha malými listy, nejsou dražší péct než zjednodušené objekty.

Nezahrnejte věci, které by neměly ovlivnit akustiku, například neviditelné kolizní ok.

Transformace objektu v době výpočtu sondy (na kartě Sondy níže) je stanovena ve výsledcích pečení. Přesunutí některého z označených objektů ve scéně bude vyžadovat předělání výpočtu sondy a přepečení scény.

### <a name="create-or-tag-a-navigation-mesh"></a>Vytvoření nebo označení navigační sítě

Navigační síť se používá k umístění bodů sondy pro simulaci. Můžete použít [unreal je Nav Meze sítě hranice svazek](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), nebo můžete zadat vlastní navigační sítě. Jako **akustickou navigaci**je nutné označit alespoň jeden objekt . Pokud používáte navigační síť Unreal, ujistěte se, že jste ji nejprve vytvořili.

### <a name="acoustics-volumes"></a>Objemy akustiky ###

K dispozici je další, pokročilé přizpůsobení, které můžete provést v navigačních oblastech pomocí **akustických svazků**. **Akustické svazky jsou objekty,** které můžete přidat do scény, které umožňují vybrat oblasti, které mají být zahrnuty a ignorovány z navigační sítě. Objekt actor zpřístupňuje vlastnost, která lze přepínat mezi "Zahrnout" a "Vyloučit". Objemy "Zahrnout" zajišťují, že jsou považovány pouze oblasti navigační sítě uvnitř nich a svazky "Vyloučit" označují tyto oblasti, které mají být ignorovány. Svazky "Vyloučit" jsou vždy použity po "Zahrnout" svazky. Ujistěte se, že tag **Akustika svazky** jako **akustika navigace** prostřednictvím obvyklý proces na kartě Objekty. Tyto objekty actor ***nejsou*** automaticky označeny.

![Snímek obrazovky s vlastnostmi hlasitosti akustiky v neskutečném stavu](media/unreal-acoustics-volume-properties.png)

"Vyloučit" objemy jsou určeny především k tomu, aby jemně odstupňované kontroly o tom, kde není umístit sondy pro zpřísnění využití zdrojů.

![Snímek obrazovky s objemem vyloučené akustiky v neskutečném](media/unreal-acoustics-volume-exclude.png)

Svazky "Zahrnout" jsou užitečné pro vytváření ručních částí scény, například pokud chcete rozdělit scénu do více akustických zón. Například, pokud máte velkou scénu, mnoho kilometrů na druhou, a máte dvě oblasti zájmu, které chcete péct akustiku na. Můžete nakreslit dva velké svazky "Zahrnout" ve scéně a vytvářet soubory ACE pro každý z nich jeden po druhém. Pak ve hře, můžete použít spouštěcí svazky v kombinaci s podrobným voláním načíst příslušný soubor ACE, když se hráč přiblíží každé dlaždici.

**Akustika Svazky** omezují pouze navigaci a ***nikoli*** geometrii. Každá sonda uvnitř **svazku "Zahrnout" Akustiku** bude při provádění simulací vln stále vytahovat veškerou potřebnou geometrii mimo hlasitost. Proto by neměly existovat žádné nespojitosti v okluzi nebo jiné akustice vyplývající z křížení hráče z jedné sekce do druhé.

## <a name="select-acoustic-materials"></a>Výběr akustických materiálů

Jakmile jsou objekty označeny, klikněte na tlačítko **Materiály** a přejděte na kartu Materiály. Tato karta bude použita k určení vlastností materiálu pro každý materiál v úrovni. Před označením všech herců bude prázdné.

Akustické materiály řídí množství zvukové energie odražené od každého povrchu. Výchozí akustický materiál má absorpci podobnou betonu. Projekt Akustika navrhuje materiály založené na názvu materiálu scény.

Doba dozvuku daného materiálu v místnosti nepřímo souvisí s jeho absorpčním koeficientem, přičemž většina materiálů má hodnoty absorpce v rozsahu 0,01 až 0,20. Materiály s absorpčními koeficienty nad tento rozsah jsou velmi absorpční. Pokud například místnost zní příliš dozvuku, změňte akustický materiál stěn, podlahy nebo stropu na něco s vyšší absorpční hodnotou. Přiřazení akustického materiálu platí pro všechny objekty actor, které používají tento materiál scény.

![Graf znázorňující negativní korelaci doby dozvuku s absorpčním koeficientem](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Části karty Materiály

![Snímek obrazovky s kartou Objekty akustiky v neskutečném](media/unreal-materials-tab-details.png)

1. Tlačítko **Materiály,** které slouží k zobrazení této stránky.
2. Stručný popis toho, co je třeba udělat pomocí této stránky.
3. Seznam materiálů použitých v úrovni, převzato z aktice označené jako **AcousticsGeometry**. Kliknutím na materiál zde vyberete všechny objekty ve scéně, které tento materiál používají.
4. Zobrazuje akustický materiál, ke kterému byl materiál scény přiřazen. Kliknutím na rozevírací položku znovu přiřadíte materiál scény jinému akustickému materiálu.
5. Zobrazuje koeficient akustické absorpce materiálu vybraného v předchozím sloupci. Nulová hodnota znamená dokonale reflexní (bez absorpce), zatímco hodnota 1 znamená dokonale absorpční (bez odrazu). Změna této hodnoty aktualizuje materiál akustiky (krok #4) na **vlastní**.

Pokud provedete změny materiálů ve scéně, budete muset přepnout karty v modulu plug-in Akustika projektu, abyste viděli, že se tyto změny projeví na kartě **Materiály.**

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a kontrola umístění sondy posluchače

Po přiřazení materiálů přepněte na kartu **Sondy.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Části karty Sondy

![Snímek obrazovky s kartou Akustické sondy v neskutečném](media/unreal-probes-tab-details.png)

1. Tlačítko **Sondy** použité k zobrazení této stránky
2. Stručný popis toho, co je třeba udělat pomocí této stránky
3. Pomocí této možnosti můžete zvolit hrubé nebo jemné simulační rozlišení. Hrubý je rychlejší, ale má určité kompromisy. Podrobnosti najdete níže v [části Bake Resolution.](bake-resolution.md)
4. Pomocí tohoto pole zvolte umístění, kam mají být umístěny datové soubory akustiky. Klikněte na tlačítko s "..." použijete výběr složek. Další informace o datových souborech naleznete níže [v tématu Datové soubory.](#Data-Files)
5. Datové soubory pro tuto scénu budou pojmenovány pomocí zde uvedené předpony. Výchozí hodnota je "[Název úrovně]_AcousticsData".
6. Kliknutím na tlačítko **Vypočítat** scénu voxelize a vypočítat umístění bodů sondy. To se provádí lokálně na vašem počítači, a musí být provedeno před tím, než péct. Po výpočtu sond budou výše uvedené ovládací prvky zakázány a toto tlačítko se změní tak, aby **bylo**jasné . Klepnutím na tlačítko **Vymazat** vymažete výpočty a povolíte ovládací prvky, abyste je mohli přepočítat pomocí nových nastavení.

Sondy musí být umístěny prostřednictvím automatizovaného procesu uvedeného na kartě **Sondy.**


### <a name="what-the-calculate-button-calculates"></a>Co vypočítá tlačítko "Vypočítat"

Tlačítko **Vypočítat** přebírá všechna data, která jste dosud poskytli (geometrie, navigace, materiály a hrubé/jemné nastavení) a prochází několika kroky:

1. Vezme geometrii z ok scény a vypočítá objem voxelu. Objem voxelu je 3-dimenzionální objem, který obklopuje celou scénu a je tvořen malými kubickými "voxels". Velikost voxelů je určena frekvencí simulace, která je nastavena nastavením **rozlišení simulace.** Každý voxel je označen jako "pod širým nebem" nebo obsahuje geometrii scény. Pokud voxel obsahuje geometrii, je voxel označen koeficientem absorpce materiálu přiřazeného k této geometrii.
2. Poté použije navigační data k výpočtu akusticky zajímavých míst, kam by hráč mohl jít. Snaží se najít poměrně malý soubor těchto míst, která zahrnuje menší plochy, jako jsou dveře a chodby, a pak do místností, do otevřených prostor. U malých scén je to obvykle méně než 100 míst, zatímco velké scény mohou mít až tisíc.
3. Pro každé konečné posluchače umístění vypočítá, určuje řadu parametrů, jako je například jak "open" je prostor, velikost místnosti, ve které se nachází, atd.
4. Výsledky těchto výpočtů jsou uloženy v souborech v zadaném umístění (viz [datové soubory](#Data-Files) níže)

V závislosti na velikosti scény a rychlosti vašeho stroje mohou tyto výpočty trvat několik minut.

Po dokončení těchto výpočtů si můžete prohlédnout jak data voxelu, tak umístění bodů sondy, abyste zajistili, že pečení vám poskytne dobré výsledky. Věci, jako je špatná navigační síť nebo chybějící/extra geometrie, budou obvykle v náhledu rychle viditelné, takže je můžete opravit.


## <a name="debug-display"></a>Zobrazení ladění

Po dokončení výpočtu sondy se v world outlineru s názvem **AcousticsDebugRenderer**zobrazí nový objekt actor . Zaškrtnutím zaškrtávacích políček **Vykreslení sond** a **Voxels** umožníte zobrazení ladění uvnitř výřezu editoru.

![Snímek obrazovky s hercem Renderer emitátoru ladění akustiky v editoru Unreal](media/acoustics-debug-renderer.png)

Pokud na vaší úrovni nevidíte žádné voxely nebo sondy, ujistěte se, že je ve výřezu povoleno vykreslování v reálném čase.

![Snímek obrazovky s možností vykreslování v reálném čase v neskutečném](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels jsou zobrazeny v okně scény jako zelené kostky kolem zúčastněné geometrie. Voxels, které obsahují pouze vzduch nejsou zobrazeny. Kolem celé scény je velké zelené pole, které označuje celý objem voxelu, který bude použit v simulaci.
Pohybujte se po scéně a ověřte, zda má akusticky okličující geometrie voxels. Zkontrolujte také, zda nebyly objekty neakustiky, jako jsou kolizní ok, voxelizovány. Scéna kamera musí být do asi 5 metrů od objektu pro voxels ukázat.

Pokud porovnáte voxels vytvořené s hrubým rozlišením vs jemné rozlišení, uvidíte, že hrubé voxels jsou dvakrát tak velké.

![Snímek obrazovky s náhledem Acoustics voxels v editoru Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Body sondy

Body sondy jsou synonymem pro možné umístění přehrávače (posluchače). Při pečení simulace vypočítá akustiku spojující všechna možná zdrojová místa s každým bodem sondy. Za běhu je umístění posluchače interpolováno mezi blízkými body sondy.

Je důležité zkontrolovat, zda sondy existují kdekoli, kde se očekává, že hráč bude cestovat ve scéně. Body sondy jsou umístěny na navigační síti motorem Project Acoustics a nelze je přesunout ani upravit, takže se ujistěte, že navigační síť pokrývá všechna možná umístění hráčů kontrolou bodů sondy.

![Snímek obrazovky s náhledem akustických sond v neskutečném](media/unreal-probes-preview.png)

Další podrobnosti o hrubém vs jemném rozlišení najdete v tématu [Bake Resolution.](bake-resolution.md)

## <a name="bake-your-level-using-azure-batch"></a>Upéct úroveň pomocí Azure Batch

Scénu můžete upéct pomocí výpočetního clusteru v cloudu pomocí služby Azure Batch. Modul plug-in Project Acoustics Unreal se připojuje přímo k Azure Batch, aby se instance, správa a stržení clusteru Azure Batch pro každé pečení. Na kartě Pečeme zadejte svoje přihlašovací údaje Azure, vyberte typ a velikost clusterového počítače a klikněte na Pečeme.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Části karty pečeme

![Snímek obrazovky karty Akustika pečení v neskutečném](media/unreal-bake-tab-details.png)

1. Tlačítko Pečeme tab slouží k zobrazení této stránky.
2. Stručný popis toho, co dělat na této stránce.
3. Pole pro zadání přihlašovacích údajů Azure po vytvoření účtu Azure. Další informace najdete [v tématu Vytvoření dávkového účtu Azure](create-azure-account.md).
4. Spusťte portál Azure a spravujte svá předplatná, monitorujte využití a zobrazte fakturační údaje atd. 
5. Typ dávkového výpočetního uzlu Azure, který se má použít pro výpočet. Typ uzlu musí být podporován umístěním datového centra Azure. Pokud si nejste jisti, nechte **Standard_F8s_v2**.
6. Počet uzlů, které mají být pro tento výpočet určeny. Číslo, které zde zadáte, ovlivňuje čas dokončení pečení a je omezeno přidělením jádra Azure Batch. Výchozí přidělení umožňuje pouze pro dva 8 základních uzlů nebo jeden 16 jádrový uzel, ale lze rozbalit. Další informace o základních omezeních přidělení najdete [v tématu Vytvoření dávkového účtu Azure](create-azure-account.md).
7. Zaškrtnutím tohoto políčka nakonfigurujete výpočetní fond tak, aby používal [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Výpočetní uzly s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo mohou být kdykoli předpnuty.
8. Množství uplynulý čas, který se očekává, že pro vaši úlohu spustit v cloudu. To nezahrnuje čas spuštění uzlu. Jakmile se úloha spustí, je to o tom, jak dlouho by mělo trvat, než se dostanete zpět výsledky. Všimněte si, že se jedná pouze o odhad.
9. Celkové množství výpočetního času potřebného ke spuštění simulací. Toto je celkové množství výpočetního času uzlu, který se bude používat v Azure. Další informace o použití této hodnoty naleznete níže v [tématu Odhad nákladů](#Estimating-bake-cost) na pečení.
10. Klikněte na tlačítko Pečeme odeslat pečeme do cloudu. Při spuštění úlohy se místo toho zobrazí **funkce Zrušit úlohu.** Pokud jsou na této kartě nějaké chyby nebo pokud pracovní postup na kartě **Sondy** nebyl dokončen, bude toto tlačítko zakázáno.
11. Počet sondy pro vaši scénu vypočítaný na kartě **Sondy.** Počet sond určuje počet simulací, které je třeba spustit v cloudu. Nelze zadat více uzlů, než jsou sondy.
12. Tato zpráva informuje o aktuálním stavu úlohy nebo o chybách na této kartě, jaké jsou tyto chyby.

Úplné informace o aktivních úlohách, výpočetních fondech a úložišti můžete vždy získat na [webu Azure Portal](https://portal.azure.com).

Při spuštění úlohy se tlačítko **Pečeme** změní na **Zrušit úlohu**. Toto tlačítko slouží ke zrušení probíhající úlohy. Zrušení úlohy nelze vrátit zpět, žádné výsledky nebudou k dispozici a bude vám stále účtován a kredit za jakýkoli výpočetní čas Azure použitý před zrušením.

Jakmile začnete péct, můžete zavřít Unreal. V závislosti na projektu, typu uzlu a počtu uzlů může pečení v cloudu trvat několik hodin. Stav úlohy připečení bude aktualizován při opětovném načtení projektu a otevření okna Akustika. Pokud je úloha dokončena, bude stažen výstupní soubor.

Přihlašovací údaje Azure jsou bezpečně uloženy ve vašem místním počítači a přidruženy k projektu Unreal. Používají se výhradně k navázání zabezpečeného připojení k Azure.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Odhad nákladů na pečení Azure

Chcete-li odhadnout, jaké budou náklady dané pečení, vezměte hodnotu uvedenou pro **odhadované výpočetní náklady**, což je doba trvání, a vynásobte ji hodinovými náklady v místní měně vybraného typu uzlu **virtuálního počítače.** Výsledek nebude zahrnovat čas uzlu potřebný k zprovoznění uzlů. Pokud například vyberete **Standard_F8s_v2** pro typ uzlu, který má náklady 0,40 USD/hod. Skutečné náklady budou pravděpodobně o něco vyšší kvůli dodatečnému času, aby se uzly začaly. Hodinové náklady na uzel najdete na stránce Ceny dávek Azure (pro danou kategorii vyberte možnost Vypočítat optimalizované nebo "Vysoce výkonné výpočetní prostředky").A najdete hodinové náklady na uzel.Can find the hourly node cost on [the Azure Batch Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux) page (select "Compute optimized" or "High performance compute" for the category).

### <a name="reviewing-the-bake-results"></a>Kontrola výsledků pečení

Po dokončení pečení zkontrolujte, zda jsou body voxels a sondy v očekávaných místech spuštěním modulu runtime.

## <a name="data-files"></a><a name="Data-Files"></a>Datové soubory

Existují čtyři datové soubory vytvořené tímto pluginem na různých místech. Pouze jeden z nich je potřeba za běhu a je umístěn ve složce Content/Acoustics projektu, která je automaticky přidána do cesty balení projektu. Ostatní tři jsou uvnitř složky Akustika data a nejsou zabaleny.

* **[Project]/Config/ProjectAcoustics.cfg**: Tento soubor ukládá data, která zadáte do polí v uzdu v režimu akustiky. Umístění a název tohoto souboru nelze změnit. Existují další hodnoty uložené v tomto souboru, které ovlivňují pečeme, ale jsou pro pokročilé uživatele a neměly by být změněny.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Tento soubor je vytvořen během simulace pečení a obsahuje vyhledávací data používaná modulem runtime k vykreslení akustiky scény. Umístění a název tohoto souboru lze změnit pomocí polí na kartě **Sondy.** Pokud chcete tento soubor po vytvoření přejmenovat, odstraňte uasset z projektu Unreal, přejmenujte soubor mimo Unreal v Průzkumníku souborů a pak znovu importujte tento soubor do unrealu, abyste vytvořili nový UAsset. Přejmenování UAsset sám o sobě nebude fungovat.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Tento soubor ukládá geometrii akustiky a vlastnosti materiálu. Vypočítá se pomocí tlačítka **Vypočítat** na kartě **Sondy.** Umístění a název tohoto souboru lze změnit pomocí polí na kartě **Sondy.**
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Tento soubor ukládá parametry vypočítané pomocí tlačítka **Vypočítat** na kartě **Sondy.** Umístění a název tohoto souboru lze změnit pomocí polí na kartě **Sondy.**

Dávejte pozor, abyste neodstranili soubor *.ace stažený z Azure. Tento soubor nelze obnovit, s výjimkou přepečení scény.

## <a name="next-steps"></a>Další kroky
* Prozkoumejte [ovládací prvky návrhu pro Unreal](unreal-workflow.md)
* Seznamte se s [koncepty návrhu aplikace Project Acoustics](design-process.md)

