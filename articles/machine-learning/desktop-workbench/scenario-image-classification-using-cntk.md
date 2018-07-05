---
title: Využitím CNTK v aplikaci Azure Machine Learning Workbench klasifikace obrázků | Dokumentace Microsoftu
description: Trénování, vyhodnocení a nasazení modelu klasifikace vlastní image pomocí aplikace Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 48c21638fe5756e6527288ed0fdc73dd9e331afd
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "35622214"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Klasifikace obrázků s využitím Azure Machine Learning Workbench

Přístupy klasifikace Image je možné vyřešit velké množství problémů pro počítačové zpracování obrazu.
Mezi ně patří vytváření modelů, které odpovědi na otázky jako: *je k dispozici v bitové kopii objektu?* kde OBJEKT může být například *pes*, *car*, nebo  *dodávejte*. Nebo složitější otázky typu: *jaké třída závažnosti stádiu oka je evinced podle této pacienta retinal kontroly?*.

Tento kurz adresy řešení těchto problémů. Vám ukážeme, jak pro trénování, vyhodnocení a nasazení vlastní image klasifikace modelu pomocí [Microsoft Cognitive Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) pro hloubkové učení.
Příklad obrázky jsou k dispozici, ale čtečka můžete také přinést své vlastní datové sady a trénování vlastních modelů vlastní.

Počítačové zpracování obrazu řešení obvykle vyžaduje odborných znalostí ručně identifikovat a implementovat takzvané *funkce*, který zvýraznit požadované informace na obrázcích.
Změnil se tento Ruční postup 2012 se známý [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] pro hloubkové učení papír a v současné době, hluboké Neuronové sítě (DNN) se používají k vyhledání automaticky tyto funkce.
Dopředné vedla k obrovské vylepšení v poli, nikoli pouze pro klasifikace obrázků, ale také pro jiné pro počítačové zpracování obrazu problémy, jako je zjišťování objektů a image podobnosti.


## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Přehled

V tomto kurzu se dělí do tří částí:

- 1. část ukazuje, jak pro trénování, vyhodnocení a nasazení bitové kopie systému klasifikace pomocí předem vytrénovaných DNN jako featurizer a školení SVM na jeho výstup.
- Část 2 pak ukazuje, jak zlepšit přesnost, například upřesnění DNN spíše než ho použijete jako dlouhodobý featurizer.
- 3. část popisuje, jak použít vlastní datovou sadu místo imagí poskytnutý příklad a v případě potřeby, jak vytvořit vlastní datové sady pomocí automatizované získávání dat obrázky ze sítě.

Zatímco předchozí zkušenosti s machine learning a CNTK není vyžadována, je užitečné k pochopení základních zásad. Přesnost čísla, školení čas atd. v tomto kurzu jsou pouze pro referenci a skutečných hodnot při spouštění kódu téměř jistě lišit.


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

