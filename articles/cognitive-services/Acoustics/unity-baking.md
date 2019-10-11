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
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243129"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Výukový kurz pro Zanesliu Unity v projektu
V tomto kurzu se dozvíte, jak pomocí akustického navýšení projektů v Unity vymezit.

Požadavky na software:
* [Unity 2018.2 +](https://unity3d.com) pro Windows nebo MacOS
* [Modul plug-in projektového akustického prostředí integrovaný do vašeho projektu Unity](unity-integration.md) nebo [projekt akustického obsahu Unity](unity-quickstart.md) .
* *Volitelné:* [Účet Azure Batch](create-azure-account.md) , který urychluje vytváří pomocí cloud computingu

## <a name="open-the-project-acoustics-bake-window"></a>Otevřete okno akustické zanesli projektu.
V části Unity vyberte z nabídky **okno** **akustické** .

![Editor Unity s možností zvukové možnosti, která se v nabídce okna zvýrazní](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Vytvoření navigační mřížky
Akustické využití projektu používá navigační síť k umístění testovacích bodů naslouchacího procesu pro simulaci. Můžete použít [pracovní postup mřížky navigace](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)v Unity. Nebo můžete použít jiný balíček 3D modelování k návrhu vlastní sítě.

## <a name="mark-acoustic-scene-objects"></a>Označit objekty akustické scény
Akustické množství projektu spoléhá na dva typy objektů scény pro simulaci:
- Objekty, které reflektují a occlude zvuk v simulaci
- Navigační mřížka přehrávače, která omezuje body testu naslouchacího procesu v simulaci

Oba typy objektů jsou označeny pomocí karty **objekty** .

Vzhledem k tomu, že označení objektů jednoduše přidá do objektu komponenty *AcousticsGeometry* nebo *AcousticsNavigation* , můžete také použít [standardní pracovní postup komponenty Unity](https://docs.unity3d.com/Manual/UsingComponents.html) k označení nebo odznačení objektů. Můžete označit jenom zobrazovací a terény mřížky. Všechny ostatní typy objektů budou ignorovány. Zaškrtávací políčka označují nebo zruší označení všech ovlivněných objektů.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Označit akustickou překrytí a geometrii reflexe
Otevřete kartu **objekty** v okně **akustické** množství. Označte všechny objekty jako *akustickou geometrii* , pokud by měly occlude, odrážely nebo absorbovat zvuk. Geometrie akustického množství může zahrnovat věci, jako je například uzemnění, stěny, střechy, okna a sklo, rugs a velký nábytek. Pro tyto objekty můžete použít libovolnou úroveň složitosti. Vzhledem k tomu, že se scéna před simulací voxelized, velmi podrobné sítě, jako jsou například stromy s mnoha listy, nejsou dražší pro zanesli než zjednodušené objekty.

Nezahrnovat věci, které by neměly mít vliv na akustické, například na neviditelné sítě kolizí.

Transformace objektu během výpočtu sondy (prostřednictvím karty **sondy** ) je opravena ve výsledcích zanesli. Pokud se jakékoliv označené objekty na scéně přesunou později, je nutné provést výpočet a pečení.

### <a name="mark-the-navigation-mesh"></a>Označte navigační síť.
Navigační sítě, které byly vytvořené prostřednictvím pracovního postupu Unity, se vybírají prostřednictvím akustického systému. Chcete-li použít vlastní sítě, označte je na kartě **objekty** .

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: části karty objekty
Části stránky karty (s obrázkem v rámci popisů):

1. Tlačítka výběru karet (se zvolenou kartou **objekty** ) Pomocí těchto tlačítek můžete procházet různými kroky zanesli akustického provozu, zleva doprava.
1. Stručný popis toho, co můžete dělat pomocí této karty.
1. Dostupné filtry pro okno hierarchie Pomocí těchto možností můžete filtrovat okno hierarchie na objekty zadaného typu, abyste je mohli snadno označit. Pokud jste ještě nic neoznačili jako akustické, vyberete poslední dvě možnosti, že nebudete nic zobrazovat. Tyto možnosti vám ale pomůžou najít objekty po jejich označení.
1. Pokud nejsou vybrány žádné objekty, v této části se zobrazuje stav všech objektů ve scéně.
    * Total (celkem): celkový počet aktivních, neskrytých objektů.
    * Ignorováno: počet objektů, které nejsou zobrazovacími jednotkami nebo podsítěmi.
    * Síť: počet objektů zobrazovacího objektu mřížky.
    * Terén: počet objektů terénu.
    * Geometry: počet objektů sítě nebo terénu, které jsou označeny jako **geometrie akustického**množství.
    * Navigace: počet objektů na síti nebo terénu, které jsou označené jako **akustické množství navigace**. Toto číslo nezahrnuje Unity NavMesh.
1. Celkový počet "vypsaných" objektů ve scéně, což jsou pouze zobrazovací objekty a pole terénu. Pomocí zaškrtávacích políček označte (přidejte příslušnou komponentu do) těchto objektů jako geometrii nebo navigaci pro akustické hodnoty.
1. Pokud není nic vybráno, v případě potřeby si všimněte, že v případě potřeby vyberete objekty pro označení. Můžete také zaškrtnout jedno nebo obě zaškrtávací políčka pro označení všech objektů ve scéně.
1. Když jsou vybrány objekty, v této části se zobrazuje stav pouze vybraných objektů.
1. Celkový počet vybraných objektů označených příznakem "označitelné". Zaškrtněte nebo zrušte zaškrtnutí políček nebo zrušte označení pouze vybraných objektů.

Pokud ve scéně nemáte nic vybrané, karta **objekty** vypadá jako na následujícím obrázku.

![Karta akustické objekty bez výběru nic](media/objects-tab-no-selection-detail.png)

Pokud máte něco vybrané v okně scéna nebo hierarchie, karta vypadá jako na následujícím obrázku.

![Karta akustické objekty s výběry](media/objects-tab-selection-detail.png)

Pokud jsou některé objekty označeny a některé nejsou, zobrazí se příslušná zaškrtávací políčka jako "smíšená" hodnota, podobně jako na následujícím obrázku.

![Karta akustické objekty se smíšeným výběrem ikon zvýrazněných](media/mixed-object-selection-detail.png)

Zaškrtnutím políčka označíte všechny položky. Zrušte zaškrtnutí políčka zrušit označení všech objektů.

Objekty mohou být označeny pro geometrii i pro navigaci.

## <a name="select-acoustic-materials"></a>Vybrat akustické materiály
Po označení objektů vyberte tlačítko **materiály** . Pak přiřaďte akustické materiály. Systém materiálových materiálů v projektu je svázán se systémem pro vizuální materiály v Unity. Aby dva objekty měly samostatné akustické materiály, musí mít samostatné vizuální materiály.

Výběr akustického materiálu určuje množství zvukové energie, které se odrazí z každého povrchu. Výchozí akustický materiál má absorpci podobnou ocelovému. Akustické projekty naznačují materiály na základě názvu vizuálního materiálu. Můžete také přiřadit akustický materiál, který je **vlastní** pro materiál, a aktivovat tak upravitelný posuvník absorpčního součinitele.

Reverberation čas daného materiálu v místnosti je nezávisle na jeho koeficient absorpce. Většina materiálů obsahuje hodnoty absorpce v rozsahu od 0,01 do 0,20. Materiály, které mají absorpční koeficienty mimo tento rozsah, jsou velice absorpční.

![Graf zobrazuje negativní korelaci reverberation času a koeficient absorpce.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: části karty materiály
![Karta akustické materiály v Unity](media/materials-tab-detail.png)
1. Na tlačítku **materiály** se zobrazí tato karta.
2. Stručný popis toho, co můžete dělat pomocí této karty.
3. Pokud je toto políčko zaškrtnuté, jsou uvedeny pouze materiály, které jsou používány objekty označenými jako **akustické** . V opačném případě jsou uvedeny všechny materiály, které jsou použity na scéně.
4. Pomocí těchto možností můžete změnit pořadí možností v nabídce ve sloupci **akustické** (#6). Seřaďte akustické materiály podle **názvu** nebo podle **Absorptivity**, od nízké po vysoké.
5. Abecední setříděný seznam materiálů, které se používají ve scéně. Pokud je zaškrtnuto políčko **Zobrazit pouze označené** jako (#3), zobrazí se pouze materiály používané objekty, které jsou označeny jako **akustické** . Vyberte nějaký materiál pro výběr všech objektů ve scéně, které tento materiál používají.
6. Akustický materiál, ke kterému byl přiřazen materiál scény. Vyberte libovolnou položku, chcete-li změnit akustický materiál přiřazený k danému materiálu scény. Chcete-li změnit pořadí řazení těchto nabídek, použijte možnosti **Řadit akustické podle** možností (#4).
7. Akustický koeficient absorpce materiálu vybraného ve sloupci nalevo (#6). Hodnota 0 znamená dokonale odrážet (bez absorpce), zatímco 1 znamená dokonale absorptive (bez reflexe). Faktor absorpce se nedá změnit, pokud vybraný materiál není **vlastní.**
8. Pro materiál označený jako **vlastní**se aktivuje posuvník. Můžete přesunout posuvník nebo zadat hodnotu a přiřadit koeficient absorpce.

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a kontrola umístění testu naslouchacího procesu
Po přiřazení materiálů přepněte na kartu **sondy** . Vyberte možnost **Vypočítat** a umístěte body testu naslouchacího procesu pro simulaci.

### <a name="what-the-calculate-button-does"></a>Co dělá tlačítko "Výpočet"
Tlačítko **Vypočítat** používá vybranou mikrogeometrii scény k přípravě scény pro simulaci:

- Vezme geometrii z mřížek scény a vypočítá *Voxel svazek*. Voxel svazek je objem celé scény sestávající z malých krychlí "voxels". Velikost Voxel je určena frekvencí simulace, která je řízena nastavením **rozlišení simulace** . Každý Voxel je označený jako "otevřený vzduch" nebo obsahuje geometrii scény. Pokud Voxel obsahuje geometrii, Voxel je označen koeficientem absorpce materiálu, který je přiřazen k dané geometrii.
- Používá navigační sítě k umístění zkušebních bodů naslouchacího procesu. Algoritmus vyrovnává konkurenční obavy o prostorové pokrytí a dobu simulace a velikost souboru. Cílem je zajistit, aby úzká chodba a malé prostory měly vždy určité pokrytí. Typický bod sondy počítá rozsah od 100 po malé scény až na několik tisíc pro velké scény.

V závislosti na velikosti scény a rychlosti počítače můžou tyto výpočty trvat několik minut.

### <a name="review-voxel-and-probe-placement"></a>Kontrola umístění Voxel a testování paměti
Zobrazte náhled dat Voxel a míst testovacího bodu, abyste měli jistotu, že jste připraveni k zanesli své scény. Nekompletní navigační síť nebo chybějící nebo extra akustická geometrie je obvykle snadno viditelná ve verzi Preview. Povolte nebo zakažte Voxel a umístění sondy pomocí nabídky **gizma** .

![Nabídka Gizma v Unity](media/gizmos-menu.png)

Voxels, které obsahují akustickou geometrii, se zobrazují jako zelené datové krychle. Prozkoumejte svoji scénu a ověřte, že voxels vše, co by mělo být geometrie. Kamera scény musí být v rozmezí od 5 měřičů objektu, aby se voxels zobrazovalo.

Pokud porovnáte voxels vytvořené s hrubou přesností a jemným rozlišením, uvidíte, že hrubý voxels je dvakrát velký.

![Hrubý voxels ve verzi Preview v editoru Unity](media/voxel-cubes-preview.png)

Výsledky simulace se interpolují mezi umístěními testovacího bodu testu za běhu. Ověřte, zda jsou v blízkosti všech míst, kde hráč očekává, přejít na scénu body sond.

![Sondy ve verzi Preview v editoru Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Postará se o přejmenování scény
Název scény slouží k připojení scény k souborům, které ukládají umístění testovacího bodu a voxelization. Pokud přejmenujete scénu po výpočtu bodů sondy, bude přiřazení materiálu a data o umístění ztraceno a mělo by být znovu spuštěno.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: části karty sondy
![Karta sondy akustického testování v Unity](media/probes-tab-detail.png)

1. Tlačítko **sondy** přinese tuto kartu.
2. Stručný popis toho, co můžete na této kartě dělat.
3. Tyto možnosti slouží k nastavení hrubého nebo jemného rozlišení simulace. Hrubý je rychlejší, ale existují kompromisy. Podrobnosti najdete v tématu věnovaném [řešení zanesli](bake-resolution.md).
4. Určuje, kam se mají ukládat zvukové datové soubory. Kliknutím na tlačítko **...** získáte přístup k výběru složky. Výchozím umístěním jsou *assety/AcousticsData*. V tomto umístění se vytvoří také podsložka *editoru* . Další informace najdete v části [datové soubory přidané pomocí procesu zanesli](#Data-Files)dále v tomto článku.
5. Předpona, která je zde uvedena, slouží k pojmenování datových souborů pro tuto scénu. Výchozí hodnota je "Acoustics_ *[název scény]* ".
6. Po výpočtu sond jsou ovládací prvky, které jsme právě popsány, zakázané. Pokud chcete vymazat výpočty a povolit ovládací prvky, aby bylo možné přepočítat s novými nastaveními, vyberte tlačítko **Vymazat** .
7. Vyberte možnost **Vypočítat** , aby se voxelizea scéna a počítala místa pro body testu paměti. Výpočet se provádí místně na vašem počítači. Je nutné provést před provedením zanesli.

V této verzi akustického projektu nelze sondy umístit ručně. Na kartě **sondy** použijte automatizovaný proces.

Další informace o hrubých a jemných řešeních najdete v tématu věnovaném [řešení zanesli](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Zanesli své scény pomocí Azure Batch
Své scény můžete zanesli na výpočetním clusteru v cloudu pomocí služby Azure Batch. Modul plug-in Unity v projektu se připojuje přímo k Azure Batch k vytvoření instance, správě a zrušení Azure Batch clusteru pro každou zanesli. Na kartě **zanesli** zadejte svoje přihlašovací údaje Azure, vyberte typ a velikost počítače clusteru a pak vyberte **zanesli**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: části karty zanesli
![Karta akustické Zanesliy v Unity](media/bake-tab-details.png)

1. Tato karta se zobrazí na tlačítku **zanesli** .
2. Stručný popis toho, co můžete na této stránce provést.
3. Po vytvoření účtu Azure zadejte svoje přihlašovací údaje Azure do těchto polí. Další informace najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
4. Pole značky obrázku Docker pro sadu nástrojů akustického zobrazení
5. Otevře Azure Portal pro správu předplatných, monitorování využití a zobrazení fakturačních informací.
6. Určuje typ Azure Batch výpočetního uzlu, který se má použít pro výpočet. Typ uzlu musí být podporovaný vaším umístěním datacentra Azure. Pokud si nejste jistí, ponechte jako **Standard_F8s_v2**.
7. Počet uzlů, které se mají použít pro výpočet. Toto číslo ovlivňuje zanesliý čas. Je omezeno vaším přidělením Azure Batchho jádra. Výchozí přidělení povoluje pouze dva uzly s osmi jádry nebo 1 16, ale lze je rozšířit. Další informace o omezeních základní alokace najdete v tématu [Vytvoření účtu Azure Batch](create-azure-account.md).
8. Toto políčko zaškrtněte, pokud chcete fond COMPUTE nakonfigurovat tak, aby používal [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Výpočetní uzly s nízkou prioritou mají mnohem nižší náklady. Ale nemusí být vždy k dispozici nebo je možné je kdykoli zapustit.
9. Počet testů pro vaši scénu, jak je vypočítáno na kartě **sondy** . Počet sond určuje počet simulací, které musí běžet v cloudu. Nemůžete zadat více uzlů, než jsou sondy.
10. Odhad doby, po kterou bude úloha trvat běžet v cloudu, s výjimkou času spuštění uzlu. Po spuštění úlohy se v tomto poli zobrazí odhad doby, než se vrátí výsledky.
11. Celková velikost výpočetní doby potřebná ke spuštění simulace. Tato hodnota je celková velikost výpočetního času uzlu, který se bude používat v Azure. Další informace najdete v tématu [odhad nákladů Azure zanesli](#Estimating-bake-cost) dále v tomto článku.
12. Tato zpráva obsahuje informace o tom, kde budou výsledky zanesli po dokončení úlohy uloženy.
13. *(Pouze rozšířené použití:)* toto tlačítko vynutí, abyste zazapomněli o zanesli, který jste odeslali. Pokud jste například stáhli výsledky pomocí jiného počítače, vyberte tlačítko **Vymazat stav** , aby se tato úloha zapomenout. Výsledný soubor se po jeho *přípravě nebude stahovat* . *To není totéž jako zrušení úlohy. Pokud je úloha spuštěná, bude i nadále běžet v cloudu.*
14. Výběrem tohoto tlačítka odešlete zanesli do cloudu. Když je úloha spuštěná, toto tlačítko se **zruší úlohu**.
15. Toto tlačítko vyberte, pokud chcete [na svém počítači připravit simulaci akustického](#Local-bake)zpracování.
16. Tato oblast zobrazuje stav zanesli. Po dokončení zanesli se zobrazí zpráva "staženo".

Vždy můžete získat úplné informace o aktivních úlohách, výpočetních fondech a úložišti v [Azure Portal](https://portal.azure.com).

Když je úloha spuštěná, popisek tlačítka **zanesli** se změní na **Zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Zobrazí se výzva k potvrzení. Zrušení úlohy nelze vrátit zpět. Po zrušení nebudou dostupné žádné výsledky, ale pořád se vám bude účtovat libovolný použitý čas Azure Compute.

Po spuštění zanesli můžete tuto Unity zavřít. V závislosti na projektu, typu uzlu a počtu uzlů může zanesli cloudu trvat několik hodin. Stav úlohy zanesli se aktualizuje, když znovu nasadíte projekt a otevřete okno akustické. Pokud je úloha dokončena, bude stažen výstupní soubor.

Z důvodu zabezpečení jsou přihlašovací údaje Azure uložené na vašem místním počítači a přidruženy k vašemu editoru Unity. Používají se jenom k navázání zabezpečeného připojení k Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Vyhledá stav spuštěné úlohy na Azure Portal.

1. Na kartě **zanesli** najdete ID úlohy zanesli.

    ![ID úlohy Unity zanesli zvýrazněné na kartě zanesli](media/unity-job-id.png)  

2. Otevřete [Azure Portal](https://portal.azure.com), vyhledejte účet Batch, který se použil pro zanesli, a vyberte **úlohy**.

    ![Odkaz na úlohy v Azure Portal](media/azure-batch-jobs.png)  

3. V seznamu úloh vyhledejte ID úlohy.

   ![Stav úlohy zanesli se v Azure Portal zvýrazní.](media/azure-bake-job-status.png)  

4. Vyberte ID úlohy a zobrazte stav souvisejících úloh a celkový stav úlohy.

   ![Stav úlohy zanesli](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Odhad nákladů na Azure zanesli

K odhadu toho, co zanesli bude mít náklady, začněte **odhadovanou hodnotou výpočetních nákladů** , což je doba trvání. Vynásobí tuto hodnotu hodinovým nákladům ve vaší místní měně pro vybraný **typ uzlu virtuálního počítače** . Všimněte si, že výsledek nebude zahrnovat čas uzlu, který je potřeba k tomu, aby se uzly daly v provozu.

Řekněme například, že vyberete **Standard_F8s_v2** pro typ uzlu, který má cenu $0.40/hod. Pokud jsou **Odhadované výpočetní náklady** 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy budou $0,40 × ~ 4 hodiny = ~ $1,60. Skutečné náklady budou pravděpodobně vyšší než vyšší čas na to, aby bylo možné uzly získat.

Hledání hodinových uzlů se účtuje za [Azure Batch cen](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Jako kategorie vyberte výpočetní **optimalizované** nebo **vysoce výkonné výpočty** .)

## <a name="Local-bake"></a>Zanesli své scény na svém počítači
Svou scénu můžete také zanesli na svém vlastním počítači. Tato metoda může být užitečná pro experimentování s akustickými a malými scény před vytvořením účtu Azure Batch. Upozorňujeme však, že simulace místních akustických objemů může trvat dlouhou dobu v závislosti na velikosti scény.

### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware
* Procesor X86-64 s aspoň 8 jádry a 32 GB paměti RAM
* [Technologie Hyper-V s povoleným](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) spouštěním Docker

Příklad: v našem testování na 8-Core počítači, Intel Xeon E5-1660 @ 3 GHz a 32 GB paměti RAM:
* Malé scény s 100 testy trvalo přibližně 2 hodiny na hrubý zanesli nebo 32 hodin za zanesli.
* Středně velké scény s 1 000 testy trvalo přibližně 20 hodin za hrubý zanesli nebo 21 dní pro zanesli.

### <a name="set-up-docker"></a>Nastavení Docker
Nainstalujte a nakonfigurujte Docker na počítači, který bude zpracovávat simulaci:
1. Nainstalujte [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Otevřete nastavení Docker, pokračujte na **Upřesnit**a nakonfigurujte prostředky aspoň na 8 GB paměti RAM. Čím více procesorů můžete přidělit Docker, tím rychleji bude zanesli dokončeno.  
![Sample Docker Settings @ no__t-1
1. Přejít na **sdílené jednotky**a zapnout sdílení pro jednotku, která se používá ke zpracování.  
@no__t – možnosti sdílené jednotky 0Docker @ no__t-1

### <a name="run-the-local-bake"></a>Spustit místní zanesli
1. Na kartě **zanesli** vyberte tlačítko **připravit místní zanesli** . Pak vyberte umístění složky pro uložení vstupních souborů a spouštěcích skriptů do. Zanesli pak můžete spustit na jakémkoli počítači, pokud splňuje minimální požadavky na hardware a nainstalujete Docker zkopírováním složky do daného počítače.
2. Chcete-li spustit simulaci, spusťte skript *runlocalbake. bat* ve Windows nebo skriptu *runlocalbake.sh* na MacOS. Tento skript načte obraz Docker s použitím sady nástrojů, který je nezbytný pro zpracování simulace, a spustí simulaci.
3. Po dokončení simulace Zkopírujte výsledný soubor *. ACE* zpátky do projektu Unity. Abyste se ujistili, že Unity rozezná jako binární soubor, přidejte do přípony souboru ". bytes" (například "scene1. ACE. bytes"). Podrobné protokoly pro simulaci jsou uloženy v *AcousticsLog. txt.* Pokud narazíte na nějaké problémy, prozkoumejte tento soubor, abyste mohli problém diagnostikovat.

## <a name="Data-Files"></a>Datové soubory přidané procesem zanesli

Během procesu zanesli se vytvoří následující čtyři datové soubory. Jeden obsahuje výsledky simulace a dodává se s vaším názvem. Jiní uživatelé ukládají data související s editorem Unity.

Výsledek simulace:
* *Assety/AcousticsData/akustické zdroje @ no__t-1 [scéna]. ACE. bytes*: Tento soubor je běhová vyhledávací tabulka. Obsahuje výsledky simulace a voxelized akustické scény. Název a umístění tohoto souboru můžete změnit na kartě **sondy** .

   *Dejte pozor, abyste neodstranili soubor výsledků simulace. Nedá se zotavit s výjimkou úprav scény.*

Datové soubory editoru:
* *Assets/Editor/[scéna] \_AcousticsParameters. Asset*: Tento soubor ukládá data, která zadáte do polí v uživatelském rozhraní akustického prostředí. Nemůžete změnit název a umístění tohoto souboru.
* *Assets/AcousticsData/Editor/Acoustics_ [scéna]. Vox*: Tento soubor uchovává geometrii voxelized akustické a vlastnosti materiálu, které jsou vypočítány po výběru tlačítka **Vypočítat** na kartě **sondy** . Název a umístění tohoto souboru můžete změnit na kartě **sondy** .
* *Assets/AcousticsData/Editor/akustické datové vazby @ no__t-1 [scéna] @no__t -2config. XML*: Tento soubor uchovává parametry simulace, které jsou vypočítány při výběru položky **Vypočítat**. Název a umístění tohoto souboru můžete změnit na kartě **sondy** .

## <a name="set-up-the-acoustics-lookup-table"></a>Nastavení vyhledávací tabulky akustického obsahu
Přetáhněte z panelu projekt **akustické** prefaby projektu do své scény:

![Akustické prefaby v Unity](media/acoustics-prefab.png)

Vyberte objekt hry **ProjectAcoustics** a pak přejít na jeho panel inspektor. Zadejte umístění vašeho výsledku zanesli (soubor. ACE, in *assets/AcousticsData*): přetáhněte ho do skriptu správce akustického navýšení nebo vyberte tlačítko kruh vedle textového pole.

![Správce akustického prefabu v Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Další kroky
* Prozkoumejte [ovládací prvky návrhu Unity](unity-workflow.md).
* Prozkoumejte [Koncepty návrhu projektů](design-process.md).
