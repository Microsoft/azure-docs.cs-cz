---
title: Kurz pečení akustiky projektu Akustika
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje akustiku pečení s project acoustics v jednotě.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243129"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Kurz pečení akustiky projektu Akustika
Tento kurz popisuje akustiku pečení pomocí projektu Akustika v jednotě.

Požadavky na software:
* [Unity 2018.2+](https://unity3d.com) pro Windows nebo MacOS
* [Modul plug-in Project Acoustics integrovaný do projektu Unity](unity-integration.md) nebo [ukázkového obsahu aplikace Project Acoustics Unity](unity-quickstart.md)
* *Nepovinné:* [Účet Azure Batch](create-azure-account.md) pro urychlení pečení pomocí cloud computingu

## <a name="open-the-project-acoustics-bake-window"></a>Otevření okna Pečení akustiky projektu
V unity vyberte **akustiku** z nabídky **Okno.**

![Editor Unity s možností Akustika zvýrazněnou v nabídce Okno](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Vytvoření navigační sítě
Aplikace Project Acoustics používá navigační síť k umístění bodů sondy posluchače pro simulaci. Můžete použít [pracovní postup navigační sítě](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)Unity . Nebo můžete použít jiný balíček 3D modelování k návrhu vlastní sítě.

## <a name="mark-acoustic-scene-objects"></a>Označení objektů akustické scény
Aplikace Project Acoustics závisí na dvou typech objektů scény pro simulaci:
- Objekty, které odrážejí a okluzují zvuk v simulaci
- Navigační síť přehrávače, která omezuje body sondy posluchače v simulaci

Oba typy objektů jsou označeny pomocí karty **Objekty.**

Vzhledem k tomu, že označení objektů jednoduše přidá *komponenty AcousticsGeometry* nebo *AcousticsNavigation* do objektu, můžete také použít [standardní pracovní postup komponenty Unity](https://docs.unity3d.com/Manual/UsingComponents.html) k označení nebo zrušení označení objektů. Můžete označit pouze vykreslovače sítí a terény. Všechny ostatní typy objektů budou ignorovány. Zaškrtávací políčka označí nebo odznačí všechny ovlivněné objekty.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Označit akustickou okluzi a geometrii odrazu
Otevřete kartu **Objekty** v okně **Akustika.** Označte všechny objekty jako *geometrii akustiky,* pokud by měly okluzovat, odrážet nebo absorbovat zvuk. Geometrie akustiky může zahrnovat věci jako země, stěny, střechy, okna a okenní sklo, koberce a velký nábytek. Pro tyto objekty můžete použít libovolnou úroveň složitosti. Vzhledem k tomu, že scéna je voxelized před simulací, vysoce detailní ok, jako jsou stromy, které mají mnoho listů, nejsou dražší péct než zjednodušené objekty.

Nezahrnejte věci, které by neměly ovlivnit akustiku, například neviditelné kolizní ok.

Transformace objektu během výpočtu sondy (prostřednictvím karty **Sondy)** je stanovena ve výsledcích pečení. Pokud jsou některé označené objekty ve scéně přesunuty později, je třeba předělat výpočet sondy a pečení.

### <a name="mark-the-navigation-mesh"></a>Označení navigační sítě
Navigační mříže, které byly vytvořeny prostřednictvím pracovního postupu Unity, budou vyzvednuty systémem Akustiky. Chcete-li použít vlastní mše, označte je na kartě **Objekty.**

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Díly kartě Objekty
Části stránky karty (na obrázku za popisy) jsou:

1. Tlačítka pro výběr karty (s vybranou kartou **Objekty).** Pomocí těchto tlačítek se můžete pohybovat v různých krocích akustiky pečeme, zleva doprava.
1. Stručný popis toho, co můžete udělat pomocí této karty.
1. Dostupné filtry pro okno hierarchie. Pomocí těchto možností můžete filtrovat okno hierarchie na objekty zadaného typu, abyste je mohli snadno označit. Pokud jste ještě nic neoznačili pro akustiku, výběrem posledních dvou možností se vám nic neukáže. Tyto možnosti však pomáhají najít objekty po jejich označení.
1. Pokud nejsou vybrány žádné objekty, tato část zobrazuje stav všech objektů ve scéně.
    * Celkem: Celkový počet aktivních, neskrytých objektů.
    * Ignorováno: Počet objektů, které nejsou vykreslovacími sítěmi nebo terény.
    * Síť: Počet objektů vykreslovače sítě.
    * Terén: Počet terénních objektů.
    * Geometrie: Počet objektů sítě nebo terénu, které jsou označeny jako **Geometrie akustiky**.
    * Navigace: Počet objektů sítě nebo terénu označených jako **Navigace v akustice**. Toto číslo nezahrnuje Unity NavMesh.
1. Celkový počet "značitelných" objektů ve scéně, což jsou pouze vykreslovače sítí a terény. Pomocí zaškrtávacích políček označte (přidat příslušnou součást) tyto objekty jako geometrii nebo navigaci pro akustiku.
1. Pokud není nic vybráno, tato poznámka vám připomene vybrat objekty pro označení, v případě potřeby. Můžete také zaškrtnout jedno nebo obě políčka a označit tak všechny objekty ve scéně.
1. Když jsou objekty vybrány, tato část zobrazuje stav pouze vybraných objektů.
1. Celkový počet "označitelných" vybraných objektů. Zaškrtnutím nebo zrušením zaškrtnutí zaškrtávacích políček označíte nebo odznačíte pouze vybrané objekty.

Pokud ve scéně není nic vybraného, bude karta **Objekty** vypadat jako na následujícím obrázku.

![Karta Objekty akustiky s vybranou možností](media/objects-tab-no-selection-detail.png)

Pokud máte něco vybraného v okně scény nebo hierarchie, bude karta vypadat jako na následujícím obrázku.

![Karta Objekty akustiky s výběry](media/objects-tab-selection-detail.png)

Pokud jsou některé objekty označeny a některé ne, příslušná zaškrtávací políčka zobrazí "smíšenou" hodnotu, například na následujícím obrázku.

![Karta Objekty akustiky se zvýrazněným smíšeným výběrem ikon](media/mixed-object-selection-detail.png)

Zaškrtnutím políčka označíte všechny položky. Odznačte ji, chcete-li zrušit označení všech objektů.

Objekty mohou být označeny jak pro geometrii, tak pro navigaci.

## <a name="select-acoustic-materials"></a>Výběr akustických materiálů
Po označení objektů vyberte tlačítko **Materiály.** Pak přiřaďte akustické materiály. Systém materiálů Akustiky projektu je vázán na systém vizuálních materiálů Unity. Aby měly dva objekty samostatné akustické materiály, musí mít samostatné vizuální materiály.

Volba akustického materiálu určuje množství zvukové energie, která se odráží zpět od každého povrchu. Výchozí akustický materiál má absorpci podobnou oceli. Projekt Akustika navrhuje materiály založené na názvu vizuálního materiálu. Můžete také přiřadit akustický materiál **Vlastní** materiálu k materiálu aktivovat nastavitelný koeficient absorpce jezdce.

Doba dozvuku daného materiálu v místnosti nepřímo souvisí s jeho absorpčním koeficientem. Většina materiálů má hodnoty absorpce v rozmezí 0,01 až 0,20. Materiály, které mají absorpční koeficienty mimo tento rozsah, jsou velmi absorpční.

![Graf ukazuje zápornou korelaci doby dozvuku a koeficientu absorpce.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Části karty Materiály
![Karta Akustické materiály v unity](media/materials-tab-detail.png)
1. Tlačítko **Materiály** zobrazí tuto kartu.
2. Stručný popis toho, co můžete udělat pomocí této karty.
3. Pokud je toto políčko zaškrtnuto, jsou uvedeny pouze materiály, které jsou používány objekty označenými jako **Geometrie akustiky.** V opačném případě jsou uvedeny všechny materiály, které jsou použity ve scéně.
4. Pomocí těchto možností můžete změnit pořadí voleb v nabídce ve sloupci **Akustika** (#6). Třídit akustické materiály podle **názvu** nebo **absorpční ,** od nízké po vysokou.
5. Abecedně seřazený seznam materiálů, které se používají ve scéně. Pokud je zaškrtnuté políčko **Zobrazit pouze označeno** (#3), zobrazí se pouze materiály používané objekty označenými jako **Geometrie akustiky.** Vyberte zde materiál a vyberte všechny objekty ve scéně, které tento materiál používají.
6. Akustický materiál, ke kterému byl materiál scény přiřazen. Vyberte libovolnou položku, chcete-li změnit akustický materiál přiřazený k danému materiálu scény. Chcete-li změnit pořadí řazení těchto nabídek, použijte **volby Seřadit akustiku** podle (#4).
7. Koeficient akustické absorpce materiálu vybraného ve sloupci vlevo (#6). Hodnota 0 znamená dokonale reflexní (bez absorpce), zatímco 1 znamená dokonale absorpční (bez odrazu). Koeficient absorpce nelze změnit, pokud vybraný materiál není **Vlastní.**
8. U materiálu označeného jako **Vlastní**se aktivuje posuvník. Můžete posunout jezdec nebo zadat hodnotu pro přiřazení koeficientu absorpce.

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a kontrola umístění sondy posluchače
Po přiřazení materiálů přepněte na kartu **Sondy.** Vyberte **Vypočítat,** chcete-li umístit body sondy posluchače pro simulaci.

### <a name="what-the-calculate-button-does"></a>K čemu dělá tlačítko "Vypočítat"
Tlačítko **Vypočítat** používá vybranou geometrii akustické scény k přípravě scény pro simulaci:

- Vezme geometrii z ok scény a vypočítá *objem voxelu*. Objem voxelu je objem celé scény složený z malých kubických "voxelů". Velikost Voxelu je určena frekvencí simulace, která je řízena nastavením **rozlišení simulace.** Každý voxel je označen jako "pod širým nebem" nebo obsahuje geometrii scény. Pokud voxel obsahuje geometrii, je voxel označen koeficientem absorpce materiálu, který je přiřazen k této geometrii.
- Používá navigační chození umístit naslouchací proces sondy bodů. Algoritmus vyvažuje konkurenční obavy z prostorového pokrytí a času simulace a velikosti souboru. Jeho cílem je zajistit, aby úzké chodby a malé prostory měly vždy určité pokrytí. Počet typických bodů sondy se pohybuje od 100 pro malé scény až po několik tisíc pro velké scény.

V závislosti na velikosti scény a rychlosti vašeho stroje mohou tyto výpočty trvat několik minut.

### <a name="review-voxel-and-probe-placement"></a>Zkontrolujte umístění voxelu a sondy
Prohlédněte si náhled dat voxelu i umístění bodů sondy, abyste se ujistili, že jste připraveni upéct scénu. Neúplná navigační síť nebo chybějící nebo extra akustická geometrie je obvykle snadno viditelná v náhledu. Povolte nebo zakažte umístění voxelu a sondy pomocí nabídky **Gizmos.**

![Nabídka Gizmos v Unity](media/gizmos-menu.png)

Voxels, které obsahují akustickou geometrii jsou zobrazeny jako zelené kostky. Prozkoumejte svou scénu a ověřte, zda vše, co by mělo být geometrií, má voxels. Scéna kamera musí být do asi 5 metrů od objektu pro voxels ukázat.

Pokud porovnáte voxels vytvořené s hrubým rozlišením versus jemné rozlišení, uvidíte, že hrubé voxels jsou dvakrát tak velké.

![Hrubý voxels náhled v editoru Unity](media/voxel-cubes-preview.png)

Výsledky simulace jsou interpolovány mezi umístěními bodů sondy naslouchací proces za běhu. Zkontrolujte, zda jsou v blízkosti všech míst, kde se očekává, že hráč jde do scény, zkontrolujte, zda jsou v blízkosti všech míst, kde se očekává, že hráč.

![Náhled sond v editoru Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Dávejte pozor s přejmenování scény
Název scény se používá k připojení scény k souborům, které ukládají umístění bodu sondy a voxelizaci. Pokud scénu přejmenujete po výpočtu bodů sondy, dojde ke ztrátě dat přiřazení materiálu a umístění a data se mají znovu spustit.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Části karty Sondy
![Karta Akustické sondy v unity](media/probes-tab-detail.png)

1. Tlačítko **Sondy** zobrazí tuto kartu.
2. Stručný popis toho, co můžete dělat na této kartě.
3. Tyto možnosti slouží k nastavení hrubého nebo jemného simulačního rozlišení. Hrubé je rychlejší, ale existují kompromisy. Podrobnosti viz [Rozlišení pečení](bake-resolution.md).
4. Určuje, kam umístit datové soubory akustiky. Chcete-li získat přístup k výběru složek, vyberte tlačítko **" ...**". Výchozí umístění je *Assets/AcousticsData*. V tomto umístění je také vytvořena podsložka *editoru.* Další informace naleznete [v tématu Datové soubory přidané procesem pečení](#Data-Files)dále v tomto článku.
5. Zde zadaná předpona se používá k pojmenování datových souborů pro tuto scénu. Výchozí hodnota je "Acoustics_*[Název scény]*".
6. Po výpočtu sond jsou ovládací prvky, které jsme právě popsali, zakázány. Výběrem tlačítka **Vymazat** vymažete výpočty a povolte ovládací prvky, abyste je mohli přepočítat pomocí nových nastavení.
7. Vyberte **Vypočítat,** chcete-li scénu voxelize a vypočítat umístění bodů sondy. Výpočet se provádí lokálně na vašem počítači. To musí být provedeno před tím, než péct.

V této verzi projektu Akustika sondy nelze umístit ručně. Použijte automatizovaný proces na kartě **Sondy.**

Další informace o hrubém a jemném rozlišení naleznete v tématu [Bake resolution](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Upéct scénu pomocí Azure Batch
Scénu můžete upéct ve výpočetním clusteru v cloudu pomocí služby Azure Batch. Modul plug-in Project Acoustics Unity se připojuje přímo k Azure Batch k vytváření instancí, správy a stržení clusteru Azure Batch pro každé pečení. Na kartě **Pečeme** zadejte svoje přihlašovací údaje Azure, vyberte typ a velikost clusterového počítače a pak vyberte **Pečeme**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Části karty Pečeme
![Karta Akustika pečeme v unity](media/bake-tab-details.png)

1. Tlačítko **Pečeme** zobrazí tuto kartu.
2. Stručný popis toho, co můžete na této stránce udělat.
3. Po vytvoření účtu Azure zadejte do těchto polí svoje přihlašovací údaje Azure. Další informace najdete [v tématu Vytvoření účtu Azure Batch](create-azure-account.md).
4. Pole značky image Dockeru pro sadu nástrojů Akustika.
5. Otevře portál Azure portal pro správu předplatných, sledování využití a zobrazení fakturačních údajů.
6. Určuje typ výpočetního uzlu Azure Batch, který se má použít pro výpočet. Typ uzlu musí být podporován umístěním datového centra Azure. Pokud si nejste jisti, nechte **Standard_F8s_v2**.
7. Počet uzlů, které mají být pro výpočet určeny. Toto číslo ovlivňuje dobu pečení. Je omezena přidělením jádra Azure Batch. Výchozí přidělení umožňuje pouze pro dva uzly s 8 jádry nebo jeden uzel s 16 jádry, ale lze jej rozbalit. Další informace o základních omezeních přidělení najdete [v tématu Vytvoření účtu Azure Batch](create-azure-account.md).
8. Zaškrtnutím tohoto políčka nakonfigurujete výpočetní fond tak, aby používal [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Výpočetní uzly s nízkou prioritou mají mnohem nižší náklady. Ale nemusí být vždy k dispozici, nebo mohou být v pokušení kdykoliv.
9. Počet sondy pro vaši scénu vypočítaný na kartě **Sondy.** Počet sond určuje počet simulací, které mají být spuštěny v cloudu. Nelze zadat více uzlů, než jsou sondy.
10. Odhad doby, po kterou bude spuštění úlohy v cloudu trvat, s výjimkou doby spuštění uzlu. Po spuštění úlohy se v tomto poli zobrazí odhad doby, po kterou se výsledky vrátí.
11. Celkové množství výpočetního času potřebného ke spuštění simulací. Tato hodnota je celkové množství výpočetního času uzlu, který se bude používat v Azure. Další informace najdete [v tématu Odhad nákladů na pečení Azure](#Estimating-bake-cost) dále v tomto článku.
12. Tato zpráva vám řekne, kde budou výsledky pečení uloženy po dokončení úlohy.
13. *(Rozšířené použití pouze:)* Toto tlačítko nutí Unity zapomenout na pečeme, které jste odeslali. Pokud jste například stáhli výsledky pomocí jiného počítače, vyberte tlačítko **Vymazat stav,** chcete-li tuto úlohu zapomenout. Výsledný soubor, pokud je připraven, *nebude* stažen. *To není totéž jako zrušení úlohy. Úloha, pokud běží, bude nadále běžet v cloudu.*
14. Toto tlačítko vyberte, chcete-li odeslat pečit do cloudu. Při spuštění úlohy se toto tlačítko změní na **tlačítko Zrušit úlohu**.
15. Toto tlačítko vyberte, chcete-li se připravit [na simulaci zpracování akustiky v počítači](#Local-bake).
16. Tato oblast zobrazuje stav pečení. Po dokončení pečení se zobrazí "Staženo".

Úplné informace o aktivních úlohách, výpočetních fondech a úložišti můžete vždy získat na [webu Azure Portal](https://portal.azure.com).

Když je úloha spuštěna, změní se popisek tlačítka **Pečeme** na **Zrušit úlohu**. Toto tlačítko slouží ke zrušení probíhající úlohy. Budete vyzváni k potvrzení. Zrušení úlohy nelze vrátit zpět. Když předplatné zrušíte, nebudou k dispozici žádné výsledky, ale za jakýkoli použitý výpočetní čas Azure se vám bude účtovat.

Poté, co jste začali péct, můžete zavřít Unity. V závislosti na projektu, typu uzlu a počtu uzlů může pečení v cloudu trvat několik hodin. Stav úlohy připečení bude aktualizován při opětovném načtení projektu a otevření okna Akustika. Pokud je úloha dokončena, bude stažen výstupní soubor.

Z bezpečnostních důvodů jsou přihlašovací údaje Azure uloženy ve vašem místním počítači a přidruženy k editoru Unity. Používají se jenom k navázání zabezpečeného připojení k Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Najděte stav spuštěné úlohy na webu Azure Portal

1. Na kartě **Pečení** najděte ID pečící úlohy.

    ![ID úlohy pečeme Unity zvýrazněné na kartě Pečeme](media/unity-job-id.png)  

2. Otevřete [portál Azure](https://portal.azure.com), přejděte na účet Batch, který se použil pro pečení, a vyberte **Úlohy**.

    ![Odkaz Úlohy na webu Azure Portal](media/azure-batch-jobs.png)  

3. Vyhledejte ID úlohy v seznamu úloh.

   ![Stav úlohy pečení zvýrazněný na portálu Azure](media/azure-bake-job-status.png)  

4. Výběrem ID úlohy zobrazíte stav souvisejících úkolů a celkový stav úlohy.

   ![Stav úkolu pečení](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Odhad nákladů na pečení Azure

Chcete-li odhadnout, kolik bude pečení stát, začněte s hodnotou **Odhadované výpočetní náklady,** což je doba trvání. Vynásobte tuto hodnotu hodinovými náklady v místní měně pro **vybraný typ uzlu virtuálního** počítače. Všimněte si, že výsledek nebude obsahovat čas uzlu, který je potřeba k zprovoznění uzlů.

Řekněme například, že vyberete **Standard_F8s_v2** pro typ uzlu, který má náklady $0.40/hod. Pokud **odhadované výpočetní náklady** jsou 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy budou $0.40 * ~4 hodiny = ~1,60 USD. Skutečné náklady budou pravděpodobně o něco vyšší, protože čas navíc, aby se uzly začaly.

Najděte hodinové uzly náklady na [azure batch ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Vyberte **Compute optimalizované** nebo **vysoce výkonné výpočetní jako** kategorie.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Upéct scénu na počítači
Můžete také péct scénu na vlastním počítači. Tato metoda může být užitečná pro experimentování s akustikou pro malé scény před vytvořením účtu Azure Batch. Ale všimněte si, že simulace místní akustiky může trvat dlouhou dobu v závislosti na velikosti scény.

### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware
* Procesor x86-64 s alespoň 8 jádry a 32 GB paměti RAM
* [Hyper-V povoleno](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) pro spuštění Dockeru

Jako příklad lze to například při testování na 8jádrovém počítači, Intel Xeon E5-1660 @ 3 GHz a 32 GB paměti RAM:
* Malá scéna se 100 sondami trvala asi 2 hodiny na hrubé pečení nebo 32 hodin pro jemné pečení.
* Středně velká scéna s 1000 sondami trvala asi 20 hodin na hrubé pečení nebo 21 dní na jemné pečení.

### <a name="set-up-docker"></a>Nastavení Dockeru
Instalace a konfigurace Dockeru v počítači, který bude zpracovávat simulaci:
1. Nainstalujte [dockerovou plochu](https://www.docker.com/products/docker-desktop).
2. Otevřete nastavení Dockeru, přejděte na **Upřesnit**a nakonfigurujte prostředky pro alespoň 8 GB paměti RAM. Čím více procesorů můžete přidělit Dockeru, tím rychleji bude pečení dokončeno.  
![Ukázkové nastavení Dockeru](media/docker-settings.png)
1. Přejděte na **Sdílené jednotky**a zapněte sdílení pro jednotku, která se používá ke zpracování.  
![Možnosti sdílené jednotky v Dockeru](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Spusťte místní pečeme
1. Na kartě **Pečení** vyberte tlačítko **Připravit místní pečení.** Potom vyberte umístění složky, do kterých chcete uložit vstupní soubory a skripty spuštění. Potom můžete spustit pečeme na libovolném počítači tak dlouho, dokud splňuje minimální požadavky na hardware a nainstalovat Docker zkopírováním složky do tohoto počítače.
2. Chcete-li spustit simulaci, spusťte skript *runlocalbake.bat* v systému Windows nebo *skript runlocalbake.sh* v systému MacOS. Tento skript načte image Project Acoustics Docker s sadou nástrojů nezbytnou pro zpracování simulace a spustí simulaci.
3. Po dokončení simulace zkopírujte výsledný soubor *Ace* zpět do projektu Unity. Chcete-li se ujistit, že Unity rozpozná jako binární soubor, přidejte ".bytes" k příponě souboru (například "Scene1.ace.bytes"). Podrobné protokoly pro simulaci jsou uloženy v *AcousticsLog.txt.* Pokud narazíte na nějaké problémy, zkontrolujte tento soubor pomoci diagnostikovat problém.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>Datové soubory přidané procesem pečení

Následující čtyři datové soubory jsou vytvořeny během procesu pečení. Jeden obsahuje výsledky simulace a dodává se s vaším titulem. Ostatní ukládat data související s editorem Unity.

Výsledek simulace:
* *Asset/AcousticsData/Acoustics\_[SceneName].ace.bytes*: Tento soubor je vyhledávací tabulka za běhu. Obsahuje výsledky simulace a voxelized akustické scénické prvky. Název a umístění tohoto souboru můžete změnit na kartě **Sondy.**

   *Dávejte pozor, abyste neodstranili soubor výsledků simulace. Není to obnovitelné, kromě toho, že jste místo činu přepečovali.*

Datové soubory editoru:
* *Asset/Editor/[SceneName]\_AcousticsParameters.asset*: Tento soubor ukládá data, která zadáte do polí v uživatelském rozhraní Akustiky. Název a umístění tohoto souboru nelze změnit.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*: Tento soubor ukládá geometrii akustiky voxelized a vlastnosti materiálu, které jsou vypočteny při výběru tlačítka **Vypočítat** na kartě **Sondy.** Název a umístění tohoto souboru můžete změnit na kartě **Sondy.**
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*: Tento soubor ukládá parametry simulace, které jsou vypočteny při výběru **možnosti Vypočítat**. Název a umístění tohoto souboru můžete změnit na kartě **Sondy.**

## <a name="set-up-the-acoustics-lookup-table"></a>Nastavení vyhledávací tabulky akustiky
Přetáhněte panel **Akustika projektu** z panelu projektu do scény:

![Akustika panelák v jednotě](media/acoustics-prefab.png)

Vyberte herní objekt **ProjectAcoustics** a přejděte na jeho panel inspektoru. Zadejte umístění výsledku pečení (soubor Ace v *poli Prostředky/AcousticsData*): Přetáhněte jej do skriptu Správce akustiky nebo vyberte tlačítko kruhu vedle textového pole.

![Akustika Manager panelové v Jednotě](media/acoustics-manager.png)

## <a name="next-steps"></a>Další kroky
* Prozkoumejte [ovládací prvky návrhu Unity](unity-workflow.md).
* Prozkoumejte [koncepty návrhu aplikace Project Acoustics](design-process.md).
