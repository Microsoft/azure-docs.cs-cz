---
title: Vytvoření Akustika s projektu Akustika – Cognitive Services
description: Tento dokument popisuje proces odesílání Akustika která má označení vytvoření pomocí rozšíření Unity editor.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7a409b1ecdd693a0f28d2303d55a27b177644eb0
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855404"
---
# <a name="bake-acoustics"></a>Vytvoření Akustika

Tento dokument popisuje proces odesílání Akustika která má označení vytvoření pomocí rozšíření Unity editor. Další informace o Akustika, naleznete v tématu [novinky Akustika](what-is-acoustics.md). Úvodní příručky, naleznete v tématu [Začínáme](getting-started.md).

## <a name="import-the-plugin"></a>Import modulu plug-in

Importujte balíček Akustika modulu plug-in do projektu. Pak výběrem otevřete uživatelské rozhraní Akustika **okna > Akustika** z nabídky Unity:

![Otevřít Akustika okna](media/WindowAcoustics.png)

## <a name="principles"></a>Zásady

Panel nástrojů Akustika shromažďuje informace Akustika, že modul je potřeba vypočítat Akustika pro vaše Scéna. Existuje pět kroků až po provádění která má označení vytvoření:

1. Vytvořit nebo označit vaše síť player navigace
2. Označit Akustika geometrie
3. Přiřazení vlastností akustický materiály geometrie
4. Umístění testu ve verzi Preview
5. Vytvoření

Po dokončení která má označení vytvoření se zobrazí [přehled procesu návrhu pro Akustika](design-process.md) pokyny volitelné po která má označení vytvoření návrhu.

## <a name="create-or-mark-a-navigation-mesh"></a>Vytvořit nebo označte sítě navigace

Navigace síť se používá k umístění bodů sondy pro simulaci. Můžete používat Unity a [pracovního postupu síť navigace](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), nebo můžete zadat vlastní síť navigace. Navigace mřížek vytvořené pomocí Unity a pracovní postup neexistoval, použije systém Akustika. Pokud chcete použít vlastní OK, neoznačí z **objekty** kartu, jak je popsáno v dalším kroku.

## <a name="objects-tab"></a>Karta objekty

