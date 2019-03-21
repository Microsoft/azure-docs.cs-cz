---
title: Kurz Unreal která má označení vytvoření Akustika projektu
titlesuffix: Azure Cognitive Services
description: Tento dokument popisuje proces odesílání Akustika která má označení vytvoření pomocí rozšíření Unreal editoru.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: michem
ms.openlocfilehash: afe4421bea27ff029bd4a1a7808241a54027a6ac
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136389"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Kurz Unreal která má označení vytvoření Akustika projektu
Tento dokument popisuje proces odesílání Akustika která má označení vytvoření pomocí rozšíření Unreal editoru.

Existuje pět kroků až po provádění která má označení vytvoření:

1. Vytvořit nebo označit vaše síť player navigace
2. Značka Akustika geometrie
3. Přiřazení vlastností akustický materiály geometrie
4. Umístění testu ve verzi Preview
5. Vytvoření

## <a name="open-the-project-acoustics-editor-mode"></a>Otevřete projekt Akustika režimu úprav

Importujte balíček modulu plug-in Akustika projektu do projektu. Vám pomůžou s tím, [Unreal integrace](unreal-integration.md) tématu. Jakmile je integrovaný modul plug-in, otevřete kliknutím na ikonu nový režim Akustika uživatelské rozhraní Akustika.