1. [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
2. [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Stručná Instalační příručka](../service/quickstart-installation.md) k instalaci a vytvoření pracovního prostoru.  
3. Počítače s Windows. Operační systém Windows je nezbytné, protože v aplikaci Workbench podporuje pouze Windows a MacOS při společnosti Microsoft Cognitive Toolkit (který použijeme jako knihovna obsáhlého learningu) podporuje pouze Windows a Linux.
4. Vyhrazené GPU se nevyžaduje ke spuštění trénovací SVM v části 1, ale je potřeba pro upřesnění sady DNN je popsáno v části 2. Pokud nemají silné GPU, má tak moct trénovat na více GPU nebo nemáte počítače s Windows, zvažte použití operačního systému Windows Azure pro virtuální počítač pro hloubkové učení. Zobrazit [tady](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) pro Průvodce nasazením 1 jedním kliknutím. Po nasazení, připojte se k virtuálnímu počítači pomocí připojení ke vzdálené ploše, existuje aplikaci Workbench můžete nainstalovat a místně spusťte kód z virtuálního počítače.
5. Různé knihovny jazyka Python, jako je například OpenCV potřeba nainstalovat. Klikněte na tlačítko *otevřít příkazový řádek* z *souboru* nabídky v aplikaci Workbench a spusťte následující příkazy pro instalaci těchto závislostí:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` Po stažení OpenCV kolečka z http://www.lfd.uci.edu/~gohlke/pythonlibs/ (přesným názvem a verzí můžete změnit)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Řešení potíží / známých chyb
- Grafického procesoru je potřeba pro 2. část a jinak "Normalizace školení na procesoru není dosud implementována Batch" je vyvolána chyba při pokusu o Upřesnit DNN.
- Chyby na více instancí z důvodu nedostatku paměti při školení DNN se můžete vyhnout tím, že snižuje velikost minibatch (proměnná `cntk_mb_size` v `PARAMETERS.py`).
- Kódu byl testován pomocí CNTK 2.2 a by měl také běží na starší (až do verze 2.0) a novější verze bez nebo pouze malé změny.
- Azure Machine Learning Workbench v době psaní měla problémy s zobrazující poznámkových bloků větší než 5 MB k dispozici. Poznámkové bloky této velké velikosti může dojít, pokud Poznámkový blok je uložen pod všechny buňky výstup zobrazený. Pokud k této chybě dojde, otevřete příkazový řádek aplikaci Workbench v nabídce Soubor, spustit `jupyter notebook`, otevřete Poznámkový blok, vymazat veškerý výstup a uložit poznámkového bloku. Po provedení těchto kroků, poznámkového bloku se správně v aplikaci Azure Machine Learning Workbench znovu otevřete.
- Všechny skripty, které jsou k dispozici v této ukázce musí být spuštěn lokálně a ne na například vzdáleném prostředí dockeru. Všech poznámkových bloků je potřeba spustit s jádra nastavena na jádru místní projekt s názvem "PROJECTNAME místní" (například "myImgClassUsingCNTK místní").

    
## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace workbench

Chcete-li vytvořit nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete aplikaci Azure Machine Learning Workbench.
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**.
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt.
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Obrázku klasifikaci" a vyberte šablonu.
5.  Klikněte na možnost **Vytvořit**.

Provádějící tyto kroky vytvoří strukturu projektu je uvedeno níže. Adresář projektu je omezen na být kratší než 25 MB k dispozici, protože aplikace Azure Machine Learning Workbench se vytvoří kopie této složky po každém spuštění (aby historie spuštění). Proto všechny image a dočasné soubory se uloží do a z adresáře *~/Desktop/imgClassificationUsingCntk_data* (označované jako *DATA_DIR* v tomto dokumentu).

  Složka| Popis
  ---|---
  aml_config/|                           Adresáře, který obsahuje konfigurační soubory aplikace Azure Machine Learning Workbench
  knihovny /|                              Adresáře, který obsahuje všechny Python a Jupyter pomocné funkce
  poznámkové bloky /|                              Adresáře, který obsahuje všechny poznámkové bloky
  prostředky /|                              Adresáře, který obsahuje všechny prostředky (pro příklad adresy url obrázků módy)
  skripty /|                              Adresáře, který obsahuje všechny skripty
  PARAMETERS.py|                       Zadání všech parametrů skriptu Pythonu
  Readme.MD|                           Tento dokument readme


## <a name="data-description"></a>Popis dat

Tento kurz používá jako příklad spuštěn dataset textury oblečení horní tělo skládající se z až 428 imagí. Každé image je označena jako jeden ze tří různých textury (tečkovaná, prokládané leopard). Jsme udržováno počet obrázků malé tak, aby v tomto kurzu lze rychle spustit. Kód je však dobře otestovaný a funguje s desítkami tisíc obrázků nebo více. Všechny bitové kopie byly získaný pomocí Bingu pro vyhledávání obrázků a označena popsaná v dolním [část 3](#using-a-custom-dataset). Obrázek adresy URL s jejich příslušných atributů jsou uvedené v */resources/fashionTextureUrls.tsv* souboru.

Skript `0_downloadData.py` stáhne všechny bitové kopie do *DATA_DIR/Image/fashionTexture/* adresáře. Některé z 428 adresy URL jsou pravděpodobně poškozený. Není problém a to pouze znamená, že máme o něco méně bitových kopií pro trénování a testování. Všechny skripty, které jsou k dispozici v této ukázce musí být spuštěn lokálně a ne na například vzdáleném prostředí dockeru.

Následující obrázek znázorňuje příklady pro atributy (vlevo), tečkami prokládané (střední) a nejnovější verzí leopard (vpravo). Poznámky se provést podle položky oblečení horní textu.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Část 1 – Model trénování a hodnocení

V první části tohoto kurzu jsme se školení systému, který používá, ale neprovede žádné změny, předem natrénovaných hluboké neuronové sítě. Tato předem vytrénovaných DNN slouží jako featurizer a lineární SVM se trénuje předpovědět atribut (tečkované, prokládaný, nebo nejnovější verzí leopard) danou Image.

Nyní jsme popsané tento přístup v krok za krokem, podrobnosti a zobrazit skripty, které je potřeba spustit. Doporučujeme zkontrolovat soubory, které se zapisují a ve kterém jsou zapsány do po provedení každého kroku.

Jsou zadány všechny důležité parametry a s krátkým vysvětlením zadaná na jednom místě: `PARAMETERS.py` souboru.




### <a name="step-1-data-preparation"></a>Krok 1: Příprava dat
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Poznámkový blok `showImages.ipynb` lze použít k vizualizaci imagí a opravte jejich anotace podle potřeby. Poznámkový blok spustit, otevřete ho v aplikaci Azure Machine Learning Workbench, klikněte na "spustit Server poznámkového bloku" Pokud tato možnost se zobrazí, změňte na jádru místní projekt s názvem "PROJECTNAME místní" (například "myImgClassUsingCNTK místní"), a pak proveďte všechny buňky v Poznámkový blok. Pokud dojde k chybě stěžovali Poznámkový blok je příliš velký, který se má zobrazit, naleznete v části řešení problémů v tomto dokumentu.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Nyní spusťte tento skript s názvem `1_prepareData.py`, které přiřadí všechny bitové kopie buď školení nastavení nebo nastavení testu. Toto přiřazení se vzájemně vylučuje - žádný obrázek školení se také používá pro účely testování nebo naopak. Ve výchozím nastavení náhodných 75 % imagí z každého atributu třídy jsou přiřazeny k trénování a zbývajících 25 % jsou přiřazeny k testování. Generovaný skript všechna data se ukládají do *DATA_DIR/proc/fashionTexture/* složky.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Krok 2: Úprava hluboké Neuronové sítě
`Script: 2_refineDNN.py`

Jak jsme je popsáno v části 1 tohoto kurzu, předem natrénovaných DNN zůstane pevné (to znamená, že není kontrast). Ale skript s názvem `2_refineDNN.py` stále provádí v části 1, během načítání předem vytrénovaných [modelem ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] modelu a změní ho, například umožňuje vyšší rozlišení vstupního obrázku. Tento krok je rychlé (v sekundách) a nevyžaduje grafického procesoru.

V části 2 tohoto kurzu, úpravy PARAMETERS.py soubor způsobí, že `2_refineDNN.py` skript, který také upřesnit předem vytrénovaných DNN. Ve výchozím nastavení zajišťuje každodenní provoz 45 školení epoch během vylepšení.

V obou případech finálního modelu jsou následně zapsána do souboru *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Krok 3: Vyhodnocení DNN všechny Image
`Script: 3_runDNN.py`

Můžete teď používáme (pravděpodobně kontrast) DNN z posledního kroku k vytrénovaných naše Image. Zadaný obrázek jako vstup DNN, výstup je 512 float vektoru z nejnovější vrstvy modelu. Tento problém je jednorozměrné mnohem menší než samotné. Nicméně ji by měl obsahovat (a dokonce zvýraznit) všechny informace relevantní pro rozpoznat na obrázku atribut, který, pokud má položka oblečení tečkovaná, rozdělena, obrázku nebo textury leopard.

Všechny reprezentace DNN bitové kopie se uloží do souboru *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Krok 4: Support Vector Machine školení
`Script: 4_trainSVM.py`

512float reprezentace počítaný v předchozím kroku, se teď používají k natrénování SVM třídění: Zadaný obrázek jako vstup, výstup SVM skóre pro každý atribut bude k dispozici. V naší datové sadě příkladu to znamená, že skóre pro "prokládané" pro "tečkovaná" a "leopard".

Skript `4_trainSVM.py` načte trénovacích obrázků, trénovat SVM pro různé hodnoty parametru regularizace (slack) C a udržuje SVM s nejvyšší přesností. Přesnost klasifikace je vytisknout na konzole a vykreslit v aplikaci Workbench. Pro data zadaná textury tyto hodnoty by měly tvořit přibližně 100 % a 88 % v uvedeném pořadí. Nakonec je trénovaného SVM zapsána do souboru *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Krok 5: Testování a vizualizace
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Přesnost třídění trénovaného image lze změřit pomocí skriptu `5_evaluate.py`. Skript skóre všechny Image testu pomocí trénovaného SVM třídění, přiřadí každý obrázek atribut s nejvyšším skóre a porovná předpokládané atributy s poznámkami pravdy základu.

Výstup skriptu `5_evaluate.py` je uveden níže. Přesnost klasifikace každé jednotlivé třídy je stejně jako přesnost pro celý test set ("celkové přesnost") a průměr vypočítaný přes jednotlivé přesností ("celkové zprůměrované třídy přesnost"). 100 % odpovídá nejlepší možné přesnost a jako nejhorší % 0. Náhodné opakovaně uhodnout v průměru vyprodukuje zprůměrované třídy přesnost 1 za počet atributů: v našem případě by tato přesnost 33,33 %. Tyto výsledky se výrazně zvýšit při použití, jako vyšší vstupního rozlišení `rf_inputResoluton = 1000`, ale za cenu delší dobu výpočtu DNN.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Kromě přesnost je křivka roc s více TŘÍDAMI vykreslují příslušné oblasti – v části křivky (vlevo); a je zobrazena chybová matice (vpravo):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Nakonec Poznámkový blok `showResults.py` je k dispozici pro procházení obrázků testu a vizualizaci skóre jejich příslušné klasifikace. Jak je vysvětleno v následující kroky 1, musí používat místní projekt jádra s názvem "PROJECTNAME místní" každý poznámkového bloku v této ukázce:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Krok 6: nasazení
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Trénované systému můžete teď publikovat jako rozhraní REST API. Nasazení je vysvětleno v poznámkovém bloku `deploy.ipynb`a podle funkcí v rámci Azure Machine Learning Workbench (Nezapomeňte nastavit jako jádra jádra místní projekt s názvem "Místní PROJECTNAME"). Viz také část vynikající nasazení [IRIS kurzu](tutorial-classifying-iris-part-3.md) nasazení další související informace.

Po nasazení webové služby je možné volat pomocí skriptu `6_callWebservice.py`. Všimněte si, že IP adresy (buď místní nebo v cloudu) webové služby musí být nastavena ve skriptu. Poznámkový blok `deploy.ipynb` vysvětluje, jak zjistit tuto IP adresu.








## <a name="part-2---accuracy-improvements"></a>Část 2 – vylepšení přesnost

V části 1 jsme vám ukázali jak klasifikovat bitovou kopii pomocí školení lineární Support Vector Machine na 512 float výstup hluboké Neuronové sítě. Tato DNN byl předem vytrénovaných miliony bitové kopie a vrátí nejnovější vrstvy jako funkce vector. Tento přístup je rychlý, protože DNN slouží jako-se, ale přesto často poskytuje dobré výsledky.

Nyní Představujeme několik způsobů, jak zlepšit přesnost modelu z část 1. Zejména jsme Upřesnit DNN spíše než udržování problém odstranil.

### <a name="dnn-refinement"></a>Vylepšení DNN

Místo SVM jeden provést zařazení přímo v neuronové sítě. Toho dosáhnete tak, že přidáte novou vrstvu poslední předem vytrénovaných DNN, která přebírá hodnot float 512 z nejnovější vrstvy jako vstup. Výhodou provádění zařazení DNN je, že teď může být retrained plné síti pomocí zpětné šíření chyb. Tento přístup se často vede k mnohem lepší přesností klasifikace ve srovnání s použitím předem vytrénovaných DNN jako-se, ale za cenu mnohem delší dobu školení (i s GPU).

Cvičení Neuronové sítě místo SVM se provádí tak, že změníte proměnné `classifier` v `PARAMETERS.py` z `svm` k `dnn`. Jak je popsáno v části 1, všech skriptech s výjimkou přípravu dat (krok 1) a školení SVM (krok 4) musíte provést znovu. Vylepšení DNN vyžaduje grafického procesoru. Pokud nebyla nalezena žádná GPU nebo GPU je uzamčena (třeba podle předchozího spuštění CNTK) pak skript `2_refineDNN.py` vyvolá chybu. DNN školení může vyvolat chyby na více instancí z důvodu nedostatku paměti na některé GPU, které můžete se vyhnout snížením velikosti minibatch (proměnná `cntk_mb_size` v `PARAMETERS.py`).

Po dokončení školení kontrast modelu je uložen do *DATA_DIR/proc/fashionTexture/cntk_refined.model*, a vykreslení vykreslit, který ukazuje, jak změnit klasifikace chyby trénování a testování během cvičení. Poznámka: v této diagram, který v chybové zprávě na cvičnou sadou je mnohem menší než u sady testů. Toto chování takzvané over-pass-the těsně můžete snížit, například pomocí vyšší hodnotu pro kurz Odpadlík `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Jak je vidět v následujícím diagramu, je přesnost pomocí DNN vylaďování pro zadaný datovou sadu 92.35 % a % 88.92 před (část 1). Zejména "tečkovaná" imagí výrazné zlepšení, se oblasti – v části křivka ROC z 0,98 s vylaďování vs. 0.94 před. Používáme malé datové sady, a proto se liší skutečné přesností spuštění kódu. Tento rozdíl je z důvodu stochastického efekty, jako je například náhodné rozdělení bitové kopie do trénování a testování sad.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Sledování historie spuštění

Úložiště Azure Machine Learning Workbench historie každého spuštění v Azure umožňuje porovnání dvou nebo více spuštění, které jsou i týdnů od sebe. To je podrobně popsaný v části [Iris kurzu](tutorial-classifying-iris-part-2.md). Je také znázorněno na následujících snímcích obrazovky, kde jsme porovnat dvě spuštění skriptu `5_evaluate.py`, pomocí obou DNN vylaďování tedy `classifier = "dnn"`(spuštění číslo 148) nebo SVM vzdělávání, to znamená, `classifier = "svm"` (spuštění číslo 150).

V první snímek obrazovky vylepšení DNN vede k vyšší přesností než SVM školení pro všechny třídy. Druhý snímek obrazovky ukazuje všechny metriky, které jsou sledovány, včetně toho, co byl třídění. Toto sledování se provádí ve skriptu `5_evaluate.py` voláním protokolovacího nástroje Azure Machine Learning Workbench. Kromě toho tento skript také uloží matici, aby nejasnostem a křivky roc s více TŘÍDAMI *výstupy* složky. To *výstupy* složka je speciální, jeho obsah je také sledován pomocí funkce historie aplikace Workbench a proto výstupní soubory je přístupná v okamžiku, bez ohledu na to, zda máte místní kopie přepsány.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Optimalizace parametrů

Stejně jako u většiny strojového učení projekty, získávání dobré výsledky pro nová datová sada vyžaduje pečlivé parametr ladění, stejně jako vaše rozhodnutí vyzkoušet různé rozhodnutí. Abyste s těmito úkoly, jsou zadány všechny důležité parametry a s krátkým vysvětlením zadaná na jednom místě: `PARAMETERS.py` souboru.

Mezi největší slibně cesty vedoucí k vylepšení patří:

- Kvalita dat: zajistit vysoce kvalitní měly učení a testovací sady. To znamená, bitové kopie jsou správně s poznámkami, nejednoznačný Image odebrány (například oblečení položky s pruhy a tečky) a atributy se vzájemně vylučují (to znamená, zvolili tak, aby každá image patří do právě jeden atribut).

- Pokud je objekt-vás zajímají malý obrázek přístupy klasifikace obrázků známé nechcete pracovat dobře. V takových případech zvažte použití přístupu objektu zjišťování, jak je popsáno v tomto [kurzu](https://github.com/Azure/ObjectDetectionUsingCntk).
- Vylepšení DNN: pravděpodobně nejdůležitější parametr, chcete-li získat správný je rychlost učení `rf_lrPerMb`. Pokud přesnost na školení nastavit (první obrázek v 2. část) se nenachází v blízkosti 0 – 5 %, pravděpodobně je z důvodu nesprávnou rychlost učení. Další parametry spuštění s `rf_` méně důležité. Chyba školení by měl obvykle snížení exponenciálně a být blízko 0 % po školení.

- Vstupní rozlišení: výchozí rozlišení obrázku je 224 x 224 pixelů. Použitím vyšší rozlišení obrázku (parametr: `rf_inputResoluton`), například 448 x 448 nebo 896 x 896 pixelů často významné zvyšují přesnost, ale může zpomalit DNN vylepšení. **Použití vyšší rozlišení obrázku je téměř má svou cenu a téměř vždy posiluje přesnost**.

- Útoky over-pass-the přizpůsobování DNN: vyhnout velké mezery mezi trénovací a testovací přesnost během vylaďování DNN (první obrázek v 2. část). Překonání tohoto rozdílu lze snížit pomocí kurzů Odpadlík `rf_dropoutRate` 0,5 nebo víc a zvýšením váha regularizer `rf_l2RegWeight`. Použití vysoké Odpadlík sazba může být užitečné, pokud je vysoké rozlišení vstupního obrázku DNN.

- Zkuste použít podrobnější dopředné změnou `rf_pretrainedModelFilename` z `ResNet_18.model` buď `ResNet_34.model` nebo `ResNet_50.model`. Model modelem Resnet-50 není pouze hlubší, ale výstup předposlední vrstvy je velikost 2048 float (vs. 512 float modelů modelem ResNet-18 a modelem ResNet-34). Tato vyšší dimenze může být zvlášť užitečné, když školení SVM třídění.

## <a name="part-3---custom-dataset"></a>Část 3 – vlastní datové sady

V části 1 a 2 jsme trénují a vyhodnocují model klasifikace obrázků pomocí imagí zadaná horní hlavní oblečení textury. Nyní vám ukážeme, jak místo toho použít vlastní uživatelské datové sady. Nebo, pokud není k dispozici, jak vygenerovat a opatřit poznámkami, například datové sady pomocí Bingu Image hledání.

### <a name="using-a-custom-dataset"></a>Použití vlastní datové sady

Nejprve dopřejeme si pohled na strukturu složek pro data oblečení textury. Všimněte si, jak všechny Image pro různé atributy jsou v příslušné podsložky *tečkovaná*, * leopard, a *prokládané* na *DATA_DIR/Image/fashionTexture/*. Všimněte si také, jak název složky image také probíhá `PARAMETERS.py` souboru:
```python
datasetName = "fashionTexture"
```

Použití vlastní datové sady je snadné – stačí reprodukce tuto strukturu složek kde jsou všechny Image v podsložkách podle jejich atribut a zkopírovat tyto podsložky do nového adresáře zadaného uživatelem *DATA_DIR/Image/newDataSetName/*. Pouze vyžadována změna kódu je nastavit `datasetName` proměnnou *newDataSetName*. Skripty 1-5, může pak provést v pořadí, a všechny mezilehlé soubory jsou zapsány do *DATA_DIR/proc/newDataSetName/*. Nejsou potřeba žádné další změny kódu.

Je důležité, že každé image je možné přiřadit na přesně jeden atribut. Například by být nesprávné se atributy pro "zvířat" a 'leopard', protože bitové kopie "leopard" by také patřit do "zvířat". Je také vhodné odebrat bitové kopie, které je nejednoznačný a je proto obtížné opatřovat je poznámkami.



### <a name="image-scraping-and-annotation"></a>Automatizované získávání dat bitové kopie a poznámky

Shromažďování dostatečně velký počet imagí s poznámkami pro trénování a testování může být obtížné. Jedním způsobem, jak tento problém vyřešit, je scrape imagí z Internetu. Například níže jsou uvedeny výsledky vyhledávání obrázků Bingu dotazu *trička prokládané*. Podle očekávání, většina imagí ve skutečnosti jsou rozdělené trička. Několik nesprávný nebo je nejednoznačný obrázků (jako je například sloupec 1, 1; řádek nebo sloupec 3, řádek 2) můžete identifikovat a snadno odebrat:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Pokud chcete generovat velké a různorodé datové sady, by měla sloužit více dotazů. Například 7\*dotazy můžete syntetizovat automaticky všechny kombinace oblečení položky {halenka, hoodie, pulovru, svetr, tričko, tričko, vestě} a atributy {prokládaný, tečkovaná, leopard} pomocí 3 = 21. Stahování imagí prvních 50 každý dotaz by vést k maximálně 21 * 50 = 1050 bitové kopie.

Místo ručního stahování imagí z Bingu pro vyhledávání obrázků, je mnohem jednodušší použít namísto toho [API vyhledávání obrázků Bingu služeb Cognitive Services](https://www.microsoft.com/cognitive-services/bing-image-search-api) který vrátí sadu zadaný řetězec dotazu adresy URL obrázků.

Některé z stažené obrázky jsou přesné nebo blízko ní. duplicitní položky (například lišit jednoduše tak, že image artefakty řešení nebo jpg). Tyto duplikáty byste měli odebrat tak, aby rozdělení trénování a testování neobsahují stejnou Image. Odebírání duplicitních obrázků lze dosáhnout pomocí přístupu na základě výpočtu hodnoty hash, který pracuje ve dvou krocích: (i) nejprve řetězec hash je vypočítán všechny Image, (ii) v druhého průchodu přes imagí jsou řetězce hash, která ještě nebyla udržuje pouze těchto imagí. Další Image se zahodí. Našli jsme `dhash` přístup v knihovně Python `imagehash` a popsané v tomto [blogu](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) provádět také pomocí parametru `hash_size` nastaven na hodnotu 16. Je OK nesprávně odebrat některé obrázky neduplicitní tak dlouho, dokud odebírají většinou skutečné duplicitní položky.





## <a name="conclusion"></a>Závěr

K nejdůležitějším prvkům v tomto příkladu jsou:
- Kód k trénování, vyhodnocení a nasazování modelů klasifikace obrázků.
- Ukázkové obrázky k dispozici, ale snadno přizpůsobitelné (jeden řádek změnit) Chcete-li použít vlastní image datovou sadu.
- Stav nejmodernější odborné funkce implementované tak moct trénovat přesnější modely založené na přenos.
- Interaktivní model vývoje pomocí Azure Machine Learning Workbench a Poznámkový blok Jupyter.


## <a name="references"></a>Odkazy

[1] Alex Krizhevsky Ilya Sutskever a Geoffrey E. Hinton, [ _ImageNet klasifikace s hlubokou Konvoluční Neuronové sítě_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Potokar, Shaoqing Ren a Jian Sun, [ _hluboké plyne ze zbytkových výuky pro rozpoznávání obrazu_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