Otevřít **objekty** karty **Akustika** okna. Tato karta umožňuje označit objekty ve scéně, která jednoduše přidá **AcousticsGeometry** nebo **AcousticsNavigation** komponenty na objekt. Můžete také použít [standardních pracovních postupů komponenty Unity](https://docs.unity3d.com/Manual/UsingComponents.html) označit nebo zrušit označení objekty.

Vyberte jeden nebo více objektů v hierarchii nebo scény okna a pak označit nebo zrušit označení je pro **AcousticsGeometry** nebo **AcousticsNavigation** jak je popsáno níže. Pokud není nic vybráno, můžete označit nebo zrušit označení všechno, co ve scéně najednou.

Může být označen pouze sítě Renderery a terénů. Všechny ostatní typy objekt se bude ignorovat. Zaškrtávací políčka se označit nebo zrušit označení všechny ovlivněné objekty.

Pokud není nic vybráno ve scéně, bude vypadat jako na následujícím obrázku:

![Objekty kartě žádný výběr](media/ObjectsTabNoSelectionDetail.png)

Pokud máte něco, co je vybrán v okně scény nebo hierarchii, bude vypadat jako na následujícím obrázku:

![Objekty kartě žádný výběr](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Objekty karta součásti

Části stránky karty jsou:

1. Výběr tlačítka kartu (**objekty** vybraná karta). Pomocí těchto tlačítek lze projít různé kroky dělat Akustika která má označení vytvoření, zleva doprava.
2. Stručný popis co je potřeba provést pomocí této stránky.
3. K dispozici filtry pro údržbu hierarchie. Použijte k filtrování v okně hierarchie objektů zadaného typu, můžete je snadno označit. Pokud ještě nebyly označeny nic pro Akustika, vyberte poslední dvě možnosti vám ukáže, nothing. Nicméně je možné užitečná k vyhledání označené jako objekty, jakmile to uděláte.
4. Pokud nejsou vybrané žádné objekty, tato část ukazuje stav všech objektů na scéně.:
    * Celkem - Celkový počet aktivních, že není skrytý objekty na scéně.
    * Ignorováno – počet objektů, které nejsou Renderery sítě nebo terénů.
    * Síť – počet mřížky Renderer objektů na scéně.
    * Terénu - počet terénu objektů na scéně.
    * Geometrie - počet sítě nebo terénu objektů ve scéně označené jako "Akustika geometrie"
    * Navigace – počet sítě nebo terénu objektů ve scéně označené jako "Akustika navigaci". Toto číslo nezahrnuje NavMesh Unity a.
5. Zobrazí celkový počet "mark možnost" objekty ve scéně, což je pouze sítě Renderery a terénů. Zobrazuje zaškrtávací políčka můžete použít k označení (přidat příslušné součásti) tyto objekty jako geometrie a navigace pro Akustika
6. Pokud není nic vybráno, tato poznámka upozorní, v případě potřeby vyberte objekty pro označení. Můžete také zkontrolovat jednoho nebo obou políček označit všechny objekty ve scéně bez provedení výběru.
7. Když jsou vybrané objekty, tato část ukazuje stav pouze vybrané objekty.
8. Zobrazuje celkový počet "mark možnost" vybraných objektů. Zaškrtnutím nebo zrušením zaškrtnutí políčka se označit nebo zrušit označení pouze vybrané objekty.

Pokud některé nejsou některé objekty jsou označené, příslušné zaškrtávací políčko se zobrazí hodnotu "hybridní":

![Smíšené hodnota zaškrtávacího políčka](media/MixedObjectSelectionDetail.png)

Kliknutím na zaškrtávací políčko vynutí všechny objekty označit a kliknutím na znovu se zrušit všechny objekty.

Objekty lze označit pro geometrie a navigace.

### <a name="guidelines-for-marking-objects"></a>Pokyny pro označení objekty

Nezapomeňte označit všechny objekty jako **Akustika geometrie** , by měl occlude, odrážejí, zda vyrovnat se s zvuk. Geometrie Akustika patří takové věci, jako základu stěn, střechy, windows & okno lupy, jiných textilních podlahových krytin a velké nábytek. Je možné zahrnout menší objekty, jako je například svítilny, dekorativní položky, svítidla, jak není zvyšují znatelně která má označení vytvoření nákladů. Je důležité si Nenechte si ujít hlavní prvky, jako jsou začátku nebo horní mez. Navíc jsou věci, které by neměla mít vliv Akustika, jako je například mřížek kolizí.

Transformace objektu v okamžiku výpočtu sondy (prostřednictvím **sondy** kartě níže) vyřešen ve výsledcích která má označení vytvoření. Přesunete označené jako objekty na scéně. bude nutné opakování výpočtu testu a rebaking scény.

## <a name="materials-tab"></a>Karta materiály

Jakmile jsou označeny objekty, klikněte na tlačítko **materiály** tlačítko přejdete na kartu materiály.

### <a name="parts-of-the-materials-tab"></a>Součástí kartě materiály

![Podrobnosti karty materiály](media/MaterialsTabDetail.png)

1. **Materiály** tlačítko kartu, zobrazí tato stránka se používá.
2. Stručný popis co je potřeba provést pomocí této stránky.
3. Pokud je zaškrtnuto, pouze materiály, které používají objekty označen jako **Akustika geometrie** budou uvedeny. V opačném případě budou zobrazeny všechny materiály použité ve scéně.
4. Pomocí těchto možností lze změnit pořadí z rozevírací nabídky, která se zobrazí po kliknutí na rozevírací seznam v sloupci Akustika níže (6). **Název** seřadí akustický materiálů podle názvu. "Absorptivity" je seřadí v pořadí absorptivity z nízké na vysokou.
5. Seznam použitých materiálů ve scéně, seřazená podle abecedy. Pokud **označený jenom zobrazit** zaškrtávací políčko je zaškrtnuto (č. 3), pouze materiály, které používají objekty označený jako **Akustika geometrie** jsou uvedeny. Kliknutím na materiály sem vybere všechny objekty ve scéně, použít tento materiál.
6. Ukazuje akustický materiálu, že byla přiřazena materiálu scény. Klikněte na rozevírací seznam pro změnu přiřazení scény materiál, který různých akustický materiálu. Můžete změnit pořadí řazení v nabídce zobrazené po kliknutí na položku tady **Akustika řadit podle:** možnosti výše (č. 4).
7. Ukazuje akustický pohltivosti materiálu vybrána v předchozím sloupci. Hodnota nula znamená, že nemusíte zajistit dokonalou reflektivní (žádné absorpční), při hodnota 1 znamená, že nemusíte zajistit dokonalou pohlcující (žádné reflexi). Pohltivosti nelze změnit, dokud je vybraný materiál "Vlastní".
8. Pro "Vlastní", posuvník přiřazen materiál již je zakázané a zvolíte pohltivosti pomocí posuvníku nebo pomocí klávesové zkratky v hodnotě. Další informace o vlastnosti materiálu najdete v tématu [přehled procesu návrhu pro Akustika](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Pokyny pro přiřazování materiály (nebo hodnoty absorpce)

Na této kartě se pokusí odhadnout, co je přijetí svých materiálů na základě názvu. Například pokud je název materiálu scény LivingRoom_WoodTable, počáteční akustický materiálu přiřazené k němu bude "dřeva". Materiály, kde nelze určit známé materiálu jsou přiřazeny materiálu "Výchozí", která má absorpční podobný konkrétní.

Akustický materiály, které každý materiál scény můžete opětovně přiřazovat. Například pokud místnosti zvuky příliš reverberant, změňte na něco vyšší absorptivity akustický materiálu stěn, dolní mez nebo horní mez. Akustický materiálu přiřazení se vztahuje na všechny objekty, které používají tento materiál scény.

## <a name="probes-tab"></a>Karta testy paměti

Po přiřazení materiály, přepněte **sondy** kartu.

### <a name="parts-of-the-probes-tab"></a>Na kartě testy částí

![Podrobnosti karty testy paměti](media/ProbesTabDetail.png)

1. **Sondy** kartu tlačítko použít zobrazíte na této stránce
2. Stručný popis, co je potřeba provést pomocí této stránky
3. Použijte k výběru řešení hrubý nebo jemné simulace. Hrubé je rychlejší, ale má některé kompromisy. V tématu ["hrubé vs jemné řešení"](#Coarse-vs-Fine-Resolution) níže podrobnosti.
4. Vyberte umístění, kde mají být data soubory Akustika umístěny podle tohoto pole. Klikněte na tlačítko s "..." použití ovládacího prvku pro výběr složky. Výchozí hodnota je **prostředky/AcousticsData**. **Editor** podsložku se také vytvoří v tomto umístění. Další informace o datových souborů najdete v tématu ["Datové soubory"](#Data-Files) níže.
5. Datové soubory pro tento scény bude mít název pomocí předpony k dispozici tady. Výchozí hodnota je "Acoustics_ [název scény]".
6. Poté, co byla vypočtena sond, ovládací prvky výše se deaktivuje. Klikněte na tlačítko **vymazat** tlačítko Vymazat výpočty a povolit ovládacích prvků tak, aby znovu vypočte pomocí nového nastavení.
7. Klikněte na tlačítko **vypočítat...**  tlačítko voxelize scény a vypočítat umístění bodů testu. To se provádí místně na svém počítači a je nutné provést před tím, která má označení vytvoření.

V této verzi systému projektu Akustika sondy nemůže být umístěn ručně a musí být umístěné prostřednictvím automatizovaného procesu součástí **sondy** kartu.

### <a name="what-the-calculate-button-calculates"></a>Co se vypočítá tlačítko "Vypočítat..."

**Vypočítat...**  tlačítko trvá všechna data, které jste zatím zadali (geometrie, navigace, materiály a hrubé/jemné nastavení) a prochází několik kroků:

1. Přijímá geometrie od scény mřížek a vypočítá voxel svazku. Svazek voxel je 3rozměrné svazku, který obklopuje celý scény a se skládá z malých kubické "voxels". Velikost voxels se určuje podle frekvence simulace, která se nastavuje přes **simulace rozlišení** nastavení. Každý voxel je označen jako buď "Otevřít vzduchu" nebo obsahující geometrie scény. Pokud voxel obsahuje geometrie voxel příznakem s pohltivosti materiálu, který je přiřazen k této geometrie.
2. Navigace dat pak použije k výpočtu akusticky zajímavé umístění, kam se můžou obrátit hráč. Pokusí se najít měly být přiměřeně malé sadu z těchto umístění, která zahrnuje menší oblasti, jako je například dveří a předsálí a potom do místnosti, otevřete mezery. Pro malé scény obvykle jde méně než 100 umístění během velké scén může mít až tisíce.
3. Pro každé umístění poslední naslouchací proces, který vypočítává Určuje, že určité parametry, jako je například jak "otevřená" je místo, velikost místa, které je v atd.
4. Výsledky tyto výpočty jsou uloženy v souborech v umístění, které zadáte (viz ["Datové soubory"](#Data-Files) níže)

V závislosti na velikosti vaší scény a rychlosti vašeho počítače můžete tyto výpočty trvat několik minut.

Po dokončení těchto výpočtů lze zobrazit náhled dat voxel a umístění testu bod k zajištění, že která má označení vytvoření získáte dobré výsledky. Věci jako chybný navigace sítě nebo chybějící/největší geometrie obvykle bude rychle viditelné ve verzi preview, můžete ho opravit.

### <a name="scene-rename"></a>Přejmenování scény

Název scény se používá pro připojení k uložení umístění bodu testu a voxelization soubory scény. Pokud přejmenování scény po body test paměti se počítá materiálu přiřazení a umístění dat dojde ke ztrátě a by měl spustit znovu.

## <a name="debug-display-through-gizmos"></a>Zobrazení prostřednictvím Gizma ladění

Ve výchozím nastavení jak **sondy** a **Voxels** gizma jsou zapnuté. Ty se seznámíte voxels a test bodů umístění, které byly vypočítány. Můžete povolit nebo zakázat, Gizma nabídky:

![Gizma nabídky](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Voxels se zobrazují v okně scény jako zelená datových krychlí kolem zúčastněných geometry. Voxels, které obsahují pouze air nejsou zobrazeny. Existuje velké zeleného pole kolem celého scéně, která označuje dokončení voxel svazek, který se použije v simulaci.
Pohyb scéně a ověřte, zda vše, co by měl být geometrie voxels. Fotoaparát scény musí být v rámci objektu pro voxels zobrazíte přibližně 5 měřiče.

Pokud srovnáte voxels vytvořené pomocí řešení jemné hrubé řešení vs, zobrazí hrubý voxels jsou dvakrát velké.

![Voxel ve verzi Preview](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Body pro zjišťování

Test body jsou synonymem umístění možné player (naslouchací proces). Při provádění která má označení vytvoření, se pro každou z těchto bodů testu počítá Akustika pro zdroje zvuku kdekoli ve scéně. Pokud hráč není přímo na umístění bodu sondu, data z bodů testu nejblíže hráč se používají k interpolovat parametry v tomto umístění.

Proto je důležité zajistit, že existují body test kdekoli hráč má cestují ve scéně a odpovídajícím způsobem reprezentované malé oblasti a dveří. Test body jsou umístěné modulem projektu Akustika podle výklad vaše Scéna geometrie a se nedá přesunout ani upravovat v této verzi Preview návrháře. Použijte je ověřit správnost označení geometrie a dat pomocný parametr navigace.

![Sondy ve verzi Preview](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Hrubý vs jemné řešení

Jediným rozdílem mezi hrubé a jemné nastavení překladu je frekvenci, s jakou se provádí simulace. Používá jemné frekvenci dvakrát co nejvyšší hrubé.
Když to může zdát jednoduché, má několik dopadů na akustický simulace:

* Vlnové pro hrubé je dvakrát, jak dlouho jako správně a proto jsou dvakrát voxels velké.
* Čas simulace přímo souvisí s velikostí voxel, a proto hrubé zanést informace o 16krát rychleji než jemné která má označení vytvoření.
* Portály (například dveře nebo windows) menší než velikost voxel nemůže být Simulovaná. Hrubý nastavení může způsobit, že některé z těchto menších portály, které nebudou simulované; Proto že nebudou předávat zvukové prostřednictvím za běhu. Můžete zobrazit, pokud dochází k tomu zobrazením voxels.
* Nižší četností simulace za následek méně diffraction kolem a rohy.
* Zvukové zdroje nelze umístit uvnitř "vyplněné" voxels, který je voxels obsahující geometrie – výsledkem je žádný zvukový signál. Je obtížnější najít zdroje zvuku, nejsou uvnitř větší voxels hrubé než nastavení v pořádku.
* Větší voxels bude více pronikat do portálů, jak je znázorněno níže. První image byla vytvořena pomocí hrubé, zatímco druhá je stejný vaší bránou pomocí jemné řešení. Označené červenou označení, je mnohem méně neoprávněného vniknutí do vaší bránou pomocí nastavení v pořádku. Modrá čára je vaší bránou dle geometrie, zatímco červená čára je efektivní akustický portál definován velikostí voxel. Jak tento neoprávněného vniknutí hraje v dané situaci závisí zcela jak voxels zarovnejte s geometrie na portálu, který je určena velikost a umístění objektů na scéně.

![Hrubý dveřmi](media/CoarseVoxelDoorway.png)

![Bez problémů dveřmi](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Vytvoření karty

Jakmile budete spokojeni s daty ve verzi preview, použijte **zanést** kartu k odeslání vaší která má označení vytvoření do cloudu.

### <a name="parts-of-the-bake-tab"></a>Částí, která má označení vytvoření karty

![Podrobnosti karty zanést](media/BakeTabDetails.png)

1. Vytvoření karty tlačítka sloužícího k otevřete tuto stránku.
2. Stručný popis toho, jak provést na této stránce.
3. Pole k zadání přihlašovacích údajů Azure po vytvoření účtu Azure. Další informace najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
4. Značce image dockeru pro Akustika sadu nástrojů.
5. Spuštění webu Azure portal ke správě vašich předplatných, sledování využití a zobrazit informace o fakturaci atd. 
6. Služba Azure batch výpočetní uzel typ, který se použije k výpočtu. Typ uzlu musí podporovat vaše umístěním Azure datového centra. Pokud nejste si jistí, ponechte **Standard_F8s_v2**.
7. Počet uzlů pro tento výpočet. Číslo, které tady zadáte ovlivňuje dobu pro dokončení která má označení vytvoření a přidělení základní služby Azure Batch, je omezen. Výchozí přidělení pouze umožňuje uzly dva 8 jader nebo uzlu jeden 16 jader, ale můžete rozšířit. Další informace o omezení přidělení core najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
8. Zaškrtnutím tohoto políčka konfigurovat fond výpočetních používat [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Uzlů s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo může dojít ke zrušení kdykoli.
9. Počet sondy pro vaše Scéna spočítané na **sondy** kartu. Počet sond určuje počet simulace, které je nutné spustit v cloudu. Nelze zadat více uzlů, než jsou testy.
10. Množství uplynulého času, který se očekává, abyste mohli pro vaše úlohy určené ke spuštění v cloudu. To nezahrnuje čas spuštění uzlu. Po spuštění úlohy, jde o tom, jak dlouho by mělo být předtím, než se dostanete zpět výsledky. Všimněte si, že se jedná pouze o odhad.
11. Celkové množství výpočetního času potřebné ke spuštění simulace. Toto je celková velikost uzlu výpočetního času, který se použije v Azure. Zobrazit [odhadování nákladů která má označení vytvoření](#Estimating-bake-cost) níže pro další informace o použití této hodnoty.
12. Tato zpráva znamená, kam uložit výsledky která má označení vytvoření po dokončení úlohy.
13. (Pouze pro pokročilé uživatele) Pokud z nějakého důvodu potřebujete vynutit Unity nemusíte zabývat která má označení vytvoření odeslán (třeba jste si stáhli výsledky použití jiného počítače), klikněte na tlačítko **Vymazat stav** tlačítko zapomenout o úloze, která byla odeslána. Všimněte si, že to znamená, že výsledného souboru, až budete připraveni, bude **není** stáhli, a **to není stejné jako zrušení úlohy**. Úlohy, pokud spuštěný, bude nadále spouštět v cloudu.
14. Klikněte na tlačítko která má označení vytvoření k odeslání, která má označení vytvoření do cloudu. Když úloha běží, zobrazí se **zrušit úlohu** místo.
15. Připraví ke zpracování Akustika simulace na místních počítačích. Zobrazit [místní která má označení vytvoření](#Local-bake) Další informace.  
16. Tato oblast se zobrazuje stav která má označení vytvoření. Po dokončení, měl by se zobrazit **stažené**.

Vždy můžete získat podrobnější informace o aktivních úloh, fondů výpočetních a úložiště na webu [webu Azure Portal](https://portal.azure.com).

Když úloha běží **zanést** tlačítko se změní na **zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Zobrazí se výzva k potvrzení předtím, než dojde ke zrušení úlohy. Zrušení úlohy nejde vrátit zpět, nebudou k dispozici žádné výsledky a vám bude nadále účtovat pro použití jakékoli výpočetní prostředky Azure.

Po zahájení která má označení vytvoření, můžete zavřít Unity. V závislosti na projektu, typ a počet uzlů která má označení vytvoření cloudu může trvat několik hodin. Stav úlohy která má označení vytvoření aktualizuje při načtení projektu a otevřete okno Akustika. Pokud se úloha dokončí, stáhnou se výstupní soubor.

Přihlašovací údaje Azure jsou bezpečně uložené na místním počítači a přidružené Unity editor. Slouží pouze k navázat zabezpečené připojení k Azure.

### <a name="Estimating-bake-cost"></a> Provádí se odhad nákladů Azure která má označení vytvoření

Pokud chcete odhadnout, co bude daný která má označení vytvoření nákladů, přijmout hodnoty zobrazené pro **odhadované náklady na výpočetní**, což je dobu trvání a vícenásobně ve každou hodinu nákladů ve vaší místní měně **typ uzlu virtuálního počítače** jste vybrali. Výsledek nebude obsahovat uzel době potřebné ke zprovoznění uzly a spouštění. Pokud vyberete třeba **Standard_F8s_v2** typu uzlu, který má náklady 0,40 $/ hod a odhadované náklady na výpočetní je 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy bude $0,40 * ~ 4 hodin = ~ $1.60. Skutečné náklady se pravděpodobně o něco vyšší kvůli čas navíc k získání uzly spuštěna. Hodinové náklady na uzel můžete najít [ceny služby Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) stránky (vyberte "optimalizováno pro výpočty" nebo "vysokovýkonné výpočetní prostředí" kategorie).

### <a name="reviewing-the-bake-results"></a>Vyhodnocení výsledků která má označení vytvoření

Po dokončení která má označení vytvoření, zkontrolujte, zda body voxels a kontroly jsou v jejich očekávané umístění spuštěním modulu runtime. Další informace jsou v [přehled procesu návrhu pro Akustika](design-process.md).

## <a name="Local-bake"></a>Místní která má označení vytvoření
Která má označení vytvoření místního spuštění Akustika simulace ve vašem vlastním počítači místo snižování zátěže úloh k výpočetnímu clusteru služby Azure Batch. To může být dobrou volbou pro experimentování s Akustika nevyžaduje předplatné Azure. Mějte na paměti, že simulace Akustika výpočetně náročné a může trvat dlouhou dobu v závislosti na velikosti scény, simulace konfigurace a raw výpočetní výkon zpracování počítače.

### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware
64bitový procesor Intel s alespoň s 8 jádry a 32 GB paměti RAM nebo vyšší.

Třeba na počítači s technologií Intel Xeon E5-1660 @ 3 GHz a 32 GB paměti RAM – 8 jader
* Malé scény s 100 testů trvá přibližně 2 hodin pro hrubý která má označení vytvoření a přibližně 32 hodin která má označení vytvoření jemné řešení.
* Větší scény s 1000 testy paměti může trvat až přibližně 20 hodin, hrubé vyřešení tohoto problému a ~ 21 dnů která má označení vytvoření jemné řešení.

### <a name="setup-docker"></a>Instalace Dockeru
Instalace a konfigurace Dockeru na počítači, který bude zpracovávat simulace-
1. Nainstalujte [nástrojů Dockeru](https://www.docker.com/products/docker-desktop).
2. Spusťte nastavení Dockeru, přejděte na možnosti "Pokročilé" a konfigurovat prostředky k dispozici ochucené, o obsahu 8 GB paměti RAM. Více procesorů může přidělit Dockeru, tím rychleji která má označení vytvoření dokončí. ![Příklad nastavení Dockeru](media/DockerSettings.png)
3. Přejděte na "Sdílené jednotky" a zapněte sdílení má jednotka použitá ke zpracování.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Spusťte místní která má označení vytvoření
1. Klikněte na tlačítko "Příprava místní zanést", která má označení vytvoření karty a vyberte složku, kam bude uložena vstupních souborů a provádění skriptů. Poté lze která má označení vytvoření na jakýkoli počítač splňuje minimální hardwarové požadavky a pokud je Docker nainstalovaný tak, že zkopírujete složku do tohoto počítače.
2. Spuštění simulace použití skriptu "runlocalbake.bat". Tento skript se image Dockeru Akustika projekt pomocí sady nástrojů, které jsou nezbytné pro simulaci zpracování načte a spustí simulace. 
3. Jakmile se simulace dokončí, zkopírujte výsledný soubor .ace zpět na vašem Unity projektu. Pokud chcete mít jistotu, že Unity to rozpozná jako binárního souboru, připojte k rozšíření souboru (například "Scene1.ace.bytes") ".bytes". Podrobné protokoly pro simulaci se ukládají v "AcousticsLog.txt." Pokud narazíte na nějaké problémy, sdílejte tento soubor jako pomoc při diagnóze.

## <a name="Data-Files"></a>Datové soubory

Existují čtyři datové soubory, které vytvořil tento modul plug-in v různých fázích. Je potřeba jenom jeden z nich za běhu, proto ostatních tří jsou uvnitř složky s názvem "Editor", takže nesmí být zkompilovány do vašeho projektu.

* **Prostředky a Editor / [SceneName]\_AcousticsParameters.asset**: Tento soubor uchovává data, zadejte do polí v Uživatelském rozhraní Akustika. Umístění a název tohoto souboru nelze změnit. Existují jiné hodnoty uložené v tomto souboru, které mají vliv která má označení vytvoření, ale jsou určené pro pokročilé uživatele a neměl měnit.
* **Prostředky/AcousticsData/Akustika\_[SceneName].ace.bytes**: Tento soubor je, co se vytvoří během simulace která má označení vytvoření a obsahuje vyhledávací data použít k vykreslení Akustika vaše Scéna modulem runtime. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Tento soubor uchovává geometrie Akustika voxelized a vlastností materiálu. Vypočítán s použitím **Calculate...**  tlačítko na kartě testy. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.
* **Prostředky/AcousticsData/Editor/Akustika\_[SceneName]\_config.xml**: Tento soubor uchovává vypočítán s použitím parametrů **Calculate...**  tlačítko **sondy** kartu. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.

Aby se postaral neodstraňovat *. soubor ace.bytes stáhne z: která má označení vytvoření. Tento soubor nebude obnovitelné s výjimkou rebaking scény.

## <a name="next-steps"></a>Další postup
* Použití která má označení vytvoření výsledků do zdroje zvuku v [přehled procesu návrhu pro Akustika](design-process.md).

