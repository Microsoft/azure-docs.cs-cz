---
title: Projekt Akustika Unity, která má označení vytvoření kurz
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje Akustika pečení s Akustika projektu v Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8875674b0f9c621a573dda591b4dc2b6f018a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790225"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projekt Akustika Unity, která má označení vytvoření kurz
Tento kurz popisuje Akustika pečení s Akustika projektu v Unity.

Požadavky na software:
* [Unity 2018.2 +](http://unity3d.com) pro Windows
* [Modul plug-in Akustika projekt integrované ve vašem Unity projektu](unity-integration.md) nebo [ukázkový obsah projektu Akustika Unity](unity-quickstart.md)
* Volitelné: [Účtu Azure Batch](create-azure-account.md) ke zrychlení vytváří pomocí cloud computingu

## <a name="open-the-project-acoustics-bake-window"></a>Otevřít projekt Akustika vytvoření okna
Zvolte **okna > Akustika** z nabídky Unity:

![Snímek obrazovky z Unity editoru se zvýrazněnou možností nabídky okna Akustika](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Vytvoření sítě navigace
Akustika projekt používá síť navigace umístěte test body naslouchacího procesu pro simulaci. Můžete používat Unity a [pracovního postupu síť navigace](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), nebo použijte jiný balíček 3D modelování návrhu vaší vlastní sítě. 

## <a name="mark-acoustic-scene-objects"></a>Označit objekty akustický scény
Projekt Akustika spoléhá na dva druhy objekty scény pro simulaci: objekty, které budou odrážet a occlude zvuky v simulace a síť player navigace, která omezuje body naslouchacího procesu testu v simulaci. Oba typy objektů jsou označeny pomocí **objekty** kartu. 

Vzhledem k tomu, že jednoduše označení objekty přidá **AcousticsGeometry** nebo **AcousticsNavigation** komponenty na objekt, můžete použít také [standardních Unity součást pracovních postupů](https://docs.unity3d.com/Manual/UsingComponents.html)označit nebo zrušit označení objekty. Může být označen pouze sítě Renderery a terénů. Všechny ostatní typy objekt se bude ignorovat. Zaškrtávací políčka se označit nebo zrušit označení všechny ovlivněné objekty.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Označit akustický geometrie uzavření a reflexe
Otevřít **objekty** karty **Akustika** okna. Označte všechny objekty jako **Akustika geometrie** , by měl occlude, odrážejí, zda vyrovnat se s zvuk. Geometrie Akustika patří takové věci, jako základu stěn, střechy, windows & okno lupy, jiných textilních podlahových krytin a velké nábytek. Můžete použít libovolný libovolného úroveň složitosti pro tyto objekty. Protože je Scéna voxelized před simulace, vysoce podrobné mřížky, jako je například stromové struktury s mnoha malých listy, nejsou dražší než zjednodušené objekty zanést.

Nezahrnují věcí, které by neměla mít vliv Akustika, jako jsou neviditelná kolizí mřížky.

Transformace objektu v okamžiku výpočtu sondy (prostřednictvím **sondy** kartě níže) vyřešen ve výsledcích která má označení vytvoření. Přesunete označené jako objekty na scéně. bude nutné opakování výpočtu testu a rebaking scény.

### <a name="mark-the-navigation-mesh"></a>Označí navigační síť
Navigace mřížek vytvořené pomocí Unity a pracovní postup neexistoval, použije systém Akustika. Pokud chcete použít vlastní OK, neoznačí z **objekty** kartu.

### <a name="for-reference-the-objects-tab-parts"></a>Pro referenci: Karta částí objektů
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

Pokud není nic vybráno ve scéně, na kartě objektů bude vypadat jako na následujícím obrázku:

![Snímek obrazovky Akustika objekty karta se žádný výběr](media/objects-tab-no-selection-detail.png)

Pokud máte něco, co je vybrán v okně scény nebo hierarchii, bude vypadat jako na následujícím obrázku:

![Snímek obrazovky z Akustika objekty karta se výběr uvedený](media/objects-tab-selection-detail.png)

Pokud některé nejsou některé objekty jsou označené, příslušné zaškrtávací políčko se zobrazí hodnotu "hybridní":

![Kartu objekty Akustika snímek obrazovky s ikonou Smíšený výběr zvýrazněnou](media/mixed-object-selection-detail.png)

Kliknutím na zaškrtávací políčko vynutí všechny objekty označit a kliknutím na znovu se zrušit všechny objekty.

Objekty lze označit pro geometrie a navigace.

## <a name="select-acoustic-materials"></a>Vyberte akustický materiály
Jakmile jsou označeny objekty, klikněte na tlačítko **materiály** tlačítko a přiřaďte akustický materiály. Systém projektu Akustika materiálů se váže na Unity visual materiály systému: pro dva objekty mít samostatné akustický materiály, musí mít samostatné visual materiály.

Akustický materiály řízení velikosti šířky zvukové energie projeví zpět z každé povrchu. Výchozí zvukové materiál má absorpční podobný konkrétní. Projekt Akustika navrhuje materiálu založen na názvu visual materiálu. Akustický materiál "Vlastní" můžete přiřadit materiálu k povolení absorpce koeficient posuvník.

Čas reverberation dané materiálů v místnosti nepřímo souvisí jeho pohltivosti většina materiálů s absorpce hodnoty v rozsahu 0.01, 0.20 a novější. Jsou velmi absorpčního materiály s absorpční koeficienty mimo tento rozsah.

![Graf zobrazující negativní korelace reverberation čas, který nabízí pohltivosti](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Pro referenci: Součástí kartě materiály
![Snímek obrazovky Akustika materiály kartu v Unity](media/materials-tab-detail.png)

1. **Materiály** tlačítko kartu, zobrazí tato stránka se používá.
2. Stručný popis co je potřeba provést pomocí této stránky.
3. Pokud je zaškrtnuto, pouze materiály, které používají objekty označen jako **Akustika geometrie** budou uvedeny. V opačném případě budou zobrazeny všechny materiály použité ve scéně.
4. Pomocí těchto možností lze změnit pořadí z rozevírací nabídky, která se zobrazí po kliknutí na rozevírací seznam v sloupci Akustika níže (6). **Název** seřadí akustický materiálů podle názvu. "Absorptivity" je seřadí v pořadí absorptivity z nízké na vysokou.
5. Seznam použitých materiálů ve scéně, seřazená podle abecedy. Pokud **označený jenom zobrazit** zaškrtávací políčko je zaškrtnuto (č. 3), pouze materiály, které používají objekty označený jako **Akustika geometrie** jsou uvedeny. Kliknutím na materiály sem vybere všechny objekty ve scéně, použít tento materiál.
6. Ukazuje akustický materiálu, že byla přiřazena materiálu scény. Klikněte na rozevírací seznam pro změnu přiřazení scény materiál, který různých akustický materiálu. Můžete změnit pořadí řazení v nabídce zobrazené po kliknutí na položku tady **Akustika řadit podle:** možnosti výše (č. 4).
7. Ukazuje akustický pohltivosti materiálu vybrána v předchozím sloupci. Hodnota nula znamená, že nemusíte zajistit dokonalou reflektivní (žádné absorpční), při hodnota 1 znamená, že nemusíte zajistit dokonalou pohlcující (žádné reflexi). Pohltivosti nelze změnit, dokud je vybraný materiál "Vlastní".
8. Pro "Vlastní", posuvník přiřazen materiál již je zakázané a zvolíte pohltivosti pomocí posuvníku nebo pomocí klávesové zkratky v hodnotě.

## <a name="calculate-and-review-listener-probe-locations"></a>Výpočet a zkontrolujte umístění testu naslouchací proces
Po přiřazení materiály, přepněte na **sondy** kartě a klikněte na tlačítko **Calculate** umístěte test body naslouchacího procesu pro simulaci.

### <a name="what-the-calculate-button-calculates"></a>Co se vypočítá tlačítko "Vypočítat..."
**Vypočítat...**  tlačítko používá vaše vybrané akustický scény geometrie připravit vaše Scéna simulace:

1. Přijímá geometrie od scény mřížek a vypočítá voxel svazku. Svazek voxel je 3rozměrné svazku, který obklopuje celý scény a se skládá z malých kubické "voxels". Velikost voxels se určuje podle frekvence simulace, která se nastavuje přes **simulace rozlišení** nastavení. Každý voxel je označen jako buď "Otevřít vzduchu" nebo obsahující geometrie scény. Pokud voxel obsahuje geometrie voxel příznakem s pohltivosti materiálu, který je přiřazen k této geometrie.
2. Použije mesh(es) navigace, které umístí test body naslouchacího procesu. Algoritmus vyrovnává konkurenční obavy prostorových pokrytí a simulaci času a velikost souboru, zatímco je zajištěna, které zúžit chodbách a malé prostory vždy měly určitou míru pokrytí. Typické test bodu počítá rozsahu od 100 pro malé scény do několika tisíc pro velké scény.

V závislosti na velikosti vaší scény a rychlosti vašeho počítače můžete tyto výpočty trvat několik minut.

### <a name="review-voxel-and-probe-placement"></a>Kontrola umístění voxel a kontroly
Zobrazte náhled dat voxel a ujistěte se, že jste připraveni zanést vaše Scéna bod umístění testu. K neúplné navigace sítě nebo chybí nebo je velmi akustický geometrie bude obvykle rychle viditelné ve verzi preview. Voxel a kontroly umístění je možné povolit nebo zakázat pomocí nabídky Gizma:

![Snímek obrazovky Gizma nabídky v Unity](media/gizmos-menu.png)

Voxels obsahující zvukové geometrie se zobrazují jako zelená datové krychle. Prozkoumat vaše Scéna a ověřte, zda vše, co by měl být geometrie voxels. Fotoaparát scény musí být v rámci objektu pro voxels zobrazíte přibližně 5 měřiče.

Pokud srovnáte voxels vytvořené pomocí řešení jemné hrubé řešení vs, zobrazí hrubý voxels jsou dvakrát velké.

![Snímek obrazovky s hrubým voxels ve verzi preview v Unity editoru](media/voxel-cubes-preview.png)

Simulace výsledky jsou interpolovány mezi umístěními bodu naslouchací proces test za běhu. Zkontrolujte bodů testu téměř kdekoli hráč se očekává projít ve scéně.

![Snímek obrazovky s testy ve verzi preview v Unity editoru](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Postará se přejmenuje scény
Název scény se používá pro připojení k uložení umístění bodu testu a voxelization soubory scény. Pokud přejmenování scény po body test paměti se počítá materiálu přiřazení a umístění dat dojde ke ztrátě a by měl spustit znovu.

### <a name="for-reference-parts-of-the-probes-tab"></a>Pro referenci: Na kartě testy částí
![Snímek obrazovky sondy Akustika kartu v Unity](media/probes-tab-detail.png)

1. **Sondy** kartu tlačítko použít zobrazíte na této stránce
2. Stručný popis, co je potřeba provést pomocí této stránky
3. Použijte k výběru řešení hrubý nebo jemné simulace. Hrubé je rychlejší, ale má některé kompromisy. Zobrazit [zanést rozlišení](bake-resolution.md) níže podrobnosti.
4. Vyberte umístění, kde mají být data soubory Akustika umístěny podle tohoto pole. Klikněte na tlačítko s "..." použití ovládacího prvku pro výběr složky. Výchozí hodnota je **prostředky/AcousticsData**. **Editor** podsložku se také vytvoří v tomto umístění. Další informace o datových souborů najdete v tématu [datové soubory](#Data-Files) níže.
5. Datové soubory pro tento scény bude mít název pomocí předpony k dispozici tady. Výchozí hodnota je "Acoustics_ [název scény]".
6. Poté, co byla vypočtena sond, ovládací prvky výše se deaktivuje. Klikněte na tlačítko **vymazat** tlačítko Vymazat výpočty a povolit ovládacích prvků tak, aby znovu vypočte pomocí nového nastavení.
7. Klikněte na tlačítko **vypočítat...**  tlačítko voxelize scény a vypočítat umístění bodů testu. To se provádí místně na svém počítači a je nutné provést před tím, která má označení vytvoření.

V této verzi systému projektu Akustika sondy nemůže být umístěn ručně a musí být umístěné prostřednictvím automatizovaného procesu součástí **sondy** kartu.

Zobrazit [zanést rozlišení](bake-resolution.md) podrobné informace o hrubý vs jemné řešení.

## <a name="bake-your-scene-using-azure-batch"></a>Vytvoření vašeho scény pomocí služby Azure Batch
Můžete zanést vaše Scéna s výpočetní cluster v cloudu pomocí služby Azure Batch. Modul plug-in Unity Akustika projekt připojuje přímo k Azure Batch k vytvoření instance, spravovat a dovolí pro každý která má označení vytvoření clusteru služby Azure Batch. Na **zanést** kartu, zadejte přihlašovací údaje Azure, vyberte typ počítače clusteru a velikost a klikněte na tlačítko **zanést**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Pro referenci: Částí, která má označení vytvoření karty
![Snímek obrazovky vytvoření Akustika kartu v Unity](media/bake-tab-details.png)

1. Vytvoření karty tlačítka sloužícího k otevřete tuto stránku.
2. Stručný popis toho, jak provést na této stránce.
3. Pole k zadání přihlašovacích údajů Azure po vytvoření účtu Azure. Další informace najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
4. Značce image dockeru pro Akustika sadu nástrojů.
5. Spuštění webu Azure portal ke správě vašich předplatných, sledování využití a zobrazit informace o fakturaci atd. 
6. Služba Azure batch výpočetní uzel typ, který se použije k výpočtu. Typ uzlu musí podporovat vaše umístěním Azure datového centra. Pokud nejste si jistí, ponechte **Standard_F8s_v2**.
7. Počet uzlů pro tento výpočet. Číslo, které tady zadáte ovlivňuje dobu pro dokončení která má označení vytvoření a přidělení základní služby Azure Batch, je omezen. Výchozí přidělení pouze umožňuje uzly dva 8 jader nebo uzlu jeden 16 jader, ale můžete rozšířit. Další informace o omezení přidělení core najdete v tématu [vytvořit účet Azure Batch](create-azure-account.md).
8. Zaškrtnutím tohoto políčka konfigurovat fond výpočetních používat [uzly s nízkou prioritou](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Uzlů s nízkou prioritou mají mnohem nižší náklady, ale nemusí být vždy k dispozici nebo může dojít ke zrušení kdykoli.
9. Počet sondy pro vaše Scéna spočítané na **sondy** kartu. Počet sond určuje počet simulace, které je nutné spustit v cloudu. Nelze zadat více uzlů, než jsou testy.
10. Množství uplynulého času, který očekává se, abyste mohli pro vaše úlohy určené ke spuštění v cloudu. To nezahrnuje čas spuštění uzlu. Po spuštění úlohy, jde o tom, jak dlouho by mělo být předtím, než se dostanete zpět výsledky. Všimněte si, že se jedná pouze o odhad.
11. Celkové množství výpočetního času potřebné ke spuštění simulace. Toto je celková velikost uzlu výpočetního času, který se použije v Azure. Zobrazit [odhadování nákladů která má označení vytvoření](#Estimating-bake-cost) níže pro další informace o použití této hodnoty.
12. Tato zpráva znamená, kam uložit výsledky která má označení vytvoření po dokončení úlohy.
13. (Pouze pro pokročilé uživatele) Pokud z nějakého důvodu potřebujete vynutit Unity nemusíte zabývat která má označení vytvoření odeslán (třeba jste si stáhli výsledky použití jiného počítače), klikněte na tlačítko **Vymazat stav** tlačítko zapomenout o úloze, která byla odeslána. Všimněte si, že to znamená, že výsledného souboru, až budete připraveni, bude **není** stáhli, a **to není stejné jako zrušení úlohy**. Úlohy, pokud spuštěný, bude nadále spouštět v cloudu.
14. Klikněte na tlačítko **zanést** tlačítko k odeslání, která má označení vytvoření do cloudu. Když úloha běží, zobrazí se **zrušit úlohu** místo.
15. Připraví ke zpracování [Akustika simulace ve vašem počítači](#Local-bake).
16. Tato oblast se zobrazuje stav která má označení vytvoření. Po dokončení, měl by se zobrazit **stažené**.

Vždy můžete získat podrobnější informace o aktivních úloh, fondů výpočetních a úložiště na webu [webu Azure portal](https://portal.azure.com).

Když úloha běží **zanést** tlačítko se změní na **zrušit úlohu**. Pomocí tohoto tlačítka můžete zrušit probíhající úlohu. Zobrazí se výzva k potvrzení předtím, než dojde ke zrušení úlohy. Zrušení úlohy nejde vrátit zpět, nebudou k dispozici žádné výsledky a vám bude nadále účtovat pro použití jakékoli výpočetní prostředky Azure.

Po zahájení která má označení vytvoření, můžete zavřít Unity. V závislosti na projektu, typ a počet uzlů která má označení vytvoření cloudu může trvat několik hodin. Stav úlohy která má označení vytvoření aktualizuje při načtení projektu a otevřete okno Akustika. Pokud se úloha dokončí, stáhnou se výstupní soubor.

Přihlašovací údaje Azure jsou bezpečně uložené na místním počítači a přidružené Unity editor. Slouží pouze k navázat zabezpečené připojení k Azure.

### <a name="Estimating-bake-cost"></a> Provádí se odhad nákladů Azure která má označení vytvoření

Pokud chcete odhadnout, co bude daný která má označení vytvoření nákladů, přijmout hodnoty zobrazené pro **odhadované náklady na výpočetní**, což je dobu trvání a vícenásobně ve každou hodinu nákladů ve vaší místní měně **typ uzlu virtuálního počítače** jste vybrali. Výsledek nebude obsahovat uzel době potřebné ke zprovoznění uzly a spouštění. Pokud vyberete třeba **Standard_F8s_v2** typu uzlu, který má náklady 0,40 $/ hod a odhadované náklady na výpočetní je 3 hodiny a 57 minut, odhadované náklady na spuštění úlohy bude $0,40 * ~ 4 hodin = ~ $1.60. Skutečné náklady se pravděpodobně o něco vyšší kvůli čas navíc k získání uzly spuštěna. Hodinové náklady na uzel můžete najít [ceny služby Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) stránky (vyberte "optimalizováno pro výpočty" nebo "vysokovýkonné výpočetní prostředí" kategorie).

## <a name="Local-bake"></a> Vytvoření vaše Scéna ve vašem počítači
Vaše scény můžete zanést na vlastním počítači. To může být užitečné pro experimentování s Akustika s malé scény před vytvořením účtu Azure Batch. Poznámka: simulace Akustika může trvat dlouhou dobu v závislosti na velikosti od scény.

### <a name="minimum-hardware-requirements"></a>Minimální požadavky na hardware
* Procesor x86 – x 64 s alespoň s 8 jádry a 32 GB paměti RAM

Jako příklad najdete v našich testech na počítači s technologií Intel Xeon E5-1660 8 jader @ 3 GHz a 32 GB paměti RAM-
* Malé scény s 100 testů může trvat přibližně 2 hodin pro hrubý která má označení vytvoření nebo 32 hodin pro jemné která má označení vytvoření.
* Střední scény s 1 000 testů může trvat asi 20 hodin pro hrubý která má označení vytvoření nebo jemné která má označení vytvoření na 21 dnů.

### <a name="setup-docker"></a>Instalace Dockeru
Instalace a konfigurace Dockeru na počítači, který bude zpracovávat simulace-
1. Nainstalujte [nástrojů Dockeru](https://www.docker.com/products/docker-desktop).
2. Spusťte nastavení Dockeru, přejděte na možnosti "Pokročilé" a konfigurovat prostředky mít aspoň 8GB paměti RAM. Více procesorů může přidělit Dockeru, tím rychleji která má označení vytvoření dokončí. ![Snímek obrazovky s příklady nastavení Dockeru](media/docker-settings.png)
3. Přejděte na "Sdílené jednotky" a zapněte sdílení má jednotka použitá ke zpracování.![Možnosti snímku obrazovky Dockeru sdílené jednotky](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Spusťte místní která má označení vytvoření
1. Klikněte na tlačítko "Příprava místní zanést" na **zanést** kartě a vyberte složku, kam bude uložena vstupních souborů a provádění skriptů. Poté lze která má označení vytvoření na jakýkoli počítač splňuje minimální hardwarové požadavky a pokud je Docker nainstalovaný tak, že zkopírujete složku do tohoto počítače.
2. Spuštění simulace použití skriptu "runlocalbake.bat". Tento skript se image Dockeru Akustika projekt pomocí sady nástrojů, které jsou nezbytné pro simulaci zpracování načte a spustí simulace. 
3. Jakmile se simulace dokončí, zkopírujte výsledný soubor .ace zpět na vašem Unity projektu. Pokud chcete mít jistotu, že Unity to rozpozná jako binárního souboru, připojte k rozšíření souboru (například "Scene1.ace.bytes") ".bytes". Podrobné protokoly pro simulaci se ukládají v "AcousticsLog.txt." Pokud narazíte na nějaké problémy, sdílejte tento soubor jako pomoc při diagnóze.

## <a name="Data-Files"></a> Datové soubory přidány procesem, která má označení vytvoření

Existují čtyři datové soubory vytvořené během procesu která má označení vytvoření. Jeden obsahuje výsledky simulace a dodává se svým názvem. Ostatní ukládat data související s Unity Editor.

Simulace výsledek:
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes**: Toto je vyhledávací tabulce modulu runtime a obsahuje výsledky simulace a prvky voxelized akustický scény. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.

Je třeba dbát na soubor s výsledky simulace. To nebude v obnovitelných s výjimkou rebaking scény.

Editor datových souborů:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Tento soubor uchovává data, která zadáte do polí v Uživatelském rozhraní Akustika. Umístění a název tohoto souboru nelze změnit.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Tento soubor uchovává geometrie Akustika voxelized a vlastností materiálu, které jsou vypočítány pomocí **Calculate...**  tlačítko na kartě testy. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.
* **Prostředky/AcousticsData/Editor/Akustika\_[SceneName]\_config.xml**: Tento soubor uchovává vypočítán s použitím parametrů simulace **Calculate...**  tlačítko **sondy** kartu. Umístění a název tohoto souboru lze změnit pomocí pole na **sondy** kartu.

## <a name="set-up-the-acoustics-lookup-table"></a>Nastavit Akustika vyhledávací tabulky
Přetáhnout myší **projektu Akustika** prefab z panelu Projekt do vaše Scéna:

![Snímek obrazovky Akustika prefab v Unity](media/acoustics-prefab.png)

Klikněte na **ProjectAcoustics** hru objektu a přejděte do jeho inspector panelů. Zadejte umístění, která má označení vytvoření výsledků (). ACE souboru, v **prostředky/AcousticsData**) pádem a přetáhněte ji do skriptu Akustika správce, nebo kliknutím na tlačítko kroužek vedle textového pole.

![Snímek obrazovky Správce Akustika prefab v Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Další postup
* Prozkoumejte [návrh ovládacích prvků pro Unity](unity-workflow.md)
* Prozkoumejte [konceptů návrhu Akustika projektu](design-process.md)