![Otevřít Akustika režimu](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Značka actors pro Akustika

Na kartě objekty je první karta, který se zobrazí při otevření Akustika režimu. Na této kartě lze značka actors ve vaší úrovni, který přidá **AcousticsGeometry** nebo **AcousticsNavigation** značky pro účastníky.

Vyberte jeden nebo více objektů v Tvorba osnovy svět, nebo použijte **Hromadný výběr** části vám pomohou s výběrem všechny objekty konkrétní kategorie. Jakmile jsou vybrané objekty, použijte **označení** části použití požadované značky pro vybrané objekty.

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Karta částí objektů

![Podrobnosti o kartě Unreal objekty](media/unreal-objects-tab-details.png)

1. Výběr tlačítka kartu (**objekty** vybraná karta). Pomocí těchto tlačítek lze projít různé kroky dělat Akustika která má označení vytvoření, shora dolů.
2. Stručný popis co je potřeba provést pomocí této stránky.
3. K dispozici selektory pro objekty actor na úrovni.
4. Kliknutím na **vyberte** vybere všechny objekty na úrovni, které odpovídají alespoň jeden z typů checked objektu actor.
5. Kliknutím na **zrušit výběr** vymaže aktuální výběr. To je stejný jako dosažení klávesou ESC.
6. Pomocí těchto přepínačů vyberte, jestli se má použít značku geometrie a navigace pro vybrané objekty actor.
7. Kliknutím na **značka** Přidá vybranou značku pro všechny vybrané objekty actor.
8. Kliknutím na **Odtagovat** se odebrat vybranou značku ze všech aktuálně vybraných objektů actor.
9. Kliknutím na **vyberte označené** zrušit aktuální výběr a vyberte všechny actors s aktuálně vybranou značkou.
10. Na tyto statistiky ukazují, kolik actors jsou označené pomocí jednotlivých typů značek.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Značka Akustika uzavření a reflexe geometrie

Otevřete kartu objekty Akustika okna. Pokud by měl occlude, odrážejí nebo vyrovnat se s zvuk, označte jako Akustika geometrie žádné objekty. Geometrie Akustika patří takové věci, jako základu stěn, střechy, windows & okno lupy, jiných textilních podlahových krytin a velké nábytek. Můžete použít libovolný libovolného úroveň složitosti pro tyto objekty. Protože je Scéna voxelized před simulace, vysoce podrobné mřížky, jako je například stromové struktury s mnoha malých listy, nejsou dražší než zjednodušené objekty zanést.

Nezahrnují věcí, které by neměla mít vliv Akustika, jako jsou neviditelná kolizí mřížky.

Transformace objektu v okamžiku výpočtu sondy (na kartě testy níže) problém je vyřešený v která má označení vytvoření výsledky. Přesunete označené jako objekty na scéně. bude nutné opakování výpočtu testu a rebaking scény.

## <a name="create-or-tag-a-navigation-mesh"></a>Vytvořit nebo označení sítě navigace

Navigace síť se používá k umístění bodů sondy pro simulaci. Můžete použít pro Unreal [svazku hranice sítě Nav](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), nebo můžete zadat vlastní síť navigace. Musíte označit alespoň jeden objekt jako **Akustika navigace**.

## <a name="select-acoustic-materials"></a>Vyberte akustický materiály

Po označení jsou objekty, klikněte na tlačítko **materiály** tlačítko přejdete na kartu materiály. Na této kartě se použije k určení vlastností materiálu pro každý materiálu na úrovni. Před všechny objekty actor jsou označené, bude prázdné.

Akustický materiály řízení velikosti šířky zvukové energie projeví zpět z každé povrchu. Výchozí zvukové materiál má absorpční podobný konkrétní. Projekt Akustika navrhuje materiálů na základě názvu materiálu scény.

Čas reverberation dané materiálů v místnosti nepřímo souvisí jeho pohltivosti většina materiálů s absorpce hodnoty v rozsahu 0.01, 0.20 a novější. Jsou velmi absorpčního materiály s absorpční koeficienty nad tento rozsah. Například pokud místnosti zvuky příliš reverberant, změňte na něco vyšší absorptivity akustický materiálu stěn, dolní mez nebo horní mez. Akustický materiálu přiřazení se vztahuje na všechny objekty actor, které používají tento materiál scény.

![Graf doby dozvuku](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Součástí kartě materiály

![Podrobnosti o kartě Unreal objekty](media/unreal-materials-tab-details.png)

1. **Materiály** tlačítko kartu, zobrazí tato stránka se používá.
2. Stručný popis co je potřeba provést pomocí této stránky.
3. Označí seznam materiálů na úrovni, na základě aktéry **AcousticsGeometry**. Kliknutím na materiály sem vybere všechny objekty ve scéně, použít tento materiál.
4. Ukazuje akustický materiálu, že byla přiřazena materiálu scény. Klikněte na rozevírací seznam pro změnu přiřazení scény materiál, který různých akustický materiálu.
5. Ukazuje akustický pohltivosti materiálu vybrána v předchozím sloupci. Hodnota nula znamená, že nemusíte zajistit dokonalou reflektivní (žádné absorpční), při hodnota 1 znamená, že nemusíte zajistit dokonalou pohlcující (žádné reflexi). Změna této hodnoty Akustika materiál (krok #4), který se aktualizuje **vlastní**.


## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a zkontrolujte umístění testu naslouchací proces

Po přiřazení materiály, přepněte **sondy** kartu.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Na kartě testy částí

![Podrobnosti karty testy paměti](media/unreal-probes-tab-details.png)

1. **Sondy** kartu tlačítko použít zobrazíte na této stránce
2. Stručný popis, co je potřeba provést pomocí této stránky
3. Použijte k výběru řešení hrubý nebo jemné simulace. Hrubé je rychlejší, ale má některé kompromisy. Zobrazit [hrubý vs jemné řešení](#Coarse-vs-Fine-Resolution) níže podrobnosti.
4. Vyberte umístění, kde mají být data soubory Akustika umístěny podle tohoto pole. Klikněte na tlačítko s "..." použití ovládacího prvku pro výběr složky. Další informace o datových souborů najdete v tématu [datové soubory](#Data-Files) níže.
5. Datové soubory pro tento scény bude mít název pomocí předpony k dispozici tady. Výchozí hodnota je "_AcousticsData [název úrovně]".
6. Klikněte na tlačítko **Calculate** tlačítko voxelize scény a vypočítat umístění bodů testu. To se provádí místně na svém počítači a je nutné provést před tím, která má označení vytvoření. Poté, co vypočítali sond, ovládacích prvků nad bude zakázán a toto tlačítko se změní na Řekněme, že **vymazat**. Klikněte na tlačítko **vymazat** tlačítko Vymazat výpočty a povolit ovládacích prvků tak, aby znovu vypočte pomocí nového nastavení.

Sondy musí být umístěn prostřednictvím automatizovaného procesu součástí **sondy** kartu.


### <a name="what-the-calculate-button-calculates"></a>Co se počítá na tlačítko "Vypočítat"

**Calculate** tlačítko trvá všechna data, které jste zatím zadali (geometrie, navigace, materiály a hrubý/jemné nastavení) a prochází několik kroků:

1. Přijímá geometrie od scény mřížek a vypočítá voxel svazku. Svazek voxel je 3rozměrné svazku, který obklopuje celý scény a se skládá z malých kubické "voxels". Velikost voxels se určuje podle frekvence simulace, která se nastavuje přes **simulace rozlišení** nastavení. Každý voxel je označen jako buď "Otevřít vzduchu" nebo obsahující geometrie scény. Pokud voxel obsahuje geometrie voxel příznakem s pohltivosti materiálu, který je přiřazen k této geometrie.
2. Navigace dat pak použije k výpočtu akusticky zajímavé umístění, kam se můžou obrátit hráč. Pokusí se najít měly být přiměřeně malé sadu z těchto umístění, která zahrnuje menší oblasti, jako je například dveří a předsálí a potom do místnosti, otevřete mezery. Pro malé scény obvykle jde méně než 100 umístění během velké scén může mít až tisíců.
3. Pro každé umístění poslední naslouchací proces, který vypočítává Určuje, že určité parametry, jako je například jak "otevřená" je místo, velikost místa, které je v atd.
4. Výsledky tyto výpočty jsou uloženy v souborech v umístění, které zadáte (viz [datové soubory](#Data-Files) níže)

V závislosti na velikosti vaší scény a rychlosti vašeho počítače můžete tyto výpočty trvat několik minut.

Po dokončení těchto výpočtů lze zobrazit náhled dat voxel a umístění testu bod k zajištění, že která má označení vytvoření získáte dobré výsledky. Věci jako chybný navigace sítě nebo chybějící/největší geometrie obvykle bude rychle viditelné ve verzi preview, můžete ho opravit.


## <a name="debug-display"></a>Zobrazení ladění

Po dokončení testu výpočtu nového objektu actor se zobrazí v Tvorba osnovy World, volá **AcousticsDebugRenderer**. Kontroluje **vykreslení sondy** a **vykreslení Voxels** zaškrtávací políčka vám umožní zobrazení ladění uvnitř zobrazení editoru.

![Akustika ladění nástroj pro vykreslování](media/acoustics-debug-renderer.png)

Pokud nevidíte žádné voxels nebo testy jako překryvný obrázek na úroveň, ujistěte se, že v zobrazení je povoleno vykreslení v reálném čase.

![Povolit vykreslování v reálném čase](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels se zobrazují v okně scény jako zelená datových krychlí kolem zúčastněných geometry. Voxels, které obsahují pouze air nejsou zobrazeny. Existuje velké zeleného pole kolem celého scéně, která označuje dokončení voxel svazek, který se použije v simulaci.
Pohyb scéně a ověřte, zda má geometrie akusticky occluding voxels. Zkontrolujte také, že bez Akustika objekty, jako je OK kolizí nebyly voxelized. Fotoaparát scény musí být v rámci objektu pro voxels zobrazíte přibližně 5 měřiče.

Pokud srovnáte voxels vytvořené pomocí řešení jemné hrubé řešení vs, zobrazí hrubý voxels jsou dvakrát velké.

![Voxel Preview](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Body pro zjišťování

Test body jsou synonymem umístění možné player (naslouchací proces). Když pečení, simulace vypočítá Akustika připojení všechny možné zdrojová umístění pro každý bod testu. Za běhu je umístění naslouchací proces interpolovaných mezi blízkými body testu.

Je důležité zkontrolovat, že existují test body všude, kde hráč má cestují ve scéně. Test body jsou umístěny na síť navigace modulem Akustika projektu a nelze přesunout nebo upravovat, zajistěte proto na pozadí síť navigace všech možných player umístění zkontrolováním bodů testu.

![Sondy ve verzi Preview](media/unreal-probes-preview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Hrubý vs jemné řešení

Jediným rozdílem mezi nastavení pro nalezení hrubý a bez problémů je frekvenci, s jakou se provádí simulace. Používá jemné frekvenci dvakrát tak vysoké jako hrubý.
Když to může zdát jednoduché, má několik dopadů na akustický simulace:

* Vlnové pro hrubý je dvakrát až pořádku, a proto je voxels dvakrát velké.
* Čas simulace přímo souvisí s velikostí voxel, a proto hrubý která má označení vytvoření asi 16krát rychleji než jemné která má označení vytvoření.
* Portály (například dveře nebo windows) menší než velikost voxel nemůže být Simulovaná. Hrubý nastavení může způsobit, že některé z těchto menších portály, které nebudou simulované; Proto že nebudou předávat zvukové prostřednictvím za běhu. Můžete zobrazit, pokud dochází k tomu zobrazením voxels.
* Nižší četností simulace za následek méně diffraction kolem a rohy.
* Zvukové zdroje nelze umístit uvnitř "vyplněné" voxels, který je voxels obsahující geometrie – výsledkem je žádný zvukový signál. Je obtížnější umístit zdrojů zvuku, nejsou uvnitř větší voxels z hrubé než při použití nastavení v pořádku.
* Větší voxels bude více pronikat do portálů, jak je znázorněno níže. První image byla vytvořena pomocí hrubý, druhá je stejný vaší bránou pomocí jemné řešení. Označené červenou označení, je mnohem méně neoprávněného vniknutí do vaší bránou pomocí nastavení v pořádku. Modrá čára je vaší bránou dle geometrie, zatímco červená čára je efektivní akustický portál definován velikostí voxel. Jak tento neoprávněného vniknutí hraje v dané situaci závisí zcela jak voxels zarovnejte s geometrie na portálu, který je určena velikost a umístění objektů na scéně.

![Hrubý dveřmi](media/unreal-coarse-bake.png)

![Bez problémů dveřmi](media/unreal-fine-bake.png)

## <a name="bake-your-level-using-azure-batch"></a>Vytvoření vaší úrovni pomocí služby Azure Batch

Můžete zanést vaše Scéna s výpočetní cluster v cloudu pomocí služby Azure Batch. Modul plug-in pro projekt Akustika Unreal připojuje přímo k Azure Batch k vytvoření instance, spravovat a dovolí pro každý která má označení vytvoření clusteru služby Azure Batch. Na kartě, která má označení vytvoření zadejte přihlašovací údaje Azure, vyberte typ počítače clusteru a velikost a klikněte na která má označení vytvoření.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Částí, která má označení vytvoření karty

![Podrobnosti karty zanést](media/unreal-bake-tab-details.png)

1. Vytvoření karty tlačítka sloužícího k otevřete tuto stránku.
2. Stručný popis toho, jak provést na této stránce.
3. Pole k zadání přihlašovacích údajů Azure po vytvoření účtu Azure. Další informace najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
4. Spuštění webu Azure portal ke správě vašich předplatných, sledování využití a zobrazit informace o fakturaci atd. 
5. Služba Azure batch výpočetní uzel typ, který se použije k výpočtu. Typ uzlu musí podporovat vaše umístěním Azure datového centra. Pokud nejste si jistí, ponechte **Standard_F8s_v2**.
6. Počet uzlů pro tento výpočet. Číslo, které tady zadáte ovlivňuje dobu pro dokončení která má označení vytvoření a přidělení základní služby Azure Batch, je omezen. Výchozí přidělení pouze umožňuje uzly dva 8 jader nebo uzlu jeden 16 jader, ale můžete rozšířit. Další informace o omezení přidělení core najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
7. Zaškrtnutím tohoto políčka konfigurovat fond výpočetních používat [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Uzlů s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo může dojít ke zrušení kdykoli.
8. Množství uplynulého času, který se očekává, abyste mohli pro vaše úlohy určené ke spuštění v cloudu. To nezahrnuje čas spuštění uzlu. Po spuštění úlohy, jde o tom, jak dlouho by mělo být předtím, než se dostanete zpět výsledky. Všimněte si, že se jedná pouze o odhad.
9. Celkové množství výpočetního času potřebné ke spuštění simulace. Toto je celková velikost uzlu výpočetního času, který se použije v Azure. Zobrazit [odhadování nákladů která má označení vytvoření](#Estimating-bake-cost) níže pro další informace o použití této hodnoty.
10. Klikněte na tlačítko která má označení vytvoření k odeslání, která má označení vytvoření do cloudu. Když úloha běží, zobrazí se **zrušit úlohu** místo. Pokud na této kartě nejsou žádné chyby, nebo pokud pracovní postup na **sondy** kartu nebyla dokončena, toto tlačítko bude zakázané.
11. Počet sondy pro vaše Scéna spočítané na **sondy** kartu. Počet sond určuje počet simulace, které je nutné spustit v cloudu. Nelze zadat více uzlů, než jsou testy.
12. Tato zpráva znamená, aktuální stav úlohy, nebo pokud na této kartě nejsou žádné chyby, co tyto chyby jsou.

Vždy můžete získat podrobnější informace o aktivních úloh, fondů výpočetních a úložiště na webu [webu Azure portal](https://portal.azure.com).

Když úloha běží **zanést** tlačítko se změní na **zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Zrušení úlohy nejde vrátit zpět, nebudou k dispozici žádné výsledky a vám bude nadále účtovat kdykoli výpočetní prostředky Azure, která je použita před zrušení.

Po zahájení která má označení vytvoření, Unreal můžete zavřít. V závislosti na projektu, typ a počet uzlů která má označení vytvoření cloudu může trvat několik hodin. Stav úlohy která má označení vytvoření aktualizuje při načtení projektu a otevřete okno Akustika. Pokud se úloha dokončí, stáhnou se výstupní soubor.

Přihlašovací údaje Azure jsou bezpečně uložené na místním počítači a přidružené Unreal projektu. Slouží pouze k navázat zabezpečené připojení k Azure.

### <a name="Estimating-bake-cost"></a> Provádí se odhad nákladů Azure která má označení vytvoření

Pokud chcete odhadnout, co bude daný která má označení vytvoření nákladů, přijmout hodnoty zobrazené pro **odhadované náklady na výpočetní**, což je dobu trvání a vícenásobně ve každou hodinu nákladů ve vaší místní měně **typ uzlu virtuálního počítače** jste vybrali. Výsledek nebude obsahovat uzel době potřebné ke zprovoznění uzly a spouštění. Pokud vyberete třeba **Standard_F8s_v2** typu uzlu, který má náklady 0,40 $/ hod a odhadované náklady na výpočetní je 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy bude $0,40 * ~ 4 hodin = ~ $1.60. Skutečné náklady se pravděpodobně o něco vyšší kvůli čas navíc k získání uzly spuštěna. Hodinové náklady na uzel můžete najít [ceny služby Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) stránky (vyberte "optimalizováno pro výpočty" nebo "vysokovýkonné výpočetní prostředí" kategorie).

### <a name="reviewing-the-bake-results"></a>Vyhodnocení výsledků která má označení vytvoření

Po dokončení která má označení vytvoření, zkontrolujte, zda body voxels a kontroly jsou v jejich očekávané umístění spuštěním modulu runtime.

## <a name="Data-Files"></a>Datové soubory

Existují čtyři datové soubory, které vytvořil tento modul plug-in v různých fázích. Pouze jeden z nich je potřeba za běhu a je umístěn ve složce obsahu nebo Akustika vašeho projektu, která se automaticky přidá do vašeho projektu balení cesty. Ostatní tři ve složce Akustika Data a nejsou zabaleny.

* **[Project]/Config/ProjectAcoustics.cfg**: Tento soubor uchovává data, která zadáte do polí v Uživatelském režimu Akustika. Umístění a název tohoto souboru nelze změnit. Existují jiné hodnoty uložené v tomto souboru, které mají vliv která má označení vytvoření, ale jsou určené pro pokročilé uživatele a neměl měnit.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Tento soubor je, co se vytvoří během simulace která má označení vytvoření a obsahuje vyhledávací data použít k vykreslení Akustika vaše Scéna modulem runtime. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Tento soubor uchovává geometrie Akustika voxelized a vlastností materiálu. Vypočítán s použitím **Calculate** tlačítko **sondy** kartu. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Tento soubor uchovává vypočítán s použitím parametrů **Calculate** tlačítko **sondy** kartu. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.

Aby se postaral neodstraňovat *.ace soubor stažený z webu Azure. Tento soubor nebude obnovitelné s výjimkou rebaking scény.

## <a name="next-steps"></a>Další postup
* Prozkoumejte [návrh ovládacích prvků pro Unreal](unreal-workflow.md)
* Prozkoumejte [konceptů návrhu Akustika projektu](design-process.md)

