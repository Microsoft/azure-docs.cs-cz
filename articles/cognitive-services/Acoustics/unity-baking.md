---
title: Výukový kurz pro Zanesliu Unity v projektu
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje akustické a pečení s využitím akustického projektu v Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b7249c3048ba3af3adbaac01f43770482a0d38ad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933193"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Výukový kurz pro Zanesliu Unity v projektu
Tento kurz popisuje akustické a pečení s využitím akustického projektu v Unity.

Požadavky na software:
* [Unity 2018.2 +](https://unity3d.com) pro Windows nebo MacOS
* [Modul plug-in s akustickými podprojekty integrovaný do vašeho projektu Unity](unity-integration.md) nebo v [projektu s akustickým obsahem Unity](unity-quickstart.md)
* Volitelné: [Účet Azure Batch](create-azure-account.md) pro urychlení vytváří pomocí cloud computingu

## <a name="open-the-project-acoustics-bake-window"></a>Otevřete okno akustické zanesli projektu.
V nabídce Unity vyberte **okno > akustické** :

![Snímek obrazovky s vybranými možnostmi nabídky okna s akustickými obrazovkami v editoru Unity](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Vytvoření navigační mřížky
Akustické využití projektu používá navigační síť k umístění testovacích bodů naslouchacího procesu pro simulaci. Můžete použít [pracovní postup navigační mřížky](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)Unity nebo použít jiný balíček 3D modelování k návrhu vlastní sítě. 

## <a name="mark-acoustic-scene-objects"></a>Označit objekty akustické scény
Akustické množství projektu spoléhá na dva typy objektů scény pro simulaci: objekty, které se budou odrazit a occlude se zvukem v simulaci, a navigační síť přehrávače, která omezuje testovací body testu v simulaci. Oba typy objektů jsou označeny pomocí karty **objekty** . 

Vzhledem k tomu, že označení objektů jednoduše přidá do objektu komponenty **AcousticsGeometry** nebo **AcousticsNavigation** , můžete také použít [standardní pracovní postup komponenty Unity](https://docs.unity3d.com/Manual/UsingComponents.html) k označení nebo odznačení objektů. Označit lze pouze zobrazovací jednotky a terény OK. Všechny ostatní typy objektů budou ignorovány. Zaškrtávací políčka označí nebo zruší označení všech ovlivněných objektů.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Označit akustickou překrytí a geometrii reflexe
Otevřete kartu **objekty** v okně **akustické** množství. Označte všechny objekty jako **akustickou geometrii** , pokud by měly occlude, odrážely nebo absorbovat zvuk. Geometrie akustického množství může zahrnovat věci, jako je například uzemnění, zdi, střechy, okna Windows & skla, rugs a velký nábytek. Pro tyto objekty můžete použít libovolnou úroveň složitosti. Vzhledem k tomu, že se scéna před simulací voxelized, vysoce podrobné sítě, jako jsou stromy s mnoha malými listy, nejsou dražší pro zanesli než zjednodušené objekty.

Nezahrnovat věci, které by neměly mít vliv na akustické, například na neviditelné sítě kolizí.

Transformace objektu v době výpočtu sondy (na kartě sondy níže) je opravena ve výsledcích zanesli. Přesunutí libovolného z označených objektů ve scéně bude vyžadovat, aby se výpočet sondy prováděl znovu a aby se scény mohla znovu vylepšovat.

### <a name="mark-the-navigation-mesh"></a>Označte navigační síť.
Navigační sítě vytvořené pomocí pracovního postupu Unity budou vyzvednuty v systému akustického využití. Chcete-li použít vlastní sítě, označte je na kartě **objekty** .

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Části karty objekty
Části stránky karty jsou:

1. Tlačítka výběru karet (vybraná karta**objekty** ). Pomocí těchto tlačítek můžete procházet různými kroky pro zaneslií akustického navýšení, zleva doprava.
2. Stručný popis toho, co je třeba použít na této stránce.
3. Dostupné filtry pro okno hierarchie Tuto možnost použijte k filtrování okna hierarchie na objekty zadaného typu, abyste je mohli snadněji označit. Pokud jste ještě nic neoznačili jako akustické, vyberete poslední dvě možnosti, že nebudete nic zobrazovat. Mohou však být užitečné k vyhledání označených objektů, jakmile je tak provedete.
4. Pokud nejsou vybrány žádné objekty, v této části se zobrazuje stav všech objektů ve scéně:
    * Total – Celkový počet aktivních neskrytých objektů ve scéně.
    * Ignorováno – počet objektů, které nejsou zobrazovacími jednotkami nebo poli terénu.
    * Mřížka – počet objektů zobrazovacího objektu mřížky ve scéně
    * Terén – počet objektů terénu ve scéně
    * Geometrie – počet objektů v mřížce nebo terénu na scéně označený jako "akustické" geometrie
    * Navigace – počet objektů v mřížce nebo terénu v scéně označených jako "akustické" navigace. Toto číslo nezahrnuje NavMesh Unity.
5. Zobrazuje celkový počet objektů s označením "označit" na scéně, což jsou pouze zobrazovací objekty a podstruktury OK. Zobrazí zaškrtávací políčka, která lze použít k označení (přidání příslušné komponenty do) těchto objektů jako geometrie nebo navigace pro akustické hodnoty.
6. Pokud není nic vybráno, zobrazí se v případě potřeby v této poznámce vybrat objekty pro označení. Můžete také zaškrtnout jedno nebo obě políčka pro označení všech objektů v scéně bez výběru cokoli.
7. Když jsou vybrány objekty, v této části se zobrazuje stav pouze vybraných objektů.
8. Zobrazuje celkový počet vybraných objektů, které je možné označit. Zaškrtnutím nebo zrušením zaškrtnutí políček dojde k označení nebo zrušení označení pouze vybraných objektů.

Pokud ve scéně nemáte nic vybrané, karta objekty bude vypadat jako na následujícím obrázku:

![Obrazovka karty akustického množství objektů bez výběru](media/objects-tab-no-selection-detail.png)

Pokud máte něco vybrané v okně scéna nebo hierarchie, bude vypadat jako na následujícím obrázku:

![Obrazovka karty akustické objekty se zobrazeným výběrem](media/objects-tab-selection-detail.png)

Pokud jsou některé objekty označeny a některé nejsou, zobrazí příslušné zaškrtávací políčko "smíšená" hodnota:

![Obrazovka karty akustické objekty se zvýrazněnou ikonou smíšeného výběru](media/mixed-object-selection-detail.png)

Kliknutím na zaškrtávací políčko vynutíte označení všech objektů a opětovným kliknutím na položku zruší označení všech objektů.

Objekty mohou být označeny pro geometrii i pro navigaci.

## <a name="select-acoustic-materials"></a>Vybrat akustické materiály
Po označení objektů klikněte na tlačítko **materiály** a přiřaďte zvukové materiály. Systém materiálových materiálů v projektu je svázán se systémem vizuálních materiálů Unity: pro dva objekty, které mají samostatné akustické materiály, musí mít samostatné vizuální materiály.

Akustické materiály ovládají množství zvukové energie, které se projeví zpátky z každého povrchu. Výchozí akustický materiál má absorpci podobnou konkrétní. Akustické projekty naznačují materiály na základě názvu vizuálního materiálu. Chcete-li povolit posuvník absorpčního součinitele, můžete k materiálu přiřadit akustický materiál "vlastní".

Reverberation čas daného materiálu v místnosti je nezávisle na jeho faktor absorpce s největšími materiály, které mají hodnoty absorpce v rozsahu 0,01 až 0,20. Materiály s absorpčními koeficienty mimo tento rozsah jsou velice absorpční.

![Graf znázorňující zápornou korelaci reverberation času s koeficientem absorpce](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Části karty materiály
![Snímek karty akustického materiálu v Unity](media/materials-tab-detail.png)

1. Tlačítko karty **materiály** , které slouží k uvedení této stránky.
2. Stručný popis toho, co je třeba použít na této stránce.
3. Při zaškrtnutí budou v seznamu uvedeny pouze materiály používané objekty označenými jako akustické akustické. Jinak budou v seznamu uvedeny všechny materiály použité na scéně.
4. Pomocí těchto možností můžete změnit pořadí rozevírací nabídky, která se zobrazí po kliknutí na rozevírací nabídku ve sloupci zvukové položky (#6). **Název** seřadí akustické materiály podle názvu. "Absorptivity" seřadí je v pořadí podle Absorptivity od nízké po vysoké.
5. Seznam materiálů použitých ve scéně seřazený podle abecedy. Pokud je zaškrtnuto políčko **Zobrazit pouze označené** jako (#3), zobrazí se pouze materiály používané objekty, které jsou označeny jako akustické. Když kliknete na nějaký materiál, vyberou se všechny objekty ve scéně, které tento materiál používají.
6. Zobrazuje akustický materiál, ke kterému byl přiřazen materiál scény. Kliknutím na rozevírací seznam znovu přiřadíte materiál scény k jinému akustickému materiálu. Můžete změnit pořadí řazení nabídky, která se zobrazí po kliknutí na položku zde, pomocí možnosti **Řadit akustické podle:** výše (#4).
7. Zobrazuje koeficient akustické absorpce materiálu vybraného v předchozím sloupci. Hodnota nula znamená dokonale odrážet (bez absorpce), zatímco hodnota 1 znamená dokonale absorptive (bez reflexe). Faktor absorpce nejde změnit, pokud vybraný materiál není Custom (vlastní).
8. Pro materiál přiřazený k "vlastní" už posuvník není zakázaný a vy můžete vybrat faktor absorpce pomocí posuvníku nebo zadáním hodnoty.

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a kontrola umístění testu naslouchacího procesu
Po přiřazení materiálů přepněte na kartu sondy a kliknutím na **Vypočítat** umístěte body testu naslouchacího procesu pro simulaci.

### <a name="what-the-calculate-button-calculates"></a>Jak vypočítat... tlačítko počítá
Tlačítko **Vypočítat...** používá zvolenou akustickou geometrii scény k přípravě scény pro simulaci:

1. Vezme geometrii z mřížek scény a vypočítá Voxel svazek. Voxel svazek je prostorový svazek, který obklopuje celou scénu a skládá se z malých krychlí "voxels". Velikost voxels je určena frekvencí simulace, která je nastavena nastavením **rozlišení simulace** . Každý Voxel je označený jako "otevřený vzduch" nebo obsahuje geometrii scény. Pokud Voxel obsahuje geometrii, Voxel je označen koeficientem absorpce materiálu přiřazeného k této geometrii.
2. K umísťování bodů sondy naslouchacího procesu používá navigační síť (y). Algoritmus vyrovnává konkurenční obavy o prostorové pokrytí a dobu simulace a velikost souboru a zároveň zajišťuje, aby úzká chodba a malé prostory měly vždy určité množství pokrytí. Typický bod sondy počítá rozsah od 100 po malé scény až na několik tisíc pro velké scény.

V závislosti na velikosti scény a rychlosti počítače můžou tyto výpočty trvat několik minut.

### <a name="review-voxel-and-probe-placement"></a>Kontrola umístění Voxel a testování paměti
Zobrazte náhled dat Voxel i míst testovacího bodu, abyste měli jistotu, že jste připraveni k zanesli své scény. Nekompletní navigační síť nebo chybějící nebo extra akustická geometrie se většinou ve verzi Preview rychle zobrazí. Voxel a testování paměti lze povolit nebo zakázat pomocí nabídky Gizma:

![Snímek nabídky Gizma v Unity](media/gizmos-menu.png)

Voxels obsahující akustickou geometrii se zobrazují jako zelené datové krychle. Prozkoumejte svoji scénu a ověřte, že voxels vše, co by mělo být geometrie. Kamera scény musí být v rozmezí od 5 měřičů objektu, aby se voxels zobrazovalo.

Pokud porovnáte voxels vytvořenou s hrubou přesností a přesnější rozlišením, uvidíte, že hrubý voxels bude dvakrát velký.

![Snímek obrazovky hrubých voxels Preview v editoru Unity](media/voxel-cubes-preview.png)

Výsledky simulace se interpolují mezi umístěními testovacího bodu testu za běhu. Podívejte se na všechny body sondy poblíž místa, kde se očekává, že hráč musí cestovat na scéně.

![Snímek obrazovky s ukázkami testu v editoru Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Postará se o přejmenování scény
Název scény slouží k připojení scény k souborům, které ukládají umístění a voxelizationí bodu testování. Pokud je scéna po výpočtu bodů sondy přejmenována, přiřazení materiálu a data o umístění budou ztracena a měla by být znovu spuštěna.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Části karty sondy
![Obrazovka karty sondy akustického množství v Unity](media/probes-tab-detail.png)

1. Tlačítko karty sondy použité k uvedení této stránky
2. Stručný popis toho, co je potřeba udělat pomocí této stránky
3. Použijte je k výběru hrubého nebo jemného rozlišení simulace. Hrubý je rychlejší, ale má určité kompromisy. Podrobnosti najdete v tématu věnovaném [řešení zanesli](bake-resolution.md) .
4. Vyberte umístění, do kterého se mají pomocí tohoto pole umístit datové soubory s akustickými daty. Klikněte na tlačítko s "..." pro použití výběru složky. Výchozí hodnota je **assets/AcousticsData**. V tomto umístění bude také vytvořena podsložka **editoru** . Další informace o datových souborech najdete v tématu [datové soubory](#Data-Files) níže.
5. Datové soubory pro tuto scénu budou pojmenovány pomocí předpony uvedené zde. Výchozí hodnota je "Acoustics_ [název scény]".
6. Po výpočtu sond budou výše uvedené ovládací prvky zakázané. Kliknutím na tlačítko **Vymazat** vymažete výpočty a povolíte ovládací prvky, aby bylo možné přepočítat pomocí nových nastavení.
7. Klikněte na tlačítko **Vypočítat...** , aby se voxelizea scéna, a vypočítejte umístění zkušebních bodů. To se provádí místně na vašem počítači a musí se provést před tím, než zanesli.

V této verzi akustického projektu nelze sondy umístit ručně a je třeba je umístit pomocí automatizovaného procesu, který je k dispozici na kartě **sondy** .

Další podrobnosti najdete v tématu věnovaném řešení hrubého a jemného rozlišení v [zanesli](bake-resolution.md) .

## <a name="bake-your-scene-using-azure-batch"></a>Zanesli své scény pomocí Azure Batch
Svou scénu můžete zanesli s výpočetním clusterem v cloudu pomocí služby Azure Batch. Modul plug-in Unity v projektu se připojuje přímo k Azure Batch k vytvoření instance, správě a zrušení Azure Batch clusteru pro každou zanesli. Na kartě **zanesli** zadejte svoje přihlašovací údaje Azure, vyberte typ a velikost počítače clusteru a klikněte na **zanesli**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Části karty zanesli
![Snímek obrazovky s akustickými Zanesliy na kartě v Unity](media/bake-tab-details.png)

1. Tlačítko karty zanesli, které slouží k uvedení této stránky
2. Stručný popis toho, co dělat na této stránce.
3. Pole, která se mají zadat v přihlašovacích údajích Azure po vytvoření účtu Azure. Další informace najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
4. Značka obrázku Docker pro sadu nástrojů akustického zobrazení
5. Spusťte Azure Portal ke správě předplatných, monitorování využití a zobrazení informací o fakturaci atd. 
6. Typ výpočetního uzlu služby Azure Batch, který se má použít pro výpočet. Typ uzlu musí být podporován vaším umístěním datového centra Azure. Pokud si to nejste jistí, ponechte na **Standard_F8s_v2**.
7. Počet uzlů, které se mají použít pro tento výpočet Číslo, které zde zadáte, bude mít vliv na čas k dokončení zanesli a je omezené vaším přidělením Azure Batchho jádra. Výchozí přidělení umožňuje pouze dva základní uzly a uzel 1 16 Core, ale lze je rozšířit. Další informace o omezeních přidělení základní části najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
8. Zaškrtnutím tohoto políčka nakonfigurujete výpočetní fond tak, aby používal [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Výpočetní uzly s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo mohou být kdykoli přerušeny.
9. Počet testů pro vaši scénu, jak je vypočítáno na kartě **sondy** . Počet sond určuje počet simulací, které je třeba spustit v cloudu. Nemůžete zadat více uzlů, než jsou sondy.
10. Doba, po kterou se očekává, že bude trvat spuštění úlohy v cloudu. To nezahrnuje čas spuštění uzlu. Po spuštění úlohy se jedná o to, jak dlouho by měla být předtím, než se vrátí výsledky. Všimněte si, že toto je pouze odhad.
11. Celková velikost výpočetní doby potřebná ke spuštění simulace. Toto je celková velikost výpočetního času uzlu, který se bude používat v Azure. Další informace o použití této hodnoty najdete v tématu [odhad zanesli nákladů](#Estimating-bake-cost) níže.
12. Tato zpráva obsahuje informace o tom, kde budou výsledky zanesli po dokončení úlohy uloženy.
13. (Jenom rozšířené použití) Pokud z nějakého důvodu potřebujete vynutit Unity o zanesli, který jste odeslali (například jste stáhli výsledky pomocí jiného počítače), klikněte na tlačítko **Vymazat stav** , abyste se zazapomněli o úlohu, která byla odeslána. Všimněte si, že to znamená, že soubor výsledků se po jeho přípravě nebude stahovat a nebude **stejný jako zrušení úlohy**. Pokud je úloha spuštěná, bude i nadále běžet v cloudu.
14. Kliknutím na tlačítko **zanesli** odešlete zanesli do cloudu. Když je úloha spuštěná, zobrazí se místo toho **úloha zrušení úlohy** .
15. Připravuje na [Váš počítač simulaci akustického](#Local-bake)zpracování.
16. Tato oblast zobrazuje stav zanesli. Po dokončení by se měl zobrazit **stažený**.

Vždy můžete získat úplné informace o aktivních úlohách, výpočetních fondech a úložišti na [Azure Portal](https://portal.azure.com).

Zatímco úloha spouští tlačítko **zanesli** , se změní na **Zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Před zrušením úlohy se zobrazí výzva k potvrzení. Zrušení úlohy nelze vrátit zpět, nebudou k dispozici žádné výsledky a bude vám stále účtována veškerá použitá výpočetní doba Azure.

Po spuštění zanesli můžete tuto Unity zavřít. V závislosti na projektu, typu uzlu a počtu uzlů může zanesli cloudu trvat několik hodin. Stav úlohy zanesli se aktualizuje, když znovu nasadíte projekt a otevřete okno akustické. Pokud byla úloha dokončena, bude stažen výstupní soubor.

Přihlašovací údaje Azure se bezpečně ukládají na vašem místním počítači a jsou spojené s vaším editorem Unity. Používají se výhradně k navázání zabezpečeného připojení k Azure.

## <a name="to-find-the-status-of-a-running-job-on-the-azure-portal"></a>Zjištění stavu spuštěné úlohy na Azure Portal

1. Na kartě zanesli najdete ID úlohy zanesli:

![Snímek obrazovky s ID úlohy Unity zanesli](media/unity-job-id.png)  

2. Otevřete [Azure Portal](https://portal.azure.com), přejděte k účtu Batch použitému pro zanesli a vyberte **úlohy** .

![Snímek obrazovky s odkazem na úlohy](media/azure-batch-jobs.png)  

3. V seznamu úloh vyhledejte ID úlohy.

![Snímek obrazovky se stavem úlohy zanesli](media/azure-bake-job-status.png)  

4. Kliknutím na ID úlohy zobrazíte stav souvisejících úloh a celkového stavu úlohy.

![Snímek obrazovky se stavem úlohy zanesli](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Odhad nákladů na Azure zanesli

K odhadu toho, jaké zanesli budou náklady, vezměte hodnotu zobrazenou pro **Odhadované výpočetní náklady**, což je doba trvání, a vynásobte ji hodinovou sazbou v místní měně vybraného **typu uzlu virtuálního počítače** . Výsledek nebude zahrnovat čas uzlu potřebný k zprovoznění uzlů. Pokud například vyberete **Standard_F8s_v2** pro typ uzlu, který má cenu $0.40/hod, a odhadované náklady na výpočetní výkon jsou 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy budou $0,40 × ~ 4 hodiny = ~ $1,60. Skutečné náklady budou pravděpodobně vyšší než vyšší čas na to, aby bylo možné uzly získat. Náklady na hodinové uzly najdete na stránce s [cenami za Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (pro kategorii vyberte výpočetní optimalizované nebo vysoký výkon).

## <a name="Local-bake"></a>Zanesli své scény na svém počítači
Své scény můžete zanesli na svém vlastním počítači. To může být užitečné při experimentování s akustickými a malými scény před vytvořením účtu Azure Batch. Všimněte si, že simulace akustického množství může trvat dlouhou dobu v závislosti na velikosti scény.

### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware
* Procesor X86-64 s aspoň 8 jádry a 32 GB paměti RAM
* [Technologie Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) s povoleným spouštěním Docker

Příklad: v našem testování na 8 základních počítačích s Intel Xeon E5-1660 @ 3 GHz a 32 GB RAM –
* Malá scéna s 100 sondami může trvat přibližně 2 hodiny za hrubý zanesli nebo 32 hodin za zanesli.
* Střední velikost scény s 1000 sondami může trvat přibližně 20 hodin po hrubý zanesli nebo 21 dní, aby se zanesli.

### <a name="setup-docker"></a>Nastavení Docker
Instalace a konfigurace Docker na počítači, který bude zpracovávat simulaci –
1. Nainstalujte [plochu Docker](https://www.docker.com/products/docker-desktop).
2. Spusťte nastavení Docker, přejděte na možnosti Upřesnit a nakonfigurujte prostředky tak, aby měly aspoň 8 GB paměti RAM. Čím více procesorů můžete přidělit Docker, tím rychleji se zanesli dokončí.  
![Snímek obrazovky s ukázkovým nastavením Docker](media/docker-settings.png)
1. Přejděte na sdílené jednotky a zapněte sdílení jednotky používané ke zpracování.  
![Snímek obrazovky s možnostmi sdíleného disku Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Spustit místní zanesli
1. Klikněte na tlačítko "připravit místní zanesli" na kartě **zanesli** a vyberte složku, do které se budou ukládat vstupní soubory a skripty spouštění. Pak můžete zanesli spustit na jakémkoli počítači, pokud splňuje minimální požadavky na hardware a máte nainstalovaný Docker zkopírováním složky do daného počítače.
2. Spusťte simulaci pomocí skriptu "runlocalbake. bat" ve Windows nebo pomocí skriptu "runlocalbake.sh" na MacOS. Tento skript načte obraz Docker s použitím sady nástrojů, který je nezbytný pro zpracování simulace, a spustí simulaci. 
3. Až se simulace dokončí, zkopírujte výsledný soubor. ACE zpátky do projektu Unity. Chcete-li zajistit, že Unity rozpozná toto jako binární soubor, přidejte do přípony souboru ". bytes" (například "scene1. ACE. bytes"). Podrobné protokoly pro simulaci jsou uloženy v "AcousticsLog. txt". Pokud narazíte na nějaké problémy, nasdílejte tento soubor pro pomoc s diagnostikou.

## <a name="Data-Files"></a>Datové soubory přidané procesem zanesli

Během procesu zanesli se vytvořily čtyři datové soubory. Jeden obsahuje výsledky simulace a dodává se s vaším názvem. Jiní uživatelé ukládají data související s editorem Unity.

Výsledek simulace:
* **Assety/AcousticsData/akustické\_[| scény]. ACE. bytes**: Toto je běhová vyhledávací tabulka, která obsahuje výsledky simulace a prvky voxelized akustické scény. Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** .

Je potřeba se starat o odstranění souboru výsledků simulace. Není obnovitelné s výjimkou úprav scény.

Datové soubory editoru:
* Assets **/Editor/[scéna]\_AcousticsParameters. Asset**: Tento soubor ukládá data, která zadáte do polí v uživatelském rozhraní akustického prostředí. Umístění a název tohoto souboru nelze změnit.
* **Assets/AcousticsData/Editor/Acoustics_ [scéna]. Vox**: Tento soubor uchovává geometrii akustických dat voxelized a vlastnosti materiálu, které jsou vypočítané pomocí tlačítka **Vypočítat...** na kartě sondy. Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** .
* **Assets/AcousticsData/Editor/akustické\_[| scény]\_config. XML**: Tento soubor ukládá parametry simulace vypočítané pomocí tlačítka **Vypočítat...** na kartě **sondy** . Umístění a název tohoto souboru lze změnit pomocí polí na kartě **sondy** .

## <a name="set-up-the-acoustics-lookup-table"></a>Nastavení vyhledávací tabulky akustického obsahu
Přetáhněte z **projektu akustické** Prefab z panelu projekt do své scény:

![Snímek obrazovky s akustickými prefaby v Unity](media/acoustics-prefab.png)

Klikněte na objekt hry **ProjectAcoustics** a přejděte na jeho panel inspektor. Zadejte umístění vašeho výsledku zanesli (. Soubor ACE, v **assets/AcousticsData**) přetáhnutím myší do skriptu správce akustického příznaku nebo kliknutím na tlačítko kruh vedle textového pole.

![Snímek obrazovky s Prefab a správcem akustického vlastnictví v Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Další kroky
* Prozkoumejte [ovládací prvky návrhu pro Unity](unity-workflow.md)
* Prozkoumejte [Koncepty návrhu projektových](design-process.md) řešení

